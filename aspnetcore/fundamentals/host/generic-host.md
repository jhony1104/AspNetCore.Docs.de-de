---
title: Generischer .NET-Host
author: rick-anderson
description: Erfahren Sie mehr über den generischen Host in .NET Core, der für das Starten der App und das Verwalten der Lebensdauer verantwortlich ist.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
uid: fundamentals/host/generic-host
ms.openlocfilehash: 0f8f03dabf65f2cbfe4c41d36b02a25d7902cefb
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/24/2020
ms.locfileid: "80219219"
---
# <a name="net-generic-host"></a><span data-ttu-id="272c8-103">Generischer .NET-Host</span><span class="sxs-lookup"><span data-stu-id="272c8-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="272c8-104">Dieser Artikel bietet eine Einführung in den generischen .NET Core-Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) sowie Hinweise zu dessen Verwendung.</span><span class="sxs-lookup"><span data-stu-id="272c8-104">This article introduces the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) and provides guidance on how to use it.</span></span>

## <a name="whats-a-host"></a><span data-ttu-id="272c8-105">Was ist ein Host?</span><span class="sxs-lookup"><span data-stu-id="272c8-105">What's a host?</span></span>

<span data-ttu-id="272c8-106">Der *Host* ist ein Objekt, das alle Ressourcen der App kapselt, z. B.:</span><span class="sxs-lookup"><span data-stu-id="272c8-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="272c8-107">Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="272c8-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="272c8-108">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="272c8-108">Logging</span></span>
* <span data-ttu-id="272c8-109">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="272c8-109">Configuration</span></span>
* <span data-ttu-id="272c8-110">`IHostedService`-Implementierungen</span><span class="sxs-lookup"><span data-stu-id="272c8-110">`IHostedService` implementations</span></span>

<span data-ttu-id="272c8-111">Wenn ein Host gestartet wird, ruft er `IHostedService.StartAsync` für jede Implementierung von <xref:Microsoft.Extensions.Hosting.IHostedService> auf, die im DI-Container gefunden wird.</span><span class="sxs-lookup"><span data-stu-id="272c8-111">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="272c8-112">In einer Web-App ist eine der `IHostedService`-Implementierungen ein Webdienst, der eine [HTTP-Serverimplementierung](xref:fundamentals/index#servers) startet.</span><span class="sxs-lookup"><span data-stu-id="272c8-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="272c8-113">Der wichtigste Grund für das Einschließen aller unabhängigen Ressourcen der App in einem Objekt ist die Verwaltung der Lebensdauer: steuern von Start und ordnungsgemäßem Herunterfahren der App.</span><span class="sxs-lookup"><span data-stu-id="272c8-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="272c8-114">In Versionen von ASP.NET Core vor 3.0 wird der [Webhost](xref:fundamentals/host/web-host) für HTTP-Workloads verwendet.</span><span class="sxs-lookup"><span data-stu-id="272c8-114">In versions of ASP.NET Core earlier than 3.0, the [Web Host](xref:fundamentals/host/web-host) is used for HTTP workloads.</span></span> <span data-ttu-id="272c8-115">Der Webhost wird nicht mehr für Web-Apps empfohlen und bleibt aus Gründen der Abwärtskompatibilität weiterhin verfügbar.</span><span class="sxs-lookup"><span data-stu-id="272c8-115">The Web Host is no longer recommended for web apps and remains available only for backward compatibility.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="272c8-116">Einrichten eines Hosts</span><span class="sxs-lookup"><span data-stu-id="272c8-116">Set up a host</span></span>

<span data-ttu-id="272c8-117">Der Host wird in der Regel per Code in der `Program`-Klasse konfiguriert, erstellt und ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="272c8-117">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="272c8-118">Die `Main`-Methode:</span><span class="sxs-lookup"><span data-stu-id="272c8-118">The `Main` method:</span></span>

* <span data-ttu-id="272c8-119">Ruft eine `CreateHostBuilder`-Methode zum Erstellen und Konfigurieren eines Generatorobjekts auf.</span><span class="sxs-lookup"><span data-stu-id="272c8-119">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="272c8-120">Ruft `Build`- und `Run`-Methoden für das Generatorobjekt auf.</span><span class="sxs-lookup"><span data-stu-id="272c8-120">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="272c8-121">Hier sehen Sie den *Program.cs*-Code für einen Nicht-HTTP-Workload mit einer einzelnen `IHostedService`-Implementierung, die dem DI-Container hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="272c8-121">Here's *Program.cs* code for a non-HTTP workload, with a single `IHostedService` implementation added to the DI container.</span></span> 

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

<span data-ttu-id="272c8-122">Für einen HTTP-Workload ist die `Main`-Methode die gleiche, `CreateHostBuilder` ruft jedoch `ConfigureWebHostDefaults` auf:</span><span class="sxs-lookup"><span data-stu-id="272c8-122">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="272c8-123">Ändern Sie nicht den Namen bzw. die Signatur der `CreateHostBuilder`-Methode, wenn die App Entity Framework Core verwendet.</span><span class="sxs-lookup"><span data-stu-id="272c8-123">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="272c8-124">Die [Entity Framework Core-Tools](/ef/core/miscellaneous/cli/) erwarten eine `CreateHostBuilder`-Methode, die den Host konfiguriert, ohne die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="272c8-124">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="272c8-125">Weitere Informationen finden Sie unter [DbContext-Instanzerstellung zur Entwurfszeit](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="272c8-125">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="272c8-126">Standardeinstellungen für den Generator</span><span class="sxs-lookup"><span data-stu-id="272c8-126">Default builder settings</span></span>

<span data-ttu-id="272c8-127">Die <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>-Methode:</span><span class="sxs-lookup"><span data-stu-id="272c8-127">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="272c8-128">Legt das [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) auf den Pfad fest, der von <xref:System.IO.Directory.GetCurrentDirectory*> zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="272c8-128">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="272c8-129">Lädt Hostkonfiguration aus:</span><span class="sxs-lookup"><span data-stu-id="272c8-129">Loads host configuration from:</span></span>
  * <span data-ttu-id="272c8-130">Umgebungsvariablen mit dem Präfix `DOTNET_`.</span><span class="sxs-lookup"><span data-stu-id="272c8-130">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="272c8-131">Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="272c8-131">Command-line arguments.</span></span>
* <span data-ttu-id="272c8-132">Lädt App-Konfiguration aus:</span><span class="sxs-lookup"><span data-stu-id="272c8-132">Loads app configuration from:</span></span>
  * <span data-ttu-id="272c8-133">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="272c8-133">*appsettings.json*.</span></span>
  * <span data-ttu-id="272c8-134">*appsettings.{Environment}.json*</span><span class="sxs-lookup"><span data-stu-id="272c8-134">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="272c8-135">[Geheimnis-Manager](xref:security/app-secrets), wenn die App in der `Development`-Umgebung ausgeführt wird</span><span class="sxs-lookup"><span data-stu-id="272c8-135">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="272c8-136">Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="272c8-136">Environment variables.</span></span>
  * <span data-ttu-id="272c8-137">Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="272c8-137">Command-line arguments.</span></span>
* <span data-ttu-id="272c8-138">Fügt die folgenden [Protokollierungsanbieter](xref:fundamentals/logging/index) hinzu:</span><span class="sxs-lookup"><span data-stu-id="272c8-138">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="272c8-139">Konsole</span><span class="sxs-lookup"><span data-stu-id="272c8-139">Console</span></span>
  * <span data-ttu-id="272c8-140">Debug</span><span class="sxs-lookup"><span data-stu-id="272c8-140">Debug</span></span>
  * <span data-ttu-id="272c8-141">EventSource</span><span class="sxs-lookup"><span data-stu-id="272c8-141">EventSource</span></span>
  * <span data-ttu-id="272c8-142">EventLog (nur bei Ausführung unter Windows)</span><span class="sxs-lookup"><span data-stu-id="272c8-142">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="272c8-143">Aktiviert [Bereichsvalidierung](xref:fundamentals/dependency-injection#scope-validation) und [Abhängigkeitsüberprüfung](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild), wenn es sich um eine Entwicklungsumgebung handelt.</span><span class="sxs-lookup"><span data-stu-id="272c8-143">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="272c8-144">Die `ConfigureWebHostDefaults`-Methode:</span><span class="sxs-lookup"><span data-stu-id="272c8-144">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="272c8-145">Lädt Hostkonfiguration aus Umgebungsvariablen mit dem Präfix `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="272c8-145">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="272c8-146">Legt den [Kestrel](xref:fundamentals/servers/kestrel)-Server als Webserver fest und konfiguriert ihn mithilfe der Hostkonfigurationsanbieter der App.</span><span class="sxs-lookup"><span data-stu-id="272c8-146">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="272c8-147">Die Standardoptionen des Kestrel-Servers finden Sie unter <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="272c8-147">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="272c8-148">Fügt [Middleware zum Filtern von Hosts](xref:fundamentals/servers/kestrel#host-filtering) hinzu.</span><span class="sxs-lookup"><span data-stu-id="272c8-148">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="272c8-149">Fügt [Middleware für weitergeleitete Header](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) hinzu, wenn `ASPNETCORE_FORWARDEDHEADERS_ENABLED` den Wert `true` aufweist.</span><span class="sxs-lookup"><span data-stu-id="272c8-149">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="272c8-150">Ermöglicht die Integration von IIS.</span><span class="sxs-lookup"><span data-stu-id="272c8-150">Enables IIS integration.</span></span> <span data-ttu-id="272c8-151">Informationen zu den IIS-Standardoptionen finden Sie unter <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="272c8-151">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="272c8-152">In den Abschnitten [Einstellungen für alle App-Typen](#settings-for-all-app-types) und [Einstellungen für Web-Apps](#settings-for-web-apps) weiter unten in diesem Artikel wird beschrieben, wie die Standardeinstellungen des Generators außer Kraft gesetzt werden.</span><span class="sxs-lookup"><span data-stu-id="272c8-152">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="272c8-153">Von Frameworks bereitgestellte Dienste</span><span class="sxs-lookup"><span data-stu-id="272c8-153">Framework-provided services</span></span>

<span data-ttu-id="272c8-154">Die folgenden Dienste werden automatisch registriert:</span><span class="sxs-lookup"><span data-stu-id="272c8-154">The following services are registered automatically:</span></span>

* [<span data-ttu-id="272c8-155">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="272c8-155">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="272c8-156">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="272c8-156">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="272c8-157">IHostEnvironment/IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="272c8-157">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="272c8-158">Weitere Informationen zu den vom Framework bereitgestellten Diensten finden Sie unter <xref:fundamentals/dependency-injection#framework-provided-services>.</span><span class="sxs-lookup"><span data-stu-id="272c8-158">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="272c8-159">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="272c8-159">IHostApplicationLifetime</span></span>

<span data-ttu-id="272c8-160">Fügt den <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>-Dienst (vorher `IApplicationLifetime`-Dienst) in eine beliebige Klasse ein, um die Aktivitäten nach dem Starten und die Aufgaben für ordnungsgemäßes Herunterfahren zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="272c8-160">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="272c8-161">Bei drei der Eigenschaften der Schnittstelle handelt es sich um Abbruchtokens, die zum Registrieren von Ereignishandlermethoden zum Starten und Beenden von Apps verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="272c8-161">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="272c8-162">Die Benutzeroberfläche umfasst auch eine `StopApplication`-Methode.</span><span class="sxs-lookup"><span data-stu-id="272c8-162">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="272c8-163">Das folgende Beispiel zeigt eine `IHostedService`-Implementierung, die `IHostApplicationLifetime`-Ereignisse registriert:</span><span class="sxs-lookup"><span data-stu-id="272c8-163">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="272c8-164">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="272c8-164">IHostLifetime</span></span>

<span data-ttu-id="272c8-165">Die <xref:Microsoft.Extensions.Hosting.IHostLifetime>-Implementierung steuert, wann der Host gestartet und beendet wird.</span><span class="sxs-lookup"><span data-stu-id="272c8-165">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="272c8-166">Die zuletzt registrierte Implementierung wird verwendet.</span><span class="sxs-lookup"><span data-stu-id="272c8-166">The last implementation registered is used.</span></span>

<span data-ttu-id="272c8-167">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` ist die `IHostLifetime`-Standardimplementierung.</span><span class="sxs-lookup"><span data-stu-id="272c8-167">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="272c8-168">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="272c8-168">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="272c8-169">lauscht auf <kbd>STRG</kbd>+<kbd>C</kbd>/SIGINT oder SIGTERM und ruft <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> auf, um das Herunterfahren zu beginnen.</span><span class="sxs-lookup"><span data-stu-id="272c8-169">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="272c8-170">Hebt die Blockierung von Erweiterungen wie [RunAsync](#runasync) und [WaitForShutdownAsync](#waitforshutdownasync) auf.</span><span class="sxs-lookup"><span data-stu-id="272c8-170">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="272c8-171">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="272c8-171">IHostEnvironment</span></span>

<span data-ttu-id="272c8-172">Fügt den <xref:Microsoft.Extensions.Hosting.IHostEnvironment>-Dienst in eine Klasse ein, um Informationen über die folgenden Einstellungen abzurufen:</span><span class="sxs-lookup"><span data-stu-id="272c8-172">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="272c8-173">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="272c8-173">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="272c8-174">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="272c8-174">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="272c8-175">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="272c8-175">ContentRootPath</span></span>](#contentrootpath)

<span data-ttu-id="272c8-176">Web-Apps implementieren die `IWebHostEnvironment`-Schnittstelle, die `IHostEnvironment` erbt und [WebRootPath](#webroot) hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="272c8-176">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="272c8-177">Konfiguration des Hosts</span><span class="sxs-lookup"><span data-stu-id="272c8-177">Host configuration</span></span>

<span data-ttu-id="272c8-178">Die Hostkonfiguration wird für die Eigenschaften der <xref:Microsoft.Extensions.Hosting.IHostEnvironment>-Implementierung verwendet.</span><span class="sxs-lookup"><span data-stu-id="272c8-178">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="272c8-179">Sie ist über [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> verfügbar.</span><span class="sxs-lookup"><span data-stu-id="272c8-179">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="272c8-180">Nach `ConfigureAppConfiguration` wird `HostBuilderContext.Configuration` durch die App-Konfiguration ersetzt.</span><span class="sxs-lookup"><span data-stu-id="272c8-180">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="272c8-181">Rufen Sie <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> für `IHostBuilder` auf, um die Hostkonfiguration hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="272c8-181">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="272c8-182">`ConfigureHostConfiguration` kann mehrfach mit additiven Ergebnissen aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="272c8-182">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="272c8-183">Der Host verwendet die Option, die zuletzt einen Wert für einen bestimmten Schlüssel festlegt.</span><span class="sxs-lookup"><span data-stu-id="272c8-183">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="272c8-184">Der Umgebungsvariablenanbieter mit dem Präfix `DOTNET_` und die Befehlszeilenargumente sind in `CreateDefaultBuilder` enthalten.</span><span class="sxs-lookup"><span data-stu-id="272c8-184">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="272c8-185">Für Web-Apps wird der Umgebungsvariablenanbieter mit dem Präfix `ASPNETCORE_` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="272c8-185">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="272c8-186">Das Präfix wird entfernt, wenn die Umgebungsvariablen gelesen werden.</span><span class="sxs-lookup"><span data-stu-id="272c8-186">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="272c8-187">Der Wert der Umgebungsvariable für `ASPNETCORE_ENVIRONMENT` wird z. B. der Hostkonfigurationswert für den `environment`-Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="272c8-187">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="272c8-188">Im folgenden Beispiel wird eine Hostkonfiguration erstellt:</span><span class="sxs-lookup"><span data-stu-id="272c8-188">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="272c8-189">App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="272c8-189">App configuration</span></span>

<span data-ttu-id="272c8-190">Die App-Konfiguration wird durch Aufrufen von <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> für `IHostBuilder` erstellt.</span><span class="sxs-lookup"><span data-stu-id="272c8-190">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="272c8-191">`ConfigureAppConfiguration` kann mehrfach mit additiven Ergebnissen aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="272c8-191">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="272c8-192">Die App verwendet die Option, die zuletzt einen Wert für einen bestimmten Schlüssel festlegt.</span><span class="sxs-lookup"><span data-stu-id="272c8-192">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="272c8-193">Die von `ConfigureAppConfiguration` erstellte Konfiguration ist unter [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) verfügbar und kann für nachfolgende Vorgänge und als Dienst für die Abhängigkeitsinjektion verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="272c8-193">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="272c8-194">Die Hostkonfiguration wird ebenfalls der App-Konfiguration hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="272c8-194">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="272c8-195">Weitere Informationen finden Sie unter [Konfiguration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="272c8-195">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="272c8-196">Einstellungen für alle App-Typen</span><span class="sxs-lookup"><span data-stu-id="272c8-196">Settings for all app types</span></span>

<span data-ttu-id="272c8-197">In diesem Abschnitt werden Hosteinstellungen aufgeführt, die sowohl für HTTP- als auch für Nicht-HTTP-Workloads gelten.</span><span class="sxs-lookup"><span data-stu-id="272c8-197">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="272c8-198">Standardmäßig können Umgebungsvariablen, die zur Konfiguration dieser Einstellungen verwendet werden, ein `DOTNET_`- oder `ASPNETCORE_`-Präfix haben.</span><span class="sxs-lookup"><span data-stu-id="272c8-198">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="272c8-199">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="272c8-199">ApplicationName</span></span>

<span data-ttu-id="272c8-200">Die Eigenschaft [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) wird von der Hostkonfiguration während der Hosterstellung festgelegt.</span><span class="sxs-lookup"><span data-stu-id="272c8-200">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="272c8-201">**Schlüssel**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="272c8-201">**Key**: `applicationName`</span></span>  
<span data-ttu-id="272c8-202">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="272c8-202">**Type**: `string`</span></span>  
<span data-ttu-id="272c8-203">**Standard**: Der Name der Assembly, die den Einstiegspunkt der App enthält.</span><span class="sxs-lookup"><span data-stu-id="272c8-203">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="272c8-204">**Umgebungsvariable:** `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="272c8-204">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="272c8-205">Verwenden Sie die Umgebungsvariable, um diesen Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="272c8-205">To set this value, use the environment variable.</span></span> 

### <a name="contentrootpath"></a><span data-ttu-id="272c8-206">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="272c8-206">ContentRootPath</span></span>

<span data-ttu-id="272c8-207">Die Eigenschaft [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) bestimmt, wo der Host mit der Suche nach Inhaltsdateien beginnt.</span><span class="sxs-lookup"><span data-stu-id="272c8-207">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="272c8-208">Wenn der Pfad nicht vorhanden ist, kann der Host nicht gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="272c8-208">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="272c8-209">**Schlüssel**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="272c8-209">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="272c8-210">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="272c8-210">**Type**: `string`</span></span>  
<span data-ttu-id="272c8-211">**Standard**: Der Ordner, in dem die App-Assembly gespeichert ist.</span><span class="sxs-lookup"><span data-stu-id="272c8-211">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="272c8-212">**Umgebungsvariable:** `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="272c8-212">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="272c8-213">Verwenden Sie die Umgebungsvariable, oder rufen Sie `UseContentRoot` für `IHostBuilder` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="272c8-213">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="272c8-214">Weitere Informationen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="272c8-214">For more information, see:</span></span>

* [<span data-ttu-id="272c8-215">Grundlagen: Inhaltsstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="272c8-215">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="272c8-216">WebRoot</span><span class="sxs-lookup"><span data-stu-id="272c8-216">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="272c8-217">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="272c8-217">EnvironmentName</span></span>

<span data-ttu-id="272c8-218">Die Eigenschaft [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) kann auf einen beliebigen Wert festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="272c8-218">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="272c8-219">Zu den durch Frameworks definierten Werten zählen `Development`, `Staging` und `Production`.</span><span class="sxs-lookup"><span data-stu-id="272c8-219">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="272c8-220">Die Groß-/Kleinschreibung wird bei Werten nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="272c8-220">Values aren't case-sensitive.</span></span>

<span data-ttu-id="272c8-221">**Schlüssel**: `environment`</span><span class="sxs-lookup"><span data-stu-id="272c8-221">**Key**: `environment`</span></span>  
<span data-ttu-id="272c8-222">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="272c8-222">**Type**: `string`</span></span>  
<span data-ttu-id="272c8-223">**Standard**: `Production`</span><span class="sxs-lookup"><span data-stu-id="272c8-223">**Default**: `Production`</span></span>  
<span data-ttu-id="272c8-224">**Umgebungsvariable:** `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="272c8-224">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="272c8-225">Verwenden Sie die Umgebungsvariable, oder rufen Sie `UseEnvironment` für `IHostBuilder` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="272c8-225">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="272c8-226">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="272c8-226">ShutdownTimeout</span></span>

<span data-ttu-id="272c8-227">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) legt das Zeitlimit für <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> fest.</span><span class="sxs-lookup"><span data-stu-id="272c8-227">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="272c8-228">Der Standardwert beträgt fünf Sekunden.</span><span class="sxs-lookup"><span data-stu-id="272c8-228">The default value is five seconds.</span></span>  <span data-ttu-id="272c8-229">Während des Zeitlimits verhält sich der Host folgendermaßen:</span><span class="sxs-lookup"><span data-stu-id="272c8-229">During the timeout period, the host:</span></span>

* <span data-ttu-id="272c8-230">Er löst [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.ihostapplicationlifetime.applicationstopping) aus.</span><span class="sxs-lookup"><span data-stu-id="272c8-230">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="272c8-231">Er versucht, gehostete Dienste zu beenden und protokolliert Fehler für Dienste, die nicht beendet werden können.</span><span class="sxs-lookup"><span data-stu-id="272c8-231">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="272c8-232">Wenn das Zeitlimit abläuft bevor alle gehosteten Dienste beendet sind, werden alle verbleibenden aktiven Dienste beendet, wenn die App herunterfährt.</span><span class="sxs-lookup"><span data-stu-id="272c8-232">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="272c8-233">Die Dienste werden beendet, selbst wenn die Verarbeitung noch nicht abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="272c8-233">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="272c8-234">Wenn die Dienste mehr Zeit zum Beenden benötigen, sollten Sie das Zeitlimit erhöhen.</span><span class="sxs-lookup"><span data-stu-id="272c8-234">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="272c8-235">**Schlüssel**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="272c8-235">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="272c8-236">**Typ:** `int`</span><span class="sxs-lookup"><span data-stu-id="272c8-236">**Type**: `int`</span></span>  
<span data-ttu-id="272c8-237">**Standard**: 5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="272c8-237">**Default**: 5 seconds</span></span>  
<span data-ttu-id="272c8-238">**Umgebungsvariable:** `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="272c8-238">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="272c8-239">Verwenden Sie die Umgebungsvariable, oder konfigurieren Sie `HostOptions`, um diesen Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="272c8-239">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="272c8-240">Im folgenden Beispiel wird das Zeitlimit auf 20 Sekunden festgelegt:</span><span class="sxs-lookup"><span data-stu-id="272c8-240">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="272c8-241">Deaktivieren des Neuladens der App-Konfiguration bei Änderungen</span><span class="sxs-lookup"><span data-stu-id="272c8-241">Disable app configuration reload on change</span></span>

<span data-ttu-id="272c8-242">Die Dateien *appsettings.json* und *appsettings.{Environment}.json* werden [standardmäßig](xref:fundamentals/configuration/index#default) neu geladen, wenn die Datei geändert wird.</span><span class="sxs-lookup"><span data-stu-id="272c8-242">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="272c8-243">Zum Deaktivieren dieses Verhaltens in ASP.NET Core 5.0 Preview 3 oder höher müssen Sie den `hostBuilder:reloadConfigOnChange`-Schlüssel auf `false` festlegen.</span><span class="sxs-lookup"><span data-stu-id="272c8-243">To disable this reload behavior in ASP.NET Core 5.0 Preview 3 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="272c8-244">**Schlüssel**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="272c8-244">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="272c8-245">**Typ:** `bool` (`true` oder `1`)</span><span class="sxs-lookup"><span data-stu-id="272c8-245">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="272c8-246">**Standard**: `true`</span><span class="sxs-lookup"><span data-stu-id="272c8-246">**Default**: `true`</span></span>  
<span data-ttu-id="272c8-247">**Befehlszeilenargument:** `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="272c8-247">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="272c8-248">**Umgebungsvariable:** `<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="272c8-248">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="272c8-249">Das Trennzeichen `:` funktioniert nicht auf allen Plattformen mit den hierarchischen Schlüsseln von Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="272c8-249">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="272c8-250">Weitere Informationen finden Sie unter [Umgebungsvariablen](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="272c8-250">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="272c8-251">Einstellungen für Web-Apps</span><span class="sxs-lookup"><span data-stu-id="272c8-251">Settings for web apps</span></span>

<span data-ttu-id="272c8-252">Einige Hosteinstellungen gelten nur für HTTP-Workloads.</span><span class="sxs-lookup"><span data-stu-id="272c8-252">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="272c8-253">Standardmäßig können Umgebungsvariablen, die zur Konfiguration dieser Einstellungen verwendet werden, ein `DOTNET_`- oder `ASPNETCORE_`-Präfix haben.</span><span class="sxs-lookup"><span data-stu-id="272c8-253">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="272c8-254">Für diese Einstellungen sind Erweiterungsmethoden in `IWebHostBuilder` verfügbar.</span><span class="sxs-lookup"><span data-stu-id="272c8-254">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="272c8-255">In den Codebeispielen, die zeigen, wie Sie die Erweiterungsmethoden aufrufen können, wird davon ausgegangen, dass `webBuilder` eine Instanz von `IWebHostBuilder` ist. Sehen Sie hierzu das folgende Beispiel:</span><span class="sxs-lookup"><span data-stu-id="272c8-255">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="272c8-256">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="272c8-256">CaptureStartupErrors</span></span>

<span data-ttu-id="272c8-257">Wenn diese `false` ist, führen Fehler beim Start zum Beenden des Hosts.</span><span class="sxs-lookup"><span data-stu-id="272c8-257">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="272c8-258">Wenn diese `true` ist, erfasst der Host Ausnahmen während des Starts und versucht, den Server zu starten.</span><span class="sxs-lookup"><span data-stu-id="272c8-258">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="272c8-259">**Schlüssel**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="272c8-259">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="272c8-260">**Typ:** `bool` (`true` oder `1`)</span><span class="sxs-lookup"><span data-stu-id="272c8-260">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="272c8-261">**Standard**: Die Standardeinstellung ist gleich `false`, es sei denn, die App wird mit Kestrel hinter IIS ausgeführt, dann ist sie gleich `true`.</span><span class="sxs-lookup"><span data-stu-id="272c8-261">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="272c8-262">**Umgebungsvariable:** `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="272c8-262">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="272c8-263">Verwenden Sie die Konfiguration, oder rufen Sie `CaptureStartupErrors` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="272c8-263">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="272c8-264">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="272c8-264">DetailedErrors</span></span>

<span data-ttu-id="272c8-265">Wenn diese Einstellung aktiviert ist oder die Umgebung auf `Development` festgelegt ist, erfasst die App detaillierte Fehler.</span><span class="sxs-lookup"><span data-stu-id="272c8-265">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="272c8-266">**Schlüssel**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="272c8-266">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="272c8-267">**Typ:** `bool` (`true` oder `1`)</span><span class="sxs-lookup"><span data-stu-id="272c8-267">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="272c8-268">**Standard**: `false`</span><span class="sxs-lookup"><span data-stu-id="272c8-268">**Default**: `false`</span></span>  
<span data-ttu-id="272c8-269">**Umgebungsvariable:** `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="272c8-269">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="272c8-270">Verwenden Sie die Konfiguration, oder rufen Sie `UseSetting` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="272c8-270">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="272c8-271">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="272c8-271">HostingStartupAssemblies</span></span>

<span data-ttu-id="272c8-272">Eine durch Semikolons getrennte Zeichenfolge der Hostingstartassemblys, die beim Start geladen werden soll.</span><span class="sxs-lookup"><span data-stu-id="272c8-272">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="272c8-273">Obwohl der Konfigurationswert standardmäßig auf eine leere Zeichenfolge festgelegt ist, enthalten die Hostingstartassemblys immer die Assembly der App.</span><span class="sxs-lookup"><span data-stu-id="272c8-273">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="272c8-274">Wenn Hostingstartassemblys bereitgestellt werden, werden diese zur Assembly der App hinzugefügt, damit diese geladen werden, wenn die App während des Starts die allgemeinen Dienste erstellt.</span><span class="sxs-lookup"><span data-stu-id="272c8-274">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="272c8-275">**Schlüssel**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="272c8-275">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="272c8-276">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="272c8-276">**Type**: `string`</span></span>  
<span data-ttu-id="272c8-277">**Standard**: Leere Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="272c8-277">**Default**: Empty string</span></span>  
<span data-ttu-id="272c8-278">**Umgebungsvariable:** `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="272c8-278">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="272c8-279">Verwenden Sie die Konfiguration, oder rufen Sie `UseSetting` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="272c8-279">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="272c8-280">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="272c8-280">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="272c8-281">Eine durch Semikolons getrennte Zeichenfolge der Hostingstartassemblys, die beim Start ausgeschlossen werden sollen.</span><span class="sxs-lookup"><span data-stu-id="272c8-281">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="272c8-282">**Schlüssel**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="272c8-282">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="272c8-283">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="272c8-283">**Type**: `string`</span></span>  
<span data-ttu-id="272c8-284">**Standard**: Leere Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="272c8-284">**Default**: Empty string</span></span>  
<span data-ttu-id="272c8-285">**Umgebungsvariable:** `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="272c8-285">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="272c8-286">Verwenden Sie die Konfiguration, oder rufen Sie `UseSetting` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="272c8-286">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="272c8-287">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="272c8-287">HTTPS_Port</span></span>

<span data-ttu-id="272c8-288">Der HTTPS-Umleitungsport.</span><span class="sxs-lookup"><span data-stu-id="272c8-288">The HTTPS redirect port.</span></span> <span data-ttu-id="272c8-289">Wird in [Erzwingen von HTTPS](xref:security/enforcing-ssl) verwendet.</span><span class="sxs-lookup"><span data-stu-id="272c8-289">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="272c8-290">**Schlüssel**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="272c8-290">**Key**: `https_port`</span></span>  
<span data-ttu-id="272c8-291">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="272c8-291">**Type**: `string`</span></span>  
<span data-ttu-id="272c8-292">**Standard**: Es ist kein Standardwert festgelegt.</span><span class="sxs-lookup"><span data-stu-id="272c8-292">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="272c8-293">**Umgebungsvariable:** `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="272c8-293">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="272c8-294">Verwenden Sie die Konfiguration, oder rufen Sie `UseSetting` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="272c8-294">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="272c8-295">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="272c8-295">PreferHostingUrls</span></span>

<span data-ttu-id="272c8-296">Gibt an, ob der Host auf die URLs lauschen soll, die mit `IWebHostBuilder` konfiguriert wurden, anstatt auf die URLs zu lauschen, die mit der `IServer`-Implementierung konfiguriert wurden.</span><span class="sxs-lookup"><span data-stu-id="272c8-296">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="272c8-297">**Schlüssel**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="272c8-297">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="272c8-298">**Typ:** `bool` (`true` oder `1`)</span><span class="sxs-lookup"><span data-stu-id="272c8-298">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="272c8-299">**Standard**: `true`</span><span class="sxs-lookup"><span data-stu-id="272c8-299">**Default**: `true`</span></span>  
<span data-ttu-id="272c8-300">**Umgebungsvariable:** `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="272c8-300">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="272c8-301">Verwenden Sie die Umgebungsvariable, oder rufen Sie `PreferHostingUrls` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="272c8-301">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="272c8-302">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="272c8-302">PreventHostingStartup</span></span>

<span data-ttu-id="272c8-303">Verhindert das automatische Laden der Hostingstartassemblys, einschließlich denen, die von der Assembly der App konfiguriert wurden.</span><span class="sxs-lookup"><span data-stu-id="272c8-303">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="272c8-304">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="272c8-304">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="272c8-305">**Schlüssel**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="272c8-305">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="272c8-306">**Typ:** `bool` (`true` oder `1`)</span><span class="sxs-lookup"><span data-stu-id="272c8-306">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="272c8-307">**Standard**: `false`</span><span class="sxs-lookup"><span data-stu-id="272c8-307">**Default**: `false`</span></span>  
<span data-ttu-id="272c8-308">**Umgebungsvariable:** `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="272c8-308">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="272c8-309">Verwenden Sie die Umgebungsvariable, oder rufen Sie `UseSetting` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="272c8-309">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="272c8-310">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="272c8-310">StartupAssembly</span></span>

<span data-ttu-id="272c8-311">Die Assembly, die nach der `Startup`-Klasse suchen soll.</span><span class="sxs-lookup"><span data-stu-id="272c8-311">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="272c8-312">**Schlüssel**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="272c8-312">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="272c8-313">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="272c8-313">**Type**: `string`</span></span>  
<span data-ttu-id="272c8-314">**Standard**: Die Assembly der App</span><span class="sxs-lookup"><span data-stu-id="272c8-314">**Default**: The app's assembly</span></span>  
<span data-ttu-id="272c8-315">**Umgebungsvariable:** `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="272c8-315">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="272c8-316">Verwenden Sie die Umgebungsvariable, oder rufen Sie `UseStartup` auf, um diesen Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="272c8-316">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="272c8-317">`UseStartup` kann einen Assemblynamen (`string`) oder einen Typ (`TStartup`) annehmen.</span><span class="sxs-lookup"><span data-stu-id="272c8-317">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="272c8-318">Wenn mehrere `UseStartup`-Methoden aufgerufen werden, hat die letzte Vorrang.</span><span class="sxs-lookup"><span data-stu-id="272c8-318">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="272c8-319">URLs</span><span class="sxs-lookup"><span data-stu-id="272c8-319">URLs</span></span>

<span data-ttu-id="272c8-320">Eine durch Semikolons getrennte Liste mit IP-Adressen oder Hostadressen mit Ports und Protokollen, denen der Server für Anforderungen lauschen soll.</span><span class="sxs-lookup"><span data-stu-id="272c8-320">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="272c8-321">Beispielsweise `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="272c8-321">For example, `http://localhost:123`.</span></span> <span data-ttu-id="272c8-322">Verwenden Sie \*, um anzugeben, dass der Server mithilfe des angegebenen Ports und Protokolls (z.B. `http://*:5000`) den Anfragen aller IP-Adressen oder Hostnamen lauschen soll.</span><span class="sxs-lookup"><span data-stu-id="272c8-322">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="272c8-323">Das Protokoll (`http://` oder `https://`) muss in jeder URL enthalten sein.</span><span class="sxs-lookup"><span data-stu-id="272c8-323">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="272c8-324">Die unterstützten Formate variieren bei den verschiedenen Servern.</span><span class="sxs-lookup"><span data-stu-id="272c8-324">Supported formats vary among servers.</span></span>

<span data-ttu-id="272c8-325">**Schlüssel**: `urls`</span><span class="sxs-lookup"><span data-stu-id="272c8-325">**Key**: `urls`</span></span>  
<span data-ttu-id="272c8-326">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="272c8-326">**Type**: `string`</span></span>  
<span data-ttu-id="272c8-327">**Standard**: `http://localhost:5000` und `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="272c8-327">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="272c8-328">**Umgebungsvariable:** `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="272c8-328">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="272c8-329">Verwenden Sie die Umgebungsvariable, oder rufen Sie `UseUrls` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="272c8-329">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="272c8-330">Kestrel verfügt über eine eigene API für die Endpunktkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="272c8-330">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="272c8-331">Weitere Informationen finden Sie unter <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="272c8-331">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="272c8-332">WebRoot</span><span class="sxs-lookup"><span data-stu-id="272c8-332">WebRoot</span></span>

<span data-ttu-id="272c8-333">Der relative Pfad für die statischen Objekte der App.</span><span class="sxs-lookup"><span data-stu-id="272c8-333">The relative path to the app's static assets.</span></span>

<span data-ttu-id="272c8-334">**Schlüssel**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="272c8-334">**Key**: `webroot`</span></span>  
<span data-ttu-id="272c8-335">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="272c8-335">**Type**: `string`</span></span>  
<span data-ttu-id="272c8-336">**Standard**: Der Standardwert ist `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="272c8-336">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="272c8-337">Der Pfad zu *{Inhaltsstammverzeichnis}/wwwroot* muss vorhanden sein.</span><span class="sxs-lookup"><span data-stu-id="272c8-337">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="272c8-338">Wenn der Pfad nicht vorhanden ist, wird ein Dateianbieter ohne Funktion verwendet.</span><span class="sxs-lookup"><span data-stu-id="272c8-338">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="272c8-339">**Umgebungsvariable:** `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="272c8-339">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="272c8-340">Verwenden Sie die Umgebungsvariable, oder rufen Sie `UseWebRoot` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="272c8-340">To set this value, use the environment variable or call `UseWebRoot`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="272c8-341">Weitere Informationen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="272c8-341">For more information, see:</span></span>

* [<span data-ttu-id="272c8-342">Grundlagen: Webstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="272c8-342">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="272c8-343">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="272c8-343">ContentRootPath</span></span>](#contentrootpath)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="272c8-344">Verwalten der Lebensdauer des Hosts</span><span class="sxs-lookup"><span data-stu-id="272c8-344">Manage the host lifetime</span></span>

<span data-ttu-id="272c8-345">Rufen Sie Methoden für die erstellte <xref:Microsoft.Extensions.Hosting.IHost>-Implementierung auf, um die App zu starten und zu beenden.</span><span class="sxs-lookup"><span data-stu-id="272c8-345">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="272c8-346">Diese Methoden wirken sich auf alle <xref:Microsoft.Extensions.Hosting.IHostedService>-Implementierungen aus, die im Dienstcontainer registriert sind.</span><span class="sxs-lookup"><span data-stu-id="272c8-346">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="272c8-347">Run</span><span class="sxs-lookup"><span data-stu-id="272c8-347">Run</span></span>

<span data-ttu-id="272c8-348">Durch <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> wird die App gestartet und der aufrufende Thread blockiert, bis der Host heruntergefahren wird.</span><span class="sxs-lookup"><span data-stu-id="272c8-348"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="272c8-349">RunAsync</span><span class="sxs-lookup"><span data-stu-id="272c8-349">RunAsync</span></span>

<span data-ttu-id="272c8-350">Durch <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> wird die App ausgeführt und eine <xref:System.Threading.Tasks.Task>-Methode ausgegeben, die abgeschlossen wird, wenn das Abbruchtoken oder das Herunterfahren ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="272c8-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="272c8-351">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="272c8-351">RunConsoleAsync</span></span>

<span data-ttu-id="272c8-352"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> aktiviert die Unterstützung der Konsole, erstellt und startet den Host und lauscht auf <kbd>STRG</kbd>+<kbd>C</kbd>/SIGINT oder SIGTERM, um das Herunterfahren auszulösen.</span><span class="sxs-lookup"><span data-stu-id="272c8-352"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="272c8-353">Starten</span><span class="sxs-lookup"><span data-stu-id="272c8-353">Start</span></span>

<span data-ttu-id="272c8-354"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> startet den Host synchron.</span><span class="sxs-lookup"><span data-stu-id="272c8-354"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="272c8-355">StartAsync</span><span class="sxs-lookup"><span data-stu-id="272c8-355">StartAsync</span></span>

<span data-ttu-id="272c8-356">Durch <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> wird der Host gestartet und eine <xref:System.Threading.Tasks.Task>-Methode ausgegeben, die abgeschlossen wird, wenn das Abbruchtoken oder das Herunterfahren ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="272c8-356"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="272c8-357"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> wird am Anfang der `StartAsync`-Methode aufgerufen, die den Vorgang erst fortsetzt, wenn sie abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="272c8-357"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="272c8-358">Dies kann verwendet werden, um den Start zu verzögern, bis dieser durch ein externes Ereignis initiiert wird.</span><span class="sxs-lookup"><span data-stu-id="272c8-358">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="272c8-359">StopAsync</span><span class="sxs-lookup"><span data-stu-id="272c8-359">StopAsync</span></span>

<span data-ttu-id="272c8-360"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> versucht, den Host innerhalb des angegebenen Timeouts zu beenden.</span><span class="sxs-lookup"><span data-stu-id="272c8-360"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="272c8-361">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="272c8-361">WaitForShutdown</span></span>

<span data-ttu-id="272c8-362"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blockiert den aufrufenden Thread, bis das Herunterfahren durch den IHostLifetime, z. B. über <kbd>STRG</kbd>+<kbd>C</kbd>/SIGINT oder SIGTERM, ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="272c8-362"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="272c8-363">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="272c8-363">WaitForShutdownAsync</span></span>

<span data-ttu-id="272c8-364"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> gibt eine <xref:System.Threading.Tasks.Task>-Methode zurück, die abgeschlossen wird, wenn das Herunterfahren über das angegebene Token ausgelöst wird, und ruft <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> auf.</span><span class="sxs-lookup"><span data-stu-id="272c8-364"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="272c8-365">Externe Steuerung</span><span class="sxs-lookup"><span data-stu-id="272c8-365">External control</span></span>

<span data-ttu-id="272c8-366">Die Lebensdauer des Hosts kann mithilfe von Methoden, die extern aufgerufen werden können, direkt gesteuert werden:</span><span class="sxs-lookup"><span data-stu-id="272c8-366">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0 <= aspnetcore-3.1"

<span data-ttu-id="272c8-367">Dieser Artikel bietet eine Einführung in den generischen .NET Core-Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) sowie Hinweise zu dessen Verwendung.</span><span class="sxs-lookup"><span data-stu-id="272c8-367">This article introduces the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) and provides guidance on how to use it.</span></span>

## <a name="whats-a-host"></a><span data-ttu-id="272c8-368">Was ist ein Host?</span><span class="sxs-lookup"><span data-stu-id="272c8-368">What's a host?</span></span>

<span data-ttu-id="272c8-369">Der *Host* ist ein Objekt, das alle Ressourcen der App kapselt, z. B.:</span><span class="sxs-lookup"><span data-stu-id="272c8-369">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="272c8-370">Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="272c8-370">Dependency injection (DI)</span></span>
* <span data-ttu-id="272c8-371">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="272c8-371">Logging</span></span>
* <span data-ttu-id="272c8-372">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="272c8-372">Configuration</span></span>
* <span data-ttu-id="272c8-373">`IHostedService`-Implementierungen</span><span class="sxs-lookup"><span data-stu-id="272c8-373">`IHostedService` implementations</span></span>

<span data-ttu-id="272c8-374">Wenn ein Host gestartet wird, ruft er `IHostedService.StartAsync` für jede Implementierung von <xref:Microsoft.Extensions.Hosting.IHostedService> auf, die im DI-Container gefunden wird.</span><span class="sxs-lookup"><span data-stu-id="272c8-374">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="272c8-375">In einer Web-App ist eine der `IHostedService`-Implementierungen ein Webdienst, der eine [HTTP-Serverimplementierung](xref:fundamentals/index#servers) startet.</span><span class="sxs-lookup"><span data-stu-id="272c8-375">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="272c8-376">Der wichtigste Grund für das Einschließen aller unabhängigen Ressourcen der App in einem Objekt ist die Verwaltung der Lebensdauer: steuern von Start und ordnungsgemäßem Herunterfahren der App.</span><span class="sxs-lookup"><span data-stu-id="272c8-376">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="272c8-377">In Versionen von ASP.NET Core vor 3.0 wird der [Webhost](xref:fundamentals/host/web-host) für HTTP-Workloads verwendet.</span><span class="sxs-lookup"><span data-stu-id="272c8-377">In versions of ASP.NET Core earlier than 3.0, the [Web Host](xref:fundamentals/host/web-host) is used for HTTP workloads.</span></span> <span data-ttu-id="272c8-378">Der Webhost wird nicht mehr für Web-Apps empfohlen und bleibt aus Gründen der Abwärtskompatibilität weiterhin verfügbar.</span><span class="sxs-lookup"><span data-stu-id="272c8-378">The Web Host is no longer recommended for web apps and remains available only for backward compatibility.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="272c8-379">Einrichten eines Hosts</span><span class="sxs-lookup"><span data-stu-id="272c8-379">Set up a host</span></span>

<span data-ttu-id="272c8-380">Der Host wird in der Regel per Code in der `Program`-Klasse konfiguriert, erstellt und ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="272c8-380">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="272c8-381">Die `Main`-Methode:</span><span class="sxs-lookup"><span data-stu-id="272c8-381">The `Main` method:</span></span>

* <span data-ttu-id="272c8-382">Ruft eine `CreateHostBuilder`-Methode zum Erstellen und Konfigurieren eines Generatorobjekts auf.</span><span class="sxs-lookup"><span data-stu-id="272c8-382">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="272c8-383">Ruft `Build`- und `Run`-Methoden für das Generatorobjekt auf.</span><span class="sxs-lookup"><span data-stu-id="272c8-383">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="272c8-384">Hier sehen Sie den *Program.cs*-Code für einen Nicht-HTTP-Workload mit einer einzelnen `IHostedService`-Implementierung, die dem DI-Container hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="272c8-384">Here's *Program.cs* code for a non-HTTP workload, with a single `IHostedService` implementation added to the DI container.</span></span> 

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

<span data-ttu-id="272c8-385">Für einen HTTP-Workload ist die `Main`-Methode die gleiche, `CreateHostBuilder` ruft jedoch `ConfigureWebHostDefaults` auf:</span><span class="sxs-lookup"><span data-stu-id="272c8-385">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="272c8-386">Ändern Sie nicht den Namen bzw. die Signatur der `CreateHostBuilder`-Methode, wenn die App Entity Framework Core verwendet.</span><span class="sxs-lookup"><span data-stu-id="272c8-386">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="272c8-387">Die [Entity Framework Core-Tools](/ef/core/miscellaneous/cli/) erwarten eine `CreateHostBuilder`-Methode, die den Host konfiguriert, ohne die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="272c8-387">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="272c8-388">Weitere Informationen finden Sie unter [DbContext-Instanzerstellung zur Entwurfszeit](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="272c8-388">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="272c8-389">Standardeinstellungen für den Generator</span><span class="sxs-lookup"><span data-stu-id="272c8-389">Default builder settings</span></span>

<span data-ttu-id="272c8-390">Die <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>-Methode:</span><span class="sxs-lookup"><span data-stu-id="272c8-390">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="272c8-391">Legt das [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) auf den Pfad fest, der von <xref:System.IO.Directory.GetCurrentDirectory*> zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="272c8-391">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="272c8-392">Lädt Hostkonfiguration aus:</span><span class="sxs-lookup"><span data-stu-id="272c8-392">Loads host configuration from:</span></span>
  * <span data-ttu-id="272c8-393">Umgebungsvariablen mit dem Präfix `DOTNET_`.</span><span class="sxs-lookup"><span data-stu-id="272c8-393">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="272c8-394">Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="272c8-394">Command-line arguments.</span></span>
* <span data-ttu-id="272c8-395">Lädt App-Konfiguration aus:</span><span class="sxs-lookup"><span data-stu-id="272c8-395">Loads app configuration from:</span></span>
  * <span data-ttu-id="272c8-396">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="272c8-396">*appsettings.json*.</span></span>
  * <span data-ttu-id="272c8-397">*appsettings.{Environment}.json*</span><span class="sxs-lookup"><span data-stu-id="272c8-397">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="272c8-398">[Geheimnis-Manager](xref:security/app-secrets), wenn die App in der `Development`-Umgebung ausgeführt wird</span><span class="sxs-lookup"><span data-stu-id="272c8-398">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="272c8-399">Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="272c8-399">Environment variables.</span></span>
  * <span data-ttu-id="272c8-400">Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="272c8-400">Command-line arguments.</span></span>
* <span data-ttu-id="272c8-401">Fügt die folgenden [Protokollierungsanbieter](xref:fundamentals/logging/index) hinzu:</span><span class="sxs-lookup"><span data-stu-id="272c8-401">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="272c8-402">Konsole</span><span class="sxs-lookup"><span data-stu-id="272c8-402">Console</span></span>
  * <span data-ttu-id="272c8-403">Debug</span><span class="sxs-lookup"><span data-stu-id="272c8-403">Debug</span></span>
  * <span data-ttu-id="272c8-404">EventSource</span><span class="sxs-lookup"><span data-stu-id="272c8-404">EventSource</span></span>
  * <span data-ttu-id="272c8-405">EventLog (nur bei Ausführung unter Windows)</span><span class="sxs-lookup"><span data-stu-id="272c8-405">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="272c8-406">Aktiviert [Bereichsvalidierung](xref:fundamentals/dependency-injection#scope-validation) und [Abhängigkeitsüberprüfung](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild), wenn es sich um eine Entwicklungsumgebung handelt.</span><span class="sxs-lookup"><span data-stu-id="272c8-406">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="272c8-407">Die `ConfigureWebHostDefaults`-Methode:</span><span class="sxs-lookup"><span data-stu-id="272c8-407">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="272c8-408">Lädt Hostkonfiguration aus Umgebungsvariablen mit dem Präfix `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="272c8-408">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="272c8-409">Legt den [Kestrel](xref:fundamentals/servers/kestrel)-Server als Webserver fest und konfiguriert ihn mithilfe der Hostkonfigurationsanbieter der App.</span><span class="sxs-lookup"><span data-stu-id="272c8-409">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="272c8-410">Die Standardoptionen des Kestrel-Servers finden Sie unter <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="272c8-410">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="272c8-411">Fügt [Middleware zum Filtern von Hosts](xref:fundamentals/servers/kestrel#host-filtering) hinzu.</span><span class="sxs-lookup"><span data-stu-id="272c8-411">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="272c8-412">Fügt [Middleware für weitergeleitete Header](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) hinzu, wenn `ASPNETCORE_FORWARDEDHEADERS_ENABLED` den Wert `true` aufweist.</span><span class="sxs-lookup"><span data-stu-id="272c8-412">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="272c8-413">Ermöglicht die Integration von IIS.</span><span class="sxs-lookup"><span data-stu-id="272c8-413">Enables IIS integration.</span></span> <span data-ttu-id="272c8-414">Informationen zu den IIS-Standardoptionen finden Sie unter <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="272c8-414">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="272c8-415">In den Abschnitten [Einstellungen für alle App-Typen](#settings-for-all-app-types) und [Einstellungen für Web-Apps](#settings-for-web-apps) weiter unten in diesem Artikel wird beschrieben, wie die Standardeinstellungen des Generators außer Kraft gesetzt werden.</span><span class="sxs-lookup"><span data-stu-id="272c8-415">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="272c8-416">Von Frameworks bereitgestellte Dienste</span><span class="sxs-lookup"><span data-stu-id="272c8-416">Framework-provided services</span></span>

<span data-ttu-id="272c8-417">Die folgenden Dienste werden automatisch registriert:</span><span class="sxs-lookup"><span data-stu-id="272c8-417">The following services are registered automatically:</span></span>

* [<span data-ttu-id="272c8-418">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="272c8-418">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="272c8-419">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="272c8-419">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="272c8-420">IHostEnvironment/IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="272c8-420">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="272c8-421">Weitere Informationen zu den vom Framework bereitgestellten Diensten finden Sie unter <xref:fundamentals/dependency-injection#framework-provided-services>.</span><span class="sxs-lookup"><span data-stu-id="272c8-421">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="272c8-422">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="272c8-422">IHostApplicationLifetime</span></span>

<span data-ttu-id="272c8-423">Fügt den <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>-Dienst (vorher `IApplicationLifetime`-Dienst) in eine beliebige Klasse ein, um die Aktivitäten nach dem Starten und die Aufgaben für ordnungsgemäßes Herunterfahren zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="272c8-423">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="272c8-424">Bei drei der Eigenschaften der Schnittstelle handelt es sich um Abbruchtokens, die zum Registrieren von Ereignishandlermethoden zum Starten und Beenden von Apps verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="272c8-424">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="272c8-425">Die Benutzeroberfläche umfasst auch eine `StopApplication`-Methode.</span><span class="sxs-lookup"><span data-stu-id="272c8-425">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="272c8-426">Das folgende Beispiel zeigt eine `IHostedService`-Implementierung, die `IHostApplicationLifetime`-Ereignisse registriert:</span><span class="sxs-lookup"><span data-stu-id="272c8-426">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="272c8-427">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="272c8-427">IHostLifetime</span></span>

<span data-ttu-id="272c8-428">Die <xref:Microsoft.Extensions.Hosting.IHostLifetime>-Implementierung steuert, wann der Host gestartet und beendet wird.</span><span class="sxs-lookup"><span data-stu-id="272c8-428">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="272c8-429">Die zuletzt registrierte Implementierung wird verwendet.</span><span class="sxs-lookup"><span data-stu-id="272c8-429">The last implementation registered is used.</span></span>

<span data-ttu-id="272c8-430">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` ist die `IHostLifetime`-Standardimplementierung.</span><span class="sxs-lookup"><span data-stu-id="272c8-430">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="272c8-431">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="272c8-431">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="272c8-432">lauscht auf <kbd>STRG</kbd>+<kbd>C</kbd>/SIGINT oder SIGTERM und ruft <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> auf, um das Herunterfahren zu beginnen.</span><span class="sxs-lookup"><span data-stu-id="272c8-432">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="272c8-433">Hebt die Blockierung von Erweiterungen wie [RunAsync](#runasync) und [WaitForShutdownAsync](#waitforshutdownasync) auf.</span><span class="sxs-lookup"><span data-stu-id="272c8-433">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="272c8-434">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="272c8-434">IHostEnvironment</span></span>

<span data-ttu-id="272c8-435">Fügt den <xref:Microsoft.Extensions.Hosting.IHostEnvironment>-Dienst in eine Klasse ein, um Informationen über die folgenden Einstellungen abzurufen:</span><span class="sxs-lookup"><span data-stu-id="272c8-435">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="272c8-436">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="272c8-436">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="272c8-437">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="272c8-437">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="272c8-438">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="272c8-438">ContentRootPath</span></span>](#contentrootpath)

<span data-ttu-id="272c8-439">Web-Apps implementieren die `IWebHostEnvironment`-Schnittstelle, die `IHostEnvironment` erbt und [WebRootPath](#webroot) hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="272c8-439">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="272c8-440">Konfiguration des Hosts</span><span class="sxs-lookup"><span data-stu-id="272c8-440">Host configuration</span></span>

<span data-ttu-id="272c8-441">Die Hostkonfiguration wird für die Eigenschaften der <xref:Microsoft.Extensions.Hosting.IHostEnvironment>-Implementierung verwendet.</span><span class="sxs-lookup"><span data-stu-id="272c8-441">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="272c8-442">Sie ist über [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> verfügbar.</span><span class="sxs-lookup"><span data-stu-id="272c8-442">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="272c8-443">Nach `ConfigureAppConfiguration` wird `HostBuilderContext.Configuration` durch die App-Konfiguration ersetzt.</span><span class="sxs-lookup"><span data-stu-id="272c8-443">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="272c8-444">Rufen Sie <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> für `IHostBuilder` auf, um die Hostkonfiguration hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="272c8-444">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="272c8-445">`ConfigureHostConfiguration` kann mehrfach mit additiven Ergebnissen aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="272c8-445">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="272c8-446">Der Host verwendet die Option, die zuletzt einen Wert für einen bestimmten Schlüssel festlegt.</span><span class="sxs-lookup"><span data-stu-id="272c8-446">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="272c8-447">Der Umgebungsvariablenanbieter mit dem Präfix `DOTNET_` und die Befehlszeilenargumente sind in `CreateDefaultBuilder` enthalten.</span><span class="sxs-lookup"><span data-stu-id="272c8-447">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="272c8-448">Für Web-Apps wird der Umgebungsvariablenanbieter mit dem Präfix `ASPNETCORE_` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="272c8-448">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="272c8-449">Das Präfix wird entfernt, wenn die Umgebungsvariablen gelesen werden.</span><span class="sxs-lookup"><span data-stu-id="272c8-449">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="272c8-450">Der Wert der Umgebungsvariable für `ASPNETCORE_ENVIRONMENT` wird z. B. der Hostkonfigurationswert für den `environment`-Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="272c8-450">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="272c8-451">Im folgenden Beispiel wird eine Hostkonfiguration erstellt:</span><span class="sxs-lookup"><span data-stu-id="272c8-451">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="272c8-452">App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="272c8-452">App configuration</span></span>

<span data-ttu-id="272c8-453">Die App-Konfiguration wird durch Aufrufen von <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> für `IHostBuilder` erstellt.</span><span class="sxs-lookup"><span data-stu-id="272c8-453">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="272c8-454">`ConfigureAppConfiguration` kann mehrfach mit additiven Ergebnissen aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="272c8-454">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="272c8-455">Die App verwendet die Option, die zuletzt einen Wert für einen bestimmten Schlüssel festlegt.</span><span class="sxs-lookup"><span data-stu-id="272c8-455">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="272c8-456">Die von `ConfigureAppConfiguration` erstellte Konfiguration ist unter [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) verfügbar und kann für nachfolgende Vorgänge und als Dienst für die Abhängigkeitsinjektion verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="272c8-456">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="272c8-457">Die Hostkonfiguration wird ebenfalls der App-Konfiguration hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="272c8-457">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="272c8-458">Weitere Informationen finden Sie unter [Konfiguration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="272c8-458">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="272c8-459">Einstellungen für alle App-Typen</span><span class="sxs-lookup"><span data-stu-id="272c8-459">Settings for all app types</span></span>

<span data-ttu-id="272c8-460">In diesem Abschnitt werden Hosteinstellungen aufgeführt, die sowohl für HTTP- als auch für Nicht-HTTP-Workloads gelten.</span><span class="sxs-lookup"><span data-stu-id="272c8-460">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="272c8-461">Standardmäßig können Umgebungsvariablen, die zur Konfiguration dieser Einstellungen verwendet werden, ein `DOTNET_`- oder `ASPNETCORE_`-Präfix haben.</span><span class="sxs-lookup"><span data-stu-id="272c8-461">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="272c8-462">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="272c8-462">ApplicationName</span></span>

<span data-ttu-id="272c8-463">Die Eigenschaft [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) wird von der Hostkonfiguration während der Hosterstellung festgelegt.</span><span class="sxs-lookup"><span data-stu-id="272c8-463">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="272c8-464">**Schlüssel**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="272c8-464">**Key**: `applicationName`</span></span>  
<span data-ttu-id="272c8-465">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="272c8-465">**Type**: `string`</span></span>  
<span data-ttu-id="272c8-466">**Standard**: Der Name der Assembly, die den Einstiegspunkt der App enthält.</span><span class="sxs-lookup"><span data-stu-id="272c8-466">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="272c8-467">**Umgebungsvariable:** `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="272c8-467">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="272c8-468">Verwenden Sie die Umgebungsvariable, um diesen Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="272c8-468">To set this value, use the environment variable.</span></span> 

### <a name="contentrootpath"></a><span data-ttu-id="272c8-469">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="272c8-469">ContentRootPath</span></span>

<span data-ttu-id="272c8-470">Die Eigenschaft [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) bestimmt, wo der Host mit der Suche nach Inhaltsdateien beginnt.</span><span class="sxs-lookup"><span data-stu-id="272c8-470">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="272c8-471">Wenn der Pfad nicht vorhanden ist, kann der Host nicht gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="272c8-471">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="272c8-472">**Schlüssel**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="272c8-472">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="272c8-473">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="272c8-473">**Type**: `string`</span></span>  
<span data-ttu-id="272c8-474">**Standard**: Der Ordner, in dem die App-Assembly gespeichert ist.</span><span class="sxs-lookup"><span data-stu-id="272c8-474">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="272c8-475">**Umgebungsvariable:** `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="272c8-475">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="272c8-476">Verwenden Sie die Umgebungsvariable, oder rufen Sie `UseContentRoot` für `IHostBuilder` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="272c8-476">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="272c8-477">Weitere Informationen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="272c8-477">For more information, see:</span></span>

* [<span data-ttu-id="272c8-478">Grundlagen: Inhaltsstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="272c8-478">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="272c8-479">WebRoot</span><span class="sxs-lookup"><span data-stu-id="272c8-479">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="272c8-480">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="272c8-480">EnvironmentName</span></span>

<span data-ttu-id="272c8-481">Die Eigenschaft [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) kann auf einen beliebigen Wert festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="272c8-481">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="272c8-482">Zu den durch Frameworks definierten Werten zählen `Development`, `Staging` und `Production`.</span><span class="sxs-lookup"><span data-stu-id="272c8-482">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="272c8-483">Die Groß-/Kleinschreibung wird bei Werten nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="272c8-483">Values aren't case-sensitive.</span></span>

<span data-ttu-id="272c8-484">**Schlüssel**: `environment`</span><span class="sxs-lookup"><span data-stu-id="272c8-484">**Key**: `environment`</span></span>  
<span data-ttu-id="272c8-485">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="272c8-485">**Type**: `string`</span></span>  
<span data-ttu-id="272c8-486">**Standard**: `Production`</span><span class="sxs-lookup"><span data-stu-id="272c8-486">**Default**: `Production`</span></span>  
<span data-ttu-id="272c8-487">**Umgebungsvariable:** `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="272c8-487">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="272c8-488">Verwenden Sie die Umgebungsvariable, oder rufen Sie `UseEnvironment` für `IHostBuilder` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="272c8-488">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="272c8-489">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="272c8-489">ShutdownTimeout</span></span>

<span data-ttu-id="272c8-490">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) legt das Zeitlimit für <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> fest.</span><span class="sxs-lookup"><span data-stu-id="272c8-490">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="272c8-491">Der Standardwert beträgt fünf Sekunden.</span><span class="sxs-lookup"><span data-stu-id="272c8-491">The default value is five seconds.</span></span>  <span data-ttu-id="272c8-492">Während des Zeitlimits verhält sich der Host folgendermaßen:</span><span class="sxs-lookup"><span data-stu-id="272c8-492">During the timeout period, the host:</span></span>

* <span data-ttu-id="272c8-493">Er löst [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.ihostapplicationlifetime.applicationstopping) aus.</span><span class="sxs-lookup"><span data-stu-id="272c8-493">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="272c8-494">Er versucht, gehostete Dienste zu beenden und protokolliert Fehler für Dienste, die nicht beendet werden können.</span><span class="sxs-lookup"><span data-stu-id="272c8-494">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="272c8-495">Wenn das Zeitlimit abläuft bevor alle gehosteten Dienste beendet sind, werden alle verbleibenden aktiven Dienste beendet, wenn die App herunterfährt.</span><span class="sxs-lookup"><span data-stu-id="272c8-495">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="272c8-496">Die Dienste werden beendet, selbst wenn die Verarbeitung noch nicht abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="272c8-496">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="272c8-497">Wenn die Dienste mehr Zeit zum Beenden benötigen, sollten Sie das Zeitlimit erhöhen.</span><span class="sxs-lookup"><span data-stu-id="272c8-497">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="272c8-498">**Schlüssel**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="272c8-498">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="272c8-499">**Typ:** `int`</span><span class="sxs-lookup"><span data-stu-id="272c8-499">**Type**: `int`</span></span>  
<span data-ttu-id="272c8-500">**Standard**: 5 Sekunden</span><span class="sxs-lookup"><span data-stu-id="272c8-500">**Default**: 5 seconds</span></span>  
<span data-ttu-id="272c8-501">**Umgebungsvariable:** `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="272c8-501">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="272c8-502">Verwenden Sie die Umgebungsvariable, oder konfigurieren Sie `HostOptions`, um diesen Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="272c8-502">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="272c8-503">Im folgenden Beispiel wird das Zeitlimit auf 20 Sekunden festgelegt:</span><span class="sxs-lookup"><span data-stu-id="272c8-503">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="272c8-504">Einstellungen für Web-Apps</span><span class="sxs-lookup"><span data-stu-id="272c8-504">Settings for web apps</span></span>

<span data-ttu-id="272c8-505">Einige Hosteinstellungen gelten nur für HTTP-Workloads.</span><span class="sxs-lookup"><span data-stu-id="272c8-505">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="272c8-506">Standardmäßig können Umgebungsvariablen, die zur Konfiguration dieser Einstellungen verwendet werden, ein `DOTNET_`- oder `ASPNETCORE_`-Präfix haben.</span><span class="sxs-lookup"><span data-stu-id="272c8-506">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="272c8-507">Für diese Einstellungen sind Erweiterungsmethoden in `IWebHostBuilder` verfügbar.</span><span class="sxs-lookup"><span data-stu-id="272c8-507">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="272c8-508">In den Codebeispielen, die zeigen, wie Sie die Erweiterungsmethoden aufrufen können, wird davon ausgegangen, dass `webBuilder` eine Instanz von `IWebHostBuilder` ist. Sehen Sie hierzu das folgende Beispiel:</span><span class="sxs-lookup"><span data-stu-id="272c8-508">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="272c8-509">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="272c8-509">CaptureStartupErrors</span></span>

<span data-ttu-id="272c8-510">Wenn diese `false` ist, führen Fehler beim Start zum Beenden des Hosts.</span><span class="sxs-lookup"><span data-stu-id="272c8-510">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="272c8-511">Wenn diese `true` ist, erfasst der Host Ausnahmen während des Starts und versucht, den Server zu starten.</span><span class="sxs-lookup"><span data-stu-id="272c8-511">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="272c8-512">**Schlüssel**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="272c8-512">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="272c8-513">**Typ:** `bool` (`true` oder `1`)</span><span class="sxs-lookup"><span data-stu-id="272c8-513">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="272c8-514">**Standard**: Die Standardeinstellung ist gleich `false`, es sei denn, die App wird mit Kestrel hinter IIS ausgeführt, dann ist sie gleich `true`.</span><span class="sxs-lookup"><span data-stu-id="272c8-514">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="272c8-515">**Umgebungsvariable:** `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="272c8-515">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="272c8-516">Verwenden Sie die Konfiguration, oder rufen Sie `CaptureStartupErrors` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="272c8-516">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="272c8-517">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="272c8-517">DetailedErrors</span></span>

<span data-ttu-id="272c8-518">Wenn diese Einstellung aktiviert ist oder die Umgebung auf `Development` festgelegt ist, erfasst die App detaillierte Fehler.</span><span class="sxs-lookup"><span data-stu-id="272c8-518">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="272c8-519">**Schlüssel**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="272c8-519">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="272c8-520">**Typ:** `bool` (`true` oder `1`)</span><span class="sxs-lookup"><span data-stu-id="272c8-520">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="272c8-521">**Standard**: `false`</span><span class="sxs-lookup"><span data-stu-id="272c8-521">**Default**: `false`</span></span>  
<span data-ttu-id="272c8-522">**Umgebungsvariable:** `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="272c8-522">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="272c8-523">Verwenden Sie die Konfiguration, oder rufen Sie `UseSetting` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="272c8-523">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="272c8-524">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="272c8-524">HostingStartupAssemblies</span></span>

<span data-ttu-id="272c8-525">Eine durch Semikolons getrennte Zeichenfolge der Hostingstartassemblys, die beim Start geladen werden soll.</span><span class="sxs-lookup"><span data-stu-id="272c8-525">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="272c8-526">Obwohl der Konfigurationswert standardmäßig auf eine leere Zeichenfolge festgelegt ist, enthalten die Hostingstartassemblys immer die Assembly der App.</span><span class="sxs-lookup"><span data-stu-id="272c8-526">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="272c8-527">Wenn Hostingstartassemblys bereitgestellt werden, werden diese zur Assembly der App hinzugefügt, damit diese geladen werden, wenn die App während des Starts die allgemeinen Dienste erstellt.</span><span class="sxs-lookup"><span data-stu-id="272c8-527">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="272c8-528">**Schlüssel**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="272c8-528">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="272c8-529">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="272c8-529">**Type**: `string`</span></span>  
<span data-ttu-id="272c8-530">**Standard**: Leere Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="272c8-530">**Default**: Empty string</span></span>  
<span data-ttu-id="272c8-531">**Umgebungsvariable:** `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="272c8-531">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="272c8-532">Verwenden Sie die Konfiguration, oder rufen Sie `UseSetting` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="272c8-532">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="272c8-533">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="272c8-533">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="272c8-534">Eine durch Semikolons getrennte Zeichenfolge der Hostingstartassemblys, die beim Start ausgeschlossen werden sollen.</span><span class="sxs-lookup"><span data-stu-id="272c8-534">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="272c8-535">**Schlüssel**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="272c8-535">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="272c8-536">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="272c8-536">**Type**: `string`</span></span>  
<span data-ttu-id="272c8-537">**Standard**: Leere Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="272c8-537">**Default**: Empty string</span></span>  
<span data-ttu-id="272c8-538">**Umgebungsvariable:** `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="272c8-538">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="272c8-539">Verwenden Sie die Konfiguration, oder rufen Sie `UseSetting` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="272c8-539">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="272c8-540">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="272c8-540">HTTPS_Port</span></span>

<span data-ttu-id="272c8-541">Der HTTPS-Umleitungsport.</span><span class="sxs-lookup"><span data-stu-id="272c8-541">The HTTPS redirect port.</span></span> <span data-ttu-id="272c8-542">Wird in [Erzwingen von HTTPS](xref:security/enforcing-ssl) verwendet.</span><span class="sxs-lookup"><span data-stu-id="272c8-542">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="272c8-543">**Schlüssel**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="272c8-543">**Key**: `https_port`</span></span>  
<span data-ttu-id="272c8-544">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="272c8-544">**Type**: `string`</span></span>  
<span data-ttu-id="272c8-545">**Standard**: Es ist kein Standardwert festgelegt.</span><span class="sxs-lookup"><span data-stu-id="272c8-545">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="272c8-546">**Umgebungsvariable:** `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="272c8-546">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="272c8-547">Verwenden Sie die Konfiguration, oder rufen Sie `UseSetting` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="272c8-547">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="272c8-548">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="272c8-548">PreferHostingUrls</span></span>

<span data-ttu-id="272c8-549">Gibt an, ob der Host auf die URLs lauschen soll, die mit `IWebHostBuilder` konfiguriert wurden, anstatt auf die URLs zu lauschen, die mit der `IServer`-Implementierung konfiguriert wurden.</span><span class="sxs-lookup"><span data-stu-id="272c8-549">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="272c8-550">**Schlüssel**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="272c8-550">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="272c8-551">**Typ:** `bool` (`true` oder `1`)</span><span class="sxs-lookup"><span data-stu-id="272c8-551">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="272c8-552">**Standard**: `true`</span><span class="sxs-lookup"><span data-stu-id="272c8-552">**Default**: `true`</span></span>  
<span data-ttu-id="272c8-553">**Umgebungsvariable:** `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="272c8-553">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="272c8-554">Verwenden Sie die Umgebungsvariable, oder rufen Sie `PreferHostingUrls` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="272c8-554">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="272c8-555">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="272c8-555">PreventHostingStartup</span></span>

<span data-ttu-id="272c8-556">Verhindert das automatische Laden der Hostingstartassemblys, einschließlich denen, die von der Assembly der App konfiguriert wurden.</span><span class="sxs-lookup"><span data-stu-id="272c8-556">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="272c8-557">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="272c8-557">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="272c8-558">**Schlüssel**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="272c8-558">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="272c8-559">**Typ:** `bool` (`true` oder `1`)</span><span class="sxs-lookup"><span data-stu-id="272c8-559">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="272c8-560">**Standard**: `false`</span><span class="sxs-lookup"><span data-stu-id="272c8-560">**Default**: `false`</span></span>  
<span data-ttu-id="272c8-561">**Umgebungsvariable:** `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="272c8-561">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="272c8-562">Verwenden Sie die Umgebungsvariable, oder rufen Sie `UseSetting` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="272c8-562">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="272c8-563">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="272c8-563">StartupAssembly</span></span>

<span data-ttu-id="272c8-564">Die Assembly, die nach der `Startup`-Klasse suchen soll.</span><span class="sxs-lookup"><span data-stu-id="272c8-564">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="272c8-565">**Schlüssel**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="272c8-565">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="272c8-566">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="272c8-566">**Type**: `string`</span></span>  
<span data-ttu-id="272c8-567">**Standard**: Die Assembly der App</span><span class="sxs-lookup"><span data-stu-id="272c8-567">**Default**: The app's assembly</span></span>  
<span data-ttu-id="272c8-568">**Umgebungsvariable:** `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="272c8-568">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="272c8-569">Verwenden Sie die Umgebungsvariable, oder rufen Sie `UseStartup` auf, um diesen Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="272c8-569">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="272c8-570">`UseStartup` kann einen Assemblynamen (`string`) oder einen Typ (`TStartup`) annehmen.</span><span class="sxs-lookup"><span data-stu-id="272c8-570">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="272c8-571">Wenn mehrere `UseStartup`-Methoden aufgerufen werden, hat die letzte Vorrang.</span><span class="sxs-lookup"><span data-stu-id="272c8-571">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="272c8-572">URLs</span><span class="sxs-lookup"><span data-stu-id="272c8-572">URLs</span></span>

<span data-ttu-id="272c8-573">Eine durch Semikolons getrennte Liste mit IP-Adressen oder Hostadressen mit Ports und Protokollen, denen der Server für Anforderungen lauschen soll.</span><span class="sxs-lookup"><span data-stu-id="272c8-573">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="272c8-574">Beispielsweise `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="272c8-574">For example, `http://localhost:123`.</span></span> <span data-ttu-id="272c8-575">Verwenden Sie \*, um anzugeben, dass der Server mithilfe des angegebenen Ports und Protokolls (z.B. `http://*:5000`) den Anfragen aller IP-Adressen oder Hostnamen lauschen soll.</span><span class="sxs-lookup"><span data-stu-id="272c8-575">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="272c8-576">Das Protokoll (`http://` oder `https://`) muss in jeder URL enthalten sein.</span><span class="sxs-lookup"><span data-stu-id="272c8-576">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="272c8-577">Die unterstützten Formate variieren bei den verschiedenen Servern.</span><span class="sxs-lookup"><span data-stu-id="272c8-577">Supported formats vary among servers.</span></span>

<span data-ttu-id="272c8-578">**Schlüssel**: `urls`</span><span class="sxs-lookup"><span data-stu-id="272c8-578">**Key**: `urls`</span></span>  
<span data-ttu-id="272c8-579">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="272c8-579">**Type**: `string`</span></span>  
<span data-ttu-id="272c8-580">**Standard**: `http://localhost:5000` und `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="272c8-580">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="272c8-581">**Umgebungsvariable:** `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="272c8-581">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="272c8-582">Verwenden Sie die Umgebungsvariable, oder rufen Sie `UseUrls` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="272c8-582">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="272c8-583">Kestrel verfügt über eine eigene API für die Endpunktkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="272c8-583">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="272c8-584">Weitere Informationen finden Sie unter <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="272c8-584">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="272c8-585">WebRoot</span><span class="sxs-lookup"><span data-stu-id="272c8-585">WebRoot</span></span>

<span data-ttu-id="272c8-586">Der relative Pfad für die statischen Objekte der App.</span><span class="sxs-lookup"><span data-stu-id="272c8-586">The relative path to the app's static assets.</span></span>

<span data-ttu-id="272c8-587">**Schlüssel**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="272c8-587">**Key**: `webroot`</span></span>  
<span data-ttu-id="272c8-588">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="272c8-588">**Type**: `string`</span></span>  
<span data-ttu-id="272c8-589">**Standard**: Der Standardwert ist `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="272c8-589">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="272c8-590">Der Pfad zu *{Inhaltsstammverzeichnis}/wwwroot* muss vorhanden sein.</span><span class="sxs-lookup"><span data-stu-id="272c8-590">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="272c8-591">Wenn der Pfad nicht vorhanden ist, wird ein Dateianbieter ohne Funktion verwendet.</span><span class="sxs-lookup"><span data-stu-id="272c8-591">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="272c8-592">**Umgebungsvariable:** `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="272c8-592">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="272c8-593">Verwenden Sie die Umgebungsvariable, oder rufen Sie `UseWebRoot` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="272c8-593">To set this value, use the environment variable or call `UseWebRoot`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="272c8-594">Weitere Informationen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="272c8-594">For more information, see:</span></span>

* [<span data-ttu-id="272c8-595">Grundlagen: Webstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="272c8-595">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="272c8-596">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="272c8-596">ContentRootPath</span></span>](#contentrootpath)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="272c8-597">Verwalten der Lebensdauer des Hosts</span><span class="sxs-lookup"><span data-stu-id="272c8-597">Manage the host lifetime</span></span>

<span data-ttu-id="272c8-598">Rufen Sie Methoden für die erstellte <xref:Microsoft.Extensions.Hosting.IHost>-Implementierung auf, um die App zu starten und zu beenden.</span><span class="sxs-lookup"><span data-stu-id="272c8-598">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="272c8-599">Diese Methoden wirken sich auf alle <xref:Microsoft.Extensions.Hosting.IHostedService>-Implementierungen aus, die im Dienstcontainer registriert sind.</span><span class="sxs-lookup"><span data-stu-id="272c8-599">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="272c8-600">Run</span><span class="sxs-lookup"><span data-stu-id="272c8-600">Run</span></span>

<span data-ttu-id="272c8-601">Durch <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> wird die App gestartet und der aufrufende Thread blockiert, bis der Host heruntergefahren wird.</span><span class="sxs-lookup"><span data-stu-id="272c8-601"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="272c8-602">RunAsync</span><span class="sxs-lookup"><span data-stu-id="272c8-602">RunAsync</span></span>

<span data-ttu-id="272c8-603">Durch <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> wird die App ausgeführt und eine <xref:System.Threading.Tasks.Task>-Methode ausgegeben, die abgeschlossen wird, wenn das Abbruchtoken oder das Herunterfahren ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="272c8-603"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="272c8-604">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="272c8-604">RunConsoleAsync</span></span>

<span data-ttu-id="272c8-605"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> aktiviert die Unterstützung der Konsole, erstellt und startet den Host und lauscht auf <kbd>STRG</kbd>+<kbd>C</kbd>/SIGINT oder SIGTERM, um das Herunterfahren auszulösen.</span><span class="sxs-lookup"><span data-stu-id="272c8-605"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="272c8-606">Starten</span><span class="sxs-lookup"><span data-stu-id="272c8-606">Start</span></span>

<span data-ttu-id="272c8-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> startet den Host synchron.</span><span class="sxs-lookup"><span data-stu-id="272c8-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="272c8-608">StartAsync</span><span class="sxs-lookup"><span data-stu-id="272c8-608">StartAsync</span></span>

<span data-ttu-id="272c8-609">Durch <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> wird der Host gestartet und eine <xref:System.Threading.Tasks.Task>-Methode ausgegeben, die abgeschlossen wird, wenn das Abbruchtoken oder das Herunterfahren ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="272c8-609"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="272c8-610"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> wird am Anfang der `StartAsync`-Methode aufgerufen, die den Vorgang erst fortsetzt, wenn sie abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="272c8-610"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="272c8-611">Dies kann verwendet werden, um den Start zu verzögern, bis dieser durch ein externes Ereignis initiiert wird.</span><span class="sxs-lookup"><span data-stu-id="272c8-611">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="272c8-612">StopAsync</span><span class="sxs-lookup"><span data-stu-id="272c8-612">StopAsync</span></span>

<span data-ttu-id="272c8-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> versucht, den Host innerhalb des angegebenen Timeouts zu beenden.</span><span class="sxs-lookup"><span data-stu-id="272c8-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="272c8-614">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="272c8-614">WaitForShutdown</span></span>

<span data-ttu-id="272c8-615"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blockiert den aufrufenden Thread, bis das Herunterfahren durch den IHostLifetime, z. B. über <kbd>STRG</kbd>+<kbd>C</kbd>/SIGINT oder SIGTERM, ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="272c8-615"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="272c8-616">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="272c8-616">WaitForShutdownAsync</span></span>

<span data-ttu-id="272c8-617"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> gibt eine <xref:System.Threading.Tasks.Task>-Methode zurück, die abgeschlossen wird, wenn das Herunterfahren über das angegebene Token ausgelöst wird, und ruft <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> auf.</span><span class="sxs-lookup"><span data-stu-id="272c8-617"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="272c8-618">Externe Steuerung</span><span class="sxs-lookup"><span data-stu-id="272c8-618">External control</span></span>

<span data-ttu-id="272c8-619">Die Lebensdauer des Hosts kann mithilfe von Methoden, die extern aufgerufen werden können, direkt gesteuert werden:</span><span class="sxs-lookup"><span data-stu-id="272c8-619">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="272c8-620">Durch ASP.NET Core-Apps kann ein Host gestartet und konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="272c8-620">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="272c8-621">Der Host ist verantwortlich für das Starten der App und das Verwalten der Lebensdauer.</span><span class="sxs-lookup"><span data-stu-id="272c8-621">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="272c8-622">In diesem Artikel wird der generische ASP.NET Core-Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) erläutert, der für das Hosten von Apps verwendet wird, die keine HTTP-Anforderungen verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="272c8-622">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="272c8-623">Das Ziel des generischen Hosts besteht darin, die HTTP-Pipeline von der Webhost-API zu entkoppeln, um mehr Hostszenarios zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="272c8-623">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="272c8-624">Messaging, Hintergrundtasks und andere Nicht-HTTP-Workloads, die auf dem generischen Host basieren, profitieren von übergreifenden Funktionen wie der Konfiguration, Dependency Injection (DI) und der Protokollerstellung.</span><span class="sxs-lookup"><span data-stu-id="272c8-624">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="272c8-625">Der generische Host ist neu in ASP.NET Core 2.1 und ist nicht für Webhostingszenarios geeignet.</span><span class="sxs-lookup"><span data-stu-id="272c8-625">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="272c8-626">Verwenden Sie den [Webhost](xref:fundamentals/host/web-host) für Webhostingszenarios.</span><span class="sxs-lookup"><span data-stu-id="272c8-626">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="272c8-627">Der generische Host wird den Webhost in einem zukünftigen Release ersetzen und als primäre Host-API in HTTP- und Nicht-HTTP-Szenarios fungieren.</span><span class="sxs-lookup"><span data-stu-id="272c8-627">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="272c8-628">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="272c8-628">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="272c8-629">Wenn Sie die Beispiel-App in [Visual Studio Code](https://code.visualstudio.com/) ausführen, verwenden Sie ein *externes oder integriertes Terminal*.</span><span class="sxs-lookup"><span data-stu-id="272c8-629">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="272c8-630">Führen Sie das Beispiel nicht in `internalConsole` aus.</span><span class="sxs-lookup"><span data-stu-id="272c8-630">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="272c8-631">So legen Sie die Konsole in Visual Studio Code fest:</span><span class="sxs-lookup"><span data-stu-id="272c8-631">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="272c8-632">Öffnen Sie die Datei *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="272c8-632">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="272c8-633">Suchen Sie in der Konfiguration **.NET Core-Start (Konsole)** den Eintrag **Konsole**.</span><span class="sxs-lookup"><span data-stu-id="272c8-633">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="272c8-634">Legen Sie den Wert auf `externalTerminal` oder `integratedTerminal` fest.</span><span class="sxs-lookup"><span data-stu-id="272c8-634">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="272c8-635">Einführung</span><span class="sxs-lookup"><span data-stu-id="272c8-635">Introduction</span></span>

<span data-ttu-id="272c8-636">Die generische Hostbibliothek ist im Namespace <xref:Microsoft.Extensions.Hosting> verfügbar und wird vom Paket [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="272c8-636">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="272c8-637">Das Paket `Microsoft.Extensions.Hosting` ist im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 oder höher) enthalten.</span><span class="sxs-lookup"><span data-stu-id="272c8-637">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="272c8-638"><xref:Microsoft.Extensions.Hosting.IHostedService> ist der Einstiegspunkt für die Ausführung des Codes.</span><span class="sxs-lookup"><span data-stu-id="272c8-638"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="272c8-639">Jede Implementierung von <xref:Microsoft.Extensions.Hosting.IHostedService> wird in der Reihenfolge der [Dienstregistrierung in ConfigureServices](#configureservices) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="272c8-639">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="272c8-640"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> wird in jeder <xref:Microsoft.Extensions.Hosting.IHostedService>-Schnittstelle aufgerufen, wenn der Host gestartet wird, und <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> wird in umgekehrter Registrierungsreihenfolge aufgerufen, wenn der Host ordnungsgemäß heruntergefahren wird.</span><span class="sxs-lookup"><span data-stu-id="272c8-640"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="272c8-641">Einrichten eines Hosts</span><span class="sxs-lookup"><span data-stu-id="272c8-641">Set up a host</span></span>

<span data-ttu-id="272c8-642"><xref:Microsoft.Extensions.Hosting.IHostBuilder> ist die Hauptkomponente, die Bibliotheken und Apps für die Initialisierung, Erstellung und Ausführung des Hosts verwenden:</span><span class="sxs-lookup"><span data-stu-id="272c8-642"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="272c8-643">Optionen</span><span class="sxs-lookup"><span data-stu-id="272c8-643">Options</span></span>

<span data-ttu-id="272c8-644"><xref:Microsoft.Extensions.Hosting.HostOptions>-Konfigurationsoptionen für <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="272c8-644"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="272c8-645">Timeout beim Herunterfahren</span><span class="sxs-lookup"><span data-stu-id="272c8-645">Shutdown timeout</span></span>

<span data-ttu-id="272c8-646"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> legt den Timeout für <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> fest.</span><span class="sxs-lookup"><span data-stu-id="272c8-646"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="272c8-647">Der Standardwert beträgt fünf Sekunden.</span><span class="sxs-lookup"><span data-stu-id="272c8-647">The default value is five seconds.</span></span>

<span data-ttu-id="272c8-648">Die folgende Optionskonfiguration in `Program.Main` erhöht den standardmäßigen Timeout beim Herunterfahren von 5 Sekunden auf 20 Sekunden:</span><span class="sxs-lookup"><span data-stu-id="272c8-648">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

```csharp
var host = new HostBuilder()
    .ConfigureServices((hostContext, services) =>
    {
        services.Configure<HostOptions>(option =>
        {
            option.ShutdownTimeout = System.TimeSpan.FromSeconds(20);
        });
    })
    .Build();
```

## <a name="default-services"></a><span data-ttu-id="272c8-649">Standarddienste</span><span class="sxs-lookup"><span data-stu-id="272c8-649">Default services</span></span>

<span data-ttu-id="272c8-650">Die folgenden Dienste werden bei der Hostinitialisierung registriert:</span><span class="sxs-lookup"><span data-stu-id="272c8-650">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="272c8-651">[Umgebung](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span><span class="sxs-lookup"><span data-stu-id="272c8-651">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="272c8-652">[Konfiguration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span><span class="sxs-lookup"><span data-stu-id="272c8-652">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="272c8-653"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="272c8-653"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="272c8-654"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="272c8-654"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="272c8-655">[Optionen](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span><span class="sxs-lookup"><span data-stu-id="272c8-655">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="272c8-656">[Protokollierung](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span><span class="sxs-lookup"><span data-stu-id="272c8-656">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="272c8-657">Konfiguration des Hosts</span><span class="sxs-lookup"><span data-stu-id="272c8-657">Host configuration</span></span>

<span data-ttu-id="272c8-658">Die Konfiguration des Hosts wird durch Folgendes erstellt:</span><span class="sxs-lookup"><span data-stu-id="272c8-658">Host configuration is created by:</span></span>

* <span data-ttu-id="272c8-659">Aufrufen von Erweiterungsmethoden in <xref:Microsoft.Extensions.Hosting.IHostBuilder>, um das [Inhaltsstammverzeichnis](#content-root) und die [Umgebung](#environment) festzulegen</span><span class="sxs-lookup"><span data-stu-id="272c8-659">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="272c8-660">Lesen der Konfiguration von Konfigurationsanbietern in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*></span><span class="sxs-lookup"><span data-stu-id="272c8-660">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="272c8-661">Erweiterungsmethoden</span><span class="sxs-lookup"><span data-stu-id="272c8-661">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="272c8-662">Anwendungsschlüssel (Name)</span><span class="sxs-lookup"><span data-stu-id="272c8-662">Application key (name)</span></span>

<span data-ttu-id="272c8-663">Die Eigenschaft [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) wird von der Hostkonfiguration während der Hostkonstruktion festgelegt.</span><span class="sxs-lookup"><span data-stu-id="272c8-663">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="272c8-664">Um den Wert explizit festzulegen, verwenden Sie den [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span><span class="sxs-lookup"><span data-stu-id="272c8-664">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="272c8-665">**Schlüssel**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="272c8-665">**Key**: `applicationName`</span></span>  
<span data-ttu-id="272c8-666">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="272c8-666">**Type**: `string`</span></span>  
<span data-ttu-id="272c8-667">**Standard**: Der Name der Assembly, die den Einstiegspunkt der App enthält.</span><span class="sxs-lookup"><span data-stu-id="272c8-667">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="272c8-668">**Festlegen mit:** `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="272c8-668">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="272c8-669">**Umgebungsvariable:** `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` ist [optional und benutzerdefiniert](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="272c8-669">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="272c8-670">Inhaltsstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="272c8-670">Content root</span></span>

<span data-ttu-id="272c8-671">Diese Einstellung bestimmt, wo der Host mit der Suche nach Inhaltsdateien beginnt.</span><span class="sxs-lookup"><span data-stu-id="272c8-671">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="272c8-672">**Schlüssel**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="272c8-672">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="272c8-673">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="272c8-673">**Type**: `string`</span></span>  
<span data-ttu-id="272c8-674">**Standard**: Entspricht standardmäßig dem Ordner, in dem die App-Assembly gespeichert ist.</span><span class="sxs-lookup"><span data-stu-id="272c8-674">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="272c8-675">**Festlegen mit:** `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="272c8-675">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="272c8-676">**Umgebungsvariable:** `<PREFIX_>CONTENTROOT` (`<PREFIX_>` ist [optional und benutzerdefiniert](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="272c8-676">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="272c8-677">Wenn der Pfad nicht vorhanden ist, kann der Host nicht gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="272c8-677">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="272c8-678">Weitere Informationen finden Sie unter [Grundlagen: Inhaltsstammverzeichnis](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="272c8-678">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="272c8-679">Umgebung</span><span class="sxs-lookup"><span data-stu-id="272c8-679">Environment</span></span>

<span data-ttu-id="272c8-680">Legt die [Umgebung](xref:fundamentals/environments) der App fest.</span><span class="sxs-lookup"><span data-stu-id="272c8-680">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="272c8-681">**Schlüssel**: `environment`</span><span class="sxs-lookup"><span data-stu-id="272c8-681">**Key**: `environment`</span></span>  
<span data-ttu-id="272c8-682">**Typ:** `string`</span><span class="sxs-lookup"><span data-stu-id="272c8-682">**Type**: `string`</span></span>  
<span data-ttu-id="272c8-683">**Standard**: `Production`</span><span class="sxs-lookup"><span data-stu-id="272c8-683">**Default**: `Production`</span></span>  
<span data-ttu-id="272c8-684">**Festlegen mit:** `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="272c8-684">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="272c8-685">**Umgebungsvariable:** `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` ist [optional und benutzerdefiniert](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="272c8-685">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="272c8-686">Die Umgebung kann auf einen beliebigen Wert festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="272c8-686">The environment can be set to any value.</span></span> <span data-ttu-id="272c8-687">Zu den durch Frameworks definierten Werten zählen `Development`, `Staging` und `Production`.</span><span class="sxs-lookup"><span data-stu-id="272c8-687">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="272c8-688">Die Groß-/Kleinschreibung wird bei Werten nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="272c8-688">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="272c8-689">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="272c8-689">ConfigureHostConfiguration</span></span>

<span data-ttu-id="272c8-690"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> verwendet einen <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>, um eine <xref:Microsoft.Extensions.Configuration.IConfiguration> für den Host zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="272c8-690"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="272c8-691">Die Hostkonfiguration dient zum Initialisieren der <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> für die Verwendung im App-Buildprozesses.</span><span class="sxs-lookup"><span data-stu-id="272c8-691">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="272c8-692"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> kann mehrfach mit additiven Ergebnissen aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="272c8-692"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="272c8-693">Der Host verwendet die Option, die zuletzt einen Wert für einen bestimmten Schlüssel festlegt.</span><span class="sxs-lookup"><span data-stu-id="272c8-693">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="272c8-694">Standardmäßig sind keine Anbieter enthalten.</span><span class="sxs-lookup"><span data-stu-id="272c8-694">No providers are included by default.</span></span> <span data-ttu-id="272c8-695">Sie müssen explizit angeben, welche Konfigurationsanbieter die App in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> erfordert, einschließlich Folgendes:</span><span class="sxs-lookup"><span data-stu-id="272c8-695">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="272c8-696">Dateikonfiguration (z.B. von einer Datei namens *hostsettings.json*)</span><span class="sxs-lookup"><span data-stu-id="272c8-696">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="272c8-697">Konfiguration von Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="272c8-697">Environment variable configuration.</span></span>
* <span data-ttu-id="272c8-698">Konfiguration von Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="272c8-698">Command-line argument configuration.</span></span>
* <span data-ttu-id="272c8-699">Alle anderen erforderlichen Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="272c8-699">Any other required configuration providers.</span></span>

<span data-ttu-id="272c8-700">Die Dateikonfiguration des Hosts erfolgt durch Angabe des App-Basispfads mit `SetBasePath` gefolgt von einem Aufruf eines der [Dateikonfigurationsanbieter](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="272c8-700">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="272c8-701">Die Beispiel-App verwendet die JSON-Datei *hostsettings.json* und ruft <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> auf, um die Hostkonfigurationseinstellungen der Datei zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="272c8-701">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="272c8-702">Um die [Umgebungsvariablenkonfiguration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) des Hosts hinzuzufügen, rufen Sie <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> im Host-Generator auf.</span><span class="sxs-lookup"><span data-stu-id="272c8-702">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="272c8-703">`AddEnvironmentVariables` akzeptiert ein optionales benutzerdefiniertes Präfix.</span><span class="sxs-lookup"><span data-stu-id="272c8-703">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="272c8-704">Die Beispiel-App verwendet das Präfix `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="272c8-704">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="272c8-705">Das Präfix wird entfernt, wenn die Umgebungsvariablen gelesen werden.</span><span class="sxs-lookup"><span data-stu-id="272c8-705">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="272c8-706">Wenn der Host der Beispiel-App konfiguriert wird, wird der Wert der Umgebungsvariable für `PREFIX_ENVIRONMENT` der Hostkonfigurationswert für den `environment`-Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="272c8-706">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="272c8-707">Wenn Sie [Visual Studio](https://visualstudio.microsoft.com) oder eine App mit `dotnet run` während der Entwicklung verwenden, können Umgebungsvariablen in der Datei *Properties/launchSettings.json* festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="272c8-707">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="272c8-708">In [Visual Studio Code](https://code.visualstudio.com/) können Umgebungsvariablen während der Entwicklung in der Datei *.vscode/launch.json* festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="272c8-708">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="272c8-709">Weitere Informationen finden Sie unter <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="272c8-709">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="272c8-710">Die [Befehlszeilenkonfiguration](xref:fundamentals/configuration/index#command-line-configuration-provider) wird durch Aufrufen von <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="272c8-710">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="272c8-711">Die Befehlszeilenkonfiguration wird zuletzt hinzugefügt, damit Befehlszeilenargumente die Konfiguration überschreiben können, die von früheren Konfigurationsanbietern bereitgestellt wurden.</span><span class="sxs-lookup"><span data-stu-id="272c8-711">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="272c8-712">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="272c8-712">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="272c8-713">Weitere Konfigurationen können durch die Schlüssel [applicationName](#application-key-name) und [contentRoot](#content-root) bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="272c8-713">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="272c8-714">Beispielkonfiguration für `HostBuilder` mithilfe von <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="272c8-714">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="272c8-715">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="272c8-715">ConfigureAppConfiguration</span></span>

<span data-ttu-id="272c8-716">Die App-Konfiguration wird durch Aufrufen von <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> in der <xref:Microsoft.Extensions.Hosting.IHostBuilder>-Implementierung erstellt.</span><span class="sxs-lookup"><span data-stu-id="272c8-716">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="272c8-717"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> verwendet einen <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>, um eine <xref:Microsoft.Extensions.Configuration.IConfiguration> für die App zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="272c8-717"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="272c8-718"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> kann mehrfach mit additiven Ergebnissen aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="272c8-718"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="272c8-719">Die App verwendet die Option, die zuletzt einen Wert für einen bestimmten Schlüssel festlegt.</span><span class="sxs-lookup"><span data-stu-id="272c8-719">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="272c8-720">Die von <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> erstellte Konfiguration ist unter [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) verfügbar und kann für nachfolgende Vorgänge und in <xref:Microsoft.Extensions.Hosting.IHost.Services*> verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="272c8-720">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="272c8-721">Auf die App-Konfiguration wird automatisch die Hostkonfiguration angewendet, die von [ConfigureHostConfiguration](#configurehostconfiguration) bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="272c8-721">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="272c8-722">Beispielkonfiguration für die App mithilfe von <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="272c8-722">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="272c8-723">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="272c8-723">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="272c8-724">*appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="272c8-724">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="272c8-725">*appsettings.Production.json*:</span><span class="sxs-lookup"><span data-stu-id="272c8-725">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="272c8-726">Um Einstellungsdateien in das Ausgabeverzeichnis zu verschieben, geben Sie die Einstellungsdateien als [MSBuild-Projektelemente](/visualstudio/msbuild/common-msbuild-project-items) in der Projektdatei an.</span><span class="sxs-lookup"><span data-stu-id="272c8-726">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="272c8-727">Die Beispiel-App verschiebt ihre JSON-App-Einstellungsdateien und *hostsettings.json* mit dem folgenden `<Content>`-Element:</span><span class="sxs-lookup"><span data-stu-id="272c8-727">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="272c8-728">Konfigurationserweiterungsmethoden wie <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> und <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> erfordern zusätzliche NuGet-Pakete, z.B. [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) und [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="272c8-728">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="272c8-729">Wenn die App nicht das [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app) verwendet, müssen diese Pakete dem Projekt zusätzlich zum [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration)-Kernpaket hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="272c8-729">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="272c8-730">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="272c8-730">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="272c8-731">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="272c8-731">ConfigureServices</span></span>

<span data-ttu-id="272c8-732">Mithilfe von <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> werden Dienste zum Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) der App hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="272c8-732"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="272c8-733"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> kann mehrfach mit additiven Ergebnissen aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="272c8-733"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="272c8-734">Ein gehosteter Dienst ist eine Klasse mit Logik für Hintergrundaufgaben, die die Schnittstelle <xref:Microsoft.Extensions.Hosting.IHostedService> implementiert.</span><span class="sxs-lookup"><span data-stu-id="272c8-734">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="272c8-735">Weitere Informationen finden Sie unter <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="272c8-735">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="272c8-736">Die [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) verwendet die Erweiterungsmethode `AddHostedService`, um einen Dienst für Lebensdauerereignisse (`LifetimeEventsHostedService`) und einen zeitgesteuerten Hintergrundtask (`TimedHostedService`) zur App hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="272c8-736">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="272c8-737">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="272c8-737">ConfigureLogging</span></span>

<span data-ttu-id="272c8-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> fügt einen Delegaten für die Konfiguration des bereitgestellten <xref:Microsoft.Extensions.Logging.ILoggingBuilder> hinzu.</span><span class="sxs-lookup"><span data-stu-id="272c8-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="272c8-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> kann mehrfach mit additiven Ergebnissen aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="272c8-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="272c8-740">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="272c8-740">UseConsoleLifetime</span></span>

<span data-ttu-id="272c8-741"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> lauscht auf <kbd>STRG</kbd>+<kbd>C</kbd>/SIGINT oder SIGTERM und ruft <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> auf, um das Herunterfahren zu beginnen.</span><span class="sxs-lookup"><span data-stu-id="272c8-741"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="272c8-742"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> hebt die Blockierung von Erweiterungen wie [RunAsync](#runasync) und [WaitForShutdownAsync](#waitforshutdownasync) auf.</span><span class="sxs-lookup"><span data-stu-id="272c8-742"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="272c8-743">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` ist vorab als Standardimplementierung für die Lebensdauer registriert.</span><span class="sxs-lookup"><span data-stu-id="272c8-743">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="272c8-744">Die letzte registrierte Lebensdauer wird verwendet.</span><span class="sxs-lookup"><span data-stu-id="272c8-744">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="272c8-745">Containerkonfiguration</span><span class="sxs-lookup"><span data-stu-id="272c8-745">Container configuration</span></span>

<span data-ttu-id="272c8-746">Der Host kann eine <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>-Schnittstelle verwenden, um die Integration in andere Container zu unterstützten.</span><span class="sxs-lookup"><span data-stu-id="272c8-746">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="272c8-747">Das Bereitstellen einer Factory ist kein Teil der DI-Containerregistrierung, sondern ein Host, der systemintern verwendet wird, um den entsprechenden DI-Container zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="272c8-747">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="272c8-748">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) überschreibt die Standardfactory, die zum Erstellen des Dienstanbieters der App verwendet wurde.</span><span class="sxs-lookup"><span data-stu-id="272c8-748">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="272c8-749">Die benutzerdefinierte Containerkonfiguration wird von der Methode <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> verwaltet.</span><span class="sxs-lookup"><span data-stu-id="272c8-749">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="272c8-750"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> stellt eine stark typisierte Möglichkeit für das Konfigurieren des Containers auf Basis der zugrunde liegenden Host-API bereit.</span><span class="sxs-lookup"><span data-stu-id="272c8-750"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="272c8-751"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> kann mehrfach mit additiven Ergebnissen aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="272c8-751"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="272c8-752">Erstellen eines Dienstcontainers für die App:</span><span class="sxs-lookup"><span data-stu-id="272c8-752">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="272c8-753">Bereitstellen einer Factory für den Dienstcontainer:</span><span class="sxs-lookup"><span data-stu-id="272c8-753">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="272c8-754">Verwenden Sie die Factory, und konfigurieren Sie den benutzerdefinierten Dienstcontainer für die App:</span><span class="sxs-lookup"><span data-stu-id="272c8-754">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="272c8-755">Erweiterungen</span><span class="sxs-lookup"><span data-stu-id="272c8-755">Extensibility</span></span>

<span data-ttu-id="272c8-756">Die Erweiterung des Hosts wird mit der Erweiterungsmethode in <xref:Microsoft.Extensions.Hosting.IHostBuilder> durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="272c8-756">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="272c8-757">Im folgenden Beispiel wird dargestellt, wie eine Erweiterungsmethode eine <xref:Microsoft.Extensions.Hosting.IHostBuilder>-Implementierung mit dem [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks)-Beispiel erweitert, das in <xref:fundamentals/host/hosted-services> gezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="272c8-757">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="272c8-758">Eine App richtet die `UseHostedService`-Erweiterungsmethode zum Registrieren des in `T` übergebenen gehosteten Diensts ein:</span><span class="sxs-lookup"><span data-stu-id="272c8-758">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

```csharp
using System;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

public static class Extensions
{
    public static IHostBuilder UseHostedService<T>(this IHostBuilder hostBuilder)
        where T : class, IHostedService, IDisposable
    {
        return hostBuilder.ConfigureServices(services =>
            services.AddHostedService<T>());
    }
}
```

## <a name="manage-the-host"></a><span data-ttu-id="272c8-759">Verwalten des Hosts</span><span class="sxs-lookup"><span data-stu-id="272c8-759">Manage the host</span></span>

<span data-ttu-id="272c8-760">Die Implementierung von <xref:Microsoft.Extensions.Hosting.IHost> ist für das Starten und Beenden der Implementierungen von <xref:Microsoft.Extensions.Hosting.IHostedService> verantwortlich, die im Dienstcontainer registriert sind.</span><span class="sxs-lookup"><span data-stu-id="272c8-760">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="272c8-761">Run</span><span class="sxs-lookup"><span data-stu-id="272c8-761">Run</span></span>

<span data-ttu-id="272c8-762">Durch <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> wird die App gestartet und der aufrufende Thread blockiert, bis der Host heruntergefahren wird:</span><span class="sxs-lookup"><span data-stu-id="272c8-762"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        host.Run();
    }
}
```

### <a name="runasync"></a><span data-ttu-id="272c8-763">RunAsync</span><span class="sxs-lookup"><span data-stu-id="272c8-763">RunAsync</span></span>

<span data-ttu-id="272c8-764">Durch <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> wird die App ausgeführt und eine <xref:System.Threading.Tasks.Task>-Methode ausgegeben, die abgeschlossen wird, wenn das Abbruchtoken oder das Herunterfahren ausgelöst wird:</span><span class="sxs-lookup"><span data-stu-id="272c8-764"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="runconsoleasync"></a><span data-ttu-id="272c8-765">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="272c8-765">RunConsoleAsync</span></span>

<span data-ttu-id="272c8-766"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> aktiviert die Unterstützung der Konsole, erstellt und startet den Host und lauscht auf <kbd>STRG</kbd>+<kbd>C</kbd>/SIGINT oder SIGTERM, um das Herunterfahren auszulösen.</span><span class="sxs-lookup"><span data-stu-id="272c8-766"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var hostBuilder = new HostBuilder();

        await hostBuilder.RunConsoleAsync();
    }
}
```

### <a name="start-and-stopasync"></a><span data-ttu-id="272c8-767">Start und StopAsync</span><span class="sxs-lookup"><span data-stu-id="272c8-767">Start and StopAsync</span></span>

<span data-ttu-id="272c8-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> startet den Host synchron.</span><span class="sxs-lookup"><span data-stu-id="272c8-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="272c8-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> versucht, den Host innerhalb des angegebenen Timeouts zu beenden.</span><span class="sxs-lookup"><span data-stu-id="272c8-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            await host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

### <a name="startasync-and-stopasync"></a><span data-ttu-id="272c8-770">StartAsync und StopAsync</span><span class="sxs-lookup"><span data-stu-id="272c8-770">StartAsync and StopAsync</span></span>

<span data-ttu-id="272c8-771"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> startet die App.</span><span class="sxs-lookup"><span data-stu-id="272c8-771"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="272c8-772"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> hält die App an.</span><span class="sxs-lookup"><span data-stu-id="272c8-772"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.StopAsync();
        }
    }
}
```

### <a name="waitforshutdown"></a><span data-ttu-id="272c8-773">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="272c8-773">WaitForShutdown</span></span>

<span data-ttu-id="272c8-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> wird über die <xref:Microsoft.Extensions.Hosting.IHostLifetime> ausgelöst, z. B. `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (lauscht auf <kbd>STRG</kbd>+<kbd>C</kbd>/SIGINT oder SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="272c8-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="272c8-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> ruft <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> auf.</span><span class="sxs-lookup"><span data-stu-id="272c8-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            host.WaitForShutdown();
        }
    }
}
```

### <a name="waitforshutdownasync"></a><span data-ttu-id="272c8-776">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="272c8-776">WaitForShutdownAsync</span></span>

<span data-ttu-id="272c8-777"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> gibt eine <xref:System.Threading.Tasks.Task>-Methode zurück, die abgeschlossen wird, wenn das Herunterfahren über das angegebene Token ausgelöst wird, und ruft <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> auf.</span><span class="sxs-lookup"><span data-stu-id="272c8-777"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.WaitForShutdownAsync();
        }

    }
}
```

### <a name="external-control"></a><span data-ttu-id="272c8-778">Externe Steuerung</span><span class="sxs-lookup"><span data-stu-id="272c8-778">External control</span></span>

<span data-ttu-id="272c8-779">Die externe Steuerung des Hosts kann mithilfe von Methoden durchgeführt werden, die extern aufgerufen werden können:</span><span class="sxs-lookup"><span data-stu-id="272c8-779">External control of the host can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

<span data-ttu-id="272c8-780"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> wird am Anfang der <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>-Methode aufgerufen, die den Vorgang erst fortsetzt, wenn sie abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="272c8-780"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="272c8-781">Dies kann verwendet werden, um den Start zu verzögern, bis dieser durch ein externes Ereignis initiiert wird.</span><span class="sxs-lookup"><span data-stu-id="272c8-781">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="272c8-782">IHostingEnvironment-Schnittstelle</span><span class="sxs-lookup"><span data-stu-id="272c8-782">IHostingEnvironment interface</span></span>

<span data-ttu-id="272c8-783"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> stellt Informationen über die Hostingumgebung der App bereit.</span><span class="sxs-lookup"><span data-stu-id="272c8-783"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="272c8-784">Verwenden Sie [Constructor Injection](xref:fundamentals/dependency-injection) zum Abrufen der <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>-Schnittstelle, um deren Eigenschaften und Erweiterungsmethoden zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="272c8-784">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

```csharp
public class MyClass
{
    private readonly IHostingEnvironment _env;

    public MyClass(IHostingEnvironment env)
    {
        _env = env;
    }

    public void DoSomething()
    {
        var environmentName = _env.EnvironmentName;
    }
}
```

<span data-ttu-id="272c8-785">Weitere Informationen finden Sie unter <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="272c8-785">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="272c8-786">IApplicationLifetime-Schnittstelle</span><span class="sxs-lookup"><span data-stu-id="272c8-786">IApplicationLifetime interface</span></span>

<span data-ttu-id="272c8-787"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> ermöglicht Aktivitäten nach dem Starten und beim Herunterfahren (einschließlich Anforderungen für ordnungsgemäßes Herunterfahren).</span><span class="sxs-lookup"><span data-stu-id="272c8-787"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="272c8-788">Bei drei der Eigenschaften der Schnittstelle handelt es sich um Abbruchtokens, die zum Registrieren von <xref:System.Action>-Methoden verwendet werden, durch die Ereignisse beim Starten und Herunterfahren definiert werden.</span><span class="sxs-lookup"><span data-stu-id="272c8-788">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="272c8-789">Abbruchtoken</span><span class="sxs-lookup"><span data-stu-id="272c8-789">Cancellation Token</span></span> | <span data-ttu-id="272c8-790">Wird ausgelöst, wenn&#8230;</span><span class="sxs-lookup"><span data-stu-id="272c8-790">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="272c8-791">Der Host vollständig gestartet wurde.</span><span class="sxs-lookup"><span data-stu-id="272c8-791">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="272c8-792">Der Host ein ordnungsgemäßes Herunterfahren abschließt.</span><span class="sxs-lookup"><span data-stu-id="272c8-792">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="272c8-793">Alle Anforderungen sollten verarbeitet worden sein.</span><span class="sxs-lookup"><span data-stu-id="272c8-793">All requests should be processed.</span></span> <span data-ttu-id="272c8-794">Das Herunterfahren wird blockiert, bis das Ereignis abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="272c8-794">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="272c8-795">Der Host ein ordnungsgemäßes Herunterfahren ausführt.</span><span class="sxs-lookup"><span data-stu-id="272c8-795">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="272c8-796">Anforderungen möglicherweise noch verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="272c8-796">Requests may still be processing.</span></span> <span data-ttu-id="272c8-797">Das Herunterfahren wird blockiert, bis das Ereignis abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="272c8-797">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="272c8-798">Fügen Sie den <xref:Microsoft.Extensions.Hosting.IApplicationLifetime>-Dienst über einen Konstruktor in eine beliebige Klasse ein.</span><span class="sxs-lookup"><span data-stu-id="272c8-798">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="272c8-799">Die [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) verwendet die Konstruktorinjektion für eine `LifetimeEventsHostedService`-Klasse (eine <xref:Microsoft.Extensions.Hosting.IHostedService>-Implementierung), um die Ereignisse zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="272c8-799">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="272c8-800">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="272c8-800">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="272c8-801"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> fordert das Beenden der App an.</span><span class="sxs-lookup"><span data-stu-id="272c8-801"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="272c8-802">Die folgende Klasse verwendet <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*>, um eine App ordnungsgemäß herunterzufahren, wenn die `Shutdown`-Methode der Klasse aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="272c8-802">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="272c8-803">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="272c8-803">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>

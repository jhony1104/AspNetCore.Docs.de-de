---
title: Generischer .NET-Host
author: tdykstra
description: Erfahren Sie mehr über den generischen Host in .NET Core, der für das Starten der App und das Verwalten der Lebensdauer verantwortlich ist.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/01/2019
uid: fundamentals/host/generic-host
ms.openlocfilehash: 75af6dc58d31aaad888b14640268bf05c193272d
ms.sourcegitcommit: e54672f5c493258dc449fac5b98faf47eb123b28
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2019
ms.locfileid: "71248275"
---
# <a name="net-generic-host"></a><span data-ttu-id="86bcb-103">Generischer .NET-Host</span><span class="sxs-lookup"><span data-stu-id="86bcb-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="86bcb-104">Dieser Artikel bietet eine Einführung in den generischen .NET Core-Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) sowie Hinweise zu dessen Verwendung.</span><span class="sxs-lookup"><span data-stu-id="86bcb-104">This article introduces the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) and provides guidance on how to use it.</span></span>

## <a name="whats-a-host"></a><span data-ttu-id="86bcb-105">Was ist ein Host?</span><span class="sxs-lookup"><span data-stu-id="86bcb-105">What's a host?</span></span>

<span data-ttu-id="86bcb-106">Der *Host* ist ein Objekt, das alle Ressourcen der App kapselt, z. B.:</span><span class="sxs-lookup"><span data-stu-id="86bcb-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="86bcb-107">Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="86bcb-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="86bcb-108">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="86bcb-108">Logging</span></span>
* <span data-ttu-id="86bcb-109">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="86bcb-109">Configuration</span></span>
* <span data-ttu-id="86bcb-110">`IHostedService`-Implementierungen</span><span class="sxs-lookup"><span data-stu-id="86bcb-110">`IHostedService` implementations</span></span>

<span data-ttu-id="86bcb-111">Wenn ein Host gestartet wird, ruft er `IHostedService.StartAsync` für jede Implementierung von <xref:Microsoft.Extensions.Hosting.IHostedService> auf, die im DI-Container gefunden wird.</span><span class="sxs-lookup"><span data-stu-id="86bcb-111">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="86bcb-112">In einer Web-App ist eine der `IHostedService`-Implementierungen ein Webdienst, der eine [HTTP-Serverimplementierung](xref:fundamentals/index#servers) startet.</span><span class="sxs-lookup"><span data-stu-id="86bcb-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="86bcb-113">Der wichtigste Grund für das Einschließen aller unabhängigen Ressourcen der App in einem Objekt ist die Verwaltung der Lebensdauer: steuern von Start und ordnungsgemäßem Herunterfahren der App.</span><span class="sxs-lookup"><span data-stu-id="86bcb-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="86bcb-114">In Versionen von ASP.NET Core vor 3.0 wird der [Webhost](xref:fundamentals/host/web-host) für HTTP-Workloads verwendet.</span><span class="sxs-lookup"><span data-stu-id="86bcb-114">In versions of ASP.NET Core earlier than 3.0, the [Web Host](xref:fundamentals/host/web-host) is used for HTTP workloads.</span></span> <span data-ttu-id="86bcb-115">Der Webhost wird nicht mehr für Web-Apps empfohlen und bleibt aus Gründen der Abwärtskompatibilität weiterhin verfügbar.</span><span class="sxs-lookup"><span data-stu-id="86bcb-115">The Web Host is no longer recommended for web apps and remains available only for backward compatibility.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="86bcb-116">Einrichten eines Hosts</span><span class="sxs-lookup"><span data-stu-id="86bcb-116">Set up a host</span></span>

<span data-ttu-id="86bcb-117">Der Host wird in der Regel per Code in der `Program`-Klasse konfiguriert, erstellt und ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="86bcb-117">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="86bcb-118">Die `Main`-Methode:</span><span class="sxs-lookup"><span data-stu-id="86bcb-118">The `Main` method:</span></span>

* <span data-ttu-id="86bcb-119">Ruft eine `CreateHostBuilder`-Methode zum Erstellen und Konfigurieren eines Generatorobjekts auf.</span><span class="sxs-lookup"><span data-stu-id="86bcb-119">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="86bcb-120">Ruft `Build`- und `Run`-Methoden für das Generatorobjekt auf.</span><span class="sxs-lookup"><span data-stu-id="86bcb-120">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="86bcb-121">Hier sehen Sie den *Program.cs*-Code für einen Nicht-HTTP-Workload mit einer einzelnen `IHostedService`-Implementierung, die dem DI-Container hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="86bcb-121">Here's *Program.cs* code for a non-HTTP workload, with a single `IHostedService` implementation added to the DI container.</span></span> 

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

<span data-ttu-id="86bcb-122">Für einen HTTP-Workload ist die `Main`-Methode die gleiche, `CreateHostBuilder` ruft jedoch `ConfigureWebHostDefaults` auf:</span><span class="sxs-lookup"><span data-stu-id="86bcb-122">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="86bcb-123">Ändern Sie nicht den Namen bzw. die Signatur der `CreateHostBuilder`-Methode, wenn die App Entity Framework Core verwendet.</span><span class="sxs-lookup"><span data-stu-id="86bcb-123">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="86bcb-124">Die [Entity Framework Core-Tools](/ef/core/miscellaneous/cli/) erwarten eine `CreateHostBuilder`-Methode, die den Host konfiguriert, ohne die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="86bcb-124">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="86bcb-125">Weitere Informationen finden Sie unter [DbContext-Instanzerstellung zur Entwurfszeit](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="86bcb-125">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="86bcb-126">Standardeinstellungen für den Generator</span><span class="sxs-lookup"><span data-stu-id="86bcb-126">Default builder settings</span></span> 

<span data-ttu-id="86bcb-127">Die <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>-Methode:</span><span class="sxs-lookup"><span data-stu-id="86bcb-127">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="86bcb-128">Legt das Inhaltsstammverzeichnis auf den Pfad fest, der von <xref:System.IO.Directory.GetCurrentDirectory*> zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="86bcb-128">Sets the content root to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="86bcb-129">Lädt Hostkonfiguration aus:</span><span class="sxs-lookup"><span data-stu-id="86bcb-129">Loads host configuration from:</span></span>
  * <span data-ttu-id="86bcb-130">Umgebungsvariablen mit dem Präfix „DOTNET_“.</span><span class="sxs-lookup"><span data-stu-id="86bcb-130">Environment variables prefixed with "DOTNET_".</span></span>
  * <span data-ttu-id="86bcb-131">Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="86bcb-131">Command-line arguments.</span></span>
* <span data-ttu-id="86bcb-132">Lädt App-Konfiguration aus:</span><span class="sxs-lookup"><span data-stu-id="86bcb-132">Loads app configuration from:</span></span>
  * <span data-ttu-id="86bcb-133">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="86bcb-133">*appsettings.json*.</span></span>
  * <span data-ttu-id="86bcb-134">*appsettings.{Environment}.json*</span><span class="sxs-lookup"><span data-stu-id="86bcb-134">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="86bcb-135">[Geheimnis-Manager](xref:security/app-secrets), wenn die App in der `Development`-Umgebung ausgeführt wird</span><span class="sxs-lookup"><span data-stu-id="86bcb-135">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="86bcb-136">Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="86bcb-136">Environment variables.</span></span>
  * <span data-ttu-id="86bcb-137">Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="86bcb-137">Command-line arguments.</span></span>
* <span data-ttu-id="86bcb-138">Fügt die folgenden [Protokollierungsanbieter](xref:fundamentals/logging/index) hinzu:</span><span class="sxs-lookup"><span data-stu-id="86bcb-138">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="86bcb-139">Konsole</span><span class="sxs-lookup"><span data-stu-id="86bcb-139">Console</span></span>
  * <span data-ttu-id="86bcb-140">Debug</span><span class="sxs-lookup"><span data-stu-id="86bcb-140">Debug</span></span>
  * <span data-ttu-id="86bcb-141">EventSource</span><span class="sxs-lookup"><span data-stu-id="86bcb-141">EventSource</span></span>
  * <span data-ttu-id="86bcb-142">EventLog (nur bei Ausführung unter Windows)</span><span class="sxs-lookup"><span data-stu-id="86bcb-142">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="86bcb-143">Aktiviert [Bereichsvalidierung](xref:fundamentals/dependency-injection#scope-validation) und [Abhängigkeitsüberprüfung](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild), wenn es sich um eine Entwicklungsumgebung handelt.</span><span class="sxs-lookup"><span data-stu-id="86bcb-143">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="86bcb-144">Die `ConfigureWebHostDefaults`-Methode:</span><span class="sxs-lookup"><span data-stu-id="86bcb-144">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="86bcb-145">Lädt Hostkonfiguration aus Umgebungsvariablen mit dem Präfix „ASPNETCORE_“.</span><span class="sxs-lookup"><span data-stu-id="86bcb-145">Loads host configuration from environment variables prefixed with "ASPNETCORE_".</span></span>
* <span data-ttu-id="86bcb-146">Legt den [Kestrel](xref:fundamentals/servers/kestrel)-Server als Webserver fest und konfiguriert ihn mithilfe der Hostkonfigurationsanbieter der App.</span><span class="sxs-lookup"><span data-stu-id="86bcb-146">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="86bcb-147">Die Standardoptionen des Kestrel-Servers finden Sie unter <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="86bcb-147">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="86bcb-148">Fügt [Middleware zum Filtern von Hosts](xref:fundamentals/servers/kestrel#host-filtering) hinzu.</span><span class="sxs-lookup"><span data-stu-id="86bcb-148">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="86bcb-149">Fügt [Middleware für weitergeleitete Header](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) hinzu, wenn ASPNETCORE_FORWARDEDHEADERS_ENABLED den Wert „true“ hat.</span><span class="sxs-lookup"><span data-stu-id="86bcb-149">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if ASPNETCORE_FORWARDEDHEADERS_ENABLED=true.</span></span>
* <span data-ttu-id="86bcb-150">Ermöglicht die Integration von IIS.</span><span class="sxs-lookup"><span data-stu-id="86bcb-150">Enables IIS integration.</span></span> <span data-ttu-id="86bcb-151">Informationen zu den IIS-Standardoptionen finden Sie unter <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="86bcb-151">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="86bcb-152">In den Abschnitten [Einstellungen für alle App-Typen](#settings-for-all-app-types) und [Einstellungen für Web-Apps](#settings-for-web-apps) weiter unten in diesem Artikel wird beschrieben, wie die Standardeinstellungen des Generators außer Kraft gesetzt werden.</span><span class="sxs-lookup"><span data-stu-id="86bcb-152">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="86bcb-153">Von Frameworks bereitgestellte Dienste</span><span class="sxs-lookup"><span data-stu-id="86bcb-153">Framework-provided services</span></span>

<span data-ttu-id="86bcb-154">Folgende Dienste werden automatisch registriert:</span><span class="sxs-lookup"><span data-stu-id="86bcb-154">Services that are registered automatically include the following:</span></span>

* [<span data-ttu-id="86bcb-155">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="86bcb-155">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="86bcb-156">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="86bcb-156">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="86bcb-157">IHostEnvironment/IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="86bcb-157">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="86bcb-158">Weitere Informationen zu den vom Framework bereitgestellten Diensten finden Sie unter <xref:fundamentals/dependency-injection#framework-provided-services>.</span><span class="sxs-lookup"><span data-stu-id="86bcb-158">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="86bcb-159">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="86bcb-159">IHostApplicationLifetime</span></span>

<span data-ttu-id="86bcb-160">Fügt den <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>-Dienst (vorher `IApplicationLifetime`-Dienst) in eine beliebige Klasse ein, um die Aktivitäten nach dem Starten und die Aufgaben für ordnungsgemäßes Herunterfahren zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="86bcb-160">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="86bcb-161">Bei drei der Eigenschaften der Schnittstelle handelt es sich um Abbruchtokens, die zum Registrieren von Ereignishandlermethoden zum Starten und Beenden von Apps verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="86bcb-161">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="86bcb-162">Die Benutzeroberfläche umfasst auch eine `StopApplication`-Methode.</span><span class="sxs-lookup"><span data-stu-id="86bcb-162">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="86bcb-163">Das folgende Beispiel zeigt eine `IHostedService`-Implementierung, die die `IApplicationLifetime`-Ereignisse registriert:</span><span class="sxs-lookup"><span data-stu-id="86bcb-163">The following example is an `IHostedService` implementation that registers the `IApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="86bcb-164">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="86bcb-164">IHostLifetime</span></span>

<span data-ttu-id="86bcb-165">Die <xref:Microsoft.Extensions.Hosting.IHostLifetime>-Implementierung steuert, wann der Host gestartet und beendet wird.</span><span class="sxs-lookup"><span data-stu-id="86bcb-165">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="86bcb-166">Die zuletzt registrierte Implementierung wird verwendet.</span><span class="sxs-lookup"><span data-stu-id="86bcb-166">The last implementation registered is used.</span></span>

<span data-ttu-id="86bcb-167"><xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> ist die `IHostLifetime`-Standardimplementierung.</span><span class="sxs-lookup"><span data-stu-id="86bcb-167"><xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="86bcb-168">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="86bcb-168">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="86bcb-169">lauscht auf STRG+C/SIGINT oder SIGTERM und ruft <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> auf, um das Herunterfahren zu beginnen.</span><span class="sxs-lookup"><span data-stu-id="86bcb-169">listens for Ctrl+C/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="86bcb-170">Hebt die Blockierung von Erweiterungen wie [RunAsync](#runasync) und [WaitForShutdownAsync](#waitforshutdownasync) auf.</span><span class="sxs-lookup"><span data-stu-id="86bcb-170">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="86bcb-171">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="86bcb-171">IHostEnvironment</span></span>

<span data-ttu-id="86bcb-172">Fügt den <xref:Microsoft.Extensions.Hosting.IHostEnvironment>-Dienst in eine Klasse ein, um Informationen abzurufen über:</span><span class="sxs-lookup"><span data-stu-id="86bcb-172">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following:</span></span>

* [<span data-ttu-id="86bcb-173">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="86bcb-173">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="86bcb-174">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="86bcb-174">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="86bcb-175">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="86bcb-175">ContentRootPath</span></span>](#contentrootpath)

<span data-ttu-id="86bcb-176">Web-Apps implementieren die `IWebHostEnvironment`-Schnittstelle, die `IHostEnvironment` erbt und Folgendes hinzufügt:</span><span class="sxs-lookup"><span data-stu-id="86bcb-176">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds:</span></span>

* [<span data-ttu-id="86bcb-177">WebRootPath</span><span class="sxs-lookup"><span data-stu-id="86bcb-177">WebRootPath</span></span>](#webroot)

## <a name="host-configuration"></a><span data-ttu-id="86bcb-178">Konfiguration des Hosts</span><span class="sxs-lookup"><span data-stu-id="86bcb-178">Host configuration</span></span>

<span data-ttu-id="86bcb-179">Die Hostkonfiguration wird für die Eigenschaften der <xref:Microsoft.Extensions.Hosting.IHostEnvironment>-Implementierung verwendet.</span><span class="sxs-lookup"><span data-stu-id="86bcb-179">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="86bcb-180">Sie ist über [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> verfügbar.</span><span class="sxs-lookup"><span data-stu-id="86bcb-180">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="86bcb-181">Nach `ConfigureAppConfiguration` wird `HostBuilderContext.Configuration` durch die App-Konfiguration ersetzt.</span><span class="sxs-lookup"><span data-stu-id="86bcb-181">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="86bcb-182">Rufen Sie <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> für `IHostBuilder` auf, um die Hostkonfiguration hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="86bcb-182">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="86bcb-183">`ConfigureHostConfiguration` kann mehrfach mit additiven Ergebnissen aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="86bcb-183">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="86bcb-184">Der Host verwendet die Option, die zuletzt einen Wert für einen bestimmten Schlüssel festlegt.</span><span class="sxs-lookup"><span data-stu-id="86bcb-184">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="86bcb-185">Der Umgebungsvariablenanbieter mit dem Präfix `DOTNET_` und die Befehlszeilenargumente werden von CreateDefaultBuilder miteinbezogen.</span><span class="sxs-lookup"><span data-stu-id="86bcb-185">The environment variable provider with prefix `DOTNET_` and command line args are included by CreateDefaultBuilder.</span></span> <span data-ttu-id="86bcb-186">Für Web-Apps wird der Umgebungsvariablenanbieter mit dem Präfix `ASPNETCORE_` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="86bcb-186">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="86bcb-187">Das Präfix wird entfernt, wenn die Umgebungsvariablen gelesen werden.</span><span class="sxs-lookup"><span data-stu-id="86bcb-187">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="86bcb-188">Der Wert der Umgebungsvariable für `ASPNETCORE_ENVIRONMENT` wird z. B. der Hostkonfigurationswert für den `environment`-Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="86bcb-188">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="86bcb-189">Im folgenden Beispiel wird eine Hostkonfiguration erstellt:</span><span class="sxs-lookup"><span data-stu-id="86bcb-189">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="86bcb-190">App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="86bcb-190">App configuration</span></span>

<span data-ttu-id="86bcb-191">Die App-Konfiguration wird durch Aufrufen von <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> für `IHostBuilder` erstellt.</span><span class="sxs-lookup"><span data-stu-id="86bcb-191">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="86bcb-192">`ConfigureAppConfiguration` kann mehrfach mit additiven Ergebnissen aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="86bcb-192">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="86bcb-193">Die App verwendet die Option, die zuletzt einen Wert für einen bestimmten Schlüssel festlegt.</span><span class="sxs-lookup"><span data-stu-id="86bcb-193">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="86bcb-194">Die von `ConfigureAppConfiguration` erstellte Konfiguration ist unter [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) verfügbar und kann für nachfolgende Vorgänge und als Dienst für die Abhängigkeitsinjektion verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="86bcb-194">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="86bcb-195">Die Hostkonfiguration wird ebenfalls der App-Konfiguration hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="86bcb-195">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="86bcb-196">Weitere Informationen finden Sie unter [Konfiguration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="86bcb-196">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="86bcb-197">Einstellungen für alle App-Typen</span><span class="sxs-lookup"><span data-stu-id="86bcb-197">Settings for all app types</span></span>

<span data-ttu-id="86bcb-198">In diesem Abschnitt werden Hosteinstellungen aufgeführt, die sowohl für HTTP- als auch für Nicht-HTTP-Workloads gelten.</span><span class="sxs-lookup"><span data-stu-id="86bcb-198">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="86bcb-199">Standardmäßig können Umgebungsvariablen, die zur Konfiguration dieser Einstellungen verwendet werden, ein `DOTNET_`- oder `ASPNETCORE_`-Präfix haben.</span><span class="sxs-lookup"><span data-stu-id="86bcb-199">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="86bcb-200">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="86bcb-200">ApplicationName</span></span>

<span data-ttu-id="86bcb-201">Die Eigenschaft [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) wird von der Hostkonfiguration während der Hosterstellung festgelegt.</span><span class="sxs-lookup"><span data-stu-id="86bcb-201">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="86bcb-202">**Schlüssel:** Anwendungsname</span><span class="sxs-lookup"><span data-stu-id="86bcb-202">**Key**: applicationName</span></span>  
<span data-ttu-id="86bcb-203">**Typ:** *Zeichenfolge*</span><span class="sxs-lookup"><span data-stu-id="86bcb-203">**Type**: *string*</span></span>  
<span data-ttu-id="86bcb-204">**Standard**: Der Name der Assembly, die den Einstiegspunkt der App enthält.</span><span class="sxs-lookup"><span data-stu-id="86bcb-204">**Default**: The name of the assembly that contains the app's entry point.</span></span>
<span data-ttu-id="86bcb-205">**Umgebungsvariable:** `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="86bcb-205">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="86bcb-206">Verwenden Sie die Umgebungsvariable, um diesen Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="86bcb-206">To set this value, use the environment variable.</span></span> 

### <a name="contentrootpath"></a><span data-ttu-id="86bcb-207">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="86bcb-207">ContentRootPath</span></span>

<span data-ttu-id="86bcb-208">Die Eigenschaft [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) bestimmt, wo der Host mit der Suche nach Inhaltsdateien beginnt.</span><span class="sxs-lookup"><span data-stu-id="86bcb-208">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="86bcb-209">Wenn der Pfad nicht vorhanden ist, kann der Host nicht gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="86bcb-209">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="86bcb-210">**Schlüssel:** contentRoot</span><span class="sxs-lookup"><span data-stu-id="86bcb-210">**Key**: contentRoot</span></span>  
<span data-ttu-id="86bcb-211">**Typ:** *Zeichenfolge*</span><span class="sxs-lookup"><span data-stu-id="86bcb-211">**Type**: *string*</span></span>  
<span data-ttu-id="86bcb-212">**Standard**: Der Ordner, in dem die App-Assembly gespeichert ist.</span><span class="sxs-lookup"><span data-stu-id="86bcb-212">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="86bcb-213">**Umgebungsvariable:** `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="86bcb-213">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="86bcb-214">Verwenden Sie die Umgebungsvariable, oder rufen Sie `UseContentRoot` für `IHostBuilder` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="86bcb-214">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

### <a name="environmentname"></a><span data-ttu-id="86bcb-215">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="86bcb-215">EnvironmentName</span></span>

<span data-ttu-id="86bcb-216">Die Eigenschaft [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) kann auf einen beliebigen Wert festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="86bcb-216">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="86bcb-217">Zu den durch Frameworks definierten Werten zählen `Development`, `Staging` und `Production`.</span><span class="sxs-lookup"><span data-stu-id="86bcb-217">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="86bcb-218">Die Groß-/Kleinschreibung wird bei Werten nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="86bcb-218">Values aren't case sensitive.</span></span>

<span data-ttu-id="86bcb-219">**Schlüssel:** environment</span><span class="sxs-lookup"><span data-stu-id="86bcb-219">**Key**: environment</span></span>  
<span data-ttu-id="86bcb-220">**Typ:** *Zeichenfolge*</span><span class="sxs-lookup"><span data-stu-id="86bcb-220">**Type**: *string*</span></span>  
<span data-ttu-id="86bcb-221">**Standardwert**: Produktion</span><span class="sxs-lookup"><span data-stu-id="86bcb-221">**Default**: Production</span></span>  
<span data-ttu-id="86bcb-222">**Umgebungsvariable:** `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="86bcb-222">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="86bcb-223">Verwenden Sie die Umgebungsvariable, oder rufen Sie `UseEnvironment` für `IHostBuilder` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="86bcb-223">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="86bcb-224">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="86bcb-224">ShutdownTimeout</span></span>

<span data-ttu-id="86bcb-225">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) legt das Zeitlimit für <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> fest.</span><span class="sxs-lookup"><span data-stu-id="86bcb-225">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="86bcb-226">Der Standardwert beträgt fünf Sekunden.</span><span class="sxs-lookup"><span data-stu-id="86bcb-226">The default value is five seconds.</span></span>  <span data-ttu-id="86bcb-227">Während des Zeitlimits verhält sich der Host folgendermaßen:</span><span class="sxs-lookup"><span data-stu-id="86bcb-227">During the timeout period, the host:</span></span>

* <span data-ttu-id="86bcb-228">Er löst [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping) aus.</span><span class="sxs-lookup"><span data-stu-id="86bcb-228">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="86bcb-229">Er versucht, gehostete Dienste zu beenden und protokolliert Fehler für Dienste, die nicht beendet werden können.</span><span class="sxs-lookup"><span data-stu-id="86bcb-229">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="86bcb-230">Wenn das Zeitlimit abläuft bevor alle gehosteten Dienste beendet sind, werden alle verbleibenden aktiven Dienste beendet, wenn die App herunterfährt.</span><span class="sxs-lookup"><span data-stu-id="86bcb-230">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="86bcb-231">Die Dienste werden beendet, selbst wenn die Verarbeitung noch nicht abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="86bcb-231">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="86bcb-232">Wenn die Dienste mehr Zeit zum Beenden benötigen, sollten Sie das Zeitlimit erhöhen.</span><span class="sxs-lookup"><span data-stu-id="86bcb-232">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="86bcb-233">**Schlüssel:** shutdownTimeoutSeconds</span><span class="sxs-lookup"><span data-stu-id="86bcb-233">**Key**: shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="86bcb-234">**Typ:** *Ganze Zahl*</span><span class="sxs-lookup"><span data-stu-id="86bcb-234">**Type**: *int*</span></span>  
<span data-ttu-id="86bcb-235">**Standard**: 5 Sekunden **Umgebungsvariable:** : `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="86bcb-235">**Default**: 5 seconds **Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="86bcb-236">Verwenden Sie die Umgebungsvariable, oder konfigurieren Sie `HostOptions`, um diesen Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="86bcb-236">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="86bcb-237">Im folgenden Beispiel wird das Zeitlimit auf 20 Sekunden festgelegt:</span><span class="sxs-lookup"><span data-stu-id="86bcb-237">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="86bcb-238">Einstellungen für Web-Apps</span><span class="sxs-lookup"><span data-stu-id="86bcb-238">Settings for web apps</span></span>

<span data-ttu-id="86bcb-239">Einige Hosteinstellungen gelten nur für HTTP-Workloads.</span><span class="sxs-lookup"><span data-stu-id="86bcb-239">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="86bcb-240">Standardmäßig können Umgebungsvariablen, die zur Konfiguration dieser Einstellungen verwendet werden, ein `DOTNET_`- oder `ASPNETCORE_`-Präfix haben.</span><span class="sxs-lookup"><span data-stu-id="86bcb-240">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="86bcb-241">Für diese Einstellungen sind Erweiterungsmethoden in `IWebHostBuilder` verfügbar.</span><span class="sxs-lookup"><span data-stu-id="86bcb-241">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="86bcb-242">In den Codebeispielen, die zeigen, wie Sie die Erweiterungsmethoden aufrufen können, wird davon ausgegangen, dass `webBuilder` eine Instanz von `IWebHostBuilder` ist. Sehen Sie hierzu das folgende Beispiel:</span><span class="sxs-lookup"><span data-stu-id="86bcb-242">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="86bcb-243">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="86bcb-243">CaptureStartupErrors</span></span>

<span data-ttu-id="86bcb-244">Wenn diese `false` ist, führen Fehler beim Start zum Beenden des Hosts.</span><span class="sxs-lookup"><span data-stu-id="86bcb-244">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="86bcb-245">Wenn diese `true` ist, erfasst der Host Ausnahmen während des Starts und versucht, den Server zu starten.</span><span class="sxs-lookup"><span data-stu-id="86bcb-245">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="86bcb-246">**Schlüssel:** captureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="86bcb-246">**Key**: captureStartupErrors</span></span>  
<span data-ttu-id="86bcb-247">**Typ:** *Boolesch* (`true` or `1`)</span><span class="sxs-lookup"><span data-stu-id="86bcb-247">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="86bcb-248">**Standardwert**: Die Standardeinstellung ist gleich `false`, es sei denn, die App wird mit Kestrel hinter IIS ausgeführt, dann ist sie gleich `true`.</span><span class="sxs-lookup"><span data-stu-id="86bcb-248">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="86bcb-249">**Umgebungsvariable:** `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="86bcb-249">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="86bcb-250">Verwenden Sie die Konfiguration, oder rufen Sie `CaptureStartupErrors` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="86bcb-250">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="86bcb-251">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="86bcb-251">DetailedErrors</span></span>

<span data-ttu-id="86bcb-252">Wenn diese Einstellung aktiviert ist oder die Umgebung auf `Development` festgelegt ist, erfasst die App detaillierte Fehler.</span><span class="sxs-lookup"><span data-stu-id="86bcb-252">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="86bcb-253">**Schlüssel:** detailedErrors</span><span class="sxs-lookup"><span data-stu-id="86bcb-253">**Key**: detailedErrors</span></span>  
<span data-ttu-id="86bcb-254">**Typ:** *Boolesch* (`true` or `1`)</span><span class="sxs-lookup"><span data-stu-id="86bcb-254">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="86bcb-255">**Standard:** FALSE</span><span class="sxs-lookup"><span data-stu-id="86bcb-255">**Default**: false</span></span>  
<span data-ttu-id="86bcb-256">**Umgebungsvariable:** `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="86bcb-256">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="86bcb-257">Verwenden Sie die Konfiguration, oder rufen Sie `UseSetting` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="86bcb-257">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="86bcb-258">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="86bcb-258">HostingStartupAssemblies</span></span>

<span data-ttu-id="86bcb-259">Eine durch Semikolons getrennte Zeichenfolge der Hostingstartassemblys, die beim Start geladen werden soll.</span><span class="sxs-lookup"><span data-stu-id="86bcb-259">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="86bcb-260">Obwohl der Konfigurationswert standardmäßig auf eine leere Zeichenfolge festgelegt ist, enthalten die Hostingstartassemblys immer die Assembly der App.</span><span class="sxs-lookup"><span data-stu-id="86bcb-260">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="86bcb-261">Wenn Hostingstartassemblys bereitgestellt werden, werden diese zur Assembly der App hinzugefügt, damit diese geladen werden, wenn die App während des Starts die allgemeinen Dienste erstellt.</span><span class="sxs-lookup"><span data-stu-id="86bcb-261">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="86bcb-262">**Schlüssel:** hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="86bcb-262">**Key**: hostingStartupAssemblies</span></span>  
<span data-ttu-id="86bcb-263">**Typ:** *Zeichenfolge*</span><span class="sxs-lookup"><span data-stu-id="86bcb-263">**Type**: *string*</span></span>  
<span data-ttu-id="86bcb-264">**Standardwert**: Leere Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="86bcb-264">**Default**: Empty string</span></span>  
<span data-ttu-id="86bcb-265">**Umgebungsvariable:** `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="86bcb-265">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="86bcb-266">Verwenden Sie die Konfiguration, oder rufen Sie `UseSetting` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="86bcb-266">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="86bcb-267">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="86bcb-267">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="86bcb-268">Eine durch Semikolons getrennte Zeichenfolge der Hostingstartassemblys, die beim Start ausgeschlossen werden sollen.</span><span class="sxs-lookup"><span data-stu-id="86bcb-268">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="86bcb-269">**Schlüssel**: hostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="86bcb-269">**Key**: hostingStartupExcludeAssemblies</span></span>  
<span data-ttu-id="86bcb-270">**Typ:** *Zeichenfolge*</span><span class="sxs-lookup"><span data-stu-id="86bcb-270">**Type**: *string*</span></span>  
<span data-ttu-id="86bcb-271">**Standardwert**: Leere Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="86bcb-271">**Default**: Empty string</span></span>  
<span data-ttu-id="86bcb-272">**Umgebungsvariable:** `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="86bcb-272">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="86bcb-273">Verwenden Sie die Konfiguration, oder rufen Sie `UseSetting` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="86bcb-273">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="86bcb-274">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="86bcb-274">HTTPS_Port</span></span>

<span data-ttu-id="86bcb-275">Der HTTPS-Umleitungsport.</span><span class="sxs-lookup"><span data-stu-id="86bcb-275">The HTTPS redirect port.</span></span> <span data-ttu-id="86bcb-276">Wird in [Erzwingen von HTTPS](xref:security/enforcing-ssl) verwendet.</span><span class="sxs-lookup"><span data-stu-id="86bcb-276">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="86bcb-277">**Schlüssel**: https_port</span><span class="sxs-lookup"><span data-stu-id="86bcb-277">**Key**: https_port</span></span>  
<span data-ttu-id="86bcb-278">**Typ:** *Zeichenfolge*</span><span class="sxs-lookup"><span data-stu-id="86bcb-278">**Type**: *string*</span></span>  
<span data-ttu-id="86bcb-279">**Standard**: Es ist kein Standardwert festgelegt.</span><span class="sxs-lookup"><span data-stu-id="86bcb-279">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="86bcb-280">**Umgebungsvariable:** `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="86bcb-280">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="86bcb-281">Verwenden Sie die Konfiguration, oder rufen Sie `UseSetting` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="86bcb-281">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="86bcb-282">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="86bcb-282">PreferHostingUrls</span></span>

<span data-ttu-id="86bcb-283">Gibt an, ob der Hosts den URLs lauschen soll, die mit `IWebHostBuilder` konfiguriert wurden, statt denen, die mit der `IServer`-Implementierung konfiguriert wurden.</span><span class="sxs-lookup"><span data-stu-id="86bcb-283">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="86bcb-284">**Schlüssel:** preferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="86bcb-284">**Key**: preferHostingUrls</span></span>  
<span data-ttu-id="86bcb-285">**Typ:** *Boolesch* (`true` or `1`)</span><span class="sxs-lookup"><span data-stu-id="86bcb-285">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="86bcb-286">**Standard:** TRUE</span><span class="sxs-lookup"><span data-stu-id="86bcb-286">**Default**: true</span></span>  
<span data-ttu-id="86bcb-287">**Umgebungsvariable:** `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="86bcb-287">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="86bcb-288">Verwenden Sie die Umgebungsvariable, oder rufen Sie `PreferHostingUrls` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="86bcb-288">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="86bcb-289">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="86bcb-289">PreventHostingStartup</span></span>

<span data-ttu-id="86bcb-290">Verhindert das automatische Laden der Hostingstartassemblys, einschließlich denen, die von der Assembly der App konfiguriert wurden.</span><span class="sxs-lookup"><span data-stu-id="86bcb-290">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="86bcb-291">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="86bcb-291">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="86bcb-292">**Schlüssel:** preventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="86bcb-292">**Key**: preventHostingStartup</span></span>  
<span data-ttu-id="86bcb-293">**Typ:** *Boolesch* (`true` or `1`)</span><span class="sxs-lookup"><span data-stu-id="86bcb-293">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="86bcb-294">**Standard:** FALSE</span><span class="sxs-lookup"><span data-stu-id="86bcb-294">**Default**: false</span></span>  
<span data-ttu-id="86bcb-295">**Umgebungsvariable:** `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="86bcb-295">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="86bcb-296">Verwenden Sie die Umgebungsvariable, oder rufen Sie `UseSetting` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="86bcb-296">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="86bcb-297">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="86bcb-297">StartupAssembly</span></span>

<span data-ttu-id="86bcb-298">Die Assembly, die nach der `Startup`-Klasse suchen soll.</span><span class="sxs-lookup"><span data-stu-id="86bcb-298">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="86bcb-299">**Schlüssel:** startupAssembly</span><span class="sxs-lookup"><span data-stu-id="86bcb-299">**Key**: startupAssembly</span></span>  
<span data-ttu-id="86bcb-300">**Typ:** *Zeichenfolge*</span><span class="sxs-lookup"><span data-stu-id="86bcb-300">**Type**: *string*</span></span>  
<span data-ttu-id="86bcb-301">**Standardwert**: Die Assembly der App</span><span class="sxs-lookup"><span data-stu-id="86bcb-301">**Default**: The app's assembly</span></span>  
<span data-ttu-id="86bcb-302">**Umgebungsvariable:** `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="86bcb-302">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="86bcb-303">Verwenden Sie die Umgebungsvariable, oder rufen Sie `UseStartup` auf, um diesen Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="86bcb-303">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="86bcb-304">`UseStartup` kann einen Assemblynamen (`string`) oder einen Typ (`TStartup`) annehmen.</span><span class="sxs-lookup"><span data-stu-id="86bcb-304">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="86bcb-305">Wenn mehrere `UseStartup`-Methoden aufgerufen werden, hat die letzte Vorrang.</span><span class="sxs-lookup"><span data-stu-id="86bcb-305">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="86bcb-306">URLs</span><span class="sxs-lookup"><span data-stu-id="86bcb-306">URLs</span></span>

<span data-ttu-id="86bcb-307">Eine durch Semikolons getrennte Liste mit IP-Adressen oder Hostadressen mit Ports und Protokollen, denen der Server für Anforderungen lauschen soll.</span><span class="sxs-lookup"><span data-stu-id="86bcb-307">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="86bcb-308">Beispielsweise `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="86bcb-308">For example, `http://localhost:123`.</span></span> <span data-ttu-id="86bcb-309">Verwenden Sie \*, um anzugeben, dass der Server mithilfe des angegebenen Ports und Protokolls (z.B. `http://*:5000`) den Anfragen aller IP-Adressen oder Hostnamen lauschen soll.</span><span class="sxs-lookup"><span data-stu-id="86bcb-309">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="86bcb-310">Das Protokoll (`http://` oder `https://`) muss in jeder URL enthalten sein.</span><span class="sxs-lookup"><span data-stu-id="86bcb-310">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="86bcb-311">Die unterstützten Formate variieren bei den verschiedenen Servern.</span><span class="sxs-lookup"><span data-stu-id="86bcb-311">Supported formats vary among servers.</span></span>

<span data-ttu-id="86bcb-312">**Schlüssel:** urls</span><span class="sxs-lookup"><span data-stu-id="86bcb-312">**Key**: urls</span></span>  
<span data-ttu-id="86bcb-313">**Typ:** *Zeichenfolge*</span><span class="sxs-lookup"><span data-stu-id="86bcb-313">**Type**: *string*</span></span>  
<span data-ttu-id="86bcb-314">**Standard**: `http://localhost:5000` und `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="86bcb-314">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="86bcb-315">**Umgebungsvariable:** `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="86bcb-315">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="86bcb-316">Verwenden Sie die Umgebungsvariable, oder rufen Sie `UseUrls` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="86bcb-316">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="86bcb-317">Kestrel verfügt über eine eigene API für die Endpunktkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="86bcb-317">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="86bcb-318">Weitere Informationen finden Sie unter <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="86bcb-318">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="86bcb-319">WebRoot</span><span class="sxs-lookup"><span data-stu-id="86bcb-319">WebRoot</span></span>

<span data-ttu-id="86bcb-320">Der relative Pfad für die statischen Objekte der App.</span><span class="sxs-lookup"><span data-stu-id="86bcb-320">The relative path to the app's static assets.</span></span>

<span data-ttu-id="86bcb-321">**Schlüssel:** webroot</span><span class="sxs-lookup"><span data-stu-id="86bcb-321">**Key**: webroot</span></span>  
<span data-ttu-id="86bcb-322">**Typ:** *Zeichenfolge*</span><span class="sxs-lookup"><span data-stu-id="86bcb-322">**Type**: *string*</span></span>  
<span data-ttu-id="86bcb-323">**Standard**: *(Inhaltsstammverzeichnis)/wwwroot*, wenn der Pfad vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="86bcb-323">**Default**: *(Content Root)/wwwroot*, if the path exists.</span></span> <span data-ttu-id="86bcb-324">Wenn der Pfad nicht vorhanden ist, wird ein Dateianbieter ohne Funktion verwendet.</span><span class="sxs-lookup"><span data-stu-id="86bcb-324">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="86bcb-325">**Umgebungsvariable:** `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="86bcb-325">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="86bcb-326">Verwenden Sie die Umgebungsvariable, oder rufen Sie `UseWebRoot` auf, um diesen Wert festzulegen:</span><span class="sxs-lookup"><span data-stu-id="86bcb-326">To set this value, use the environment variable or call `UseWebRoot`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="86bcb-327">Verwalten der Lebensdauer des Hosts</span><span class="sxs-lookup"><span data-stu-id="86bcb-327">Manage the host lifetime</span></span>

<span data-ttu-id="86bcb-328">Rufen Sie Methoden für die erstellte <xref:Microsoft.Extensions.Hosting.IHost>-Implementierung auf, um die App zu starten und zu beenden.</span><span class="sxs-lookup"><span data-stu-id="86bcb-328">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="86bcb-329">Diese Methoden wirken sich auf alle <xref:Microsoft.Extensions.Hosting.IHostedService>-Implementierungen aus, die im Dienstcontainer registriert sind.</span><span class="sxs-lookup"><span data-stu-id="86bcb-329">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="86bcb-330">Run</span><span class="sxs-lookup"><span data-stu-id="86bcb-330">Run</span></span>

<span data-ttu-id="86bcb-331">Durch <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> wird die App gestartet und der aufrufende Thread blockiert, bis der Host heruntergefahren wird.</span><span class="sxs-lookup"><span data-stu-id="86bcb-331"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="86bcb-332">RunAsync</span><span class="sxs-lookup"><span data-stu-id="86bcb-332">RunAsync</span></span>

<span data-ttu-id="86bcb-333">Durch <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> wird die App ausgeführt und eine <xref:System.Threading.Tasks.Task>-Methode ausgegeben, die abgeschlossen wird, wenn das Abbruchtoken oder das Herunterfahren ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="86bcb-333"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="86bcb-334">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="86bcb-334">RunConsoleAsync</span></span>

<span data-ttu-id="86bcb-335"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> aktiviert die Unterstützung der Konsole, erstellt und startet den Host und lauscht auf STRG+C/SIGINT oder SIGTERM, um das Herunterfahren auszulösen.</span><span class="sxs-lookup"><span data-stu-id="86bcb-335"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for Ctrl+C/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="86bcb-336">Starten</span><span class="sxs-lookup"><span data-stu-id="86bcb-336">Start</span></span>

<span data-ttu-id="86bcb-337"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> startet den Host synchron.</span><span class="sxs-lookup"><span data-stu-id="86bcb-337"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="86bcb-338">StartAsync</span><span class="sxs-lookup"><span data-stu-id="86bcb-338">StartAsync</span></span>

<span data-ttu-id="86bcb-339">Durch <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> wird der Host gestartet und eine <xref:System.Threading.Tasks.Task>-Methode ausgegeben, die abgeschlossen wird, wenn das Abbruchtoken oder das Herunterfahren ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="86bcb-339"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="86bcb-340"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> wird am Anfang der `StartAsync`-Methode aufgerufen, die den Vorgang erst fortsetzt, wenn sie abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="86bcb-340"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="86bcb-341">Dies kann verwendet werden, um den Start zu verzögern, bis dieser durch ein externes Ereignis initiiert wird.</span><span class="sxs-lookup"><span data-stu-id="86bcb-341">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="86bcb-342">StopAsync</span><span class="sxs-lookup"><span data-stu-id="86bcb-342">StopAsync</span></span>

<span data-ttu-id="86bcb-343"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> versucht, den Host innerhalb des angegebenen Timeouts zu beenden.</span><span class="sxs-lookup"><span data-stu-id="86bcb-343"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="86bcb-344">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="86bcb-344">WaitForShutdown</span></span>

<span data-ttu-id="86bcb-345"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blockiert den aufrufenden Thread, bis das Herunterfahren durch den IHostLifetime wie z. B. über STRG+C/SIGINT oder SIGTERM ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="86bcb-345"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via Ctrl+C/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="86bcb-346">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="86bcb-346">WaitForShutdownAsync</span></span>

<span data-ttu-id="86bcb-347"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> gibt eine <xref:System.Threading.Tasks.Task>-Methode zurück, die abgeschlossen wird, wenn das Herunterfahren über das angegebene Token ausgelöst wird, und ruft <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> auf.</span><span class="sxs-lookup"><span data-stu-id="86bcb-347"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="86bcb-348">Externe Steuerung</span><span class="sxs-lookup"><span data-stu-id="86bcb-348">External control</span></span>

<span data-ttu-id="86bcb-349">Die Lebensdauer des Hosts kann mithilfe von Methoden, die extern aufgerufen werden können, direkt gesteuert werden:</span><span class="sxs-lookup"><span data-stu-id="86bcb-349">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="86bcb-350">Durch ASP.NET Core-Apps kann ein Host gestartet und konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="86bcb-350">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="86bcb-351">Der Host ist verantwortlich für das Starten der App und das Verwalten der Lebensdauer.</span><span class="sxs-lookup"><span data-stu-id="86bcb-351">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="86bcb-352">In diesem Artikel wird der generische ASP.NET Core-Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) erläutert, der für das Hosten von Apps verwendet wird, die keine HTTP-Anforderungen verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="86bcb-352">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="86bcb-353">Das Ziel des generischen Hosts besteht darin, die HTTP-Pipeline von der Webhost-API zu entkoppeln, um mehr Hostszenarios zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="86bcb-353">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="86bcb-354">Messaging, Hintergrundtasks und andere Nicht-HTTP-Workloads, die auf dem generischen Host basieren, profitieren von übergreifenden Funktionen wie der Konfiguration, Dependency Injection (DI) und der Protokollerstellung.</span><span class="sxs-lookup"><span data-stu-id="86bcb-354">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="86bcb-355">Der generische Host ist neu in ASP.NET Core 2.1 und ist nicht für Webhostingszenarios geeignet.</span><span class="sxs-lookup"><span data-stu-id="86bcb-355">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="86bcb-356">Verwenden Sie den [Webhost](xref:fundamentals/host/web-host) für Webhostingszenarios.</span><span class="sxs-lookup"><span data-stu-id="86bcb-356">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="86bcb-357">Der generische Host wird den Webhost in einem zukünftigen Release ersetzen und als primäre Host-API in HTTP- und Nicht-HTTP-Szenarios fungieren.</span><span class="sxs-lookup"><span data-stu-id="86bcb-357">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="86bcb-358">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="86bcb-358">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="86bcb-359">Wenn Sie die Beispiel-App in [Visual Studio Code](https://code.visualstudio.com/) ausführen, verwenden Sie ein *externes oder integriertes Terminal*.</span><span class="sxs-lookup"><span data-stu-id="86bcb-359">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="86bcb-360">Führen Sie das Beispiel nicht in `internalConsole` aus.</span><span class="sxs-lookup"><span data-stu-id="86bcb-360">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="86bcb-361">So legen Sie die Konsole in Visual Studio Code fest:</span><span class="sxs-lookup"><span data-stu-id="86bcb-361">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="86bcb-362">Öffnen Sie die Datei *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="86bcb-362">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="86bcb-363">Suchen Sie in der Konfiguration **.NET Core-Start (Konsole)** den Eintrag **Konsole**.</span><span class="sxs-lookup"><span data-stu-id="86bcb-363">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="86bcb-364">Legen Sie den Wert auf `externalTerminal` oder `integratedTerminal` fest.</span><span class="sxs-lookup"><span data-stu-id="86bcb-364">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="86bcb-365">Einführung</span><span class="sxs-lookup"><span data-stu-id="86bcb-365">Introduction</span></span>

<span data-ttu-id="86bcb-366">Die generische Hostbibliothek ist im Namespace <xref:Microsoft.Extensions.Hosting> verfügbar und wird vom Paket [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="86bcb-366">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="86bcb-367">Das Paket `Microsoft.Extensions.Hosting` ist im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 oder höher) enthalten.</span><span class="sxs-lookup"><span data-stu-id="86bcb-367">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="86bcb-368"><xref:Microsoft.Extensions.Hosting.IHostedService> ist der Einstiegspunkt für die Ausführung des Codes.</span><span class="sxs-lookup"><span data-stu-id="86bcb-368"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="86bcb-369">Jede Implementierung von <xref:Microsoft.Extensions.Hosting.IHostedService> wird in der Reihenfolge der [Dienstregistrierung in ConfigureServices](#configureservices) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="86bcb-369">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="86bcb-370"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> wird in jeder <xref:Microsoft.Extensions.Hosting.IHostedService>-Schnittstelle aufgerufen, wenn der Host gestartet wird, und <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> wird in umgekehrter Registrierungsreihenfolge aufgerufen, wenn der Host ordnungsgemäß heruntergefahren wird.</span><span class="sxs-lookup"><span data-stu-id="86bcb-370"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="86bcb-371">Einrichten eines Hosts</span><span class="sxs-lookup"><span data-stu-id="86bcb-371">Set up a host</span></span>

<span data-ttu-id="86bcb-372"><xref:Microsoft.Extensions.Hosting.IHostBuilder> ist die Hauptkomponente, die Bibliotheken und Apps für die Initialisierung, Erstellung und Ausführung des Hosts verwenden:</span><span class="sxs-lookup"><span data-stu-id="86bcb-372"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="86bcb-373">Optionen</span><span class="sxs-lookup"><span data-stu-id="86bcb-373">Options</span></span>

<span data-ttu-id="86bcb-374"><xref:Microsoft.Extensions.Hosting.HostOptions>-Konfigurationsoptionen für <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="86bcb-374"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="86bcb-375">Timeout beim Herunterfahren</span><span class="sxs-lookup"><span data-stu-id="86bcb-375">Shutdown timeout</span></span>

<span data-ttu-id="86bcb-376"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> legt den Timeout für <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> fest.</span><span class="sxs-lookup"><span data-stu-id="86bcb-376"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="86bcb-377">Der Standardwert beträgt fünf Sekunden.</span><span class="sxs-lookup"><span data-stu-id="86bcb-377">The default value is five seconds.</span></span>

<span data-ttu-id="86bcb-378">Die folgende Optionskonfiguration in `Program.Main` erhöht den standardmäßigen Timeout beim Herunterfahren von fünf Sekunden auf 20 Sekunden:</span><span class="sxs-lookup"><span data-stu-id="86bcb-378">The following option configuration in `Program.Main` increases the default five second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="86bcb-379">Standarddienste</span><span class="sxs-lookup"><span data-stu-id="86bcb-379">Default services</span></span>

<span data-ttu-id="86bcb-380">Die folgenden Dienste werden bei der Hostinitialisierung registriert:</span><span class="sxs-lookup"><span data-stu-id="86bcb-380">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="86bcb-381">[Umgebung](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span><span class="sxs-lookup"><span data-stu-id="86bcb-381">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="86bcb-382">[Konfiguration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span><span class="sxs-lookup"><span data-stu-id="86bcb-382">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="86bcb-383"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ApplicationLifetime>)</span><span class="sxs-lookup"><span data-stu-id="86bcb-383"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ApplicationLifetime>)</span></span>
* <span data-ttu-id="86bcb-384"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime>)</span><span class="sxs-lookup"><span data-stu-id="86bcb-384"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime>)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="86bcb-385">[Optionen](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span><span class="sxs-lookup"><span data-stu-id="86bcb-385">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="86bcb-386">[Protokollierung](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span><span class="sxs-lookup"><span data-stu-id="86bcb-386">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="86bcb-387">Konfiguration des Hosts</span><span class="sxs-lookup"><span data-stu-id="86bcb-387">Host configuration</span></span>

<span data-ttu-id="86bcb-388">Die Konfiguration des Hosts wird durch Folgendes erstellt:</span><span class="sxs-lookup"><span data-stu-id="86bcb-388">Host configuration is created by:</span></span>

* <span data-ttu-id="86bcb-389">Aufrufen von Erweiterungsmethoden in <xref:Microsoft.Extensions.Hosting.IHostBuilder>, um das [Inhaltsstammverzeichnis](#content-root) und die [Umgebung](#environment) festzulegen</span><span class="sxs-lookup"><span data-stu-id="86bcb-389">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="86bcb-390">Lesen der Konfiguration von Konfigurationsanbietern in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*></span><span class="sxs-lookup"><span data-stu-id="86bcb-390">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="86bcb-391">Erweiterungsmethoden</span><span class="sxs-lookup"><span data-stu-id="86bcb-391">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="86bcb-392">Anwendungsschlüssel (Name)</span><span class="sxs-lookup"><span data-stu-id="86bcb-392">Application key (name)</span></span>

<span data-ttu-id="86bcb-393">Die Eigenschaft [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) wird von der Hostkonfiguration während der Hostkonstruktion festgelegt.</span><span class="sxs-lookup"><span data-stu-id="86bcb-393">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="86bcb-394">Um den Wert explizit festzulegen, verwenden Sie den [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span><span class="sxs-lookup"><span data-stu-id="86bcb-394">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="86bcb-395">**Schlüssel:** Anwendungsname</span><span class="sxs-lookup"><span data-stu-id="86bcb-395">**Key**: applicationName</span></span>  
<span data-ttu-id="86bcb-396">**Typ:** *Zeichenfolge*</span><span class="sxs-lookup"><span data-stu-id="86bcb-396">**Type**: *string*</span></span>  
<span data-ttu-id="86bcb-397">**Standardwert**: Der Name der Assembly, die den Einstiegspunkt der App enthält.</span><span class="sxs-lookup"><span data-stu-id="86bcb-397">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="86bcb-398">**Festlegen mit:** `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="86bcb-398">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="86bcb-399">**Umgebungsvariable:** `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` ist [optional und benutzerdefiniert](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="86bcb-399">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="86bcb-400">Inhaltsstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="86bcb-400">Content root</span></span>

<span data-ttu-id="86bcb-401">Diese Einstellung bestimmt, wo der Host mit der Suche nach Inhaltsdateien beginnt.</span><span class="sxs-lookup"><span data-stu-id="86bcb-401">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="86bcb-402">**Schlüssel:** contentRoot</span><span class="sxs-lookup"><span data-stu-id="86bcb-402">**Key**: contentRoot</span></span>  
<span data-ttu-id="86bcb-403">**Typ:** *Zeichenfolge*</span><span class="sxs-lookup"><span data-stu-id="86bcb-403">**Type**: *string*</span></span>  
<span data-ttu-id="86bcb-404">**Standardwert**: Entspricht standardmäßig dem Ordner, in dem die App-Assembly gespeichert ist.</span><span class="sxs-lookup"><span data-stu-id="86bcb-404">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="86bcb-405">**Festlegen mit:** `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="86bcb-405">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="86bcb-406">**Umgebungsvariable:** `<PREFIX_>CONTENTROOT` (`<PREFIX_>` ist [optional und benutzerdefiniert](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="86bcb-406">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="86bcb-407">Wenn der Pfad nicht vorhanden ist, kann der Host nicht gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="86bcb-407">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

### <a name="environment"></a><span data-ttu-id="86bcb-408">Umgebung</span><span class="sxs-lookup"><span data-stu-id="86bcb-408">Environment</span></span>

<span data-ttu-id="86bcb-409">Legt die [Umgebung](xref:fundamentals/environments) der App fest.</span><span class="sxs-lookup"><span data-stu-id="86bcb-409">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="86bcb-410">**Schlüssel:** environment</span><span class="sxs-lookup"><span data-stu-id="86bcb-410">**Key**: environment</span></span>  
<span data-ttu-id="86bcb-411">**Typ:** *Zeichenfolge*</span><span class="sxs-lookup"><span data-stu-id="86bcb-411">**Type**: *string*</span></span>  
<span data-ttu-id="86bcb-412">**Standardwert**: Produktion</span><span class="sxs-lookup"><span data-stu-id="86bcb-412">**Default**: Production</span></span>  
<span data-ttu-id="86bcb-413">**Festlegen mit:** `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="86bcb-413">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="86bcb-414">**Umgebungsvariable:** `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` ist [optional und benutzerdefiniert](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="86bcb-414">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="86bcb-415">Die Umgebung kann auf einen beliebigen Wert festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="86bcb-415">The environment can be set to any value.</span></span> <span data-ttu-id="86bcb-416">Zu den durch Frameworks definierten Werten zählen `Development`, `Staging` und `Production`.</span><span class="sxs-lookup"><span data-stu-id="86bcb-416">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="86bcb-417">Die Groß-/Kleinschreibung wird bei Werten nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="86bcb-417">Values aren't case sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="86bcb-418">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="86bcb-418">ConfigureHostConfiguration</span></span>

<span data-ttu-id="86bcb-419"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> verwendet einen <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>, um eine <xref:Microsoft.Extensions.Configuration.IConfiguration> für den Host zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="86bcb-419"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="86bcb-420">Die Hostkonfiguration dient zum Initialisieren der <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> für die Verwendung im App-Buildprozesses.</span><span class="sxs-lookup"><span data-stu-id="86bcb-420">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="86bcb-421"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> kann mehrfach mit additiven Ergebnissen aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="86bcb-421"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="86bcb-422">Der Host verwendet die Option, die zuletzt einen Wert für einen bestimmten Schlüssel festlegt.</span><span class="sxs-lookup"><span data-stu-id="86bcb-422">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="86bcb-423">Standardmäßig sind keine Anbieter enthalten.</span><span class="sxs-lookup"><span data-stu-id="86bcb-423">No providers are included by default.</span></span> <span data-ttu-id="86bcb-424">Sie müssen explizit angeben, welche Konfigurationsanbieter die App in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> erfordert, einschließlich Folgendes:</span><span class="sxs-lookup"><span data-stu-id="86bcb-424">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="86bcb-425">Dateikonfiguration (z.B. von einer Datei namens *hostsettings.json*)</span><span class="sxs-lookup"><span data-stu-id="86bcb-425">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="86bcb-426">Konfiguration von Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="86bcb-426">Environment variable configuration.</span></span>
* <span data-ttu-id="86bcb-427">Konfiguration von Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="86bcb-427">Command-line argument configuration.</span></span>
* <span data-ttu-id="86bcb-428">Alle anderen erforderlichen Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="86bcb-428">Any other required configuration providers.</span></span>

<span data-ttu-id="86bcb-429">Die Dateikonfiguration des Hosts erfolgt durch Angabe des App-Basispfads mit `SetBasePath` gefolgt von einem Aufruf eines der [Dateikonfigurationsanbieter](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="86bcb-429">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="86bcb-430">Die Beispiel-App verwendet die JSON-Datei *hostsettings.json* und ruft <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> auf, um die Hostkonfigurationseinstellungen der Datei zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="86bcb-430">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="86bcb-431">Um die [Umgebungsvariablenkonfiguration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) des Hosts hinzuzufügen, rufen Sie <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> im Host-Generator auf.</span><span class="sxs-lookup"><span data-stu-id="86bcb-431">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="86bcb-432">`AddEnvironmentVariables` akzeptiert ein optionales benutzerdefiniertes Präfix.</span><span class="sxs-lookup"><span data-stu-id="86bcb-432">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="86bcb-433">Die Beispiel-App verwendet das Präfix `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="86bcb-433">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="86bcb-434">Das Präfix wird entfernt, wenn die Umgebungsvariablen gelesen werden.</span><span class="sxs-lookup"><span data-stu-id="86bcb-434">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="86bcb-435">Wenn der Host der Beispiel-App konfiguriert wird, wird der Wert der Umgebungsvariable für `PREFIX_ENVIRONMENT` der Hostkonfigurationswert für den `environment`-Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="86bcb-435">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="86bcb-436">Wenn Sie [Visual Studio](https://visualstudio.microsoft.com) oder eine App mit `dotnet run` während der Entwicklung verwenden, können Umgebungsvariablen in der Datei *Properties/launchSettings.json* festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="86bcb-436">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="86bcb-437">In [Visual Studio Code](https://code.visualstudio.com/) können Umgebungsvariablen während der Entwicklung in der Datei *.vscode/launch.json* festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="86bcb-437">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="86bcb-438">Weitere Informationen finden Sie unter <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="86bcb-438">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="86bcb-439">Die [Befehlszeilenkonfiguration](xref:fundamentals/configuration/index#command-line-configuration-provider) wird durch Aufrufen von <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="86bcb-439">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="86bcb-440">Die Befehlszeilenkonfiguration wird zuletzt hinzugefügt, damit Befehlszeilenargumente die Konfiguration überschreiben können, die von früheren Konfigurationsanbietern bereitgestellt wurden.</span><span class="sxs-lookup"><span data-stu-id="86bcb-440">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="86bcb-441">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="86bcb-441">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="86bcb-442">Weitere Konfigurationen können durch die Schlüssel [applicationName](#application-key-name) und [contentRoot](#content-root) bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="86bcb-442">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="86bcb-443">Beispielkonfiguration für `HostBuilder` mithilfe von <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="86bcb-443">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="86bcb-444">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="86bcb-444">ConfigureAppConfiguration</span></span>

<span data-ttu-id="86bcb-445">Die App-Konfiguration wird durch Aufrufen von <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> in der <xref:Microsoft.Extensions.Hosting.IHostBuilder>-Implementierung erstellt.</span><span class="sxs-lookup"><span data-stu-id="86bcb-445">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="86bcb-446"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> verwendet einen <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>, um eine <xref:Microsoft.Extensions.Configuration.IConfiguration> für die App zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="86bcb-446"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="86bcb-447"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> kann mehrfach mit additiven Ergebnissen aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="86bcb-447"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="86bcb-448">Die App verwendet die Option, die zuletzt einen Wert für einen bestimmten Schlüssel festlegt.</span><span class="sxs-lookup"><span data-stu-id="86bcb-448">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="86bcb-449">Die von <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> erstellte Konfiguration ist unter [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) verfügbar und kann für nachfolgende Vorgänge und in <xref:Microsoft.Extensions.Hosting.IHost.Services*> verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="86bcb-449">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="86bcb-450">Auf die App-Konfiguration wird automatisch die Hostkonfiguration angewendet, die von [ConfigureHostConfiguration](#configurehostconfiguration) bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="86bcb-450">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="86bcb-451">Beispielkonfiguration für die App mithilfe von <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="86bcb-451">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="86bcb-452">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="86bcb-452">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="86bcb-453">*appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="86bcb-453">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="86bcb-454">*appsettings.Production.json*:</span><span class="sxs-lookup"><span data-stu-id="86bcb-454">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="86bcb-455">Um Einstellungsdateien in das Ausgabeverzeichnis zu verschieben, geben Sie die Einstellungsdateien als [MSBuild-Projektelemente](/visualstudio/msbuild/common-msbuild-project-items) in der Projektdatei an.</span><span class="sxs-lookup"><span data-stu-id="86bcb-455">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="86bcb-456">Die Beispiel-App verschiebt ihre JSON-App-Einstellungsdateien und *hostsettings.json* mit dem folgenden `<Content>`-Element:</span><span class="sxs-lookup"><span data-stu-id="86bcb-456">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="86bcb-457">Konfigurationserweiterungsmethoden wie <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> und <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> erfordern zusätzliche NuGet-Pakete, z.B. [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) und [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="86bcb-457">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="86bcb-458">Wenn die App nicht das [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app) verwendet, müssen diese Pakete dem Projekt zusätzlich zum [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration)-Kernpaket hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="86bcb-458">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="86bcb-459">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="86bcb-459">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="86bcb-460">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="86bcb-460">ConfigureServices</span></span>

<span data-ttu-id="86bcb-461">Mithilfe von <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> werden Dienste zum Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) der App hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="86bcb-461"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="86bcb-462"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> kann mehrfach mit additiven Ergebnissen aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="86bcb-462"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="86bcb-463">Ein gehosteter Dienst ist eine Klasse mit Logik für Hintergrundaufgaben, die die Schnittstelle <xref:Microsoft.Extensions.Hosting.IHostedService> implementiert.</span><span class="sxs-lookup"><span data-stu-id="86bcb-463">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="86bcb-464">Weitere Informationen finden Sie unter <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="86bcb-464">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="86bcb-465">Die [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) verwendet die Erweiterungsmethode `AddHostedService`, um einen Dienst für Lebensdauerereignisse (`LifetimeEventsHostedService`) und einen zeitgesteuerten Hintergrundtask (`TimedHostedService`) zur App hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="86bcb-465">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="86bcb-466">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="86bcb-466">ConfigureLogging</span></span>

<span data-ttu-id="86bcb-467"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> fügt einen Delegaten für die Konfiguration des bereitgestellten <xref:Microsoft.Extensions.Logging.ILoggingBuilder> hinzu.</span><span class="sxs-lookup"><span data-stu-id="86bcb-467"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="86bcb-468"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> kann mehrfach mit additiven Ergebnissen aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="86bcb-468"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="86bcb-469">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="86bcb-469">UseConsoleLifetime</span></span>

<span data-ttu-id="86bcb-470"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> lauscht auf STRG+C/SIGINT oder SIGTERM und ruft <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> auf, um das Herunterfahren zu beginnen.</span><span class="sxs-lookup"><span data-stu-id="86bcb-470"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for Ctrl+C/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="86bcb-471"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> hebt die Blockierung von Erweiterungen wie [RunAsync](#runasync) und [WaitForShutdownAsync](#waitforshutdownasync) auf.</span><span class="sxs-lookup"><span data-stu-id="86bcb-471"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="86bcb-472"><xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> ist vorab als Standardimplementierung für die Lebensdauer registriert.</span><span class="sxs-lookup"><span data-stu-id="86bcb-472"><xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="86bcb-473">Die letzte registrierte Lebensdauer wird verwendet.</span><span class="sxs-lookup"><span data-stu-id="86bcb-473">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="86bcb-474">Containerkonfiguration</span><span class="sxs-lookup"><span data-stu-id="86bcb-474">Container configuration</span></span>

<span data-ttu-id="86bcb-475">Der Host kann eine <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>-Schnittstelle verwenden, um die Integration in andere Container zu unterstützten.</span><span class="sxs-lookup"><span data-stu-id="86bcb-475">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="86bcb-476">Das Bereitstellen einer Factory ist kein Teil der DI-Containerregistrierung, sondern ein Host, der systemintern verwendet wird, um den entsprechenden DI-Container zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="86bcb-476">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="86bcb-477">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) überschreibt die Standardfactory, die zum Erstellen des Dienstanbieters der App verwendet wurde.</span><span class="sxs-lookup"><span data-stu-id="86bcb-477">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="86bcb-478">Die benutzerdefinierte Containerkonfiguration wird von der Methode <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> verwaltet.</span><span class="sxs-lookup"><span data-stu-id="86bcb-478">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="86bcb-479"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> stellt eine stark typisierte Möglichkeit für das Konfigurieren des Containers auf Basis der zugrunde liegenden Host-API bereit.</span><span class="sxs-lookup"><span data-stu-id="86bcb-479"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="86bcb-480"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> kann mehrfach mit additiven Ergebnissen aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="86bcb-480"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="86bcb-481">Erstellen eines Dienstcontainers für die App:</span><span class="sxs-lookup"><span data-stu-id="86bcb-481">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="86bcb-482">Bereitstellen einer Factory für den Dienstcontainer:</span><span class="sxs-lookup"><span data-stu-id="86bcb-482">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="86bcb-483">Verwenden Sie die Factory, und konfigurieren Sie den benutzerdefinierten Dienstcontainer für die App:</span><span class="sxs-lookup"><span data-stu-id="86bcb-483">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="86bcb-484">Erweiterungen</span><span class="sxs-lookup"><span data-stu-id="86bcb-484">Extensibility</span></span>

<span data-ttu-id="86bcb-485">Die Erweiterung des Hosts wird mit der Erweiterungsmethode in <xref:Microsoft.Extensions.Hosting.IHostBuilder> durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="86bcb-485">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="86bcb-486">Im folgenden Beispiel wird dargestellt, wie eine Erweiterungsmethode eine <xref:Microsoft.Extensions.Hosting.IHostBuilder>-Implementierung mit dem [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks)-Beispiel erweitert, das in <xref:fundamentals/host/hosted-services> gezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="86bcb-486">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="86bcb-487">Eine App richtet die `UseHostedService`-Erweiterungsmethode zum Registrieren des in `T` übergebenen gehosteten Diensts ein:</span><span class="sxs-lookup"><span data-stu-id="86bcb-487">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="86bcb-488">Verwalten des Hosts</span><span class="sxs-lookup"><span data-stu-id="86bcb-488">Manage the host</span></span>

<span data-ttu-id="86bcb-489">Die Implementierung von <xref:Microsoft.Extensions.Hosting.IHost> ist für das Starten und Beenden der Implementierungen von <xref:Microsoft.Extensions.Hosting.IHostedService> verantwortlich, die im Dienstcontainer registriert sind.</span><span class="sxs-lookup"><span data-stu-id="86bcb-489">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="86bcb-490">Run</span><span class="sxs-lookup"><span data-stu-id="86bcb-490">Run</span></span>

<span data-ttu-id="86bcb-491">Durch <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> wird die App gestartet und der aufrufende Thread blockiert, bis der Host heruntergefahren wird:</span><span class="sxs-lookup"><span data-stu-id="86bcb-491"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="86bcb-492">RunAsync</span><span class="sxs-lookup"><span data-stu-id="86bcb-492">RunAsync</span></span>

<span data-ttu-id="86bcb-493">Durch <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> wird die App ausgeführt und eine <xref:System.Threading.Tasks.Task>-Methode ausgegeben, die abgeschlossen wird, wenn das Abbruchtoken oder das Herunterfahren ausgelöst wird:</span><span class="sxs-lookup"><span data-stu-id="86bcb-493"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="86bcb-494">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="86bcb-494">RunConsoleAsync</span></span>

<span data-ttu-id="86bcb-495"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> aktiviert die Unterstützung der Konsole, erstellt und startet den Host und lauscht auf STRG+C/SIGINT oder SIGTERM, um das Herunterfahren auszulösen.</span><span class="sxs-lookup"><span data-stu-id="86bcb-495"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for Ctrl+C/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="86bcb-496">Start und StopAsync</span><span class="sxs-lookup"><span data-stu-id="86bcb-496">Start and StopAsync</span></span>

<span data-ttu-id="86bcb-497"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> startet den Host synchron.</span><span class="sxs-lookup"><span data-stu-id="86bcb-497"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="86bcb-498"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> versucht, den Host innerhalb des angegebenen Timeouts zu beenden.</span><span class="sxs-lookup"><span data-stu-id="86bcb-498"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="86bcb-499">StartAsync und StopAsync</span><span class="sxs-lookup"><span data-stu-id="86bcb-499">StartAsync and StopAsync</span></span>

<span data-ttu-id="86bcb-500"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> startet die App.</span><span class="sxs-lookup"><span data-stu-id="86bcb-500"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="86bcb-501"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> hält die App an.</span><span class="sxs-lookup"><span data-stu-id="86bcb-501"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="86bcb-502">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="86bcb-502">WaitForShutdown</span></span>

<span data-ttu-id="86bcb-503"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> wird über die <xref:Microsoft.Extensions.Hosting.IHostLifetime> ausgelöst, z. B. <xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> (lauscht auf STRG+C/SIGINT oder SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="86bcb-503"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as <xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> (listens for Ctrl+C/SIGINT or SIGTERM).</span></span> <span data-ttu-id="86bcb-504"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> ruft <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> auf.</span><span class="sxs-lookup"><span data-stu-id="86bcb-504"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="86bcb-505">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="86bcb-505">WaitForShutdownAsync</span></span>

<span data-ttu-id="86bcb-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> gibt eine <xref:System.Threading.Tasks.Task>-Methode zurück, die abgeschlossen wird, wenn das Herunterfahren über das angegebene Token ausgelöst wird, und ruft <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> auf.</span><span class="sxs-lookup"><span data-stu-id="86bcb-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="86bcb-507">Externe Steuerung</span><span class="sxs-lookup"><span data-stu-id="86bcb-507">External control</span></span>

<span data-ttu-id="86bcb-508">Die externe Steuerung des Hosts kann mithilfe von Methoden durchgeführt werden, die extern aufgerufen werden können:</span><span class="sxs-lookup"><span data-stu-id="86bcb-508">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="86bcb-509"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> wird am Anfang der <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>-Methode aufgerufen, die den Vorgang erst fortsetzt, wenn sie abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="86bcb-509"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="86bcb-510">Dies kann verwendet werden, um den Start zu verzögern, bis dieser durch ein externes Ereignis initiiert wird.</span><span class="sxs-lookup"><span data-stu-id="86bcb-510">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="86bcb-511">IHostingEnvironment-Schnittstelle</span><span class="sxs-lookup"><span data-stu-id="86bcb-511">IHostingEnvironment interface</span></span>

<span data-ttu-id="86bcb-512"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> stellt Informationen über die Hostingumgebung der App bereit.</span><span class="sxs-lookup"><span data-stu-id="86bcb-512"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="86bcb-513">Verwenden Sie [Constructor Injection](xref:fundamentals/dependency-injection) zum Abrufen der <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>-Schnittstelle, um deren Eigenschaften und Erweiterungsmethoden zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="86bcb-513">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="86bcb-514">Weitere Informationen finden Sie unter <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="86bcb-514">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="86bcb-515">IApplicationLifetime-Schnittstelle</span><span class="sxs-lookup"><span data-stu-id="86bcb-515">IApplicationLifetime interface</span></span>

<span data-ttu-id="86bcb-516"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> ermöglicht Aktivitäten nach dem Starten und beim Herunterfahren (einschließlich Anforderungen für ordnungsgemäßes Herunterfahren).</span><span class="sxs-lookup"><span data-stu-id="86bcb-516"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="86bcb-517">Bei drei der Eigenschaften der Schnittstelle handelt es sich um Abbruchtokens, die zum Registrieren von <xref:System.Action>-Methoden verwendet werden, durch die Ereignisse beim Starten und Herunterfahren definiert werden.</span><span class="sxs-lookup"><span data-stu-id="86bcb-517">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="86bcb-518">Abbruchtoken</span><span class="sxs-lookup"><span data-stu-id="86bcb-518">Cancellation Token</span></span> | <span data-ttu-id="86bcb-519">Wird ausgelöst, wenn&#8230;</span><span class="sxs-lookup"><span data-stu-id="86bcb-519">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="86bcb-520">Der Host vollständig gestartet wurde.</span><span class="sxs-lookup"><span data-stu-id="86bcb-520">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="86bcb-521">Der Host ein ordnungsgemäßes Herunterfahren abschließt.</span><span class="sxs-lookup"><span data-stu-id="86bcb-521">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="86bcb-522">Alle Anforderungen sollten verarbeitet worden sein.</span><span class="sxs-lookup"><span data-stu-id="86bcb-522">All requests should be processed.</span></span> <span data-ttu-id="86bcb-523">Das Herunterfahren wird blockiert, bis das Ereignis abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="86bcb-523">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="86bcb-524">Der Host ein ordnungsgemäßes Herunterfahren ausführt.</span><span class="sxs-lookup"><span data-stu-id="86bcb-524">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="86bcb-525">Anforderungen möglicherweise noch verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="86bcb-525">Requests may still be processing.</span></span> <span data-ttu-id="86bcb-526">Das Herunterfahren wird blockiert, bis das Ereignis abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="86bcb-526">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="86bcb-527">Fügen Sie den <xref:Microsoft.Extensions.Hosting.IApplicationLifetime>-Dienst über einen Konstruktor in eine beliebige Klasse ein.</span><span class="sxs-lookup"><span data-stu-id="86bcb-527">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="86bcb-528">Die [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) verwendet die Konstruktorinjektion für eine `LifetimeEventsHostedService`-Klasse (eine <xref:Microsoft.Extensions.Hosting.IHostedService>-Implementierung), um die Ereignisse zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="86bcb-528">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="86bcb-529">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="86bcb-529">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="86bcb-530"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> fordert das Beenden der App an.</span><span class="sxs-lookup"><span data-stu-id="86bcb-530"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="86bcb-531">Die folgende Klasse verwendet <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*>, um eine App ordnungsgemäß herunterzufahren, wenn die `Shutdown`-Methode der Klasse aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="86bcb-531">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="86bcb-532">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="86bcb-532">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>

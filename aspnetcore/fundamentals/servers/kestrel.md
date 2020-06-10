---
title: 'title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:'
author: rick-anderson
description: "'Blazor'"
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/servers/kestrel
ms.openlocfilehash: 4da32b14ca640bda4df5d6c9d685f4702c6eb266
ms.sourcegitcommit: 58722eb309767e462bdbf3082bd38737a4ef168f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84106766"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="ebec1-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebec1-103">'Identity'</span></span>

<span data-ttu-id="ebec1-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebec1-104">'Let's Encrypt'</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ebec1-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-105">'Razor'</span></span> <span data-ttu-id="ebec1-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebec1-106">'SignalR' uid:</span></span>

<span data-ttu-id="ebec1-107">Implementierung des Webservers Kestrel in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ebec1-107">Kestrel web server implementation in ASP.NET Core</span></span>

* <span data-ttu-id="ebec1-108">Von [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher) und [Stephen Halter](https://twitter.com/halter73)</span><span class="sxs-lookup"><span data-stu-id="ebec1-108">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Stephen Halter](https://twitter.com/halter73)</span></span>
* <span data-ttu-id="ebec1-109">Einführung in Kestrel, dem plattformübergreifenden [Webserver für ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="ebec1-109">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span>
* <span data-ttu-id="ebec1-110">Kestrel ist der Webserver, der standardmäßig in ASP.NET Core-Projektvorlagen enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="ebec1-110">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>
* <span data-ttu-id="ebec1-111">Kestrel unterstützt die folgenden Szenarios:</span><span class="sxs-lookup"><span data-stu-id="ebec1-111">Kestrel supports the following scenarios:</span></span>

<span data-ttu-id="ebec1-112">HTTPS</span><span class="sxs-lookup"><span data-stu-id="ebec1-112">HTTPS</span></span>

<span data-ttu-id="ebec1-113">Nicht transparente Upgrades, die zum Aktivieren von [WebSockets](https://github.com/aspnet/websockets) verwendet werden</span><span class="sxs-lookup"><span data-stu-id="ebec1-113">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>

<span data-ttu-id="ebec1-114">Unix-Sockets für eine hohe Leistung im Hintergrund von Nginx</span><span class="sxs-lookup"><span data-stu-id="ebec1-114">Unix sockets for high performance behind Nginx</span></span>

## <a name="http2-support"></a><span data-ttu-id="ebec1-115">HTTP/2 (außer unter macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="ebec1-115">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="ebec1-116">&dagger;HTTP/2 wird unter macOS in einem zukünftigen Release unterstützt.</span><span class="sxs-lookup"><span data-stu-id="ebec1-116">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

* <span data-ttu-id="ebec1-117">Kestrel wird auf allen Plattformen und für alle Versionen unterstützt, die .NET Core unterstützt.</span><span class="sxs-lookup"><span data-stu-id="ebec1-117">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>
  * <span data-ttu-id="ebec1-118">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ebec1-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>
  * <span data-ttu-id="ebec1-119">HTTP/2-Unterstützung</span><span class="sxs-lookup"><span data-stu-id="ebec1-119">HTTP/2 support</span></span>
* <span data-ttu-id="ebec1-120">[HTTP/2](https://httpwg.org/specs/rfc7540.html) ist für ASP.NET Core-Apps verfügbar, wenn die folgenden Basisanforderungen erfüllt sind:</span><span class="sxs-lookup"><span data-stu-id="ebec1-120">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>
* <span data-ttu-id="ebec1-121">Betriebssystem&dagger;</span><span class="sxs-lookup"><span data-stu-id="ebec1-121">Operating system&dagger;</span></span>
* <span data-ttu-id="ebec1-122">Windows Server 2016/Windows 10 oder höher&Dagger;</span><span class="sxs-lookup"><span data-stu-id="ebec1-122">Windows Server 2016/Windows 10 or later&Dagger;</span></span>

<span data-ttu-id="ebec1-123">Linux mit OpenSSL 1.0.2 oder höher (z.B. Ubuntu 16.04 oder höher)</span><span class="sxs-lookup"><span data-stu-id="ebec1-123">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
<span data-ttu-id="ebec1-124">Zielframework: .NET Core 2.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="ebec1-124">Target framework: .NET Core 2.2 or later</span></span> <span data-ttu-id="ebec1-125">[ALPN](https://tools.ietf.org/html/rfc7301#section-3)-Verbindung (Application-Layer Protocol Negotiation)</span><span class="sxs-lookup"><span data-stu-id="ebec1-125">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span> <span data-ttu-id="ebec1-126">TLS 1.2-Verbindung oder höher</span><span class="sxs-lookup"><span data-stu-id="ebec1-126">TLS 1.2 or later connection</span></span>

<span data-ttu-id="ebec1-127">&dagger;HTTP/2 wird unter macOS in einem zukünftigen Release unterstützt.</span><span class="sxs-lookup"><span data-stu-id="ebec1-127">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="ebec1-128">&Dagger;Kestrel bietet eingeschränkte Unterstützung für HTTP/2 unter Windows Server 2012 R2 und Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="ebec1-128">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="ebec1-129">Die Unterstützung ist eingeschränkt, weil die Liste der unterstützten TLS-Verschlüsselungssammlungen unter diesen Betriebssystemen begrenzt ist.</span><span class="sxs-lookup"><span data-stu-id="ebec1-129">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="ebec1-130">Zum Sichern von TLS-Verbindungen ist möglicherweise ein durch einen Elliptic Curve Digital Signature Algorithm (ECDSA) generiertes Zertifikat erforderlich.</span><span class="sxs-lookup"><span data-stu-id="ebec1-130">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="ebec1-131">Wenn eine HTTP/2-Verbindung hergestellt wurde, meldet [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)`HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="ebec1-131">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="ebec1-132">HTTP/2 ist standardmäßig deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="ebec1-132">HTTP/2 is disabled by default.</span></span>

<span data-ttu-id="ebec1-133">Weitere Informationen zur Konfiguration finden Sie in den Abschnitten [Kestrel-Optionen](#kestrel-options) und [ListenOptions.Protocols](#listenoptionsprotocols).</span><span class="sxs-lookup"><span data-stu-id="ebec1-133">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

![Verwenden von Kestrel mit einem Reverseproxy](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="ebec1-135">Kestrel kann eigenständig oder mit einem *Reverseproxyserver* wie z.B. [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="ebec1-135">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span>

![Ein Reverseproxyserver empfängt HTTP-Anforderungen aus dem Netzwerk und leitet diese an Kestrel weiter.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="ebec1-137">Kestrel bei Verwendung als Webserver mit direkter Internetverbindung:</span><span class="sxs-lookup"><span data-stu-id="ebec1-137">Kestrel used as an edge (Internet-facing) web server:</span></span>

<span data-ttu-id="ebec1-138">Kestrel kommuniziert direkt und ohne Reverseproxyserver mit dem Internet</span><span class="sxs-lookup"><span data-stu-id="ebec1-138">Kestrel communicates directly with the Internet without a reverse proxy server</span></span> <span data-ttu-id="ebec1-139">Kestrel bei Verwendung in einer Reverseproxykonfiguration:</span><span class="sxs-lookup"><span data-stu-id="ebec1-139">Kestrel used in a reverse proxy configuration:</span></span> <span data-ttu-id="ebec1-140">Kestrel kommuniziert indirekt mit dem Internet über einen Reverseproxyserver wie IIS, Nginx oder Apache</span><span class="sxs-lookup"><span data-stu-id="ebec1-140">Kestrel communicates indirectly with the Internet through a reverse proxy server, such as IIS, Nginx, or Apache</span></span>

<span data-ttu-id="ebec1-141">Jede der beiden Konfigurationen – mit oder ohne einen Reverseproxyserver – stellt eine unterstützte Hostingkonfiguration dar.</span><span class="sxs-lookup"><span data-stu-id="ebec1-141">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="ebec1-142">Bei Verwendung als Edgeserver ohne Reverseproxyserver unterstützt Kestrel die gemeinsame Nutzung der gleichen IP-Adresse und des gleichen Ports durch mehrere Prozesse nicht.</span><span class="sxs-lookup"><span data-stu-id="ebec1-142">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span>

* <span data-ttu-id="ebec1-143">Wenn Kestrel für das Lauschen an einem Port konfiguriert ist, verarbeitet Kestrel den gesamten Datenverkehr für diesen Port unabhängig von den `Host`-Headern der Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="ebec1-143">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span>
* <span data-ttu-id="ebec1-144">Ein Reverseproxy, der Ports freigeben kann, kann Anforderungen an Kestrel über eine eindeutige IP und einen eindeutigen Port weiterleiten.</span><span class="sxs-lookup"><span data-stu-id="ebec1-144">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>
* <span data-ttu-id="ebec1-145">Auch wenn kein Reverseproxyserver erforderlich ist, kann die Verwendung eines solchen empfehlenswert sein.</span><span class="sxs-lookup"><span data-stu-id="ebec1-145">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>
* <span data-ttu-id="ebec1-146">Für einen Reverseproxy gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="ebec1-146">A reverse proxy:</span></span> <span data-ttu-id="ebec1-147">Er kann die verfügbar gemachten öffentlichen Oberflächen der von ihm gehosteten Apps einschränken.</span><span class="sxs-lookup"><span data-stu-id="ebec1-147">Can limit the exposed public surface area of the apps that it hosts.</span></span>

> [!WARNING]
> <span data-ttu-id="ebec1-148">Er stellt eine zusätzliche Ebene für Konfiguration und Schutz bereit.</span><span class="sxs-lookup"><span data-stu-id="ebec1-148">Provide an additional layer of configuration and defense.</span></span>

## <a name="kestrel-in-aspnet-core-apps"></a><span data-ttu-id="ebec1-149">Er lässt sich besser in die vorhandene Infrastruktur integrieren.</span><span class="sxs-lookup"><span data-stu-id="ebec1-149">Might integrate better with existing infrastructure.</span></span>

<span data-ttu-id="ebec1-150">Er vereinfacht die Konfiguration von Lastenausgleich und sicherer Kommunikation (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="ebec1-150">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="ebec1-151">Nur der Reverseproxyserver erfordert ein X.509-Zertifikat, und dieser Server kann mit den Servern der App im internen Netzwerk über einfaches HTTP kommunizieren.</span><span class="sxs-lookup"><span data-stu-id="ebec1-151">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="ebec1-152">Das Hosting in einer Reverseproxykonfiguration erfordert [Hostfilterung](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="ebec1-152">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

<span data-ttu-id="ebec1-153">Kestrel in ASP.NET Core-Apps</span><span class="sxs-lookup"><span data-stu-id="ebec1-153">Kestrel in ASP.NET Core apps</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(serverOptions =>
            {
                // Set properties and call methods on options
            })
            .UseStartup<Startup>();
        });
```

## <a name="kestrel-options"></a><span data-ttu-id="ebec1-154">ASP.NET Core-Projektvorlagen verwenden Kestrel standardmäßig.</span><span class="sxs-lookup"><span data-stu-id="ebec1-154">ASP.NET Core project templates use Kestrel by default.</span></span>

<span data-ttu-id="ebec1-155">In *Program.cs* ruft die <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*>-Methode <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>auf:</span><span class="sxs-lookup"><span data-stu-id="ebec1-155">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

<span data-ttu-id="ebec1-156">Weitere Informationen zum Erstellen des Hosts finden Sie im Artikel <xref:fundamentals/host/generic-host#set-up-a-host> in den Abschnitten *Einrichten eines Hosts* und *Standardeinstellungen für den Generator*.</span><span class="sxs-lookup"><span data-stu-id="ebec1-156">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span> <span data-ttu-id="ebec1-157">Um zusätzliche Konfiguration nach dem Aufruf von `ConfigureWebHostDefaults` bereitzustellen, verwenden Sie `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="ebec1-157">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

<span data-ttu-id="ebec1-158">Kestrel-Optionen</span><span class="sxs-lookup"><span data-stu-id="ebec1-158">Kestrel options</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="ebec1-159">Der Kestrel-Webserver verfügt über einschränkende Konfigurationsoptionen, die besonders nützlich bei Bereitstellungen mit Internetzugriff sind.</span><span class="sxs-lookup"><span data-stu-id="ebec1-159">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span> <span data-ttu-id="ebec1-160">Legen Sie Einschränkungen für die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits>-Eigenschaft der <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>-Klasse fest.</span><span class="sxs-lookup"><span data-stu-id="ebec1-160">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="ebec1-161">Die `Limits`-Eigenschaft enthält eine Instanz der <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>-Klasse.</span><span class="sxs-lookup"><span data-stu-id="ebec1-161">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    },
    "DisableStringReuse": true
  }
}
```

> [!NOTE]
> <span data-ttu-id="ebec1-162">In den folgenden Beispielen wird der <xref:Microsoft.AspNetCore.Server.Kestrel.Core>-Namespace verwendet:</span><span class="sxs-lookup"><span data-stu-id="ebec1-162">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span> <span data-ttu-id="ebec1-163">In den Beispielen, die weiter unten in diesem Artikel aufgeführt sind, werden Kestrel-Optionen in C#-Code konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="ebec1-163">In examples shown later in this article, Kestrel options are configured in C# code.</span></span>

<span data-ttu-id="ebec1-164">Kestrel-Optionen können ebenso mithilfe eines [Konfigurationsanbieters](xref:fundamentals/configuration/index) festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="ebec1-164">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="ebec1-165">Beispielsweise kann der [Dateikonfigurationsanbieter](xref:fundamentals/configuration/index#file-configuration-provider) die Kestrel-Konfiguration aus einer *appsettings.json*- oder *appsettings.{Umgebung}.json*-Datei laden:</span><span class="sxs-lookup"><span data-stu-id="ebec1-165">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

  1. <span data-ttu-id="ebec1-166"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> und die [Endpunktkonfiguration](#endpoint-configuration) können über Konfigurationsanbieter konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="ebec1-166"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](#endpoint-configuration) are configurable from configuration providers.</span></span> <span data-ttu-id="ebec1-167">Die verbleibende Kestrel-Konfiguration muss in C#-Code konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="ebec1-167">Remaining Kestrel configuration must be configured in C# code.</span></span>
  2. <span data-ttu-id="ebec1-168">Verwenden Sie **einen** der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="ebec1-168">Use **one** of the following approaches:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="ebec1-169">Konfigurieren Sie Kestrel in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ebec1-169">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  <span data-ttu-id="ebec1-170">Fügen Sie eine Instanz von `IConfiguration` in die `Startup`-Klasse ein.</span><span class="sxs-lookup"><span data-stu-id="ebec1-170">Inject an instance of `IConfiguration` into the `Startup` class.</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IHostBuilder CreateHostBuilder(string[] args) =>
      Host.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .ConfigureWebHostDefaults(webBuilder =>
          {
              webBuilder.UseStartup<Startup>();
          });
  ```

<span data-ttu-id="ebec1-171">Im folgenden Beispiel wird davon ausgegangen, dass die eingefügte Konfiguration der `Configuration`-Eigenschaft zugewiesen wird.</span><span class="sxs-lookup"><span data-stu-id="ebec1-171">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="ebec1-172">Laden Sie in `Startup.ConfigureServices` den Abschnitt `Kestrel` der Konfiguration in die Konfiguration von Kestrel:</span><span class="sxs-lookup"><span data-stu-id="ebec1-172">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="ebec1-173">Konfigurieren Sie Kestrel beim Erstellen des Hosts:</span><span class="sxs-lookup"><span data-stu-id="ebec1-173">Configure Kestrel when building the host:</span></span> <span data-ttu-id="ebec1-174">Laden Sie in *Program.cs* den Abschnitt `Kestrel` der Konfiguration in die Konfiguration von Kestrel:</span><span class="sxs-lookup"><span data-stu-id="ebec1-174">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="ebec1-175">Beide vorangehenden Ansätze funktionieren mit jedem [Konfigurationsanbieter](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="ebec1-175">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="ebec1-176">Keep-Alive-Timeout</span><span class="sxs-lookup"><span data-stu-id="ebec1-176">Keep-alive timeout</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="ebec1-177">Ruft das [Keep-Alive-Timeout](https://tools.ietf.org/html/rfc7230#section-6.5) ab oder legt es fest.</span><span class="sxs-lookup"><span data-stu-id="ebec1-177">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="ebec1-178">Standardwert: 2 Minuten.</span><span class="sxs-lookup"><span data-stu-id="ebec1-178">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="ebec1-179">Maximale Anzahl der Clientverbindungen</span><span class="sxs-lookup"><span data-stu-id="ebec1-179">Maximum client connections</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="ebec1-180">Die maximale Anzahl von gleichzeitig geöffneten TCP-Verbindungen kann mithilfe von folgendem Code für die gesamte App festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="ebec1-180">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="ebec1-181">Es gibt einen separaten Grenzwert für Verbindungen, die von HTTP oder HTTPS auf ein anderes Protokoll aktualisiert wurden (z.B. auf eine WebSockets-Anforderung).</span><span class="sxs-lookup"><span data-stu-id="ebec1-181">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span>

<span data-ttu-id="ebec1-182">Nachdem eine Verbindung aktualisiert wurde, zählt diese nicht mehr für den `MaxConcurrentConnections`-Grenzwert.</span><span class="sxs-lookup"><span data-stu-id="ebec1-182">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="ebec1-183">Die maximale Anzahl von Verbindungen ist standardmäßig nicht begrenzt (NULL).</span><span class="sxs-lookup"><span data-stu-id="ebec1-183">The maximum number of connections is unlimited (null) by default.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="ebec1-184">Maximale Größe des Anforderungstexts</span><span class="sxs-lookup"><span data-stu-id="ebec1-184">Maximum request body size</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="ebec1-185">Die maximale Größe des Anforderungstexts beträgt standardmäßig 30.000.000 Byte, also ungefähr 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="ebec1-185">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="ebec1-186">Die empfohlene Methode zur Außerkraftsetzung des Grenzwerts in einer ASP.NET Core-MVC-App besteht im Verwenden des <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>-Attributs in einer Aktionsmethode:</span><span class="sxs-lookup"><span data-stu-id="ebec1-186">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

<span data-ttu-id="ebec1-187">Im folgenden Beispiel wird veranschaulicht, wie die Einschränkungen auf jeder Anforderung für die App konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="ebec1-187">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="ebec1-188">Außer Kraft setzen der Einstellung für eine bestimmte Anforderung in Middleware:</span><span class="sxs-lookup"><span data-stu-id="ebec1-188">Override the setting on a specific request in middleware:</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="ebec1-189">Eine Ausnahme wird ausgelöst, wenn die App den Grenzwert einer Anforderung konfiguriert, nachdem die App bereits mit dem Lesen der Anforderung begonnen hat.</span><span class="sxs-lookup"><span data-stu-id="ebec1-189">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="ebec1-190">Es gibt eine `IsReadOnly`-Eigenschaft, die angibt, wenn sich die `MaxRequestBodySize`-Eigenschaft im schreibgeschützten Zustand befindet, also wenn der Grenzwert nicht mehr konfiguriert werden kann.</span><span class="sxs-lookup"><span data-stu-id="ebec1-190">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span> <span data-ttu-id="ebec1-191">Wenn eine App [prozessextern](xref:host-and-deploy/iis/index#out-of-process-hosting-model) hinter dem [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) ausgeführt wird, ist das Größenlimit von Kestrel für Anforderungstext deaktiviert, weil IIS dieses Limit bereits festlegt.</span><span class="sxs-lookup"><span data-stu-id="ebec1-191">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

<span data-ttu-id="ebec1-192">Minimale Datenrate des Anforderungstexts</span><span class="sxs-lookup"><span data-stu-id="ebec1-192">Minimum request body data rate</span></span>

<span data-ttu-id="ebec1-193">Kestrel überprüft sekündlich, ob Daten mit der angegebenen Rate in Bytes/Sekunde eingehen.</span><span class="sxs-lookup"><span data-stu-id="ebec1-193">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="ebec1-194">Wenn die Rate den mindestens erforderlichen Wert unterschreitet, wird für die Verbindung wegen Timeout getrennt. Bei der Toleranzperiode handelt es sich um die Zeitspanne, die Kestrel dem Client gewährt, um die Senderate auf den mindestens erforderlichen Wert zu erhöhen. Die Rate wird währenddessen nicht überprüft.</span><span class="sxs-lookup"><span data-stu-id="ebec1-194">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span>

<span data-ttu-id="ebec1-195">Diese Toleranzperiode beugt dem Trennen von Verbindungen vor, die Daten aufgrund eines langsamen TCP-Starts anfänglich mit einer niedrigen Rate senden.</span><span class="sxs-lookup"><span data-stu-id="ebec1-195">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="ebec1-196">Die mindestens erforderliche Rate beträgt standardmäßig 240 Bytes/Sekunde mit einer Toleranzperiode von 5 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="ebec1-196">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="ebec1-197">Für die Antwort gilt ebenfalls eine mindestens erforderliche Rate.</span><span class="sxs-lookup"><span data-stu-id="ebec1-197">A minimum rate also applies to the response.</span></span> <span data-ttu-id="ebec1-198">Der Code zum Festlegen des Grenzwerts für Anforderung und Antwort ist abgesehen von `RequestBody` oder `Response` in den Namen von Eigenschaften und Schnittstelle identisch.</span><span class="sxs-lookup"><span data-stu-id="ebec1-198">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span> <span data-ttu-id="ebec1-199">In diesem Beispiel wird veranschaulicht, wie Sie die mindestens erforderlichen Datenraten in *Program.cs* konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="ebec1-199">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

<span data-ttu-id="ebec1-200">Außer Kraft setzen des minimalen Ratenlimits pro Anforderung in Middleware:</span><span class="sxs-lookup"><span data-stu-id="ebec1-200">Override the minimum rate limits per request in middleware:</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="ebec1-201">Das <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> aus dem vorherigen Beispiel ist in `HttpContext.Features` für HTTP/2-Anforderungen nicht vorhanden, weil die Änderung von Ratenlimits für jede Anforderung aufgrund der Unterstützung für Anforderungsmultiplexing von HTTP/2 von diesem Protokoll unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="ebec1-201">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="ebec1-202"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> ist jedoch in `HttpContext.Features` für HTTP/2-Anforderungen noch vorhanden, weil das Leseratenlimit weiterhin für jede Anforderung *vollständig deaktiviert* werden kann, indem `IHttpMinRequestBodyDataRateFeature.MinDataRate` auch für eine HTTP/2-Anforderung auf `null` festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="ebec1-202">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="ebec1-203">Der Versuch, `IHttpMinRequestBodyDataRateFeature.MinDataRate` zu lesen oder auf einen anderen Wert als `null` festzulegen, führt dazu, dass bei einer HTTP/2-Anforderung eine `NotSupportedException` ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="ebec1-203">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="ebec1-204">Serverweite Ratenlimits, die über `KestrelServerOptions.Limits` konfiguriert sind, gelten auch für HTTP/1.x- und HTTP/2-Verbindungen.</span><span class="sxs-lookup"><span data-stu-id="ebec1-204">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

<span data-ttu-id="ebec1-205">Timeout für Anforderungsheader</span><span class="sxs-lookup"><span data-stu-id="ebec1-205">Request headers timeout</span></span> <span data-ttu-id="ebec1-206">Ruft die maximale Zeitspanne ab, während der der Server Anforderungsheader empfängt, oder legt diese fest.</span><span class="sxs-lookup"><span data-stu-id="ebec1-206">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="ebec1-207">Der Standardwert beträgt 30 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="ebec1-207">Defaults to 30 seconds.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="ebec1-208">Maximale Datenströme pro Verbindung</span><span class="sxs-lookup"><span data-stu-id="ebec1-208">Maximum streams per connection</span></span>

<span data-ttu-id="ebec1-209">`Http2.MaxStreamsPerConnection` schränkt die Anzahl gleichzeitiger Anforderungsdatenströme pro HTTP/2-Verbindung ein.</span><span class="sxs-lookup"><span data-stu-id="ebec1-209">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="ebec1-210">Überschüssige Datenströme werden zurückgewiesen.</span><span class="sxs-lookup"><span data-stu-id="ebec1-210">Excess streams are refused.</span></span> <span data-ttu-id="ebec1-211">Der Standardwert ist 100.</span><span class="sxs-lookup"><span data-stu-id="ebec1-211">The default value is 100.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="ebec1-212">Größe der Headertabelle</span><span class="sxs-lookup"><span data-stu-id="ebec1-212">Header table size</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="ebec1-213">Der HPACK-Decoder dekomprimiert HTTP-Header für HTTP/2-Verbindungen.</span><span class="sxs-lookup"><span data-stu-id="ebec1-213">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span>

<span data-ttu-id="ebec1-214">`Http2.HeaderTableSize` schränkt die Größe der Headerkomprimierungstabelle ein, die der HPACK-Decoder verwendet.</span><span class="sxs-lookup"><span data-stu-id="ebec1-214">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="ebec1-215">Der Wert wird in Oktetten bereitgestellt und muss größer als null (0) sein.</span><span class="sxs-lookup"><span data-stu-id="ebec1-215">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="ebec1-216">Der Standardwert ist 4096.</span><span class="sxs-lookup"><span data-stu-id="ebec1-216">The default value is 4096.</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="ebec1-217">Maximale Framegröße</span><span class="sxs-lookup"><span data-stu-id="ebec1-217">Maximum frame size</span></span>

<span data-ttu-id="ebec1-218">`Http2.MaxFrameSize` gibt die maximal zulässige Größe einer vom Server empfangenen oder gesendeten HTTP/2-Verbindungsrahmen-Nutzlast an.</span><span class="sxs-lookup"><span data-stu-id="ebec1-218">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="ebec1-219">Der Wert wird in Oktetten bereitgestellt und muss zwischen 2^14 (16.384) und 2^24-1 (16.777.215) liegen.</span><span class="sxs-lookup"><span data-stu-id="ebec1-219">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span> <span data-ttu-id="ebec1-220">Der Standardwert ist 2^14 (16.384).</span><span class="sxs-lookup"><span data-stu-id="ebec1-220">The default value is 2^14 (16,384).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="ebec1-221">Maximale Größe des Anforderungsheaders</span><span class="sxs-lookup"><span data-stu-id="ebec1-221">Maximum request header size</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="ebec1-222">`Http2.MaxRequestHeaderFieldSize` gibt die maximal zulässige Größe in Oktetten der Anforderungsheaderwerte an.</span><span class="sxs-lookup"><span data-stu-id="ebec1-222">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span>

<span data-ttu-id="ebec1-223">Dieser Grenzwert gilt sowohl für den Namen als auch den Wert in der komprimierten und nicht komprimierten Darstellung.</span><span class="sxs-lookup"><span data-stu-id="ebec1-223">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="ebec1-224">Der Wert muss größer als 0 (null) sein.</span><span class="sxs-lookup"><span data-stu-id="ebec1-224">The value must be greater than zero (0).</span></span> <span data-ttu-id="ebec1-225">Der Standardwert ist 8.192.</span><span class="sxs-lookup"><span data-stu-id="ebec1-225">The default value is 8,192.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="ebec1-226">Anfangsfenstergröße der Verbindung</span><span class="sxs-lookup"><span data-stu-id="ebec1-226">Initial connection window size</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="ebec1-227">`Http2.InitialConnectionWindowSize` gibt die maximalen Anforderungstextdaten in Byte an, die der Server auf einmal für alle Anforderungen (Streams) pro Verbindung aggregiert.</span><span class="sxs-lookup"><span data-stu-id="ebec1-227">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span>

<span data-ttu-id="ebec1-228">Anforderungen werden auch durch `Http2.InitialStreamWindowSize` beschränkt.</span><span class="sxs-lookup"><span data-stu-id="ebec1-228">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="ebec1-229">Der Wert muss größer als oder gleich 65.535 und kleiner als 2^31 (2.147.483.648) sein.</span><span class="sxs-lookup"><span data-stu-id="ebec1-229">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span> <span data-ttu-id="ebec1-230">Der Standardwert ist 128 KB (131.072).</span><span class="sxs-lookup"><span data-stu-id="ebec1-230">The default value is 128 KB (131,072).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="ebec1-231">Anfangsfenstergröße des Streams</span><span class="sxs-lookup"><span data-stu-id="ebec1-231">Initial stream window size</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="ebec1-232">`Http2.InitialStreamWindowSize` gibt die maximalen Anforderungstextdaten in Byte an, die der Server auf einmal pro Anforderung (Stream) puffert.</span><span class="sxs-lookup"><span data-stu-id="ebec1-232">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span>

<span data-ttu-id="ebec1-233">Anforderungen werden auch durch `Http2.InitialConnectionWindowSize` beschränkt.</span><span class="sxs-lookup"><span data-stu-id="ebec1-233">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="ebec1-234">Der Wert muss größer als oder gleich 65.535 und kleiner als 2^31 (2.147.483.648) sein.</span><span class="sxs-lookup"><span data-stu-id="ebec1-234">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

> [!WARNING]
> <span data-ttu-id="ebec1-235">Der Standardwert ist 96 KB (98.304).</span><span class="sxs-lookup"><span data-stu-id="ebec1-235">The default value is 96 KB (98,304).</span></span> <span data-ttu-id="ebec1-236">Synchrone E/A-Vorgänge</span><span class="sxs-lookup"><span data-stu-id="ebec1-236">Synchronous I/O</span></span>

<span data-ttu-id="ebec1-237"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> steuert, ob für Anforderung und Antwort synchrone E/A-Vorgänge zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="ebec1-237"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="ebec1-238">Der Standardwert ist `false`.</span><span class="sxs-lookup"><span data-stu-id="ebec1-238">The default value is `false`.</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="ebec1-239">Sehr viele blockierende synchrone E/A-Vorgänge können zu einem Ressourcenmangel im Threadpool führen, wodurch die App nicht mehr reagiert.</span><span class="sxs-lookup"><span data-stu-id="ebec1-239">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span>

<span data-ttu-id="ebec1-240">Aktivieren Sie `AllowSynchronousIO` nur bei Verwendung einer Bibliothek, die asynchrone E/A-Vorgänge nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="ebec1-240">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

* `http://localhost:5000`
* <span data-ttu-id="ebec1-241">Das folgende Beispiel aktiviert synchrone E/A-Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="ebec1-241">The following example enables synchronous I/O:</span></span>

<span data-ttu-id="ebec1-242">Weitere Informationen zu anderen Kestrel-Optionen und -Einschränkungen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="ebec1-242">For information about other Kestrel options and limits, see:</span></span>

* <span data-ttu-id="ebec1-243">Endpunktkonfiguration</span><span class="sxs-lookup"><span data-stu-id="ebec1-243">Endpoint configuration</span></span>
* <span data-ttu-id="ebec1-244">Standardmäßig wird ASP.NET Core an Folgendes gebunden:</span><span class="sxs-lookup"><span data-stu-id="ebec1-244">By default, ASP.NET Core binds to:</span></span>
* <span data-ttu-id="ebec1-245">`https://localhost:5001` (wenn ein lokales Entwicklungszertifikat vorhanden ist)</span><span class="sxs-lookup"><span data-stu-id="ebec1-245">`https://localhost:5001` (when a local development certificate is present)</span></span>
* <span data-ttu-id="ebec1-246">Verwenden Sie Folgendes zum Angeben der URLs:</span><span class="sxs-lookup"><span data-stu-id="ebec1-246">Specify URLs using the:</span></span>

<span data-ttu-id="ebec1-247">Die Umgebungsvariable `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="ebec1-247">`ASPNETCORE_URLS` environment variable.</span></span> <span data-ttu-id="ebec1-248">Das Befehlszeilenargument `--urls`</span><span class="sxs-lookup"><span data-stu-id="ebec1-248">`--urls` command-line argument.</span></span>

<span data-ttu-id="ebec1-249">Den Hostkonfigurationsschlüssel `urls`</span><span class="sxs-lookup"><span data-stu-id="ebec1-249">`urls` host configuration key.</span></span>

<span data-ttu-id="ebec1-250">Die Erweiterungsmethode `UseUrls`</span><span class="sxs-lookup"><span data-stu-id="ebec1-250">`UseUrls` extension method.</span></span>

* <span data-ttu-id="ebec1-251">Der Wert, der mit diesen Ansätzen angegeben wird, kann mindestens ein HTTP- oder HTTPS-Endpunkt sein (HTTPS wenn ein Standardzertifikat verfügbar ist).</span><span class="sxs-lookup"><span data-stu-id="ebec1-251">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span>
* <span data-ttu-id="ebec1-252">Konfigurieren Sie den Wert als eine durch Semikolons getrennte Liste (z.B. `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="ebec1-252">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="ebec1-253">Weitere Informationen zu diesen Ansätzen finden Sie unter [Server-URLs](xref:fundamentals/host/web-host#server-urls) und [Außerkraftsetzen der Konfiguration](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="ebec1-253">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="ebec1-254">Ein Entwicklungszertifikat wird erstellt:</span><span class="sxs-lookup"><span data-stu-id="ebec1-254">A development certificate is created:</span></span>

<span data-ttu-id="ebec1-255">Wenn das [.NET Core SDK](/dotnet/core/sdk) installiert wird.</span><span class="sxs-lookup"><span data-stu-id="ebec1-255">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>

<span data-ttu-id="ebec1-256">Das [dev-cert-Tool](xref:aspnetcore-2.1#https) wird zum Erstellen eines Zertifikats verwendet.</span><span class="sxs-lookup"><span data-stu-id="ebec1-256">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="ebec1-257">Einige Browser erfordern, dass Sie die explizite Berechtigung erteilen, dem lokalen Entwicklungszertifikat zu vertrauen.</span><span class="sxs-lookup"><span data-stu-id="ebec1-257">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="ebec1-258">Projektvorlagen konfigurieren Apps, damit sie standardmäßig auf HTTPS ausgeführt werden und die [HTTPS-Umleitung und HSTS-Unterstützung](xref:security/enforcing-ssl) enthalten.</span><span class="sxs-lookup"><span data-stu-id="ebec1-258">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="ebec1-259">Rufen Sie die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>- oder <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>-Methode unter <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> auf, um URL-Präfixe und Ports für Kestrel zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="ebec1-259">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span> <span data-ttu-id="ebec1-260">`UseUrls`, das Befehlszeilenargument `--urls`, der Hostkonfigurationsschlüssel `urls` und die Umgebungsvariable `ASPNETCORE_URLS` funktionieren ebenfalls, verfügen jedoch über Einschränkungen, die im Verlauf dieses Abschnitts erläutert werden (Ein Standardzertifikat muss für die HTTPS-Endpunktkonfiguration verfügbar sein).</span><span class="sxs-lookup"><span data-stu-id="ebec1-260">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

> [!NOTE]
> <span data-ttu-id="ebec1-261">`KestrelServerOptions`-Konfiguration:</span><span class="sxs-lookup"><span data-stu-id="ebec1-261">`KestrelServerOptions` configuration:</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="ebec1-262">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="ebec1-262">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="ebec1-263">Gibt die Konfiguration von `Action` zum Ausführen von jedem angegebenen Endpunkt an.</span><span class="sxs-lookup"><span data-stu-id="ebec1-263">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="ebec1-264">Mehrmalige Aufrufe von `ConfigureEndpointDefaults` ersetzen vorherige Instanzen von `Action` mit der zuletzt angegebenen `Action`.</span><span class="sxs-lookup"><span data-stu-id="ebec1-264">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        // certificate is an X509Certificate2
        listenOptions.ServerCertificate = certificate;
    });
});
```

> [!NOTE]
> <span data-ttu-id="ebec1-265">Auf Endpunkte, die durch Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **vor** dem Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> erstellt werden, werden die Standardwerte nicht angewendet.</span><span class="sxs-lookup"><span data-stu-id="ebec1-265">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="ebec1-266">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="ebec1-266">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="ebec1-267">Gibt die Konfiguration von `Action` zum Ausführen von jedem HTTPS-Endpunkt an.</span><span class="sxs-lookup"><span data-stu-id="ebec1-267">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="ebec1-268">Mehrmalige Aufrufe von `ConfigureHttpsDefaults` ersetzen vorherige Instanzen von `Action` mit der zuletzt angegebenen `Action`.</span><span class="sxs-lookup"><span data-stu-id="ebec1-268">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="ebec1-269">Auf Endpunkte, die durch Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **vor** dem Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> erstellt werden, werden die Standardwerte nicht angewendet.</span><span class="sxs-lookup"><span data-stu-id="ebec1-269">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

<span data-ttu-id="ebec1-270">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="ebec1-270">Configure(IConfiguration)</span></span>

<span data-ttu-id="ebec1-271">Erstellt ein Konfigurationsladeprogramm für das Einrichten von Kestrel, was <xref:Microsoft.Extensions.Configuration.IConfiguration> als Eingabe erfordert.</span><span class="sxs-lookup"><span data-stu-id="ebec1-271">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span>

* <span data-ttu-id="ebec1-272">Die Konfiguration muss auf den Konfigurationsabschnitt für Kestrel festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="ebec1-272">The configuration must be scoped to the configuration section for Kestrel.</span></span> <span data-ttu-id="ebec1-273">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="ebec1-273">ListenOptions.UseHttps</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="ebec1-274">Konfiguriert Kestrel zur Verwendung von HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ebec1-274">Configure Kestrel to use HTTPS.</span></span>

* <span data-ttu-id="ebec1-275">`ListenOptions.UseHttps`-Erweiterungen:</span><span class="sxs-lookup"><span data-stu-id="ebec1-275">`ListenOptions.UseHttps` extensions:</span></span>
* <span data-ttu-id="ebec1-276">`UseHttps`: Hiermit wird Kestrel zur Verwendung von HTTPS mit dem Standardzertifikat konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="ebec1-276">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span>
* <span data-ttu-id="ebec1-277">Löst eine Ausnahme aus, wenn kein Standardzertifikat konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="ebec1-277">Throws an exception if no default certificate is configured.</span></span> <span data-ttu-id="ebec1-278">`ListenOptions.UseHttps`-Parameter:</span><span class="sxs-lookup"><span data-stu-id="ebec1-278">`ListenOptions.UseHttps` parameters:</span></span>
* <span data-ttu-id="ebec1-279">`filename` entspricht dem Pfad und Dateinamen einer Zertifikatdatei relativ zu dem Verzeichnis, das die Inhaltsdateien der App enthält.</span><span class="sxs-lookup"><span data-stu-id="ebec1-279">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="ebec1-280">`password` ist das für den Zugriff auf die X.509-Zertifikatsdaten erforderliche Kennwort.</span><span class="sxs-lookup"><span data-stu-id="ebec1-280">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="ebec1-281">`configureOptions` ist eine `Action` zum Konfigurieren von `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="ebec1-281">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span>
* <span data-ttu-id="ebec1-282">Gibt `ListenOptions` zurück.</span><span class="sxs-lookup"><span data-stu-id="ebec1-282">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="ebec1-283">`storeName` ist der Zertifikatspeicher, aus dem das Zertifikat geladen wird.</span><span class="sxs-lookup"><span data-stu-id="ebec1-283">`storeName` is the certificate store from which to load the certificate.</span></span>

<span data-ttu-id="ebec1-284">`subject` ist der Name des Antragstellers für das Zertifikat.</span><span class="sxs-lookup"><span data-stu-id="ebec1-284">`subject` is the subject name for the certificate.</span></span> <span data-ttu-id="ebec1-285">`allowInvalid` gibt an, ob ungültige Zertifikate berücksichtigt werden sollten, z.B. selbstsignierte Zertifikate.</span><span class="sxs-lookup"><span data-stu-id="ebec1-285">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>

<span data-ttu-id="ebec1-286">`location` ist der Speicherort, aus dem das Zertifikat geladen wird.</span><span class="sxs-lookup"><span data-stu-id="ebec1-286">`location` is the store location to load the certificate from.</span></span>

* <span data-ttu-id="ebec1-287">`serverCertificate` ist das X.509-Zertifikat.</span><span class="sxs-lookup"><span data-stu-id="ebec1-287">`serverCertificate` is the X.509 certificate.</span></span>
* <span data-ttu-id="ebec1-288">In der Produktion muss HTTPS explizit konfiguriert sein.</span><span class="sxs-lookup"><span data-stu-id="ebec1-288">In production, HTTPS must be explicitly configured.</span></span>
* <span data-ttu-id="ebec1-289">Zumindest muss ein Standardzertifikat angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="ebec1-289">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="ebec1-290">Die im Folgenden beschriebenen unterstützten Konfigurationen:</span><span class="sxs-lookup"><span data-stu-id="ebec1-290">Supported configurations described next:</span></span>

<span data-ttu-id="ebec1-291">Keine Konfiguration</span><span class="sxs-lookup"><span data-stu-id="ebec1-291">No configuration</span></span>

<a name="configuration"></a>

<span data-ttu-id="ebec1-292">Ersetzen des Standardzertifikats aus der Konfiguration</span><span class="sxs-lookup"><span data-stu-id="ebec1-292">Replace the default certificate from configuration</span></span>

<span data-ttu-id="ebec1-293">Ändern des Standards im Code</span><span class="sxs-lookup"><span data-stu-id="ebec1-293">Change the defaults in code</span></span> <span data-ttu-id="ebec1-294">*Keine Konfiguration*</span><span class="sxs-lookup"><span data-stu-id="ebec1-294">*No configuration*</span></span> <span data-ttu-id="ebec1-295">Kestrel überwacht `http://localhost:5000` und `https://localhost:5001` (wenn ein Standardzertifikat verfügbar ist).</span><span class="sxs-lookup"><span data-stu-id="ebec1-295">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<span data-ttu-id="ebec1-296">*Ersetzen des Standardzertifikats aus der Konfiguration*</span><span class="sxs-lookup"><span data-stu-id="ebec1-296">*Replace the default certificate from configuration*</span></span>

* <span data-ttu-id="ebec1-297">`CreateDefaultBuilder` ruft `Configure(context.Configuration.GetSection("Kestrel"))` standardmäßig zum Laden der Kestrel-Konfiguration auf.</span><span class="sxs-lookup"><span data-stu-id="ebec1-297">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>
* <span data-ttu-id="ebec1-298">Ein Standardkonfigurationsschema für HTTPS-App-Einstellungen ist für Kestrel verfügbar.</span><span class="sxs-lookup"><span data-stu-id="ebec1-298">A default HTTPS app settings configuration schema is available for Kestrel.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },
      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },
      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },
      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="ebec1-299">Konfigurieren Sie mehrere Endpunkte, einschließlich der zu verwendenden URLs und Zertifikate aus einer Datei auf dem Datenträger oder einem Zertifikatspeicher.</span><span class="sxs-lookup"><span data-stu-id="ebec1-299">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span> <span data-ttu-id="ebec1-300">Die Vorgehensweise im folgenden *appsettings.json*-Beispiel:</span><span class="sxs-lookup"><span data-stu-id="ebec1-300">In the following *appsettings.json* example:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="ebec1-301">Legen Sie **AllowInvalid** auf `true` fest, um die Verwendung von ungültigen Zertifikaten zu erlauben (z.B. selbstsignierte Zertifikate).</span><span class="sxs-lookup"><span data-stu-id="ebec1-301">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>

* <span data-ttu-id="ebec1-302">Jeder HTTPS-Endpunkt, der kein Zertifikat angibt (im folgenden Beispiel **HttpsDefaultCert**), greift auf das unter **Zertifikate** > **Standard** festgelegte Zertifikat oder das Entwicklungszertifikat zurück.</span><span class="sxs-lookup"><span data-stu-id="ebec1-302">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span> <span data-ttu-id="ebec1-303">Alternativ zur Verwendung von **Pfad** und **Kennwort** für alle Zertifikatknoten können Sie das Zertifikat mithilfe von Zertifikatspeicherfeldern angeben.</span><span class="sxs-lookup"><span data-stu-id="ebec1-303">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span>
* <span data-ttu-id="ebec1-304">Das Zertifikat unter **Zertifikate** > **Standard** kann beispielweise wie folgt angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="ebec1-304">For example, the **Certificates** > **Default** certificate can be specified as:</span></span> <span data-ttu-id="ebec1-305">Schema-Hinweise:</span><span class="sxs-lookup"><span data-stu-id="ebec1-305">Schema notes:</span></span>
* <span data-ttu-id="ebec1-306">Bei den Namen der Endpunkte wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="ebec1-306">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="ebec1-307">Zum Beispiel sind `HTTPS` und `Https` gültig.</span><span class="sxs-lookup"><span data-stu-id="ebec1-307">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="ebec1-308">Der Parameter `Url` ist für jeden Endpunkt erforderlich.</span><span class="sxs-lookup"><span data-stu-id="ebec1-308">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="ebec1-309">Das Format für diesen Parameter ist identisch mit dem allgemeinen Konfigurationsparameter `Urls`, mit der Ausnahme, dass er auf einen einzelnen Wert begrenzt ist.</span><span class="sxs-lookup"><span data-stu-id="ebec1-309">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span> <span data-ttu-id="ebec1-310">Diese Endpunkte ersetzen die in der allgemeinen `Urls`-Konfiguration festgelegten Endpunkte, anstatt zu ihnen hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="ebec1-310">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span>
* <span data-ttu-id="ebec1-311">Endpunkte, die über `Listen` in Code definiert werden, werden den im Konfigurationsabschnitt definierten Endpunkten hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="ebec1-311">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="ebec1-312">Der `Certificate`-Abschnitt ist optional.</span><span class="sxs-lookup"><span data-stu-id="ebec1-312">The `Certificate` section is optional.</span></span>
* <span data-ttu-id="ebec1-313">Wenn der `Certificate`-Abschnitt nicht angegeben ist, werden die in früheren Szenarios definierten Standardwerte verwendet.</span><span class="sxs-lookup"><span data-stu-id="ebec1-313">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span>

```csharp
webBuilder.UseKestrel((context, serverOptions) =>
{
    serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
        .Endpoint("HTTPS", listenOptions =>
        {
            listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
        });
});
```

<span data-ttu-id="ebec1-314">Wenn keine Standardwerte verfügbar sind, löst der Server eine Ausnahme aus und startet nicht.</span><span class="sxs-lookup"><span data-stu-id="ebec1-314">If no defaults are available, the server throws an exception and fails to start.</span></span>

* <span data-ttu-id="ebec1-315">Der `Certificate`-Abschnitt unterstützt die Zertifikate **Pfad**&ndash;**Kennwort** und **Subject**&ndash;**Store** (Antragsteller–Speicher).</span><span class="sxs-lookup"><span data-stu-id="ebec1-315">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="ebec1-316">Auf diese Weise kann eine beliebige Anzahl von Endpunkten definiert werden, solange Sie keine Portkonflikte verursachen.</span><span class="sxs-lookup"><span data-stu-id="ebec1-316">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span> <span data-ttu-id="ebec1-317">`options.Configure(context.Configuration.GetSection("{SECTION}"))` gibt `KestrelConfigurationLoader` mit der Methode `.Endpoint(string name, listenOptions => { })` zurück, die dazu verwendet werden kann, die Einstellungen eines Endpunkts zu ergänzen:</span><span class="sxs-lookup"><span data-stu-id="ebec1-317">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span> <span data-ttu-id="ebec1-318">Auf `KestrelServerOptions.ConfigurationLoader` kann direkt zugegriffen werden, um die Iteration auf dem vorhandenen Ladeprogramm fortzusetzen, etwa auf dem von <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> bereitgestellten Ladeprogramm.</span><span class="sxs-lookup"><span data-stu-id="ebec1-318">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>
* <span data-ttu-id="ebec1-319">Der Konfigurationsabschnitt ist für jeden Endpunkt in den Optionen der Methode `Endpoint` verfügbar, sodass benutzerdefinierte Einstellungen gelesen werden können.</span><span class="sxs-lookup"><span data-stu-id="ebec1-319">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span> <span data-ttu-id="ebec1-320">Mehrere Konfigurationen können durch erneutes Aufrufen von `options.Configure(context.Configuration.GetSection("{SECTION}"))` mit einem anderen Abschnitt geladen werden.</span><span class="sxs-lookup"><span data-stu-id="ebec1-320">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span>

<span data-ttu-id="ebec1-321">Sofern `Load` nicht in einer vorherigen Instanz explizit aufgerufen wird, wird nur die letzte Konfiguration verwendet.</span><span class="sxs-lookup"><span data-stu-id="ebec1-321">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span>

<span data-ttu-id="ebec1-322">Das Metapaket ruft `Load` nicht auf, sodass der Abschnitt mit der Standardkonfiguration ersetzt werden kann.</span><span class="sxs-lookup"><span data-stu-id="ebec1-322">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span> <span data-ttu-id="ebec1-323">`KestrelConfigurationLoader` spiegelt die API-Familie `Listen` von `KestrelServerOptions` als `Endpoint`-Überladungen, weshalb Code und Konfigurationsendpunkte am selben Ort konfiguriert werden können.</span><span class="sxs-lookup"><span data-stu-id="ebec1-323">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });

    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.SslProtocols = SslProtocols.Tls12;
    });
});
```

<span data-ttu-id="ebec1-324">Diese Überladungen verwenden keine Namen und nutzen nur Standardeinstellungen aus der Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="ebec1-324">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="ebec1-325">*Ändern des Standards im Code*</span><span class="sxs-lookup"><span data-stu-id="ebec1-325">*Change the defaults in code*</span></span> <span data-ttu-id="ebec1-326">`ConfigureEndpointDefaults` und `ConfigureHttpsDefaults` können zum Ändern der Standardeinstellungen für `ListenOptions` und `HttpsConnectionAdapterOptions` verwendet werden, einschließlich der Standardzertifikate, die im vorherigen Szenario festgelegt wurden.</span><span class="sxs-lookup"><span data-stu-id="ebec1-326">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="ebec1-327">`ConfigureEndpointDefaults` und `ConfigureHttpsDefaults` sollten aufgerufen werden, bevor Endpunkte konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="ebec1-327">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

<span data-ttu-id="ebec1-328">*Kestrel-Unterstützung für SNI*</span><span class="sxs-lookup"><span data-stu-id="ebec1-328">*Kestrel support for SNI*</span></span> <span data-ttu-id="ebec1-329">Die [Servernamensanzeige (SNI)](https://tools.ietf.org/html/rfc6066#section-3) kann zum Hosten mehrerer Domänen auf der gleichen IP-Adresse und dem gleichen Port verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="ebec1-329">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span>

<span data-ttu-id="ebec1-330">Damit die Servernamensanzeige funktioniert, sendet der Client während des TLS-Handshakes den Hostnamen für die sichere Sitzung an den Server, sodass der Server das richtige Zertifikat bereitstellen kann.</span><span class="sxs-lookup"><span data-stu-id="ebec1-330">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span>

* <span data-ttu-id="ebec1-331">Der Client verwendet das beigestellte Zertifikat für die verschlüsselte Kommunikation mit dem Server während der sicheren Sitzung nach dem TLS-Handshake.</span><span class="sxs-lookup"><span data-stu-id="ebec1-331">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span> <span data-ttu-id="ebec1-332">Kestrel unterstützt die Servernamensanzeige über den `ServerCertificateSelector`-Rückruf.</span><span class="sxs-lookup"><span data-stu-id="ebec1-332">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="ebec1-333">Der Rückruf wird für jede Verbindung einmal aufgerufen, um der App zu ermöglichen, den Hostnamen zu überprüfen und das entsprechende Zertifikat auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="ebec1-333">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>
* <span data-ttu-id="ebec1-334">Für die Unterstützung der Servernamensanzeige benötigen Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="ebec1-334">SNI support requires:</span></span> <span data-ttu-id="ebec1-335">Wird auf dem Zielframework `netcoreapp2.1` oder höher ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="ebec1-335">Running on target framework `netcoreapp2.1` or later.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ListenAnyIP(5005, listenOptions =>
    {
        listenOptions.UseHttps(httpsOptions =>
        {
            var localhostCert = CertificateLoader.LoadFromStoreCert(
                "localhost", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var exampleCert = CertificateLoader.LoadFromStoreCert(
                "example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var subExampleCert = CertificateLoader.LoadFromStoreCert(
                "sub.example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var certs = new Dictionary<string, X509Certificate2>(
                StringComparer.OrdinalIgnoreCase);
            certs["localhost"] = localhostCert;
            certs["example.com"] = exampleCert;
            certs["sub.example.com"] = subExampleCert;

            httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
            {
                if (name != null && certs.TryGetValue(name, out var cert))
                {
                    return cert;
                }

                return exampleCert;
            };
        });
    });
});
```

### <a name="connection-logging"></a><span data-ttu-id="ebec1-336">In `net461` oder höher, wird der Rückruf aufgerufen, `name` ist aber immer `null`.</span><span class="sxs-lookup"><span data-stu-id="ebec1-336">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span>

<span data-ttu-id="ebec1-337">`name` ist auch `null`, wenn der Client den Hostnamenparameter nicht im TLS-Handshake angibt.</span><span class="sxs-lookup"><span data-stu-id="ebec1-337">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span> <span data-ttu-id="ebec1-338">Alle Websites werden in derselben Kestrel-Instanz ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="ebec1-338">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="ebec1-339">Kestrel unterstützt ohne Reverseproxy keine gemeinsame IP-Adresse und keinen gemeinsamen Port für mehrere Instanzen.</span><span class="sxs-lookup"><span data-stu-id="ebec1-339">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span> <span data-ttu-id="ebec1-340">Verbindungsprotokollierung</span><span class="sxs-lookup"><span data-stu-id="ebec1-340">Connection logging</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="ebec1-341">Rufen Sie <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> auf, um Protokolle auf Debugebene für die Kommunikation auf Byteebene für eine Verbindung auszugeben.</span><span class="sxs-lookup"><span data-stu-id="ebec1-341">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span>

<span data-ttu-id="ebec1-342">Die Verbindungsprotokollierung ist beim Beheben von Problemen bei der Low-Level-Kommunikation hilfreich, wie z. B. bei der TLS-Verschlüsselung und bei Proxys.</span><span class="sxs-lookup"><span data-stu-id="ebec1-342">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="ebec1-343">Wenn `UseConnectionLogging` vor `UseHttps` platziert wird, wird der verschlüsselte Datenverkehr protokolliert.</span><span class="sxs-lookup"><span data-stu-id="ebec1-343">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="ebec1-344">Wenn `UseConnectionLogging` nach `UseHttps` platziert wird, wird der entschlüsselte Datenverkehr protokolliert.</span><span class="sxs-lookup"><span data-stu-id="ebec1-344">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="ebec1-345">Binden an einen TCP-Socket</span><span class="sxs-lookup"><span data-stu-id="ebec1-345">Bind to a TCP socket</span></span>

<span data-ttu-id="ebec1-346">Die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>-Methode wird an ein TCP-Socket gebunden, und ein Lambdaausdruck einer Option lässt die Konfiguration des X.509-Zertifikats zu:</span><span class="sxs-lookup"><span data-stu-id="ebec1-346">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="ebec1-347">Im Beispiel wird HTTPS für einen Endpunkt mit <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="ebec1-347">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="ebec1-348">Verwenden Sie die gleiche API zum Konfigurieren anderer Kestrel-Einstellungen für bestimmte Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="ebec1-348">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>
* <span data-ttu-id="ebec1-349">Binden an einen Unix-Socket</span><span class="sxs-lookup"><span data-stu-id="ebec1-349">Bind to a Unix socket</span></span>

### <a name="port-0"></a><span data-ttu-id="ebec1-350">Wie in diesem Beispiel dargestellt, lauschen Sie an einem Unix-Socket mit <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>, um eine verbesserte Leistung mit Nginx zu erzielen:</span><span class="sxs-lookup"><span data-stu-id="ebec1-350">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

<span data-ttu-id="ebec1-351">Legen Sie den Eintrag `server` > `location` > `proxy_pass` in der Nginx-Konfigurationsdatei auf `http://unix:/tmp/{KESTREL SOCKET}:/;` fest.</span><span class="sxs-lookup"><span data-stu-id="ebec1-351">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="ebec1-352">`{KESTREL SOCKET}` ist der Name des für <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> bereitgestellten Socket (zum Beispiel `kestrel-test.sock` im vorherigen Beispiel).</span><span class="sxs-lookup"><span data-stu-id="ebec1-352">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="ebec1-353">Stellen Sie sicher, dass der Socket von Nginx beschreibbar ist (z. B. `chmod go+w /tmp/kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="ebec1-353">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="ebec1-354">Port 0</span><span class="sxs-lookup"><span data-stu-id="ebec1-354">Port 0</span></span>

<span data-ttu-id="ebec1-355">Wenn die Portnummer `0` angegeben wird, wird Kestrel dynamisch an einen verfügbaren Port gebunden.</span><span class="sxs-lookup"><span data-stu-id="ebec1-355">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="ebec1-356">Im folgenden Beispiel wird veranschaulicht, wie bestimmt werden kann, für welchen Port Kestrel zur Laufzeit eine Bindung erstellt hat:</span><span class="sxs-lookup"><span data-stu-id="ebec1-356">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>
* <span data-ttu-id="ebec1-357">Wenn die App ausgeführt wird, gibt das Ausgabefenster der Konsole den dynamischen Port an, über den die App erreicht werden kann:</span><span class="sxs-lookup"><span data-stu-id="ebec1-357">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>
* <span data-ttu-id="ebec1-358">Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="ebec1-358">Limitations</span></span>

<span data-ttu-id="ebec1-359">Konfigurieren Sie Endpunkte mithilfe der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="ebec1-359">Configure endpoints with the following approaches:</span></span> <span data-ttu-id="ebec1-360">Befehlszeilenargument `--urls`</span><span class="sxs-lookup"><span data-stu-id="ebec1-360">`--urls` command-line argument</span></span>

* <span data-ttu-id="ebec1-361">Hostkonfigurationsschlüssel `urls`</span><span class="sxs-lookup"><span data-stu-id="ebec1-361">`urls` host configuration key</span></span>
* <span data-ttu-id="ebec1-362">Umgebungsvariable `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="ebec1-362">`ASPNETCORE_URLS` environment variable</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="ebec1-363">Diese Methoden sind nützlich, wenn Ihr Code mit anderen Servern als Kestrel funktionieren soll.</span><span class="sxs-lookup"><span data-stu-id="ebec1-363">These methods are useful for making code work with servers other than Kestrel.</span></span>

<span data-ttu-id="ebec1-364">Beachten Sie jedoch die folgenden Einschränkungen:</span><span class="sxs-lookup"><span data-stu-id="ebec1-364">However, be aware of the following limitations:</span></span> <span data-ttu-id="ebec1-365">HTTPS kann nicht mit diesen Ansätzen verwendet werden, außer ein Standardzertifikat wird in der HTTPS-Endpunktkonfiguration angegeben (z.B. wenn Sie wie zuvor in diesem Artikel gezeigt die `KestrelServerOptions`-Konfiguration oder eine Konfigurationsdatei verwenden).</span><span class="sxs-lookup"><span data-stu-id="ebec1-365">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="ebec1-366">Wenn die Ansätze `Listen` und `UseUrls` gleichzeitig verwendet werden, überschreiben die `Listen`-Endpunkte die `UseUrls`-Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="ebec1-366">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

<span data-ttu-id="ebec1-367">IIS-Endpunktkonfiguration</span><span class="sxs-lookup"><span data-stu-id="ebec1-367">IIS endpoint configuration</span></span> <span data-ttu-id="ebec1-368">Bei der Verwendung von IIS werden die URL-Bindungen für IIS-Überschreibungsbindungen durch `Listen` oder `UseUrls` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="ebec1-368">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span>

| <span data-ttu-id="ebec1-369">Weitere Informationen finden Sie im Artikel [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="ebec1-369">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span> | <span data-ttu-id="ebec1-370">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="ebec1-370">ListenOptions.Protocols</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="ebec1-371">Die `Protocols`-Eigenschaft richtet die HTTP-Protokolle (`HttpProtocols`) ein, die für einen Verbindungsendpunkt oder für den Server aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="ebec1-371">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="ebec1-372">Weisen Sie der `Protocols`-Eigenschaft einen Wert aus der `HttpProtocols`-Enumeration zu.</span><span class="sxs-lookup"><span data-stu-id="ebec1-372">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span> |
| `Http2`                    | <span data-ttu-id="ebec1-373">`HttpProtocols`-Enumerationswert</span><span class="sxs-lookup"><span data-stu-id="ebec1-373">`HttpProtocols` enum value</span></span> <span data-ttu-id="ebec1-374">Zulässiges Verbindungsprotokoll</span><span class="sxs-lookup"><span data-stu-id="ebec1-374">Connection protocol permitted</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="ebec1-375">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebec1-375">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="ebec1-376">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-376">'Blazor'</span></span> |

<span data-ttu-id="ebec1-377">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebec1-377">'Identity'</span></span>

<span data-ttu-id="ebec1-378">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebec1-378">'Let's Encrypt'</span></span>

* <span data-ttu-id="ebec1-379">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-379">'Razor'</span></span>
* <span data-ttu-id="ebec1-380">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebec1-380">'SignalR' uid:</span></span>
* <span data-ttu-id="ebec1-381">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebec1-381">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
* <span data-ttu-id="ebec1-382">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-382">'Blazor'</span></span>
  * <span data-ttu-id="ebec1-383">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebec1-383">'Identity'</span></span>
  * <span data-ttu-id="ebec1-384">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebec1-384">'Let's Encrypt'</span></span>
* <span data-ttu-id="ebec1-385">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-385">'Razor'</span></span>

<span data-ttu-id="ebec1-386">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebec1-386">'SignalR' uid:</span></span>

<span data-ttu-id="ebec1-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebec1-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="ebec1-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-388">'Blazor'</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="ebec1-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebec1-389">'Identity'</span></span>

<span data-ttu-id="ebec1-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebec1-390">'Let's Encrypt'</span></span> <span data-ttu-id="ebec1-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-391">'Razor'</span></span>

<span data-ttu-id="ebec1-392">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebec1-392">'SignalR' uid:</span></span>

```csharp
// using System.Net;
// using Microsoft.AspNetCore.Connections;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.UseTlsFilter();
    });
});
```

```csharp
using System;
using System.Security.Authentication;
using Microsoft.AspNetCore.Connections.Features;

namespace Microsoft.AspNetCore.Connections
{
    public static class TlsFilterConnectionMiddlewareExtensions
    {
        public static IConnectionBuilder UseTlsFilter(
            this IConnectionBuilder builder)
        {
            return builder.Use((connection, next) =>
            {
                var tlsFeature = connection.Features.Get<ITlsHandshakeFeature>();

                if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
                {
                    throw new NotSupportedException("Prohibited cipher: " +
                        tlsFeature.CipherAlgorithm);
                }

                return next();
            });
        }
    }
}
```

<span data-ttu-id="ebec1-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebec1-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

```csharp
// using System;
// using System.Net;
// using System.Security.Authentication;
// using Microsoft.AspNetCore.Connections;
// using Microsoft.AspNetCore.Connections.Features;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.Use((context, next) =>
        {
            var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

            if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
            {
                throw new NotSupportedException(
                    $"Prohibited cipher: {tlsFeature.CipherAlgorithm}");
            }

            return next();
        });
    });
});
```

<span data-ttu-id="ebec1-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-394">'Blazor'</span></span>

```csharp
// using System.Net.Security;
// using Microsoft.AspNetCore.Hosting;
// using Microsoft.AspNetCore.Server.Kestrel.Core;
// using Microsoft.Extensions.DependencyInjection;
// using Microsoft.Extensions.Hosting;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.OnAuthenticate = (context, sslOptions) =>
        {
            sslOptions.CipherSuitesPolicy = new CipherSuitesPolicy(
                new[]
                {
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,
                    // ...
                });
        };
    });
});
```

<span data-ttu-id="ebec1-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebec1-395">'Identity'</span></span>

<span data-ttu-id="ebec1-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebec1-396">'Let's Encrypt'</span></span>

<span data-ttu-id="ebec1-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-397">'Razor'</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="ebec1-398">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebec1-398">'SignalR' uid:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1"
      }
    }
  }
}
```

<span data-ttu-id="ebec1-399">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebec1-399">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="ebec1-400">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-400">'Blazor'</span></span>

<span data-ttu-id="ebec1-401">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebec1-401">'Identity'</span></span>

* <span data-ttu-id="ebec1-402">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebec1-402">'Let's Encrypt'</span></span>

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* <span data-ttu-id="ebec1-403">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-403">'Razor'</span></span>

   ```csharp
   public class Program
   {
       public static void Main(string[] args)
       {
           CreateHostBuilder(args).Build().Run();
       }

       public static IHostBuilder CreateHostBuilder(string[] args) =>
           Host.CreateDefaultBuilder(args)
               .ConfigureWebHostDefaults(webBuilder =>
               {
                   webBuilder.UseLibuv();
                   webBuilder.UseStartup<Startup>();
               });
   }
   ```

### <a name="url-prefixes"></a><span data-ttu-id="ebec1-404">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebec1-404">'SignalR' uid:</span></span>

<span data-ttu-id="ebec1-405">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebec1-405">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="ebec1-406">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-406">'Blazor'</span></span> <span data-ttu-id="ebec1-407">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebec1-407">'Identity'</span></span>

* <span data-ttu-id="ebec1-408">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebec1-408">'Let's Encrypt'</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="ebec1-409">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-409">'Razor'</span></span>

* <span data-ttu-id="ebec1-410">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebec1-410">'SignalR' uid:</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="ebec1-411">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebec1-411">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

* <span data-ttu-id="ebec1-412">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-412">'Blazor'</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="ebec1-413">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebec1-413">'Identity'</span></span> <span data-ttu-id="ebec1-414">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebec1-414">'Let's Encrypt'</span></span> <span data-ttu-id="ebec1-415">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-415">'Razor'</span></span>

  > [!WARNING]
  > <span data-ttu-id="ebec1-416">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebec1-416">'SignalR' uid:</span></span>

* <span data-ttu-id="ebec1-417">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebec1-417">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="ebec1-418">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-418">'Blazor'</span></span> <span data-ttu-id="ebec1-419">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebec1-419">'Identity'</span></span> <span data-ttu-id="ebec1-420">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebec1-420">'Let's Encrypt'</span></span>

## <a name="host-filtering"></a><span data-ttu-id="ebec1-421">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-421">'Razor'</span></span>

<span data-ttu-id="ebec1-422">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebec1-422">'SignalR' uid:</span></span> <span data-ttu-id="ebec1-423">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebec1-423">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="ebec1-424">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-424">'Blazor'</span></span> <span data-ttu-id="ebec1-425">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebec1-425">'Identity'</span></span>

<span data-ttu-id="ebec1-426">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebec1-426">'Let's Encrypt'</span></span> <span data-ttu-id="ebec1-427">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-427">'Razor'</span></span> <span data-ttu-id="ebec1-428">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebec1-428">'SignalR' uid:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="ebec1-429">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebec1-429">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="ebec1-430">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-430">'Blazor'</span></span> <span data-ttu-id="ebec1-431">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebec1-431">'Identity'</span></span>

<span data-ttu-id="ebec1-432">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebec1-432">'Let's Encrypt'</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="ebec1-433">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-433">'Razor'</span></span> <span data-ttu-id="ebec1-434">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebec1-434">'SignalR' uid:</span></span> <span data-ttu-id="ebec1-435">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebec1-435">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="ebec1-436">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-436">'Blazor'</span></span>
>
> <span data-ttu-id="ebec1-437">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebec1-437">'Identity'</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="ebec1-438">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebec1-438">'Let's Encrypt'</span></span> <span data-ttu-id="ebec1-439">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-439">'Razor'</span></span>

<span data-ttu-id="ebec1-440">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebec1-440">'SignalR' uid:</span></span>

* <span data-ttu-id="ebec1-441">------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebec1-441">------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
* <span data-ttu-id="ebec1-442">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-442">'Blazor'</span></span>
* <span data-ttu-id="ebec1-443">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebec1-443">'Identity'</span></span>
* <span data-ttu-id="ebec1-444">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebec1-444">'Let's Encrypt'</span></span>

<span data-ttu-id="ebec1-445">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-445">'Razor'</span></span>

<span data-ttu-id="ebec1-446">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebec1-446">'SignalR' uid:</span></span>

<span data-ttu-id="ebec1-447">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebec1-447">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

## <a name="http2-support"></a><span data-ttu-id="ebec1-448">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-448">'Blazor'</span></span>

<span data-ttu-id="ebec1-449">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebec1-449">'Identity'</span></span>

* <span data-ttu-id="ebec1-450">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebec1-450">'Let's Encrypt'</span></span>
  * <span data-ttu-id="ebec1-451">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-451">'Razor'</span></span>
  * <span data-ttu-id="ebec1-452">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebec1-452">'SignalR' uid:</span></span>
* <span data-ttu-id="ebec1-453">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebec1-453">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
* <span data-ttu-id="ebec1-454">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-454">'Blazor'</span></span>
* <span data-ttu-id="ebec1-455">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebec1-455">'Identity'</span></span>

<span data-ttu-id="ebec1-456">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebec1-456">'Let's Encrypt'</span></span>
<span data-ttu-id="ebec1-457">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-457">'Razor'</span></span> <span data-ttu-id="ebec1-458">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebec1-458">'SignalR' uid:</span></span> <span data-ttu-id="ebec1-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebec1-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="ebec1-460">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-460">'Blazor'</span></span>

<span data-ttu-id="ebec1-461">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebec1-461">'Identity'</span></span> <span data-ttu-id="ebec1-462">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebec1-462">'Let's Encrypt'</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="ebec1-463">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-463">'Razor'</span></span>

<span data-ttu-id="ebec1-464">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebec1-464">'SignalR' uid:</span></span> <span data-ttu-id="ebec1-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebec1-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="ebec1-466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-466">'Blazor'</span></span>

!['Identity'](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="ebec1-468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebec1-468">'Let's Encrypt'</span></span>

!['Razor'](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="ebec1-470">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebec1-470">'SignalR' uid:</span></span>

<span data-ttu-id="ebec1-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebec1-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="ebec1-472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-472">'Blazor'</span></span> <span data-ttu-id="ebec1-473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebec1-473">'Identity'</span></span>

<span data-ttu-id="ebec1-474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebec1-474">'Let's Encrypt'</span></span>

<span data-ttu-id="ebec1-475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-475">'Razor'</span></span>

* <span data-ttu-id="ebec1-476">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebec1-476">'SignalR' uid:</span></span>
* <span data-ttu-id="ebec1-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebec1-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
* <span data-ttu-id="ebec1-478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-478">'Blazor'</span></span>
* <span data-ttu-id="ebec1-479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebec1-479">'Identity'</span></span> <span data-ttu-id="ebec1-480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebec1-480">'Let's Encrypt'</span></span>

> [!WARNING]
> <span data-ttu-id="ebec1-481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-481">'Razor'</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="ebec1-482">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebec1-482">'SignalR' uid:</span></span>

<span data-ttu-id="ebec1-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebec1-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="ebec1-484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-484">'Blazor'</span></span> <span data-ttu-id="ebec1-485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebec1-485">'Identity'</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="ebec1-486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebec1-486">'Let's Encrypt'</span></span>

<span data-ttu-id="ebec1-487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-487">'Razor'</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="ebec1-488">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebec1-488">'SignalR' uid:</span></span>

```csharp
public static void Main(string[] args)
{
    var host = new WebHostBuilder()
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseKestrel()
        .UseIISIntegration()
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        })
        .Build();

    host.Run();
}
```

## <a name="kestrel-options"></a><span data-ttu-id="ebec1-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebec1-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="ebec1-490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-490">'Blazor'</span></span>

<span data-ttu-id="ebec1-491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebec1-491">'Identity'</span></span> <span data-ttu-id="ebec1-492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebec1-492">'Let's Encrypt'</span></span>

<span data-ttu-id="ebec1-493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-493">'Razor'</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="ebec1-494">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebec1-494">'SignalR' uid:</span></span> <span data-ttu-id="ebec1-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebec1-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="ebec1-496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-496">'Blazor'</span></span>

* <span data-ttu-id="ebec1-497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebec1-497">'Identity'</span></span>

  1. <span data-ttu-id="ebec1-498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebec1-498">'Let's Encrypt'</span></span> <span data-ttu-id="ebec1-499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-499">'Razor'</span></span>
  2. <span data-ttu-id="ebec1-500">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebec1-500">'SignalR' uid:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="ebec1-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebec1-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

  <span data-ttu-id="ebec1-502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-502">'Blazor'</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="ebec1-503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebec1-503">'Identity'</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="ebec1-504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebec1-504">'Let's Encrypt'</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="ebec1-505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-505">'Razor'</span></span> <span data-ttu-id="ebec1-506">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebec1-506">'SignalR' uid:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="ebec1-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebec1-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="ebec1-508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-508">'Blazor'</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="ebec1-509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebec1-509">'Identity'</span></span> <span data-ttu-id="ebec1-510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebec1-510">'Let's Encrypt'</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="ebec1-511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-511">'Razor'</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="ebec1-512">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebec1-512">'SignalR' uid:</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="ebec1-513">--------------- | | `Http1`                    | Nur HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="ebec1-513">--------------- | | `Http1`                    | HTTP/1.1 only.</span></span>

<span data-ttu-id="ebec1-514">Kann mit oder ohne TLS verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="ebec1-514">Can be used with or without TLS.</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="ebec1-515">| | `Http2`                    | Nur HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="ebec1-515">| | `Http2`                    | HTTP/2 only.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="ebec1-516">Kann nur ohne TLS verwendet werden, wenn der Client einen [Vorabkenntnis-Modus](https://tools.ietf.org/html/rfc7540#section-3.4) unterstützt.</span><span class="sxs-lookup"><span data-stu-id="ebec1-516">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="ebec1-517">| | `Http1AndHttp2`            | HTTP/1.1 und HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="ebec1-517">| | `Http1AndHttp2`            | HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="ebec1-518">Für HTTP/2 muss der Client HTTP/2 im TLS [ALPN-Handshake (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) auswählen, andernfalls wird standardmäßig eine HTTP/1.1 verwendet.</span><span class="sxs-lookup"><span data-stu-id="ebec1-518">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span>

<span data-ttu-id="ebec1-519">Der Standardwert von `ListenOptions.Protocols` ist für alle Endpunkte `HttpProtocols.Http1AndHttp2`.</span><span class="sxs-lookup"><span data-stu-id="ebec1-519">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="ebec1-520">TLS-Einschränkungen für HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="ebec1-520">TLS restrictions for HTTP/2:</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="ebec1-521">TLS Version 1.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="ebec1-521">TLS version 1.2 or later</span></span> <span data-ttu-id="ebec1-522">Erneute Aushandlung deaktiviert</span><span class="sxs-lookup"><span data-stu-id="ebec1-522">Renegotiation disabled</span></span> <span data-ttu-id="ebec1-523">Komprimierung deaktiviert</span><span class="sxs-lookup"><span data-stu-id="ebec1-523">Compression disabled</span></span>

<span data-ttu-id="ebec1-524">Minimale Größen für Austausch von flüchtigen Schlüsseln:</span><span class="sxs-lookup"><span data-stu-id="ebec1-524">Minimum ephemeral key exchange sizes:</span></span>

<span data-ttu-id="ebec1-525">ECDHE (Elliptic Curve Diffie-Hellman) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: mindestens 224 Bits</span><span class="sxs-lookup"><span data-stu-id="ebec1-525">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span> <span data-ttu-id="ebec1-526">DHE (Finite Field Diffie-Hellman) &lbrack;`TLS12`&rbrack;: mindestens 2048 Bits</span><span class="sxs-lookup"><span data-stu-id="ebec1-526">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>

<span data-ttu-id="ebec1-527">Verschlüsselungssammlung nicht gesperrt</span><span class="sxs-lookup"><span data-stu-id="ebec1-527">Cipher suite not blacklisted</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="ebec1-528">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; mit der elliptischen P-256-Kurve &lbrack;`FIPS186`&rbrack; wird standardmäßig unterstützt.</span><span class="sxs-lookup"><span data-stu-id="ebec1-528">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="ebec1-529">Das folgende Beispiel erlaubt HTTP/1.1- und HTTP/2-Verbindungen an Port 8000.</span><span class="sxs-lookup"><span data-stu-id="ebec1-529">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="ebec1-530">Die Verbindungen werden durch TLS mit einem bereitgestellten Zertifikat geschützt:</span><span class="sxs-lookup"><span data-stu-id="ebec1-530">Connections are secured by TLS with a supplied certificate:</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="ebec1-531">Verwenden Sie Verbindungsmiddleware, um TLS-Handshakes auf Verbindungsbasis für bestimmte Verschlüsselungen zu filtern, falls erforderlich.</span><span class="sxs-lookup"><span data-stu-id="ebec1-531">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="ebec1-532">Im folgenden Beispiel wird <xref:System.NotSupportedException> für jeden Verschlüsselungsalgorithmus ausgelöst, der von der App nicht unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="ebec1-532">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="ebec1-533">Alternativ können Sie [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) definieren und mit einer Liste zulässiger Verschlüsselungssammlungen vergleichen.</span><span class="sxs-lookup"><span data-stu-id="ebec1-533">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="ebec1-534">Es wird keine Verschlüsselung mit einem [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType)-Verschlüsselungsalgorithmus verwendet.</span><span class="sxs-lookup"><span data-stu-id="ebec1-534">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

<span data-ttu-id="ebec1-535">Die Verbindungsfilterung kann auch über einen <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder>-Lambdaausdruck konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="ebec1-535">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span> <span data-ttu-id="ebec1-536">Unter Linux kann <xref:System.Net.Security.CipherSuitesPolicy> zum Filtern von TLS-Handshakes auf Verbindungsbasis verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="ebec1-536">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="ebec1-537">*Festlegen des Protokolls aus der Konfiguration*</span><span class="sxs-lookup"><span data-stu-id="ebec1-537">*Set the protocol from configuration*</span></span>

### <a name="header-table-size"></a><span data-ttu-id="ebec1-538">`CreateDefaultBuilder` ruft `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` standardmäßig zum Laden der Kestrel-Konfiguration auf.</span><span class="sxs-lookup"><span data-stu-id="ebec1-538">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="ebec1-539">Das folgende *appsettings.json*-Beispiel richtet HTTP/1.1 als Standardverbindungsprotokoll für alle Endpunkte ein:</span><span class="sxs-lookup"><span data-stu-id="ebec1-539">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span> <span data-ttu-id="ebec1-540">Das folgende *appsettings.json*-Beispiel richtet HTTP/1.1 als Verbindungsprotokoll für einen bestimmten Endpunkt ein:</span><span class="sxs-lookup"><span data-stu-id="ebec1-540">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span> <span data-ttu-id="ebec1-541">Protokolle, die in Code angegeben werden, setzen Werte außer Kraft, der durch die Konfiguration festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="ebec1-541">Protocols specified in code override values set by configuration.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="ebec1-542">Transportkonfiguration</span><span class="sxs-lookup"><span data-stu-id="ebec1-542">Transport configuration</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="ebec1-543">Für Projekte, die den Einsatz von Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>) erfordern:</span><span class="sxs-lookup"><span data-stu-id="ebec1-543">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span></span>

<span data-ttu-id="ebec1-544">Fügen Sie für das [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/)-Paket eine Abhängigkeit auf die Projektdatei der App hinzu:</span><span class="sxs-lookup"><span data-stu-id="ebec1-544">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span> <span data-ttu-id="ebec1-545"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> auf `IWebHostBuilder` aufrufen:</span><span class="sxs-lookup"><span data-stu-id="ebec1-545">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> on the `IWebHostBuilder`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="ebec1-546">URL-Präfixe</span><span class="sxs-lookup"><span data-stu-id="ebec1-546">URL prefixes</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="ebec1-547">Bei der Verwendung von `UseUrls`, dem Befehlszeilenargument `--urls`, dem Hostkonfigurationsschlüssel `urls` oder der Umgebungsvariable `ASPNETCORE_URLS` können die URL-Präfixe in den folgenden Formaten vorliegen.</span><span class="sxs-lookup"><span data-stu-id="ebec1-547">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="ebec1-548">Nur HTTP-URL-Präfixe sind gültig.</span><span class="sxs-lookup"><span data-stu-id="ebec1-548">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="ebec1-549">Kestrel unterstützt HTTPS nicht beim Konfigurieren von URL-Bindungen mit `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="ebec1-549">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span> <span data-ttu-id="ebec1-550">IPv4-Adresse mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="ebec1-550">IPv4 address with port number</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="ebec1-551">Bei `0.0.0.0` handelt es sich um einen Sonderfall, für den eine Bindung an alle IPv4-Adressen erfolgt.</span><span class="sxs-lookup"><span data-stu-id="ebec1-551">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="ebec1-552">IPv6-Adresse mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="ebec1-552">IPv6 address with port number</span></span>

<span data-ttu-id="ebec1-553">`[::]` stellt das Äquivalent von IPv6 zu `0.0.0.0` für IPv4 dar.</span><span class="sxs-lookup"><span data-stu-id="ebec1-553">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span> <span data-ttu-id="ebec1-554">Hostname mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="ebec1-554">Host name with port number</span></span> <span data-ttu-id="ebec1-555">Hostnamen, `*` und `+` sind nicht spezifisch.</span><span class="sxs-lookup"><span data-stu-id="ebec1-555">Host names, `*`, and `+`, aren't special.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="ebec1-556">Alle Elemente, die nicht als gültige IP-Adresse oder `localhost` erkannt werden, werden an alle IPv4- und IPv6-IP-Adressen gebunden.</span><span class="sxs-lookup"><span data-stu-id="ebec1-556">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="ebec1-557">Verwenden Sie [HTTP.sys](xref:fundamentals/servers/httpsys) oder einen Reverseproxyserver zum Binden verschiedener Hostnamen an verschiedene ASP.NET Core-Apps auf demselben Port, z.B. IIS, Nginx oder Apache.</span><span class="sxs-lookup"><span data-stu-id="ebec1-557">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

<span data-ttu-id="ebec1-558">Das Hosting in einer Reverseproxykonfiguration erfordert [Hostfilterung](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="ebec1-558">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span> <span data-ttu-id="ebec1-559">`localhost`-Hostname mit Portnummer oder Loopback-IP mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="ebec1-559">Host `localhost` name with port number or loopback IP with port number</span></span> <span data-ttu-id="ebec1-560">Wenn `localhost` angegeben ist, versucht Kestrel, eine Bindung zu IPv4- und IPv6-Loopback-Schnittstellen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="ebec1-560">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="ebec1-561">Wenn der erforderliche Port von einem anderen Dienst auf einer der Loopback-Schnittstellen verwendet wird, tritt beim Starten von Kestrel ein Fehler auf.</span><span class="sxs-lookup"><span data-stu-id="ebec1-561">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="ebec1-562">Wenn eine der Loopback-Schnittstellen aus anderen Gründen nicht verfügbar ist (meistens durch die fehlende Unterstützung von IPv6), protokolliert Kestrel eine Warnung.</span><span class="sxs-lookup"><span data-stu-id="ebec1-562">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

<span data-ttu-id="ebec1-563">Host-Filterung</span><span class="sxs-lookup"><span data-stu-id="ebec1-563">Host filtering</span></span> <span data-ttu-id="ebec1-564">Obwohl Kestrel die Konfiguration basierend auf Präfixe wie `http://example.com:5000` unterstützt, ignoriert Kestrel den Hostnamen weitgehend.</span><span class="sxs-lookup"><span data-stu-id="ebec1-564">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span>

> [!WARNING]
> <span data-ttu-id="ebec1-565">Der Host `localhost` ist ein Sonderfall, der für die Bindung an Loopback-Adressen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="ebec1-565">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="ebec1-566">Jeder Host, der keine explizite IP-Adresse ist, wird an alle öffentlichen IP-Adressen gebunden.</span><span class="sxs-lookup"><span data-stu-id="ebec1-566">Any host other than an explicit IP address binds to all public IP addresses.</span></span>

<span data-ttu-id="ebec1-567">`Host`-Header werden nicht überprüft.</span><span class="sxs-lookup"><span data-stu-id="ebec1-567">`Host` headers aren't validated.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="ebec1-568">Verwenden Sie Middleware zum Filtern von Hosts, um dieses Problem zu umgehen.</span><span class="sxs-lookup"><span data-stu-id="ebec1-568">As a workaround, use Host Filtering Middleware.</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="ebec1-569">Die Middleware zum Filtern von Hosts wird durch das [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering)-Paket bereitgestellt, das implizit für ASP.NET Core-Apps zur Verfügung steht.</span><span class="sxs-lookup"><span data-stu-id="ebec1-569">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span>

<span data-ttu-id="ebec1-570">Die Middleware wird von <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> hinzugefügt, wodurch <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="ebec1-570">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="ebec1-571">Die Middleware zum Filtern von Hosts ist standardmäßig deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="ebec1-571">Host Filtering Middleware is disabled by default.</span></span>

<span data-ttu-id="ebec1-572">Wenn Sie die Middleware aktivieren möchten, definieren Sie einen `AllowedHosts`-Schlüssel in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span><span class="sxs-lookup"><span data-stu-id="ebec1-572">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span>

* <span data-ttu-id="ebec1-573">Der Wert ist eine durch Semikolons getrennte Liste von Hostnamen ohne Portnummern:</span><span class="sxs-lookup"><span data-stu-id="ebec1-573">The value is a semicolon-delimited list of host names without port numbers:</span></span>
* <span data-ttu-id="ebec1-574">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="ebec1-574">*appsettings.json*:</span></span>
* <span data-ttu-id="ebec1-575">[Middleware für weitergeleitete Header](xref:host-and-deploy/proxy-load-balancer) hat auch eine <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>-Option.</span><span class="sxs-lookup"><span data-stu-id="ebec1-575">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span>
* <span data-ttu-id="ebec1-576">Middleware für weitergeleitete Header und Middleware zum Filtern von Hosts besitzen ähnliche Funktionen für unterschiedliche Szenarios.</span><span class="sxs-lookup"><span data-stu-id="ebec1-576">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span>

<span data-ttu-id="ebec1-577">Legen Sie `AllowedHosts` mit Middleware für weitergeleitete Header fest, wenn der `Host`-Header beim Weiterleiten von Anforderungen mit einem Reverseproxyserver oder einem Lastenausgleichsmodul nicht beibehalten wird.</span><span class="sxs-lookup"><span data-stu-id="ebec1-577">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="ebec1-578">Legen Sie `AllowedHosts` mit Middleware zum Filtern von Hosts fest, wenn Kestrel als öffentlicher Edgeserver verwendet oder der `Host`-Header direkt weitergeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="ebec1-578">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>

<span data-ttu-id="ebec1-579">Weitere Informationen zu Middleware für weitergeleitete Header finden Sie unter <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="ebec1-579">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

<span data-ttu-id="ebec1-580">Einführung in Kestrel, dem plattformübergreifenden [Webserver für ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="ebec1-580">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span>

* <span data-ttu-id="ebec1-581">Kestrel ist der Webserver, der standardmäßig in ASP.NET Core-Projektvorlagen enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="ebec1-581">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>
* <span data-ttu-id="ebec1-582">Kestrel unterstützt die folgenden Szenarios:</span><span class="sxs-lookup"><span data-stu-id="ebec1-582">Kestrel supports the following scenarios:</span></span>

<span data-ttu-id="ebec1-583">HTTPS</span><span class="sxs-lookup"><span data-stu-id="ebec1-583">HTTPS</span></span>

<span data-ttu-id="ebec1-584">Nicht transparente Upgrades, die zum Aktivieren von [WebSockets](https://github.com/aspnet/websockets) verwendet werden</span><span class="sxs-lookup"><span data-stu-id="ebec1-584">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>

<span data-ttu-id="ebec1-585">Unix-Sockets für eine hohe Leistung im Hintergrund von Nginx</span><span class="sxs-lookup"><span data-stu-id="ebec1-585">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="ebec1-586">HTTP/2 (außer unter macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="ebec1-586">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="ebec1-587">&dagger;HTTP/2 wird unter macOS in einem zukünftigen Release unterstützt.</span><span class="sxs-lookup"><span data-stu-id="ebec1-587">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="ebec1-588">Kestrel wird auf allen Plattformen und für alle Versionen unterstützt, die .NET Core unterstützt.</span><span class="sxs-lookup"><span data-stu-id="ebec1-588">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="ebec1-589">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ebec1-589">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span> <span data-ttu-id="ebec1-590">HTTP/2-Unterstützung</span><span class="sxs-lookup"><span data-stu-id="ebec1-590">HTTP/2 support</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

> [!NOTE]
> <span data-ttu-id="ebec1-591">[HTTP/2](https://httpwg.org/specs/rfc7540.html) ist für ASP.NET Core-Apps verfügbar, wenn die folgenden Basisanforderungen erfüllt sind:</span><span class="sxs-lookup"><span data-stu-id="ebec1-591">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="ebec1-592">Betriebssystem&dagger;</span><span class="sxs-lookup"><span data-stu-id="ebec1-592">Operating system&dagger;</span></span>

<span data-ttu-id="ebec1-593">Windows Server 2016/Windows 10 oder höher&Dagger;</span><span class="sxs-lookup"><span data-stu-id="ebec1-593">Windows Server 2016/Windows 10 or later&Dagger;</span></span> <span data-ttu-id="ebec1-594">Linux mit OpenSSL 1.0.2 oder höher (z.B. Ubuntu 16.04 oder höher)</span><span class="sxs-lookup"><span data-stu-id="ebec1-594">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

> [!NOTE]
> <span data-ttu-id="ebec1-595">Zielframework: .NET Core 2.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="ebec1-595">Target framework: .NET Core 2.2 or later</span></span>


### <a name="configureiconfiguration"></a><span data-ttu-id="ebec1-596">[ALPN](https://tools.ietf.org/html/rfc7301#section-3)-Verbindung (Application-Layer Protocol Negotiation)</span><span class="sxs-lookup"><span data-stu-id="ebec1-596">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>

<span data-ttu-id="ebec1-597">TLS 1.2-Verbindung oder höher</span><span class="sxs-lookup"><span data-stu-id="ebec1-597">TLS 1.2 or later connection</span></span> <span data-ttu-id="ebec1-598">&dagger;HTTP/2 wird unter macOS in einem zukünftigen Release unterstützt.</span><span class="sxs-lookup"><span data-stu-id="ebec1-598">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="ebec1-599">&Dagger;Kestrel bietet eingeschränkte Unterstützung für HTTP/2 unter Windows Server 2012 R2 und Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="ebec1-599">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span>

<span data-ttu-id="ebec1-600">Die Unterstützung ist eingeschränkt, weil die Liste der unterstützten TLS-Verschlüsselungssammlungen unter diesen Betriebssystemen begrenzt ist.</span><span class="sxs-lookup"><span data-stu-id="ebec1-600">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span>

<span data-ttu-id="ebec1-601">Zum Sichern von TLS-Verbindungen ist möglicherweise ein durch einen Elliptic Curve Digital Signature Algorithm (ECDSA) generiertes Zertifikat erforderlich.</span><span class="sxs-lookup"><span data-stu-id="ebec1-601">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

* <span data-ttu-id="ebec1-602">Wenn eine HTTP/2-Verbindung hergestellt wurde, meldet [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)`HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="ebec1-602">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="ebec1-603">HTTP/2 ist standardmäßig deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="ebec1-603">HTTP/2 is disabled by default.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="ebec1-604">Weitere Informationen zur Konfiguration finden Sie in den Abschnitten [Kestrel-Optionen](#kestrel-options) und [ListenOptions.Protocols](#listenoptionsprotocols).</span><span class="sxs-lookup"><span data-stu-id="ebec1-604">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

* <span data-ttu-id="ebec1-605">Verwenden von Kestrel mit einem Reverseproxy</span><span class="sxs-lookup"><span data-stu-id="ebec1-605">When to use Kestrel with a reverse proxy</span></span>
* <span data-ttu-id="ebec1-606">Kestrel kann eigenständig oder mit einem *Reverseproxyserver* wie z.B. [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="ebec1-606">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span>
* <span data-ttu-id="ebec1-607">Ein Reverseproxyserver empfängt HTTP-Anforderungen aus dem Netzwerk und leitet diese an Kestrel weiter.</span><span class="sxs-lookup"><span data-stu-id="ebec1-607">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span> <span data-ttu-id="ebec1-608">Kestrel bei Verwendung als Webserver mit direkter Internetverbindung:</span><span class="sxs-lookup"><span data-stu-id="ebec1-608">Kestrel used as an edge (Internet-facing) web server:</span></span>
* <span data-ttu-id="ebec1-609">Kestrel kommuniziert direkt und ohne Reverseproxyserver mit dem Internet</span><span class="sxs-lookup"><span data-stu-id="ebec1-609">Kestrel communicates directly with the Internet without a reverse proxy server</span></span>
* <span data-ttu-id="ebec1-610">Kestrel bei Verwendung in einer Reverseproxykonfiguration:</span><span class="sxs-lookup"><span data-stu-id="ebec1-610">Kestrel used in a reverse proxy configuration:</span></span>
* <span data-ttu-id="ebec1-611">Kestrel kommuniziert indirekt mit dem Internet über einen Reverseproxyserver wie IIS, Nginx oder Apache</span><span class="sxs-lookup"><span data-stu-id="ebec1-611">Kestrel communicates indirectly with the Internet through a reverse proxy server, such as IIS, Nginx, or Apache</span></span>
* <span data-ttu-id="ebec1-612">Jede der beiden Konfigurationen – mit oder ohne einen Reverseproxyserver – stellt eine unterstützte Hostingkonfiguration dar.</span><span class="sxs-lookup"><span data-stu-id="ebec1-612">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>
* <span data-ttu-id="ebec1-613">Bei Verwendung als Edgeserver ohne Reverseproxyserver unterstützt Kestrel die gemeinsame Nutzung der gleichen IP-Adresse und des gleichen Ports durch mehrere Prozesse nicht.</span><span class="sxs-lookup"><span data-stu-id="ebec1-613">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span>

<span data-ttu-id="ebec1-614">Wenn Kestrel für das Lauschen an einem Port konfiguriert ist, verarbeitet Kestrel den gesamten Datenverkehr für diesen Port unabhängig von den `Host`-Headern der Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="ebec1-614">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="ebec1-615">Ein Reverseproxy, der Ports freigeben kann, kann Anforderungen an Kestrel über eine eindeutige IP und einen eindeutigen Port weiterleiten.</span><span class="sxs-lookup"><span data-stu-id="ebec1-615">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="ebec1-616">Auch wenn kein Reverseproxyserver erforderlich ist, kann die Verwendung eines solchen empfehlenswert sein.</span><span class="sxs-lookup"><span data-stu-id="ebec1-616">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

* <span data-ttu-id="ebec1-617">Für einen Reverseproxy gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="ebec1-617">A reverse proxy:</span></span>
* <span data-ttu-id="ebec1-618">Er kann die verfügbar gemachten öffentlichen Oberflächen der von ihm gehosteten Apps einschränken.</span><span class="sxs-lookup"><span data-stu-id="ebec1-618">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="ebec1-619">Er stellt eine zusätzliche Ebene für Konfiguration und Schutz bereit.</span><span class="sxs-lookup"><span data-stu-id="ebec1-619">Provide an additional layer of configuration and defense.</span></span>

<span data-ttu-id="ebec1-620">Er lässt sich besser in die vorhandene Infrastruktur integrieren.</span><span class="sxs-lookup"><span data-stu-id="ebec1-620">Might integrate better with existing infrastructure.</span></span>

<span data-ttu-id="ebec1-621">Er vereinfacht die Konfiguration von Lastenausgleich und sicherer Kommunikation (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="ebec1-621">Simplify load balancing and secure communication (HTTPS) configuration.</span></span>

<a name="configuration"></a>

<span data-ttu-id="ebec1-622">Nur der Reverseproxyserver erfordert ein X.509-Zertifikat, und dieser Server kann mit den Servern der App im internen Netzwerk über einfaches HTTP kommunizieren.</span><span class="sxs-lookup"><span data-stu-id="ebec1-622">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

<span data-ttu-id="ebec1-623">Das Hosting in einer Reverseproxykonfiguration erfordert [Hostfilterung](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="ebec1-623">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span> <span data-ttu-id="ebec1-624">Verwenden von Kestrel in ASP.NET Core-Apps</span><span class="sxs-lookup"><span data-stu-id="ebec1-624">How to use Kestrel in ASP.NET Core apps</span></span> <span data-ttu-id="ebec1-625">Das Paket [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) ist im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) enthalten.</span><span class="sxs-lookup"><span data-stu-id="ebec1-625">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="ebec1-626">ASP.NET Core-Projektvorlagen verwenden Kestrel standardmäßig.</span><span class="sxs-lookup"><span data-stu-id="ebec1-626">ASP.NET Core project templates use Kestrel by default.</span></span>

* <span data-ttu-id="ebec1-627">Der Vorlagencode in *Program.cs* ruft <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> auf, wodurch <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> im Hintergrund aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="ebec1-627">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>
* <span data-ttu-id="ebec1-628">Weitere Informationen zum `CreateDefaultBuilder` und zum Erstellen des Hosts finden Sie im Abschnitt *Einrichten eines Hosts* von <xref:fundamentals/host/web-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="ebec1-628">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="ebec1-629">Um zusätzliche Konfiguration nach dem Aufruf von `CreateDefaultBuilder` bereitzustellen, verwenden Sie `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="ebec1-629">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span> <span data-ttu-id="ebec1-630">Wenn die App `CreateDefaultBuilder` nicht aufruft, um den Host einzurichten, rufen Sie <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **auf**, bevor Sie `ConfigureKestrel` aufrufen:</span><span class="sxs-lookup"><span data-stu-id="ebec1-630">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="ebec1-631">Kestrel-Optionen</span><span class="sxs-lookup"><span data-stu-id="ebec1-631">Kestrel options</span></span>

* <span data-ttu-id="ebec1-632">Der Kestrel-Webserver verfügt über einschränkende Konfigurationsoptionen, die besonders nützlich bei Bereitstellungen mit Internetzugriff sind.</span><span class="sxs-lookup"><span data-stu-id="ebec1-632">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span> <span data-ttu-id="ebec1-633">Legen Sie Einschränkungen für die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits>-Eigenschaft der <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>-Klasse fest.</span><span class="sxs-lookup"><span data-stu-id="ebec1-633">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span>
* <span data-ttu-id="ebec1-634">Die `Limits`-Eigenschaft enthält eine Instanz der <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>-Klasse.</span><span class="sxs-lookup"><span data-stu-id="ebec1-634">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span> <span data-ttu-id="ebec1-635">In den folgenden Beispielen wird der <xref:Microsoft.AspNetCore.Server.Kestrel.Core>-Namespace verwendet:</span><span class="sxs-lookup"><span data-stu-id="ebec1-635">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>
* <span data-ttu-id="ebec1-636">Kestrel-Optionen, die in den folgenden Beispielen in C#-Code konfiguriert sind, können auch mit einem [Konfigurationsanbieter](xref:fundamentals/configuration/index) festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="ebec1-636">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="ebec1-637">Beispielsweise kann der Dateikonfigurationsanbieter die Kestrel-Konfiguration aus einer *appsettings.json*- oder *appsettings.{Umgebung}.json*-Datei laden:</span><span class="sxs-lookup"><span data-stu-id="ebec1-637">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>
* <span data-ttu-id="ebec1-638">Verwenden Sie **einen** der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="ebec1-638">Use **one** of the following approaches:</span></span> <span data-ttu-id="ebec1-639">Konfigurieren Sie Kestrel in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ebec1-639">Configure Kestrel in `Startup.ConfigureServices`:</span></span> <span data-ttu-id="ebec1-640">Fügen Sie eine Instanz von `IConfiguration` in die `Startup`-Klasse ein.</span><span class="sxs-lookup"><span data-stu-id="ebec1-640">Inject an instance of `IConfiguration` into the `Startup` class.</span></span>
* <span data-ttu-id="ebec1-641">Im folgenden Beispiel wird davon ausgegangen, dass die eingefügte Konfiguration der `Configuration`-Eigenschaft zugewiesen wird.</span><span class="sxs-lookup"><span data-stu-id="ebec1-641">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
* <span data-ttu-id="ebec1-642">Laden Sie in `Startup.ConfigureServices` den Abschnitt `Kestrel` der Konfiguration in die Konfiguration von Kestrel:</span><span class="sxs-lookup"><span data-stu-id="ebec1-642">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>
* <span data-ttu-id="ebec1-643">Konfigurieren Sie Kestrel beim Erstellen des Hosts:</span><span class="sxs-lookup"><span data-stu-id="ebec1-643">Configure Kestrel when building the host:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="ebec1-644">Laden Sie in *Program.cs* den Abschnitt `Kestrel` der Konfiguration in die Konfiguration von Kestrel:</span><span class="sxs-lookup"><span data-stu-id="ebec1-644">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

* <span data-ttu-id="ebec1-645">Beide vorangehenden Ansätze funktionieren mit jedem [Konfigurationsanbieter](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="ebec1-645">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>
* <span data-ttu-id="ebec1-646">Keep-Alive-Timeout</span><span class="sxs-lookup"><span data-stu-id="ebec1-646">Keep-alive timeout</span></span> <span data-ttu-id="ebec1-647">Ruft das [Keep-Alive-Timeout](https://tools.ietf.org/html/rfc7230#section-6.5) ab oder legt es fest.</span><span class="sxs-lookup"><span data-stu-id="ebec1-647">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="ebec1-648">Standardwert: 2 Minuten.</span><span class="sxs-lookup"><span data-stu-id="ebec1-648">Defaults to 2 minutes.</span></span>
* <span data-ttu-id="ebec1-649">Maximale Anzahl der Clientverbindungen</span><span class="sxs-lookup"><span data-stu-id="ebec1-649">Maximum client connections</span></span> <span data-ttu-id="ebec1-650">Die maximale Anzahl von gleichzeitig geöffneten TCP-Verbindungen kann mithilfe von folgendem Code für die gesamte App festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="ebec1-650">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

<span data-ttu-id="ebec1-651">Es gibt einen separaten Grenzwert für Verbindungen, die von HTTP oder HTTPS auf ein anderes Protokoll aktualisiert wurden (z.B. auf eine WebSockets-Anforderung).</span><span class="sxs-lookup"><span data-stu-id="ebec1-651">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span>

<span data-ttu-id="ebec1-652">Nachdem eine Verbindung aktualisiert wurde, zählt diese nicht mehr für den `MaxConcurrentConnections`-Grenzwert.</span><span class="sxs-lookup"><span data-stu-id="ebec1-652">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span> <span data-ttu-id="ebec1-653">Die maximale Anzahl von Verbindungen ist standardmäßig nicht begrenzt (NULL).</span><span class="sxs-lookup"><span data-stu-id="ebec1-653">The maximum number of connections is unlimited (null) by default.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="ebec1-654">Maximale Größe des Anforderungstexts</span><span class="sxs-lookup"><span data-stu-id="ebec1-654">Maximum request body size</span></span>

<span data-ttu-id="ebec1-655">Die maximale Größe des Anforderungstexts beträgt standardmäßig 30.000.000 Byte, also ungefähr 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="ebec1-655">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="ebec1-656">Die empfohlene Methode zur Außerkraftsetzung des Grenzwerts in einer ASP.NET Core-MVC-App besteht im Verwenden des <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>-Attributs in einer Aktionsmethode:</span><span class="sxs-lookup"><span data-stu-id="ebec1-656">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span> <span data-ttu-id="ebec1-657">Im folgenden Beispiel wird veranschaulicht, wie die Einschränkungen auf jeder Anforderung für die App konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="ebec1-657">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

<span data-ttu-id="ebec1-658">Außer Kraft setzen der Einstellung für eine bestimmte Anforderung in Middleware:</span><span class="sxs-lookup"><span data-stu-id="ebec1-658">Override the setting on a specific request in middleware:</span></span> <span data-ttu-id="ebec1-659">Eine Ausnahme wird ausgelöst, wenn die App den Grenzwert einer Anforderung konfiguriert, nachdem die App bereits mit dem Lesen der Anforderung begonnen hat.</span><span class="sxs-lookup"><span data-stu-id="ebec1-659">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span>

<span data-ttu-id="ebec1-660">Es gibt eine `IsReadOnly`-Eigenschaft, die angibt, wenn sich die `MaxRequestBodySize`-Eigenschaft im schreibgeschützten Zustand befindet, also wenn der Grenzwert nicht mehr konfiguriert werden kann.</span><span class="sxs-lookup"><span data-stu-id="ebec1-660">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

* <span data-ttu-id="ebec1-661">Wenn eine App [prozessextern](xref:host-and-deploy/iis/index#out-of-process-hosting-model) hinter dem [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) ausgeführt wird, ist das Größenlimit von Kestrel für Anforderungstext deaktiviert, weil IIS dieses Limit bereits festlegt.</span><span class="sxs-lookup"><span data-stu-id="ebec1-661">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span> <span data-ttu-id="ebec1-662">Minimale Datenrate des Anforderungstexts</span><span class="sxs-lookup"><span data-stu-id="ebec1-662">Minimum request body data rate</span></span> <span data-ttu-id="ebec1-663">Kestrel überprüft sekündlich, ob Daten mit der angegebenen Rate in Bytes/Sekunde eingehen.</span><span class="sxs-lookup"><span data-stu-id="ebec1-663">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span>
* <span data-ttu-id="ebec1-664">Wenn die Rate den mindestens erforderlichen Wert unterschreitet, wird für die Verbindung wegen Timeout getrennt. Bei der Toleranzperiode handelt es sich um die Zeitspanne, die Kestrel dem Client gewährt, um die Senderate auf den mindestens erforderlichen Wert zu erhöhen. Die Rate wird währenddessen nicht überprüft.</span><span class="sxs-lookup"><span data-stu-id="ebec1-664">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="ebec1-665">Diese Toleranzperiode beugt dem Trennen von Verbindungen vor, die Daten aufgrund eines langsamen TCP-Starts anfänglich mit einer niedrigen Rate senden.</span><span class="sxs-lookup"><span data-stu-id="ebec1-665">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        });
```

### <a name="connection-logging"></a><span data-ttu-id="ebec1-666">Die mindestens erforderliche Rate beträgt standardmäßig 240 Bytes/Sekunde mit einer Toleranzperiode von 5 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="ebec1-666">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="ebec1-667">Für die Antwort gilt ebenfalls eine mindestens erforderliche Rate.</span><span class="sxs-lookup"><span data-stu-id="ebec1-667">A minimum rate also applies to the response.</span></span> <span data-ttu-id="ebec1-668">Der Code zum Festlegen des Grenzwerts für Anforderung und Antwort ist abgesehen von `RequestBody` oder `Response` in den Namen von Eigenschaften und Schnittstelle identisch.</span><span class="sxs-lookup"><span data-stu-id="ebec1-668">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span> <span data-ttu-id="ebec1-669">In diesem Beispiel wird veranschaulicht, wie Sie die mindestens erforderlichen Datenraten in *Program.cs* konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="ebec1-669">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span> <span data-ttu-id="ebec1-670">Außer Kraft setzen des minimalen Ratenlimits pro Anforderung in Middleware:</span><span class="sxs-lookup"><span data-stu-id="ebec1-670">Override the minimum rate limits per request in middleware:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="ebec1-671">Keines dieser Ratenfeatures, auf die im vorherigen Beispiel verwiesen wird, ist in `HttpContext.Features` für HTTP/2-Anforderungen vorhanden, da die Änderung von Ratenlimits für jede Anforderung aufgrund der Unterstützung für Anforderungsmultiplexing des Protokolls nicht für HTTP/2 unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="ebec1-671">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span>

<span data-ttu-id="ebec1-672">Serverweite Ratenlimits, die über `KestrelServerOptions.Limits` konfiguriert sind, gelten auch für HTTP/1.x- und HTTP/2-Verbindungen.</span><span class="sxs-lookup"><span data-stu-id="ebec1-672">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="ebec1-673">Timeout für Anforderungsheader</span><span class="sxs-lookup"><span data-stu-id="ebec1-673">Request headers timeout</span></span> <span data-ttu-id="ebec1-674">Ruft die maximale Zeitspanne ab, während der der Server Anforderungsheader empfängt, oder legt diese fest.</span><span class="sxs-lookup"><span data-stu-id="ebec1-674">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="ebec1-675">Der Standardwert beträgt 30 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="ebec1-675">Defaults to 30 seconds.</span></span>

<span data-ttu-id="ebec1-676">Maximale Datenströme pro Verbindung</span><span class="sxs-lookup"><span data-stu-id="ebec1-676">Maximum streams per connection</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="ebec1-677">`Http2.MaxStreamsPerConnection` schränkt die Anzahl gleichzeitiger Anforderungsdatenströme pro HTTP/2-Verbindung ein.</span><span class="sxs-lookup"><span data-stu-id="ebec1-677">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="ebec1-678">Überschüssige Datenströme werden zurückgewiesen.</span><span class="sxs-lookup"><span data-stu-id="ebec1-678">Excess streams are refused.</span></span>
* <span data-ttu-id="ebec1-679">Der Standardwert ist 100.</span><span class="sxs-lookup"><span data-stu-id="ebec1-679">The default value is 100.</span></span> 

### <a name="port-0"></a><span data-ttu-id="ebec1-680">Größe der Headertabelle</span><span class="sxs-lookup"><span data-stu-id="ebec1-680">Header table size</span></span>

<span data-ttu-id="ebec1-681">Der HPACK-Decoder dekomprimiert HTTP-Header für HTTP/2-Verbindungen.</span><span class="sxs-lookup"><span data-stu-id="ebec1-681">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="ebec1-682">`Http2.HeaderTableSize` schränkt die Größe der Headerkomprimierungstabelle ein, die der HPACK-Decoder verwendet.</span><span class="sxs-lookup"><span data-stu-id="ebec1-682">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="ebec1-683">Der Wert wird in Oktetten bereitgestellt und muss größer als null (0) sein.</span><span class="sxs-lookup"><span data-stu-id="ebec1-683">The value is provided in octets and must be greater than zero (0).</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="ebec1-684">Der Standardwert ist 4096.</span><span class="sxs-lookup"><span data-stu-id="ebec1-684">The default value is 4096.</span></span>

<span data-ttu-id="ebec1-685">Maximale Framegröße</span><span class="sxs-lookup"><span data-stu-id="ebec1-685">Maximum frame size</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="ebec1-686">`Http2.MaxFrameSize` gibt die maximale Größe der zu empfangenden HTTP/2-Verbindungsframenutzlast an.</span><span class="sxs-lookup"><span data-stu-id="ebec1-686">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span>
* <span data-ttu-id="ebec1-687">Der Wert wird in Oktetten bereitgestellt und muss zwischen 2^14 (16.384) und 2^24-1 (16.777.215) liegen.</span><span class="sxs-lookup"><span data-stu-id="ebec1-687">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>
* <span data-ttu-id="ebec1-688">Der Standardwert ist 2^14 (16.384).</span><span class="sxs-lookup"><span data-stu-id="ebec1-688">The default value is 2^14 (16,384).</span></span>

<span data-ttu-id="ebec1-689">Maximale Größe des Anforderungsheaders</span><span class="sxs-lookup"><span data-stu-id="ebec1-689">Maximum request header size</span></span> <span data-ttu-id="ebec1-690">`Http2.MaxRequestHeaderFieldSize` gibt die maximal zulässige Größe in Oktetten der Anforderungsheaderwerte an.</span><span class="sxs-lookup"><span data-stu-id="ebec1-690">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span>

* <span data-ttu-id="ebec1-691">Dieser Grenzwert gilt zusammen für den Namen und Wert in komprimierten und nicht komprimierten Darstellungen.</span><span class="sxs-lookup"><span data-stu-id="ebec1-691">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span>
* <span data-ttu-id="ebec1-692">Der Wert muss größer als 0 (null) sein.</span><span class="sxs-lookup"><span data-stu-id="ebec1-692">The value must be greater than zero (0).</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="ebec1-693">Der Standardwert ist 8.192.</span><span class="sxs-lookup"><span data-stu-id="ebec1-693">The default value is 8,192.</span></span>

<span data-ttu-id="ebec1-694">Anfangsfenstergröße der Verbindung</span><span class="sxs-lookup"><span data-stu-id="ebec1-694">Initial connection window size</span></span> <span data-ttu-id="ebec1-695">`Http2.InitialConnectionWindowSize` gibt die maximalen Anforderungstextdaten in Byte an, die der Server auf einmal für alle Anforderungen (Streams) pro Verbindung aggregiert.</span><span class="sxs-lookup"><span data-stu-id="ebec1-695">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="ebec1-696">Anforderungen werden auch durch `Http2.InitialStreamWindowSize` beschränkt.</span><span class="sxs-lookup"><span data-stu-id="ebec1-696">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span>

<span data-ttu-id="ebec1-697">Der Wert muss größer als oder gleich 65.535 und kleiner als 2^31 (2.147.483.648) sein.</span><span class="sxs-lookup"><span data-stu-id="ebec1-697">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span> <span data-ttu-id="ebec1-698">Der Standardwert ist 128 KB (131.072).</span><span class="sxs-lookup"><span data-stu-id="ebec1-698">The default value is 128 KB (131,072).</span></span>

| <span data-ttu-id="ebec1-699">Anfangsfenstergröße des Streams</span><span class="sxs-lookup"><span data-stu-id="ebec1-699">Initial stream window size</span></span> | <span data-ttu-id="ebec1-700">`Http2.InitialStreamWindowSize` gibt die maximalen Anforderungstextdaten in Byte an, die der Server auf einmal pro Anforderung (Stream) puffert.</span><span class="sxs-lookup"><span data-stu-id="ebec1-700">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="ebec1-701">Anforderungen werden auch durch `Http2.InitialStreamWindowSize` beschränkt.</span><span class="sxs-lookup"><span data-stu-id="ebec1-701">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="ebec1-702">Der Wert muss größer als oder gleich 65.535 und kleiner als 2^31 (2.147.483.648) sein.</span><span class="sxs-lookup"><span data-stu-id="ebec1-702">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span> |
| `Http2`                    | <span data-ttu-id="ebec1-703">Der Standardwert ist 96 KB (98.304).</span><span class="sxs-lookup"><span data-stu-id="ebec1-703">The default value is 96 KB (98,304).</span></span> <span data-ttu-id="ebec1-704">Synchrone E/A-Vorgänge</span><span class="sxs-lookup"><span data-stu-id="ebec1-704">Synchronous I/O</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="ebec1-705"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> steuert, ob für Anforderung und Antwort synchrone E/A-Vorgänge zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="ebec1-705"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="ebec1-706">Der Standardwert ist `true`.</span><span class="sxs-lookup"><span data-stu-id="ebec1-706">The  default value is `true`.</span></span> |

<span data-ttu-id="ebec1-707">Sehr viele blockierende synchrone E/A-Vorgänge können zu einem Ressourcenmangel im Threadpool führen, wodurch die App nicht mehr reagiert.</span><span class="sxs-lookup"><span data-stu-id="ebec1-707">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span>

<span data-ttu-id="ebec1-708">Aktivieren Sie `AllowSynchronousIO` nur bei Verwendung einer Bibliothek, die asynchrone E/A-Vorgänge nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="ebec1-708">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

* <span data-ttu-id="ebec1-709">Das folgende Beispiel aktiviert synchrone E/A-Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="ebec1-709">The following example enables synchronous I/O:</span></span>
* <span data-ttu-id="ebec1-710">Weitere Informationen zu anderen Kestrel-Optionen und -Einschränkungen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="ebec1-710">For information about other Kestrel options and limits, see:</span></span>
* <span data-ttu-id="ebec1-711">Endpunktkonfiguration</span><span class="sxs-lookup"><span data-stu-id="ebec1-711">Endpoint configuration</span></span>
* <span data-ttu-id="ebec1-712">Standardmäßig wird ASP.NET Core an Folgendes gebunden:</span><span class="sxs-lookup"><span data-stu-id="ebec1-712">By default, ASP.NET Core binds to:</span></span>
  * <span data-ttu-id="ebec1-713">`https://localhost:5001` (wenn ein lokales Entwicklungszertifikat vorhanden ist)</span><span class="sxs-lookup"><span data-stu-id="ebec1-713">`https://localhost:5001` (when a local development certificate is present)</span></span>
  * <span data-ttu-id="ebec1-714">Verwenden Sie Folgendes zum Angeben der URLs:</span><span class="sxs-lookup"><span data-stu-id="ebec1-714">Specify URLs using the:</span></span>
* <span data-ttu-id="ebec1-715">Die Umgebungsvariable `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="ebec1-715">`ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="ebec1-716">Das Befehlszeilenargument `--urls`</span><span class="sxs-lookup"><span data-stu-id="ebec1-716">`--urls` command-line argument.</span></span>

<span data-ttu-id="ebec1-717">Den Hostkonfigurationsschlüssel `urls`</span><span class="sxs-lookup"><span data-stu-id="ebec1-717">`urls` host configuration key.</span></span> <span data-ttu-id="ebec1-718">Die Erweiterungsmethode `UseUrls`</span><span class="sxs-lookup"><span data-stu-id="ebec1-718">`UseUrls` extension method.</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="ebec1-719">Der Wert, der mit diesen Ansätzen angegeben wird, kann mindestens ein HTTP- oder HTTPS-Endpunkt sein (HTTPS wenn ein Standardzertifikat verfügbar ist).</span><span class="sxs-lookup"><span data-stu-id="ebec1-719">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.ConnectionAdapters.Add(new TlsFilterAdapter());
    });
});
```

```csharp
private class TlsFilterAdapter : IConnectionAdapter
{
    public bool IsHttps => false;

    public Task<IAdaptedConnection> OnConnectionAsync(ConnectionAdapterContext context)
    {
        var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

        // Throw NotSupportedException for any cipher algorithm that the app doesn't
        // wish to support. Alternatively, define and compare
        // ITlsHandshakeFeature.CipherAlgorithm to a list of acceptable cipher
        // suites.
        //
        // No encryption is used with a CipherAlgorithmType.Null cipher algorithm.
        if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
        {
            throw new NotSupportedException("Prohibited cipher: " + tlsFeature.CipherAlgorithm);
        }

        return Task.FromResult<IAdaptedConnection>(new AdaptedConnection(context.ConnectionStream));
    }

    private class AdaptedConnection : IAdaptedConnection
    {
        public AdaptedConnection(Stream adaptedStream)
        {
            ConnectionStream = adaptedStream;
        }

        public Stream ConnectionStream { get; }

        public void Dispose()
        {
        }
    }
}
```

<span data-ttu-id="ebec1-720">Konfigurieren Sie den Wert als eine durch Semikolons getrennte Liste (z.B. `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="ebec1-720">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="ebec1-721">Weitere Informationen zu diesen Ansätzen finden Sie unter [Server-URLs](xref:fundamentals/host/web-host#server-urls) und [Außerkraftsetzen der Konfiguration](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="ebec1-721">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="ebec1-722">Ein Entwicklungszertifikat wird erstellt:</span><span class="sxs-lookup"><span data-stu-id="ebec1-722">A development certificate is created:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="ebec1-723">Wenn das [.NET Core SDK](/dotnet/core/sdk) installiert wird.</span><span class="sxs-lookup"><span data-stu-id="ebec1-723">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1AndHttp2"
      }
    }
  }
}
```

<span data-ttu-id="ebec1-724">Das [dev-cert-Tool](xref:aspnetcore-2.1#https) wird zum Erstellen eines Zertifikats verwendet.</span><span class="sxs-lookup"><span data-stu-id="ebec1-724">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="ebec1-725">Einige Browser erfordern, dass Sie die explizite Berechtigung erteilen, dem lokalen Entwicklungszertifikat zu vertrauen.</span><span class="sxs-lookup"><span data-stu-id="ebec1-725">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="ebec1-726">Projektvorlagen konfigurieren Apps, damit sie standardmäßig auf HTTPS ausgeführt werden und die [HTTPS-Umleitung und HSTS-Unterstützung](xref:security/enforcing-ssl) enthalten.</span><span class="sxs-lookup"><span data-stu-id="ebec1-726">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span> <span data-ttu-id="ebec1-727">Rufen Sie die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>- oder <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>-Methode unter <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> auf, um URL-Präfixe und Ports für Kestrel zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="ebec1-727">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

* <span data-ttu-id="ebec1-728">`UseUrls`, das Befehlszeilenargument `--urls`, der Hostkonfigurationsschlüssel `urls` und die Umgebungsvariable `ASPNETCORE_URLS` funktionieren ebenfalls, verfügen jedoch über Einschränkungen, die im Verlauf dieses Abschnitts erläutert werden (Ein Standardzertifikat muss für die HTTPS-Endpunktkonfiguration verfügbar sein).</span><span class="sxs-lookup"><span data-stu-id="ebec1-728">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>
* <span data-ttu-id="ebec1-729">`KestrelServerOptions`-Konfiguration:</span><span class="sxs-lookup"><span data-stu-id="ebec1-729">`KestrelServerOptions` configuration:</span></span>

<span data-ttu-id="ebec1-730">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="ebec1-730">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

* <span data-ttu-id="ebec1-731">Gibt die Konfiguration von `Action` zum Ausführen von jedem angegebenen Endpunkt an.</span><span class="sxs-lookup"><span data-stu-id="ebec1-731">Specifies a configuration `Action` to run for each specified endpoint.</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="ebec1-732">Mehrmalige Aufrufe von `ConfigureEndpointDefaults` ersetzen vorherige Instanzen von `Action` mit der zuletzt angegebenen `Action`.</span><span class="sxs-lookup"><span data-stu-id="ebec1-732">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a><span data-ttu-id="ebec1-733">Auf Endpunkte, die durch Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **vor** dem Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> erstellt werden, werden die Standardwerte nicht angewendet.</span><span class="sxs-lookup"><span data-stu-id="ebec1-733">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

<span data-ttu-id="ebec1-734">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="ebec1-734">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="ebec1-735">Gibt die Konfiguration von `Action` zum Ausführen von jedem HTTPS-Endpunkt an.</span><span class="sxs-lookup"><span data-stu-id="ebec1-735">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="ebec1-736">Mehrmalige Aufrufe von `ConfigureHttpsDefaults` ersetzen vorherige Instanzen von `Action` mit der zuletzt angegebenen `Action`.</span><span class="sxs-lookup"><span data-stu-id="ebec1-736">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

* <span data-ttu-id="ebec1-737">Auf Endpunkte, die durch Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **vor** dem Aufrufen von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> erstellt werden, werden die Standardwerte nicht angewendet.</span><span class="sxs-lookup"><span data-stu-id="ebec1-737">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="ebec1-738">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="ebec1-738">Configure(IConfiguration)</span></span>

* <span data-ttu-id="ebec1-739">Erstellt ein Konfigurationsladeprogramm für das Einrichten von Kestrel, was <xref:Microsoft.Extensions.Configuration.IConfiguration> als Eingabe erfordert.</span><span class="sxs-lookup"><span data-stu-id="ebec1-739">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="ebec1-740">Die Konfiguration muss auf den Konfigurationsabschnitt für Kestrel festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="ebec1-740">The configuration must be scoped to the configuration section for Kestrel.</span></span>

* <span data-ttu-id="ebec1-741">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="ebec1-741">ListenOptions.UseHttps</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="ebec1-742">Konfiguriert Kestrel zur Verwendung von HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ebec1-742">Configure Kestrel to use HTTPS.</span></span> <span data-ttu-id="ebec1-743">`ListenOptions.UseHttps`-Erweiterungen:</span><span class="sxs-lookup"><span data-stu-id="ebec1-743">`ListenOptions.UseHttps` extensions:</span></span> <span data-ttu-id="ebec1-744">`UseHttps`: Hiermit wird Kestrel zur Verwendung von HTTPS mit dem Standardzertifikat konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="ebec1-744">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span>

  > [!WARNING]
  > <span data-ttu-id="ebec1-745">Löst eine Ausnahme aus, wenn kein Standardzertifikat konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="ebec1-745">Throws an exception if no default certificate is configured.</span></span>

* <span data-ttu-id="ebec1-746">`ListenOptions.UseHttps`-Parameter:</span><span class="sxs-lookup"><span data-stu-id="ebec1-746">`ListenOptions.UseHttps` parameters:</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="ebec1-747">`filename` entspricht dem Pfad und Dateinamen einer Zertifikatdatei relativ zu dem Verzeichnis, das die Inhaltsdateien der App enthält.</span><span class="sxs-lookup"><span data-stu-id="ebec1-747">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span> <span data-ttu-id="ebec1-748">`password` ist das für den Zugriff auf die X.509-Zertifikatsdaten erforderliche Kennwort.</span><span class="sxs-lookup"><span data-stu-id="ebec1-748">`password` is the password required to access the X.509 certificate data.</span></span> <span data-ttu-id="ebec1-749">`configureOptions` ist eine `Action` zum Konfigurieren von `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="ebec1-749">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="ebec1-750">Gibt `ListenOptions` zurück.</span><span class="sxs-lookup"><span data-stu-id="ebec1-750">Returns the `ListenOptions`.</span></span>

<span data-ttu-id="ebec1-751">`storeName` ist der Zertifikatspeicher, aus dem das Zertifikat geladen wird.</span><span class="sxs-lookup"><span data-stu-id="ebec1-751">`storeName` is the certificate store from which to load the certificate.</span></span> <span data-ttu-id="ebec1-752">`subject` ist der Name des Antragstellers für das Zertifikat.</span><span class="sxs-lookup"><span data-stu-id="ebec1-752">`subject` is the subject name for the certificate.</span></span> <span data-ttu-id="ebec1-753">`allowInvalid` gibt an, ob ungültige Zertifikate berücksichtigt werden sollten, z.B. selbstsignierte Zertifikate.</span><span class="sxs-lookup"><span data-stu-id="ebec1-753">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span> <span data-ttu-id="ebec1-754">`location` ist der Speicherort, aus dem das Zertifikat geladen wird.</span><span class="sxs-lookup"><span data-stu-id="ebec1-754">`location` is the store location to load the certificate from.</span></span>

<span data-ttu-id="ebec1-755">`serverCertificate` ist das X.509-Zertifikat.</span><span class="sxs-lookup"><span data-stu-id="ebec1-755">`serverCertificate` is the X.509 certificate.</span></span> <span data-ttu-id="ebec1-756">In der Produktion muss HTTPS explizit konfiguriert sein.</span><span class="sxs-lookup"><span data-stu-id="ebec1-756">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="ebec1-757">Zumindest muss ein Standardzertifikat angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="ebec1-757">At a minimum, a default certificate must be provided.</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="ebec1-758">Die im Folgenden beschriebenen unterstützten Konfigurationen:</span><span class="sxs-lookup"><span data-stu-id="ebec1-758">Supported configurations described next:</span></span> <span data-ttu-id="ebec1-759">Keine Konfiguration</span><span class="sxs-lookup"><span data-stu-id="ebec1-759">No configuration</span></span> <span data-ttu-id="ebec1-760">Ersetzen des Standardzertifikats aus der Konfiguration</span><span class="sxs-lookup"><span data-stu-id="ebec1-760">Replace the default certificate from configuration</span></span>

<span data-ttu-id="ebec1-761">Ändern des Standards im Code</span><span class="sxs-lookup"><span data-stu-id="ebec1-761">Change the defaults in code</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="ebec1-762">*Keine Konfiguration*</span><span class="sxs-lookup"><span data-stu-id="ebec1-762">*No configuration*</span></span> <span data-ttu-id="ebec1-763">Kestrel überwacht `http://localhost:5000` und `https://localhost:5001` (wenn ein Standardzertifikat verfügbar ist).</span><span class="sxs-lookup"><span data-stu-id="ebec1-763">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span> <span data-ttu-id="ebec1-764">*Ersetzen des Standardzertifikats aus der Konfiguration*</span><span class="sxs-lookup"><span data-stu-id="ebec1-764">*Replace the default certificate from configuration*</span></span> <span data-ttu-id="ebec1-765">`CreateDefaultBuilder` ruft `Configure(context.Configuration.GetSection("Kestrel"))` standardmäßig zum Laden der Kestrel-Konfiguration auf.</span><span class="sxs-lookup"><span data-stu-id="ebec1-765">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>
>
> <span data-ttu-id="ebec1-766">Ein Standardkonfigurationsschema für HTTPS-App-Einstellungen ist für Kestrel verfügbar.</span><span class="sxs-lookup"><span data-stu-id="ebec1-766">A default HTTPS app settings configuration schema is available for Kestrel.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="ebec1-767">Konfigurieren Sie mehrere Endpunkte, einschließlich der zu verwendenden URLs und Zertifikate aus einer Datei auf dem Datenträger oder einem Zertifikatspeicher.</span><span class="sxs-lookup"><span data-stu-id="ebec1-767">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span> <span data-ttu-id="ebec1-768">Die Vorgehensweise im folgenden *appsettings.json*-Beispiel:</span><span class="sxs-lookup"><span data-stu-id="ebec1-768">In the following *appsettings.json* example:</span></span>

<span data-ttu-id="ebec1-769">Legen Sie **AllowInvalid** auf `true` fest, um die Verwendung von ungültigen Zertifikaten zu erlauben (z.B. selbstsignierte Zertifikate).</span><span class="sxs-lookup"><span data-stu-id="ebec1-769">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>

* <span data-ttu-id="ebec1-770">Jeder HTTPS-Endpunkt, der kein Zertifikat angibt (im folgenden Beispiel **HttpsDefaultCert**), greift auf das unter **Zertifikate** > **Standard** festgelegte Zertifikat oder das Entwicklungszertifikat zurück.</span><span class="sxs-lookup"><span data-stu-id="ebec1-770">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>
* <span data-ttu-id="ebec1-771">Alternativ zur Verwendung von **Pfad** und **Kennwort** für alle Zertifikatknoten können Sie das Zertifikat mithilfe von Zertifikatspeicherfeldern angeben.</span><span class="sxs-lookup"><span data-stu-id="ebec1-771">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span>
* <span data-ttu-id="ebec1-772">Das Zertifikat unter **Zertifikate** > **Standard** kann beispielweise wie folgt angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="ebec1-772">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

<span data-ttu-id="ebec1-773">Schema-Hinweise:</span><span class="sxs-lookup"><span data-stu-id="ebec1-773">Schema notes:</span></span>

<span data-ttu-id="ebec1-774">Bei den Namen der Endpunkte wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="ebec1-774">Endpoints names are case-insensitive.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="ebec1-775">Zum Beispiel sind `HTTPS` und `Https` gültig.</span><span class="sxs-lookup"><span data-stu-id="ebec1-775">For example, `HTTPS` and `Https` are valid.</span></span>

<span data-ttu-id="ebec1-776">Der Parameter `Url` ist für jeden Endpunkt erforderlich.</span><span class="sxs-lookup"><span data-stu-id="ebec1-776">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="ebec1-777">Das Format für diesen Parameter ist identisch mit dem allgemeinen Konfigurationsparameter `Urls`, mit der Ausnahme, dass er auf einen einzelnen Wert begrenzt ist.</span><span class="sxs-lookup"><span data-stu-id="ebec1-777">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>

<span data-ttu-id="ebec1-778">Diese Endpunkte ersetzen die in der allgemeinen `Urls`-Konfiguration festgelegten Endpunkte, anstatt zu ihnen hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="ebec1-778">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span>

![Endpunkte, die über `Listen` in Code definiert werden, werden den im Konfigurationsabschnitt definierten Endpunkten hinzugefügt.](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="ebec1-780">Der `Certificate`-Abschnitt ist optional.</span><span class="sxs-lookup"><span data-stu-id="ebec1-780">The `Certificate` section is optional.</span></span>

![Wenn der `Certificate`-Abschnitt nicht angegeben ist, werden die in früheren Szenarios definierten Standardwerte verwendet.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="ebec1-782">Wenn keine Standardwerte verfügbar sind, löst der Server eine Ausnahme aus und startet nicht.</span><span class="sxs-lookup"><span data-stu-id="ebec1-782">If no defaults are available, the server throws an exception and fails to start.</span></span>

<span data-ttu-id="ebec1-783">Der `Certificate`-Abschnitt unterstützt die Zertifikate **Pfad**&ndash;**Kennwort** und **Subject**&ndash;**Store** (Antragsteller–Speicher).</span><span class="sxs-lookup"><span data-stu-id="ebec1-783">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span> <span data-ttu-id="ebec1-784">Auf diese Weise kann eine beliebige Anzahl von Endpunkten definiert werden, solange Sie keine Portkonflikte verursachen.</span><span class="sxs-lookup"><span data-stu-id="ebec1-784">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span> <span data-ttu-id="ebec1-785">`options.Configure(context.Configuration.GetSection("{SECTION}"))` gibt `KestrelConfigurationLoader` mit der Methode `.Endpoint(string name, listenOptions => { })` zurück, die dazu verwendet werden kann, die Einstellungen eines Endpunkts zu ergänzen:</span><span class="sxs-lookup"><span data-stu-id="ebec1-785">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

<span data-ttu-id="ebec1-786">Auf `KestrelServerOptions.ConfigurationLoader` kann direkt zugegriffen werden, um die Iteration auf dem vorhandenen Ladeprogramm fortzusetzen, etwa auf dem von <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> bereitgestellten Ladeprogramm.</span><span class="sxs-lookup"><span data-stu-id="ebec1-786">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

<span data-ttu-id="ebec1-787">Der Konfigurationsabschnitt ist für jeden Endpunkt in den Optionen der Methode `Endpoint` verfügbar, sodass benutzerdefinierte Einstellungen gelesen werden können.</span><span class="sxs-lookup"><span data-stu-id="ebec1-787">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>

* <span data-ttu-id="ebec1-788">Mehrere Konfigurationen können durch erneutes Aufrufen von `options.Configure(context.Configuration.GetSection("{SECTION}"))` mit einem anderen Abschnitt geladen werden.</span><span class="sxs-lookup"><span data-stu-id="ebec1-788">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span>
* <span data-ttu-id="ebec1-789">Sofern `Load` nicht in einer vorherigen Instanz explizit aufgerufen wird, wird nur die letzte Konfiguration verwendet.</span><span class="sxs-lookup"><span data-stu-id="ebec1-789">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span>
* <span data-ttu-id="ebec1-790">Das Metapaket ruft `Load` nicht auf, sodass der Abschnitt mit der Standardkonfiguration ersetzt werden kann.</span><span class="sxs-lookup"><span data-stu-id="ebec1-790">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="ebec1-791">`KestrelConfigurationLoader` spiegelt die API-Familie `Listen` von `KestrelServerOptions` als `Endpoint`-Überladungen, weshalb Code und Konfigurationsendpunkte am selben Ort konfiguriert werden können.</span><span class="sxs-lookup"><span data-stu-id="ebec1-791">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="ebec1-792">Diese Überladungen verwenden keine Namen und nutzen nur Standardeinstellungen aus der Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="ebec1-792">These overloads don't use names and only consume default settings from configuration.</span></span>

> [!WARNING]
> <span data-ttu-id="ebec1-793">*Ändern des Standards im Code*</span><span class="sxs-lookup"><span data-stu-id="ebec1-793">*Change the defaults in code*</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="ebec1-794">`ConfigureEndpointDefaults` und `ConfigureHttpsDefaults` können zum Ändern der Standardeinstellungen für `ListenOptions` und `HttpsConnectionAdapterOptions` verwendet werden, einschließlich der Standardzertifikate, die im vorherigen Szenario festgelegt wurden.</span><span class="sxs-lookup"><span data-stu-id="ebec1-794">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span>

<span data-ttu-id="ebec1-795">`ConfigureEndpointDefaults` und `ConfigureHttpsDefaults` sollten aufgerufen werden, bevor Endpunkte konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="ebec1-795">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

<span data-ttu-id="ebec1-796">*Kestrel-Unterstützung für SNI*</span><span class="sxs-lookup"><span data-stu-id="ebec1-796">*Kestrel support for SNI*</span></span> <span data-ttu-id="ebec1-797">Die [Servernamensanzeige (SNI)](https://tools.ietf.org/html/rfc6066#section-3) kann zum Hosten mehrerer Domänen auf der gleichen IP-Adresse und dem gleichen Port verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="ebec1-797">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span>

<span data-ttu-id="ebec1-798">Damit die Servernamensanzeige funktioniert, sendet der Client während des TLS-Handshakes den Hostnamen für die sichere Sitzung an den Server, sodass der Server das richtige Zertifikat bereitstellen kann.</span><span class="sxs-lookup"><span data-stu-id="ebec1-798">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="ebec1-799">Der Client verwendet das beigestellte Zertifikat für die verschlüsselte Kommunikation mit dem Server während der sicheren Sitzung nach dem TLS-Handshake.</span><span class="sxs-lookup"><span data-stu-id="ebec1-799">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="ebec1-800">Kestrel unterstützt die Servernamensanzeige über den `ServerCertificateSelector`-Rückruf.</span><span class="sxs-lookup"><span data-stu-id="ebec1-800">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span>

<span data-ttu-id="ebec1-801">Der Rückruf wird für jede Verbindung einmal aufgerufen, um der App zu ermöglichen, den Hostnamen zu überprüfen und das entsprechende Zertifikat auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="ebec1-801">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="ebec1-802">Für die Unterstützung der Servernamensanzeige benötigen Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="ebec1-802">SNI support requires:</span></span> <span data-ttu-id="ebec1-803">Wird auf dem Zielframework `netcoreapp2.1` oder höher ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="ebec1-803">Running on target framework `netcoreapp2.1` or later.</span></span>

<span data-ttu-id="ebec1-804">In `net461` oder höher, wird der Rückruf aufgerufen, `name` ist aber immer `null`.</span><span class="sxs-lookup"><span data-stu-id="ebec1-804">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="ebec1-805">`name` ist auch `null`, wenn der Client den Hostnamenparameter nicht im TLS-Handshake angibt.</span><span class="sxs-lookup"><span data-stu-id="ebec1-805">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span> <span data-ttu-id="ebec1-806">Alle Websites werden in derselben Kestrel-Instanz ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="ebec1-806">All websites run on the same Kestrel instance.</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="ebec1-807">Kestrel unterstützt ohne Reverseproxy keine gemeinsame IP-Adresse und keinen gemeinsamen Port für mehrere Instanzen.</span><span class="sxs-lookup"><span data-stu-id="ebec1-807">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

* <span data-ttu-id="ebec1-808">Verbindungsprotokollierung</span><span class="sxs-lookup"><span data-stu-id="ebec1-808">Connection logging</span></span>

  1. <span data-ttu-id="ebec1-809">Rufen Sie <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> auf, um Protokolle auf Debugebene für die Kommunikation auf Byteebene für eine Verbindung auszugeben.</span><span class="sxs-lookup"><span data-stu-id="ebec1-809">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="ebec1-810">Die Verbindungsprotokollierung ist beim Beheben von Problemen bei der Low-Level-Kommunikation hilfreich, wie z. B. bei der TLS-Verschlüsselung und bei Proxys.</span><span class="sxs-lookup"><span data-stu-id="ebec1-810">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span>
  2. <span data-ttu-id="ebec1-811">Wenn `UseConnectionLogging` vor `UseHttps` platziert wird, wird der verschlüsselte Datenverkehr protokolliert.</span><span class="sxs-lookup"><span data-stu-id="ebec1-811">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="ebec1-812">Wenn `UseConnectionLogging` nach `UseHttps` platziert wird, wird der entschlüsselte Datenverkehr protokolliert.</span><span class="sxs-lookup"><span data-stu-id="ebec1-812">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

  <span data-ttu-id="ebec1-813">Binden an einen TCP-Socket</span><span class="sxs-lookup"><span data-stu-id="ebec1-813">Bind to a TCP socket</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="ebec1-814">Die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>-Methode wird an ein TCP-Socket gebunden, und ein Lambdaausdruck einer Option lässt die Konfiguration des X.509-Zertifikats zu:</span><span class="sxs-lookup"><span data-stu-id="ebec1-814">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="ebec1-815">Im Beispiel wird HTTPS für einen Endpunkt mit <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="ebec1-815">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="ebec1-816">Verwenden Sie die gleiche API zum Konfigurieren anderer Kestrel-Einstellungen für bestimmte Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="ebec1-816">Use the same API to configure other Kestrel settings for specific endpoints.</span></span> <span data-ttu-id="ebec1-817">Binden an einen Unix-Socket</span><span class="sxs-lookup"><span data-stu-id="ebec1-817">Bind to a Unix socket</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="ebec1-818">Wie in diesem Beispiel dargestellt, lauschen Sie an einem Unix-Socket mit <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>, um eine verbesserte Leistung mit Nginx zu erzielen:</span><span class="sxs-lookup"><span data-stu-id="ebec1-818">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="ebec1-819">Legen Sie in der Nginx-Konfigurationsdatei den Eintrag `server` > `location` > `proxy_pass` auf `http://unix:/tmp/{KESTREL SOCKET}:/;` fest.</span><span class="sxs-lookup"><span data-stu-id="ebec1-819">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="ebec1-820">`{KESTREL SOCKET}` ist der Name des für <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> bereitgestellten Socket (zum Beispiel `kestrel-test.sock` im vorherigen Beispiel).</span><span class="sxs-lookup"><span data-stu-id="ebec1-820">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span> <span data-ttu-id="ebec1-821">Stellen Sie sicher, dass der Socket von Nginx beschreibbar ist (z. B. `chmod go+w /tmp/kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="ebec1-821">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="ebec1-822">Port 0</span><span class="sxs-lookup"><span data-stu-id="ebec1-822">Port 0</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="ebec1-823">Wenn die Portnummer `0` angegeben wird, wird Kestrel dynamisch an einen verfügbaren Port gebunden.</span><span class="sxs-lookup"><span data-stu-id="ebec1-823">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="ebec1-824">Im folgenden Beispiel wird veranschaulicht, wie bestimmt werden kann, für welchen Port Kestrel zur Laufzeit eine Bindung erstellt hat:</span><span class="sxs-lookup"><span data-stu-id="ebec1-824">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

<span data-ttu-id="ebec1-825">Wenn die App ausgeführt wird, gibt das Ausgabefenster der Konsole den dynamischen Port an, über den die App erreicht werden kann:</span><span class="sxs-lookup"><span data-stu-id="ebec1-825">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="ebec1-826">Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="ebec1-826">Limitations</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="ebec1-827">Konfigurieren Sie Endpunkte mithilfe der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="ebec1-827">Configure endpoints with the following approaches:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="ebec1-828">Befehlszeilenargument `--urls`</span><span class="sxs-lookup"><span data-stu-id="ebec1-828">`--urls` command-line argument</span></span> <span data-ttu-id="ebec1-829">Hostkonfigurationsschlüssel `urls`</span><span class="sxs-lookup"><span data-stu-id="ebec1-829">`urls` host configuration key</span></span>

<span data-ttu-id="ebec1-830">Umgebungsvariable `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="ebec1-830">`ASPNETCORE_URLS` environment variable</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="ebec1-831">Diese Methoden sind nützlich, wenn Ihr Code mit anderen Servern als Kestrel funktionieren soll.</span><span class="sxs-lookup"><span data-stu-id="ebec1-831">These methods are useful for making code work with servers other than Kestrel.</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="ebec1-832">Beachten Sie jedoch die folgenden Einschränkungen:</span><span class="sxs-lookup"><span data-stu-id="ebec1-832">However, be aware of the following limitations:</span></span> <span data-ttu-id="ebec1-833">HTTPS kann nicht mit diesen Ansätzen verwendet werden, außer ein Standardzertifikat wird in der HTTPS-Endpunktkonfiguration angegeben (z.B. wenn Sie wie zuvor in diesem Artikel gezeigt die `KestrelServerOptions`-Konfiguration oder eine Konfigurationsdatei verwenden).</span><span class="sxs-lookup"><span data-stu-id="ebec1-833">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span> <span data-ttu-id="ebec1-834">Wenn die Ansätze `Listen` und `UseUrls` gleichzeitig verwendet werden, überschreiben die `Listen`-Endpunkte die `UseUrls`-Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="ebec1-834">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

<span data-ttu-id="ebec1-835">IIS-Endpunktkonfiguration</span><span class="sxs-lookup"><span data-stu-id="ebec1-835">IIS endpoint configuration</span></span>

<span data-ttu-id="ebec1-836">Bei der Verwendung von IIS werden die URL-Bindungen für IIS-Überschreibungsbindungen durch `Listen` oder `UseUrls` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="ebec1-836">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="ebec1-837">Weitere Informationen finden Sie im Artikel [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="ebec1-837">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

<span data-ttu-id="ebec1-838">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="ebec1-838">ListenOptions.Protocols</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MinRequestBodyDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
            serverOptions.Limits.MinResponseDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
        });
```

### <a name="request-headers-timeout"></a><span data-ttu-id="ebec1-839">Die `Protocols`-Eigenschaft richtet die HTTP-Protokolle (`HttpProtocols`) ein, die für einen Verbindungsendpunkt oder für den Server aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="ebec1-839">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="ebec1-840">Weisen Sie der `Protocols`-Eigenschaft einen Wert aus der `HttpProtocols`-Enumeration zu.</span><span class="sxs-lookup"><span data-stu-id="ebec1-840">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span> <span data-ttu-id="ebec1-841">`HttpProtocols`-Enumerationswert</span><span class="sxs-lookup"><span data-stu-id="ebec1-841">`HttpProtocols` enum value</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="ebec1-842">Zulässiges Verbindungsprotokoll</span><span class="sxs-lookup"><span data-stu-id="ebec1-842">Connection protocol permitted</span></span>

<span data-ttu-id="ebec1-843">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebec1-843">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="ebec1-844">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-844">'Blazor'</span></span>

> [!WARNING]
> <span data-ttu-id="ebec1-845">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebec1-845">'Identity'</span></span> <span data-ttu-id="ebec1-846">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebec1-846">'Let's Encrypt'</span></span>

<span data-ttu-id="ebec1-847">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-847">'Razor'</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="ebec1-848">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebec1-848">'SignalR' uid:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="ebec1-849">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebec1-849">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="ebec1-850">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-850">'Blazor'</span></span>

* `http://localhost:5000`
* <span data-ttu-id="ebec1-851">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebec1-851">'Identity'</span></span>

<span data-ttu-id="ebec1-852">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebec1-852">'Let's Encrypt'</span></span>

* <span data-ttu-id="ebec1-853">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-853">'Razor'</span></span>
* <span data-ttu-id="ebec1-854">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebec1-854">'SignalR' uid:</span></span>
* <span data-ttu-id="ebec1-855">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebec1-855">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
* <span data-ttu-id="ebec1-856">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-856">'Blazor'</span></span>

<span data-ttu-id="ebec1-857">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebec1-857">'Identity'</span></span> <span data-ttu-id="ebec1-858">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebec1-858">'Let's Encrypt'</span></span>

<span data-ttu-id="ebec1-859">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-859">'Razor'</span></span>

<span data-ttu-id="ebec1-860">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebec1-860">'SignalR' uid:</span></span>

* <span data-ttu-id="ebec1-861">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebec1-861">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
* <span data-ttu-id="ebec1-862">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-862">'Blazor'</span></span>

<span data-ttu-id="ebec1-863">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebec1-863">'Identity'</span></span>

<span data-ttu-id="ebec1-864">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebec1-864">'Let's Encrypt'</span></span>

<span data-ttu-id="ebec1-865">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-865">'Razor'</span></span>

<span data-ttu-id="ebec1-866">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebec1-866">'SignalR' uid:</span></span>

<span data-ttu-id="ebec1-867">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebec1-867">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="ebec1-868">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-868">'Blazor'</span></span>

<span data-ttu-id="ebec1-869">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebec1-869">'Identity'</span></span> <span data-ttu-id="ebec1-870">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebec1-870">'Let's Encrypt'</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

> [!NOTE]
> <span data-ttu-id="ebec1-871">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-871">'Razor'</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="ebec1-872">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebec1-872">'SignalR' uid:</span></span>

<span data-ttu-id="ebec1-873">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebec1-873">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="ebec1-874">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-874">'Blazor'</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

> [!NOTE]
> <span data-ttu-id="ebec1-875">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebec1-875">'Identity'</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="ebec1-876">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebec1-876">'Let's Encrypt'</span></span>

<span data-ttu-id="ebec1-877">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-877">'Razor'</span></span> <span data-ttu-id="ebec1-878">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebec1-878">'SignalR' uid:</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="ebec1-879">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebec1-879">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="ebec1-880">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-880">'Blazor'</span></span>

<span data-ttu-id="ebec1-881">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebec1-881">'Identity'</span></span>

* <span data-ttu-id="ebec1-882">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebec1-882">'Let's Encrypt'</span></span> <span data-ttu-id="ebec1-883">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-883">'Razor'</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="ebec1-884">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebec1-884">'SignalR' uid:</span></span>

* <span data-ttu-id="ebec1-885">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebec1-885">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
* <span data-ttu-id="ebec1-886">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-886">'Blazor'</span></span>
* <span data-ttu-id="ebec1-887">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebec1-887">'Identity'</span></span> <span data-ttu-id="ebec1-888">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebec1-888">'Let's Encrypt'</span></span>
* <span data-ttu-id="ebec1-889">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-889">'Razor'</span></span>
* <span data-ttu-id="ebec1-890">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebec1-890">'SignalR' uid:</span></span>
* <span data-ttu-id="ebec1-891">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebec1-891">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
* <span data-ttu-id="ebec1-892">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-892">'Blazor'</span></span>
* <span data-ttu-id="ebec1-893">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebec1-893">'Identity'</span></span>

<span data-ttu-id="ebec1-894">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebec1-894">'Let's Encrypt'</span></span> <span data-ttu-id="ebec1-895">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-895">'Razor'</span></span>

<span data-ttu-id="ebec1-896">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebec1-896">'SignalR' uid:</span></span>

* <span data-ttu-id="ebec1-897">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebec1-897">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
* <span data-ttu-id="ebec1-898">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-898">'Blazor'</span></span>
* <span data-ttu-id="ebec1-899">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebec1-899">'Identity'</span></span>

<span data-ttu-id="ebec1-900">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebec1-900">'Let's Encrypt'</span></span>

<span data-ttu-id="ebec1-901">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-901">'Razor'</span></span>

<a name="configuration"></a>

<span data-ttu-id="ebec1-902">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebec1-902">'SignalR' uid:</span></span>

<span data-ttu-id="ebec1-903">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebec1-903">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="ebec1-904">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-904">'Blazor'</span></span> <span data-ttu-id="ebec1-905">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebec1-905">'Identity'</span></span>

<span data-ttu-id="ebec1-906">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebec1-906">'Let's Encrypt'</span></span>

* <span data-ttu-id="ebec1-907">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-907">'Razor'</span></span>
* <span data-ttu-id="ebec1-908">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebec1-908">'SignalR' uid:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="ebec1-909">------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebec1-909">------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="ebec1-910">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-910">'Blazor'</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="ebec1-911">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebec1-911">'Identity'</span></span>

* <span data-ttu-id="ebec1-912">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebec1-912">'Let's Encrypt'</span></span> <span data-ttu-id="ebec1-913">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-913">'Razor'</span></span>
* <span data-ttu-id="ebec1-914">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebec1-914">'SignalR' uid:</span></span> <span data-ttu-id="ebec1-915">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebec1-915">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
* <span data-ttu-id="ebec1-916">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-916">'Blazor'</span></span> <span data-ttu-id="ebec1-917">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebec1-917">'Identity'</span></span>
* <span data-ttu-id="ebec1-918">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebec1-918">'Let's Encrypt'</span></span> <span data-ttu-id="ebec1-919">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-919">'Razor'</span></span> <span data-ttu-id="ebec1-920">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebec1-920">'SignalR' uid:</span></span>
* <span data-ttu-id="ebec1-921">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebec1-921">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
* <span data-ttu-id="ebec1-922">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-922">'Blazor'</span></span>
* <span data-ttu-id="ebec1-923">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebec1-923">'Identity'</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="ebec1-924">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebec1-924">'Let's Encrypt'</span></span>

* <span data-ttu-id="ebec1-925">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-925">'Razor'</span></span>
* <span data-ttu-id="ebec1-926">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebec1-926">'SignalR' uid:</span></span> <span data-ttu-id="ebec1-927">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebec1-927">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="ebec1-928">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-928">'Blazor'</span></span>
* <span data-ttu-id="ebec1-929">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebec1-929">'Identity'</span></span> <span data-ttu-id="ebec1-930">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebec1-930">'Let's Encrypt'</span></span>

<span data-ttu-id="ebec1-931">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-931">'Razor'</span></span>

<span data-ttu-id="ebec1-932">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebec1-932">'SignalR' uid:</span></span> <span data-ttu-id="ebec1-933">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebec1-933">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="ebec1-934">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-934">'Blazor'</span></span>

<span data-ttu-id="ebec1-935">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebec1-935">'Identity'</span></span> <span data-ttu-id="ebec1-936">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebec1-936">'Let's Encrypt'</span></span> <span data-ttu-id="ebec1-937">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-937">'Razor'</span></span>

<span data-ttu-id="ebec1-938">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebec1-938">'SignalR' uid:</span></span> <span data-ttu-id="ebec1-939">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebec1-939">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="ebec1-940">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-940">'Blazor'</span></span>

* <span data-ttu-id="ebec1-941">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebec1-941">'Identity'</span></span> <span data-ttu-id="ebec1-942">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebec1-942">'Let's Encrypt'</span></span> <span data-ttu-id="ebec1-943">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-943">'Razor'</span></span>
* <span data-ttu-id="ebec1-944">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebec1-944">'SignalR' uid:</span></span> <span data-ttu-id="ebec1-945">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebec1-945">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        })
        .Build();
```

### <a name="connection-logging"></a><span data-ttu-id="ebec1-946">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-946">'Blazor'</span></span>

<span data-ttu-id="ebec1-947">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebec1-947">'Identity'</span></span> <span data-ttu-id="ebec1-948">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebec1-948">'Let's Encrypt'</span></span> <span data-ttu-id="ebec1-949">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-949">'Razor'</span></span> <span data-ttu-id="ebec1-950">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebec1-950">'SignalR' uid:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="ebec1-951">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebec1-951">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="ebec1-952">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-952">'Blazor'</span></span>

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

<span data-ttu-id="ebec1-953">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebec1-953">'Identity'</span></span> <span data-ttu-id="ebec1-954">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebec1-954">'Let's Encrypt'</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="ebec1-955">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-955">'Razor'</span></span>

<span data-ttu-id="ebec1-956">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebec1-956">'SignalR' uid:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock");
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock", listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testpassword");
            });
        });
```

* <span data-ttu-id="ebec1-957">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebec1-957">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="ebec1-958">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-958">'Blazor'</span></span>
* <span data-ttu-id="ebec1-959">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebec1-959">'Identity'</span></span> 

### <a name="port-0"></a><span data-ttu-id="ebec1-960">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebec1-960">'Let's Encrypt'</span></span>

<span data-ttu-id="ebec1-961">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-961">'Razor'</span></span> <span data-ttu-id="ebec1-962">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebec1-962">'SignalR' uid:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="ebec1-963">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebec1-963">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="ebec1-964">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-964">'Blazor'</span></span>

<span data-ttu-id="ebec1-965">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebec1-965">'Identity'</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="ebec1-966">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebec1-966">'Let's Encrypt'</span></span>
* <span data-ttu-id="ebec1-967">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-967">'Razor'</span></span>
* <span data-ttu-id="ebec1-968">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebec1-968">'SignalR' uid:</span></span>

<span data-ttu-id="ebec1-969">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebec1-969">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="ebec1-970">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-970">'Blazor'</span></span>

* <span data-ttu-id="ebec1-971">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebec1-971">'Identity'</span></span>
* <span data-ttu-id="ebec1-972">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebec1-972">'Let's Encrypt'</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="ebec1-973">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-973">'Razor'</span></span>

<span data-ttu-id="ebec1-974">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebec1-974">'SignalR' uid:</span></span> <span data-ttu-id="ebec1-975">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ebec1-975">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="ebec1-976">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-976">'Blazor'</span></span>

<span data-ttu-id="ebec1-977">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ebec1-977">'Identity'</span></span> <span data-ttu-id="ebec1-978">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ebec1-978">'Let's Encrypt'</span></span>

* <span data-ttu-id="ebec1-979">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ebec1-979">'Razor'</span></span>
* <span data-ttu-id="ebec1-980">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ebec1-980">'SignalR' uid:</span></span>

<span data-ttu-id="ebec1-981">--------------- | | `Http1`                    | Nur HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="ebec1-981">--------------- | | `Http1`                    | HTTP/1.1 only.</span></span>

* <span data-ttu-id="ebec1-982">Kann mit oder ohne TLS verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="ebec1-982">Can be used with or without TLS.</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="ebec1-983">| | `Http2`                    | Nur HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="ebec1-983">| | `Http2`                    | HTTP/2 only.</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a><span data-ttu-id="ebec1-984">Kann nur ohne TLS verwendet werden, wenn der Client einen [Vorabkenntnis-Modus](https://tools.ietf.org/html/rfc7540#section-3.4) unterstützt.</span><span class="sxs-lookup"><span data-stu-id="ebec1-984">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span>

<span data-ttu-id="ebec1-985">| | `Http1AndHttp2`            | HTTP/1.1 und HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="ebec1-985">| | `Http1AndHttp2`            | HTTP/1.1 and HTTP/2.</span></span>

<span data-ttu-id="ebec1-986">HTTP/2 erfordert eine TLS- und [ALPN](https://tools.ietf.org/html/rfc7301#section-3)-Verbindung (Application-Layer Protocol Negotiation). Andernfalls wird für die Verbindung standardmäßig HTTP/1.1 verwendet.</span><span class="sxs-lookup"><span data-stu-id="ebec1-986">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> <span data-ttu-id="ebec1-987">Das Standardprotokoll ist HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="ebec1-987">The default protocol is HTTP/1.1.</span></span>

* <span data-ttu-id="ebec1-988">TLS-Einschränkungen für HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="ebec1-988">TLS restrictions for HTTP/2:</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="ebec1-989">TLS Version 1.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="ebec1-989">TLS version 1.2 or later</span></span>

* <span data-ttu-id="ebec1-990">Erneute Aushandlung deaktiviert</span><span class="sxs-lookup"><span data-stu-id="ebec1-990">Renegotiation disabled</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="ebec1-991">Komprimierung deaktiviert</span><span class="sxs-lookup"><span data-stu-id="ebec1-991">Compression disabled</span></span>

* <span data-ttu-id="ebec1-992">Minimale Größen für Austausch von flüchtigen Schlüsseln:</span><span class="sxs-lookup"><span data-stu-id="ebec1-992">Minimum ephemeral key exchange sizes:</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="ebec1-993">ECDHE (Elliptic Curve Diffie-Hellman) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: mindestens 224 Bits</span><span class="sxs-lookup"><span data-stu-id="ebec1-993">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span> <span data-ttu-id="ebec1-994">DHE (Finite Field Diffie-Hellman) &lbrack;`TLS12`&rbrack;: mindestens 2048 Bits</span><span class="sxs-lookup"><span data-stu-id="ebec1-994">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span> <span data-ttu-id="ebec1-995">Verschlüsselungssammlung nicht gesperrt</span><span class="sxs-lookup"><span data-stu-id="ebec1-995">Cipher suite not blacklisted</span></span>

  > [!WARNING]
  > <span data-ttu-id="ebec1-996">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; mit der elliptischen P-256-Kurve &lbrack;`FIPS186`&rbrack; wird standardmäßig unterstützt.</span><span class="sxs-lookup"><span data-stu-id="ebec1-996">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

* <span data-ttu-id="ebec1-997">Das folgende Beispiel erlaubt HTTP/1.1- und HTTP/2-Verbindungen an Port 8000.</span><span class="sxs-lookup"><span data-stu-id="ebec1-997">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="ebec1-998">Die Verbindungen werden durch TLS mit einem bereitgestellten Zertifikat geschützt:</span><span class="sxs-lookup"><span data-stu-id="ebec1-998">Connections are secured by TLS with a supplied certificate:</span></span> <span data-ttu-id="ebec1-999">Erstellen Sie optional eine `IConnectionAdapter`-Implementierung, um TLS-Handshakes auf Verbindungsbasis für bestimmte Verschlüsselungen zu filtern:</span><span class="sxs-lookup"><span data-stu-id="ebec1-999">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span> <span data-ttu-id="ebec1-1000">*Festlegen des Protokolls aus der Konfiguration*</span><span class="sxs-lookup"><span data-stu-id="ebec1-1000">*Set the protocol from configuration*</span></span>

## <a name="host-filtering"></a><span data-ttu-id="ebec1-1001"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ruft `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` standardmäßig zum Laden der Kestrel-Konfiguration auf.</span><span class="sxs-lookup"><span data-stu-id="ebec1-1001"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="ebec1-1002">Im folgenden Beispiel *appsettings.json* wird für alle Endpunkte von Kestrel ein Standardverbindungsprotokoll (HTTP/1.1 und HTTP/2) eingerichtet:</span><span class="sxs-lookup"><span data-stu-id="ebec1-1002">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span> <span data-ttu-id="ebec1-1003">Das folgende Beispiel einer Konfigurationsdatei richtet ein Verbindungsprotokoll für einen bestimmten Endpunkt ein:</span><span class="sxs-lookup"><span data-stu-id="ebec1-1003">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span> <span data-ttu-id="ebec1-1004">Protokolle, die in Code angegeben werden, setzen Werte außer Kraft, der durch die Konfiguration festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="ebec1-1004">Protocols specified in code override values set by configuration.</span></span> <span data-ttu-id="ebec1-1005">Transportkonfiguration</span><span class="sxs-lookup"><span data-stu-id="ebec1-1005">Transport configuration</span></span>

<span data-ttu-id="ebec1-1006">Mit dem Release von ASP.NET Core 2.1 basiert der Standardtransport von Kestrel nicht mehr auf Libuv, sondern auf verwalteten Sockets.</span><span class="sxs-lookup"><span data-stu-id="ebec1-1006">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="ebec1-1007">Dies stellt einen Breaking Change für ASP.NET Core 2.0-Apps dar, die auf Version 2.1 upgraden, <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> aufrufen und von einem der folgenden Pakete abhängig sind:</span><span class="sxs-lookup"><span data-stu-id="ebec1-1007">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span> <span data-ttu-id="ebec1-1008">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direkter Paketverweis)</span><span class="sxs-lookup"><span data-stu-id="ebec1-1008">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

[<span data-ttu-id="ebec1-1009">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="ebec1-1009">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) <span data-ttu-id="ebec1-1010">Für Projekte, die den Einsatz von Libuv erfordern:</span><span class="sxs-lookup"><span data-stu-id="ebec1-1010">For projects that require the use of Libuv:</span></span> <span data-ttu-id="ebec1-1011">Fügen Sie für das [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/)-Paket eine Abhängigkeit auf die Projektdatei der App hinzu:</span><span class="sxs-lookup"><span data-stu-id="ebec1-1011">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

<span data-ttu-id="ebec1-1012">Rufen Sie <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> auf:</span><span class="sxs-lookup"><span data-stu-id="ebec1-1012">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="ebec1-1013">URL-Präfixe</span><span class="sxs-lookup"><span data-stu-id="ebec1-1013">URL prefixes</span></span> <span data-ttu-id="ebec1-1014">Bei der Verwendung von `UseUrls`, dem Befehlszeilenargument `--urls`, dem Hostkonfigurationsschlüssel `urls` oder der Umgebungsvariable `ASPNETCORE_URLS` können die URL-Präfixe in den folgenden Formaten vorliegen.</span><span class="sxs-lookup"><span data-stu-id="ebec1-1014">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span> <span data-ttu-id="ebec1-1015">Nur HTTP-URL-Präfixe sind gültig.</span><span class="sxs-lookup"><span data-stu-id="ebec1-1015">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="ebec1-1016">Kestrel unterstützt HTTPS nicht beim Konfigurieren von URL-Bindungen mit `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="ebec1-1016">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>
>
> <span data-ttu-id="ebec1-1017">IPv4-Adresse mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="ebec1-1017">IPv4 address with port number</span></span>

::: moniker-end

## <a name="http11-request-draining"></a><span data-ttu-id="ebec1-1018">Bei `0.0.0.0` handelt es sich um einen Sonderfall, für den eine Bindung an alle IPv4-Adressen erfolgt.</span><span class="sxs-lookup"><span data-stu-id="ebec1-1018">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

<span data-ttu-id="ebec1-1019">IPv6-Adresse mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="ebec1-1019">IPv6 address with port number</span></span> <span data-ttu-id="ebec1-1020">`[::]` stellt das Äquivalent von IPv6 zu `0.0.0.0` für IPv4 dar.</span><span class="sxs-lookup"><span data-stu-id="ebec1-1020">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span> <span data-ttu-id="ebec1-1021">Hostname mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="ebec1-1021">Host name with port number</span></span> <span data-ttu-id="ebec1-1022">Hostnamen, `*` und `+` sind nicht spezifisch.</span><span class="sxs-lookup"><span data-stu-id="ebec1-1022">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="ebec1-1023">Alle Elemente, die nicht als gültige IP-Adresse oder `localhost` erkannt werden, werden an alle IPv4- und IPv6-IP-Adressen gebunden.</span><span class="sxs-lookup"><span data-stu-id="ebec1-1023">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="ebec1-1024">Verwenden Sie [HTTP.sys](xref:fundamentals/servers/httpsys) oder einen Reverseproxyserver zum Binden verschiedener Hostnamen an verschiedene ASP.NET Core-Apps auf demselben Port, z.B. IIS, Nginx oder Apache.</span><span class="sxs-lookup"><span data-stu-id="ebec1-1024">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

* <span data-ttu-id="ebec1-1025">Das Hosting in einer Reverseproxykonfiguration erfordert [Hostfilterung](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="ebec1-1025">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>
* <span data-ttu-id="ebec1-1026">`localhost`-Hostname mit Portnummer oder Loopback-IP mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="ebec1-1026">Host `localhost` name with port number or loopback IP with port number</span></span>
* <span data-ttu-id="ebec1-1027">Wenn `localhost` angegeben ist, versucht Kestrel, eine Bindung zu IPv4- und IPv6-Loopback-Schnittstellen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="ebec1-1027">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span>
* <span data-ttu-id="ebec1-1028">Wenn der erforderliche Port von einem anderen Dienst auf einer der Loopback-Schnittstellen verwendet wird, tritt beim Starten von Kestrel ein Fehler auf.</span><span class="sxs-lookup"><span data-stu-id="ebec1-1028">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="ebec1-1029">Wenn eine der Loopback-Schnittstellen aus anderen Gründen nicht verfügbar ist (meistens durch die fehlende Unterstützung von IPv6), protokolliert Kestrel eine Warnung.</span><span class="sxs-lookup"><span data-stu-id="ebec1-1029">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

<span data-ttu-id="ebec1-1030">Host-Filterung</span><span class="sxs-lookup"><span data-stu-id="ebec1-1030">Host filtering</span></span>

* <span data-ttu-id="ebec1-1031">Obwohl Kestrel die Konfiguration basierend auf Präfixe wie `http://example.com:5000` unterstützt, ignoriert Kestrel den Hostnamen weitgehend.</span><span class="sxs-lookup"><span data-stu-id="ebec1-1031">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span>
* <span data-ttu-id="ebec1-1032">Der Host `localhost` ist ein Sonderfall, der für die Bindung an Loopback-Adressen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="ebec1-1032">Host `localhost` is a special case used for binding to loopback addresses.</span></span>

<span data-ttu-id="ebec1-1033">Jeder Host, der keine explizite IP-Adresse ist, wird an alle öffentlichen IP-Adressen gebunden.</span><span class="sxs-lookup"><span data-stu-id="ebec1-1033">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="ebec1-1034">`Host`-Header werden nicht überprüft.</span><span class="sxs-lookup"><span data-stu-id="ebec1-1034">`Host` headers aren't validated.</span></span> <span data-ttu-id="ebec1-1035">Verwenden Sie Middleware zum Filtern von Hosts, um dieses Problem zu umgehen.</span><span class="sxs-lookup"><span data-stu-id="ebec1-1035">As a workaround, use Host Filtering Middleware.</span></span>

<span data-ttu-id="ebec1-1036">Middleware zum Filtern von Hosts wird im Paket [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) bereitgestellt, das im [Metapaket Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) enthalten ist (ASP.NET Core 2.1 oder 2.2).</span><span class="sxs-lookup"><span data-stu-id="ebec1-1036">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span>

* <span data-ttu-id="ebec1-1037">Die Middleware wird von <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> hinzugefügt, wodurch <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="ebec1-1037">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>
* <span data-ttu-id="ebec1-1038">Die Middleware zum Filtern von Hosts ist standardmäßig deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="ebec1-1038">Host Filtering Middleware is disabled by default.</span></span>
* <span data-ttu-id="ebec1-1039">Wenn Sie die Middleware aktivieren möchten, definieren Sie einen `AllowedHosts`-Schlüssel in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span><span class="sxs-lookup"><span data-stu-id="ebec1-1039">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span>
  * <span data-ttu-id="ebec1-1040">Der Wert ist eine durch Semikolons getrennte Liste von Hostnamen ohne Portnummern:</span><span class="sxs-lookup"><span data-stu-id="ebec1-1040">The value is a semicolon-delimited list of host names without port numbers:</span></span> <span data-ttu-id="ebec1-1041">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="ebec1-1041">*appsettings.json*:</span></span>
  * <span data-ttu-id="ebec1-1042">[Middleware für weitergeleitete Header](xref:host-and-deploy/proxy-load-balancer) hat auch eine <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>-Option.</span><span class="sxs-lookup"><span data-stu-id="ebec1-1042">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span>

<span data-ttu-id="ebec1-1043">Middleware für weitergeleitete Header und Middleware zum Filtern von Hosts besitzen ähnliche Funktionen für unterschiedliche Szenarios.</span><span class="sxs-lookup"><span data-stu-id="ebec1-1043">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="ebec1-1044">Legen Sie `AllowedHosts` mit Middleware für weitergeleitete Header fest, wenn der `Host`-Header beim Weiterleiten von Anforderungen mit einem Reverseproxyserver oder einem Lastenausgleichsmodul nicht beibehalten wird.</span><span class="sxs-lookup"><span data-stu-id="ebec1-1044">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span>

<span data-ttu-id="ebec1-1045">Legen Sie `AllowedHosts` mit Middleware zum Filtern von Hosts fest, wenn Kestrel als öffentlicher Edgeserver verwendet oder der `Host`-Header direkt weitergeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="ebec1-1045">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span> <span data-ttu-id="ebec1-1046">Weitere Informationen zu Middleware für weitergeleitete Header finden Sie unter <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="ebec1-1046">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span> <span data-ttu-id="ebec1-1047">Einführung in Kestrel, dem plattformübergreifenden [Webserver für ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="ebec1-1047">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="ebec1-1048">Kestrel ist der Webserver, der standardmäßig in ASP.NET Core-Projektvorlagen enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="ebec1-1048">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="ebec1-1049">Kestrel unterstützt die folgenden Szenarios:</span><span class="sxs-lookup"><span data-stu-id="ebec1-1049">Kestrel supports the following scenarios:</span></span> <span data-ttu-id="ebec1-1050">HTTPS</span><span class="sxs-lookup"><span data-stu-id="ebec1-1050">HTTPS</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ebec1-1051">Nicht transparente Upgrades, die zum Aktivieren von [WebSockets](https://github.com/aspnet/websockets) verwendet werden</span><span class="sxs-lookup"><span data-stu-id="ebec1-1051">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>

* <span data-ttu-id="ebec1-1052">Unix-Sockets für eine hohe Leistung im Hintergrund von Nginx</span><span class="sxs-lookup"><span data-stu-id="ebec1-1052">Unix sockets for high performance behind Nginx</span></span> <span data-ttu-id="ebec1-1053">Kestrel wird auf allen Plattformen und für alle Versionen unterstützt, die .NET Core unterstützt.</span><span class="sxs-lookup"><span data-stu-id="ebec1-1053">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* <span data-ttu-id="ebec1-1054">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ebec1-1054">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

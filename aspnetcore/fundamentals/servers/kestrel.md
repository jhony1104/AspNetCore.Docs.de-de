---
title: Implementierung des Webservers Kestrel in ASP.NET Core
author: guardrex
description: Einführung in Kestrel, dem plattformübergreifenden Webserver für ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/31/2019
uid: fundamentals/servers/kestrel
ms.openlocfilehash: bab751bc1453481a11114a7a8c0787fa5576e500
ms.sourcegitcommit: 77c8be22d5e88dd710f42c739748869f198865dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/01/2019
ms.locfileid: "73427060"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="66d20-103">Implementierung des Webservers Kestrel in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="66d20-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="66d20-104">Von [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), [Stephen Halter](https://twitter.com/halter73) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="66d20-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), [Stephen Halter](https://twitter.com/halter73), and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="66d20-105">Einführung in Kestrel, dem plattformübergreifenden [Webserver für ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="66d20-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="66d20-106">Kestrel ist der Webserver, der standardmäßig in ASP.NET Core-Projektvorlagen enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="66d20-106">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="66d20-107">Kestrel unterstützt die folgenden Szenarios:</span><span class="sxs-lookup"><span data-stu-id="66d20-107">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="66d20-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="66d20-108">HTTPS</span></span>
* <span data-ttu-id="66d20-109">Nicht transparente Upgrades, die zum Aktivieren von [WebSockets](https://github.com/aspnet/websockets) verwendet werden</span><span class="sxs-lookup"><span data-stu-id="66d20-109">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="66d20-110">Unix-Sockets für eine hohe Leistung im Hintergrund von Nginx</span><span class="sxs-lookup"><span data-stu-id="66d20-110">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="66d20-111">HTTP/2 (außer unter macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="66d20-111">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="66d20-112">&dagger;HTTP/2 wird unter macOS in einem zukünftigen Release unterstützt.</span><span class="sxs-lookup"><span data-stu-id="66d20-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="66d20-113">Kestrel wird auf allen Plattformen und für alle Versionen unterstützt, die .NET Core unterstützt.</span><span class="sxs-lookup"><span data-stu-id="66d20-113">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="66d20-114">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="66d20-114">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="66d20-115">HTTP/2-Unterstützung</span><span class="sxs-lookup"><span data-stu-id="66d20-115">HTTP/2 support</span></span>

<span data-ttu-id="66d20-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) ist für ASP.NET Core-Apps verfügbar, wenn die folgenden Basisanforderungen erfüllt sind:</span><span class="sxs-lookup"><span data-stu-id="66d20-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="66d20-117">Betriebssystem&dagger;</span><span class="sxs-lookup"><span data-stu-id="66d20-117">Operating system&dagger;</span></span>
  * <span data-ttu-id="66d20-118">Windows Server 2016/Windows 10 oder höher&Dagger;</span><span class="sxs-lookup"><span data-stu-id="66d20-118">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="66d20-119">Linux mit OpenSSL 1.0.2 oder höher (z.B. Ubuntu 16.04 oder höher)</span><span class="sxs-lookup"><span data-stu-id="66d20-119">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="66d20-120">Zielframework: .NET Core 2.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="66d20-120">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="66d20-121">[ALPN](https://tools.ietf.org/html/rfc7301#section-3)-Verbindung (Application-Layer Protocol Negotiation)</span><span class="sxs-lookup"><span data-stu-id="66d20-121">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="66d20-122">TLS 1.2-Verbindung oder höher</span><span class="sxs-lookup"><span data-stu-id="66d20-122">TLS 1.2 or later connection</span></span>

<span data-ttu-id="66d20-123">&dagger;HTTP/2 wird unter macOS in einem zukünftigen Release unterstützt.</span><span class="sxs-lookup"><span data-stu-id="66d20-123">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="66d20-124">&Dagger;Kestrel bietet eingeschränkte Unterstützung für HTTP/2 unter Windows Server 2012 R2 und Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="66d20-124">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="66d20-125">Die Unterstützung ist eingeschränkt, weil die Liste der unterstützten TLS-Verschlüsselungssammlungen unter diesen Betriebssystemen begrenzt ist.</span><span class="sxs-lookup"><span data-stu-id="66d20-125">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="66d20-126">Zum Sichern von TLS-Verbindungen ist möglicherweise ein durch einen Elliptic Curve Digital Signature Algorithm (ECDSA) generiertes Zertifikat erforderlich.</span><span class="sxs-lookup"><span data-stu-id="66d20-126">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="66d20-127">Wenn eine HTTP/2-Verbindung hergestellt wurde, meldet [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="66d20-127">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="66d20-128">HTTP/2 ist standardmäßig deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="66d20-128">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="66d20-129">Weitere Informationen zur Konfiguration finden Sie in den Abschnitten [Kestrel-Optionen](#kestrel-options) und [ListenOptions.Protocols](#listenoptionsprotocols).</span><span class="sxs-lookup"><span data-stu-id="66d20-129">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="66d20-130">Verwenden von Kestrel mit einem Reverseproxy</span><span class="sxs-lookup"><span data-stu-id="66d20-130">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="66d20-131">Kestrel kann eigenständig oder mit einem *Reverseproxyserver* wie z.B. [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="66d20-131">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="66d20-132">Ein Reverseproxyserver empfängt HTTP-Anforderungen aus dem Netzwerk und leitet diese an Kestrel weiter.</span><span class="sxs-lookup"><span data-stu-id="66d20-132">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="66d20-133">Kestrel bei Verwendung als Webserver mit direkter Internetverbindung:</span><span class="sxs-lookup"><span data-stu-id="66d20-133">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel kommuniziert direkt und ohne Reverseproxyserver mit dem Internet](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="66d20-135">Kestrel bei Verwendung in einer Reverseproxykonfiguration:</span><span class="sxs-lookup"><span data-stu-id="66d20-135">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel kommuniziert indirekt mit dem Internet über einen Reverseproxyserver wie IIS, Nginx oder Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="66d20-137">Jede der beiden Konfigurationen – mit oder ohne einen Reverseproxyserver – stellt eine unterstützte Hostingkonfiguration dar.</span><span class="sxs-lookup"><span data-stu-id="66d20-137">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="66d20-138">Bei Verwendung als Edgeserver ohne Reverseproxyserver unterstützt Kestrel die gemeinsame Nutzung der gleichen IP-Adresse und des gleichen Ports durch mehrere Prozesse nicht.</span><span class="sxs-lookup"><span data-stu-id="66d20-138">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="66d20-139">Wenn Kestrel für das Lauschen an einem Port konfiguriert ist, verarbeitet Kestrel den gesamten Datenverkehr für diesen Port unabhängig von den `Host`-Headern der Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="66d20-139">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="66d20-140">Ein Reverseproxy, der Ports freigeben kann, kann Anforderungen an Kestrel über eine eindeutige IP und einen eindeutigen Port weiterleiten.</span><span class="sxs-lookup"><span data-stu-id="66d20-140">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="66d20-141">Auch wenn kein Reverseproxyserver erforderlich ist, kann die Verwendung eines solchen empfehlenswert sein.</span><span class="sxs-lookup"><span data-stu-id="66d20-141">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="66d20-142">Für einen Reverseproxy gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="66d20-142">A reverse proxy:</span></span>

* <span data-ttu-id="66d20-143">Er kann die verfügbar gemachten öffentlichen Oberflächen der von ihm gehosteten Apps einschränken.</span><span class="sxs-lookup"><span data-stu-id="66d20-143">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="66d20-144">Er stellt eine zusätzliche Ebene für Konfiguration und Schutz bereit.</span><span class="sxs-lookup"><span data-stu-id="66d20-144">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="66d20-145">Er lässt sich besser in die vorhandene Infrastruktur integrieren.</span><span class="sxs-lookup"><span data-stu-id="66d20-145">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="66d20-146">Er vereinfacht die Konfiguration von Lastenausgleich und sicherer Kommunikation (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="66d20-146">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="66d20-147">Nur der Reverseproxyserver erfordert ein X.509-Zertifikat, und dieser Server kann mit den Servern der App im internen Netzwerk über einfaches HTTP kommunizieren.</span><span class="sxs-lookup"><span data-stu-id="66d20-147">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="66d20-148">Das Hosting in einer Reverseproxykonfiguration erfordert [Hostfilterung](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="66d20-148">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="66d20-149">Verwenden von Kestrel in ASP.NET Core-Apps</span><span class="sxs-lookup"><span data-stu-id="66d20-149">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="66d20-150">ASP.NET Core-Projektvorlagen verwenden Kestrel standardmäßig.</span><span class="sxs-lookup"><span data-stu-id="66d20-150">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="66d20-151">In *Program.cs* ruft die App `ConfigureWebHostDefaults` auf, wodurch im Hintergrund <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="66d20-151">In *Program.cs*, the app calls `ConfigureWebHostDefaults`, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="66d20-152">Weitere Informationen zum Erstellen des Hosts finden Sie im Artikel <xref:fundamentals/host/generic-host#set-up-a-host> in den Abschnitten *Einrichten eines Hosts* und *Standardeinstellungen für den Generator*.</span><span class="sxs-lookup"><span data-stu-id="66d20-152">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="66d20-153">Um zusätzliche Konfiguration nach dem Aufruf von `ConfigureWebHostDefaults` bereitzustellen, verwenden Sie `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="66d20-153">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="66d20-154">Kestrel-Optionen</span><span class="sxs-lookup"><span data-stu-id="66d20-154">Kestrel options</span></span>

<span data-ttu-id="66d20-155">Der Kestrel-Webserver verfügt über einschränkende Konfigurationsoptionen, die besonders nützlich bei Bereitstellungen mit Internetzugriff sind.</span><span class="sxs-lookup"><span data-stu-id="66d20-155">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="66d20-156">Legen Sie Einschränkungen für die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits>-Eigenschaft der <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>-Klasse fest.</span><span class="sxs-lookup"><span data-stu-id="66d20-156">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="66d20-157">Die `Limits`-Eigenschaft enthält eine Instanz der <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>-Klasse.</span><span class="sxs-lookup"><span data-stu-id="66d20-157">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="66d20-158">In den folgenden Beispielen wird der <xref:Microsoft.AspNetCore.Server.Kestrel.Core>-Namespace verwendet:</span><span class="sxs-lookup"><span data-stu-id="66d20-158">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="66d20-159">Kestrel-Optionen, die in den folgenden Beispielen in C#-Code konfiguriert sind, können auch mit einem [Konfigurationsanbieter](xref:fundamentals/configuration/index) festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="66d20-159">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="66d20-160">Beispielsweise kann der Dateikonfigurationsanbieter die Kestrel-Konfiguration aus einer *appsettings.json*- oder *appsettings.{Umgebung}.json*-Datei laden:</span><span class="sxs-lookup"><span data-stu-id="66d20-160">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="66d20-161">Verwenden Sie **einen** der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="66d20-161">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="66d20-162">Konfigurieren Sie Kestrel in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="66d20-162">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="66d20-163">Fügen Sie eine Instanz von `IConfiguration` in die `Startup`-Klasse ein.</span><span class="sxs-lookup"><span data-stu-id="66d20-163">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="66d20-164">Im folgenden Beispiel wird davon ausgegangen, dass die eingefügte Konfiguration der `Configuration`-Eigenschaft zugewiesen wird.</span><span class="sxs-lookup"><span data-stu-id="66d20-164">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="66d20-165">Laden Sie in `Startup.ConfigureServices` den Abschnitt `Kestrel` der Konfiguration in die Konfiguration von Kestrel.</span><span class="sxs-lookup"><span data-stu-id="66d20-165">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration.</span></span>

     ```csharp
     // using Microsoft.Extensions.Configuration

     public void ConfigureServices(IServiceCollection services)
     {
         services.Configure<KestrelServerOptions>(
             Configuration.GetSection("Kestrel"));
     }
     ```

* <span data-ttu-id="66d20-166">Konfigurieren Sie Kestrel beim Erstellen des Hosts:</span><span class="sxs-lookup"><span data-stu-id="66d20-166">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="66d20-167">Laden Sie in *Program.cs* den Abschnitt `Kestrel` der Konfiguration in die Konfiguration von Kestrel:</span><span class="sxs-lookup"><span data-stu-id="66d20-167">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="66d20-168">Beide vorangehenden Ansätze funktionieren mit jedem [Konfigurationsanbieter](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="66d20-168">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="66d20-169">Keep-Alive-Timeout</span><span class="sxs-lookup"><span data-stu-id="66d20-169">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="66d20-170">Ruft das [Keep-Alive-Timeout](https://tools.ietf.org/html/rfc7230#section-6.5) ab oder legt es fest.</span><span class="sxs-lookup"><span data-stu-id="66d20-170">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="66d20-171">Standardwert: 2 Minuten.</span><span class="sxs-lookup"><span data-stu-id="66d20-171">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="66d20-172">Maximale Anzahl der Clientverbindungen</span><span class="sxs-lookup"><span data-stu-id="66d20-172">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="66d20-173">Die maximale Anzahl von gleichzeitig geöffneten TCP-Verbindungen kann mithilfe von folgendem Code für die gesamte App festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="66d20-173">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="66d20-174">Es gibt einen separaten Grenzwert für Verbindungen, die von HTTP oder HTTPS auf ein anderes Protokoll aktualisiert wurden (z.B. auf eine WebSockets-Anforderung).</span><span class="sxs-lookup"><span data-stu-id="66d20-174">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="66d20-175">Nachdem eine Verbindung aktualisiert wurde, zählt diese nicht mehr für den `MaxConcurrentConnections`-Grenzwert.</span><span class="sxs-lookup"><span data-stu-id="66d20-175">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="66d20-176">Die maximale Anzahl von Verbindungen ist standardmäßig nicht begrenzt (NULL).</span><span class="sxs-lookup"><span data-stu-id="66d20-176">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="66d20-177">Maximale Größe des Anforderungstexts</span><span class="sxs-lookup"><span data-stu-id="66d20-177">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="66d20-178">Die maximale Größe des Anforderungstexts beträgt standardmäßig 30.000.000 Byte, also ungefähr 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="66d20-178">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="66d20-179">Die empfohlene Methode zur Außerkraftsetzung des Grenzwerts in einer ASP.NET Core-MVC-App besteht im Verwenden des <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>-Attributs in einer Aktionsmethode:</span><span class="sxs-lookup"><span data-stu-id="66d20-179">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="66d20-180">Im folgenden Beispiel wird veranschaulicht, wie die Einschränkungen auf jeder Anforderung für die App konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="66d20-180">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="66d20-181">Außer Kraft setzen der Einstellung für eine bestimmte Anforderung in Middleware:</span><span class="sxs-lookup"><span data-stu-id="66d20-181">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="66d20-182">Eine Ausnahme wird ausgelöst, wenn die App den Grenzwert einer Anforderung konfiguriert, nachdem die App bereits mit dem Lesen der Anforderung begonnen hat.</span><span class="sxs-lookup"><span data-stu-id="66d20-182">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="66d20-183">Es gibt eine `IsReadOnly`-Eigenschaft, die angibt, wenn sich die `MaxRequestBodySize`-Eigenschaft im schreibgeschützten Zustand befindet, also wenn der Grenzwert nicht mehr konfiguriert werden kann.</span><span class="sxs-lookup"><span data-stu-id="66d20-183">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="66d20-184">Wenn eine App [prozessextern](xref:host-and-deploy/iis/index#out-of-process-hosting-model) hinter dem [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) ausgeführt wird, ist das Größenlimit von Kestrel für Anforderungstext deaktiviert, weil IIS dieses Limit bereits festlegt.</span><span class="sxs-lookup"><span data-stu-id="66d20-184">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="66d20-185">Minimale Datenrate des Anforderungstexts</span><span class="sxs-lookup"><span data-stu-id="66d20-185">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="66d20-186">Kestrel überprüft sekündlich, ob Daten mit der angegebenen Rate in Bytes/Sekunde eingehen.</span><span class="sxs-lookup"><span data-stu-id="66d20-186">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="66d20-187">Wenn die Rate den mindestens erforderlichen Wert unterschreitet, wird für die Verbindung wegen Timeout getrennt. Bei der Toleranzperiode handelt es sich um die Zeitspanne, die Kestrel dem Client gewährt, um die Senderate auf den mindestens erforderlichen Wert zu erhöhen. Die Rate wird währenddessen nicht überprüft.</span><span class="sxs-lookup"><span data-stu-id="66d20-187">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="66d20-188">Diese Toleranzperiode beugt dem Trennen von Verbindungen vor, die Daten aufgrund eines langsamen TCP-Starts anfänglich mit einer niedrigen Rate senden.</span><span class="sxs-lookup"><span data-stu-id="66d20-188">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="66d20-189">Die mindestens erforderliche Rate beträgt standardmäßig 240 Bytes/Sekunde mit einer Toleranzperiode von 5 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="66d20-189">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="66d20-190">Für die Antwort gilt ebenfalls eine mindestens erforderliche Rate.</span><span class="sxs-lookup"><span data-stu-id="66d20-190">A minimum rate also applies to the response.</span></span> <span data-ttu-id="66d20-191">Der Code zum Festlegen des Grenzwerts für Anforderung und Antwort ist abgesehen von `RequestBody` oder `Response` in den Namen von Eigenschaften und Schnittstelle identisch.</span><span class="sxs-lookup"><span data-stu-id="66d20-191">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="66d20-192">In diesem Beispiel wird veranschaulicht, wie Sie die mindestens erforderlichen Datenraten in *Program.cs* konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="66d20-192">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="66d20-193">Außer Kraft setzen des minimalen Ratenlimits pro Anforderung in Middleware:</span><span class="sxs-lookup"><span data-stu-id="66d20-193">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="66d20-194">Das <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> aus dem vorherigen Beispiel ist in `HttpContext.Features` für HTTP/2-Anforderungen nicht vorhanden, weil die Änderung von Ratenlimits für jede Anforderung aufgrund der Unterstützung für Anforderungsmultiplexing von HTTP/2 von diesem Protokoll unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="66d20-194">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="66d20-195"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> ist jedoch in `HttpContext.Features` für HTTP/2-Anforderungen noch vorhanden, weil das Leseratenlimit weiterhin für jede Anforderung *vollständig deaktiviert* werden kann, indem `IHttpMinRequestBodyDataRateFeature.MinDataRate` auch für eine HTTP/2-Anforderung auf `null` festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="66d20-195">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="66d20-196">Der Versuch, `IHttpMinRequestBodyDataRateFeature.MinDataRate` zu lesen oder auf einen anderen Wert als `null` festzulegen, führt dazu, dass bei einer HTTP/2-Anforderung eine `NotSupportedException` ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="66d20-196">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="66d20-197">Serverweite Ratenlimits, die über `KestrelServerOptions.Limits` konfiguriert sind, gelten auch für HTTP/1.x- und HTTP/2-Verbindungen.</span><span class="sxs-lookup"><span data-stu-id="66d20-197">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="66d20-198">Timeout für Anforderungsheader</span><span class="sxs-lookup"><span data-stu-id="66d20-198">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="66d20-199">Ruft die maximale Zeitspanne ab, während der der Server Anforderungsheader empfängt, oder legt diese fest.</span><span class="sxs-lookup"><span data-stu-id="66d20-199">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="66d20-200">Der Standardwert beträgt 30 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="66d20-200">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="66d20-201">Maximale Datenströme pro Verbindung</span><span class="sxs-lookup"><span data-stu-id="66d20-201">Maximum streams per connection</span></span>

<span data-ttu-id="66d20-202">`Http2.MaxStreamsPerConnection` schränkt die Anzahl gleichzeitiger Anforderungsdatenströme pro HTTP/2-Verbindung ein.</span><span class="sxs-lookup"><span data-stu-id="66d20-202">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="66d20-203">Überschüssige Datenströme werden zurückgewiesen.</span><span class="sxs-lookup"><span data-stu-id="66d20-203">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="66d20-204">Der Standardwert ist 100.</span><span class="sxs-lookup"><span data-stu-id="66d20-204">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="66d20-205">Größe der Headertabelle</span><span class="sxs-lookup"><span data-stu-id="66d20-205">Header table size</span></span>

<span data-ttu-id="66d20-206">Der HPACK-Decoder dekomprimiert HTTP-Header für HTTP/2-Verbindungen.</span><span class="sxs-lookup"><span data-stu-id="66d20-206">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="66d20-207">`Http2.HeaderTableSize` schränkt die Größe der Headerkomprimierungstabelle ein, die der HPACK-Decoder verwendet.</span><span class="sxs-lookup"><span data-stu-id="66d20-207">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="66d20-208">Der Wert wird in Oktetten bereitgestellt und muss größer als null (0) sein.</span><span class="sxs-lookup"><span data-stu-id="66d20-208">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="66d20-209">Der Standardwert ist 4096.</span><span class="sxs-lookup"><span data-stu-id="66d20-209">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="66d20-210">Maximale Framegröße</span><span class="sxs-lookup"><span data-stu-id="66d20-210">Maximum frame size</span></span>

<span data-ttu-id="66d20-211">`Http2.MaxFrameSize` gibt die maximal zulässige Größe einer vom Server empfangenen oder gesendeten HTTP/2-Verbindungsrahmen-Nutzlast an.</span><span class="sxs-lookup"><span data-stu-id="66d20-211">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="66d20-212">Der Wert wird in Oktetten bereitgestellt und muss zwischen 2^14 (16.384) und 2^24-1 (16.777.215) liegen.</span><span class="sxs-lookup"><span data-stu-id="66d20-212">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="66d20-213">Der Standardwert ist 2^14 (16.384).</span><span class="sxs-lookup"><span data-stu-id="66d20-213">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="66d20-214">Maximale Größe des Anforderungsheaders</span><span class="sxs-lookup"><span data-stu-id="66d20-214">Maximum request header size</span></span>

<span data-ttu-id="66d20-215">`Http2.MaxRequestHeaderFieldSize` gibt die maximal zulässige Größe in Oktetten der Anforderungsheaderwerte an.</span><span class="sxs-lookup"><span data-stu-id="66d20-215">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="66d20-216">Dieser Grenzwert gilt sowohl für den Namen als auch den Wert in der komprimierten und nicht komprimierten Darstellung.</span><span class="sxs-lookup"><span data-stu-id="66d20-216">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="66d20-217">Der Wert muss größer als 0 (null) sein.</span><span class="sxs-lookup"><span data-stu-id="66d20-217">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="66d20-218">Der Standardwert ist 8.192.</span><span class="sxs-lookup"><span data-stu-id="66d20-218">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="66d20-219">Anfangsfenstergröße der Verbindung</span><span class="sxs-lookup"><span data-stu-id="66d20-219">Initial connection window size</span></span>

<span data-ttu-id="66d20-220">`Http2.InitialConnectionWindowSize` gibt die maximalen Anforderungstextdaten in Byte an, die der Server auf einmal für alle Anforderungen (Streams) pro Verbindung aggregiert.</span><span class="sxs-lookup"><span data-stu-id="66d20-220">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="66d20-221">Anforderungen werden auch durch `Http2.InitialStreamWindowSize` beschränkt.</span><span class="sxs-lookup"><span data-stu-id="66d20-221">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="66d20-222">Der Wert muss größer als oder gleich 65.535 und kleiner als 2^31 (2.147.483.648) sein.</span><span class="sxs-lookup"><span data-stu-id="66d20-222">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="66d20-223">Der Standardwert ist 128 KB (131.072).</span><span class="sxs-lookup"><span data-stu-id="66d20-223">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="66d20-224">Anfangsfenstergröße des Streams</span><span class="sxs-lookup"><span data-stu-id="66d20-224">Initial stream window size</span></span>

<span data-ttu-id="66d20-225">`Http2.InitialStreamWindowSize` gibt die maximalen Anforderungstextdaten in Byte an, die der Server auf einmal pro Anforderung (Stream) puffert.</span><span class="sxs-lookup"><span data-stu-id="66d20-225">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="66d20-226">Anforderungen werden auch durch `Http2.InitialConnectionWindowSize` beschränkt.</span><span class="sxs-lookup"><span data-stu-id="66d20-226">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="66d20-227">Der Wert muss größer als oder gleich 65.535 und kleiner als 2^31 (2.147.483.648) sein.</span><span class="sxs-lookup"><span data-stu-id="66d20-227">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="66d20-228">Der Standardwert ist 96 KB (98.304).</span><span class="sxs-lookup"><span data-stu-id="66d20-228">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="66d20-229">Synchrone E/A-Vorgänge</span><span class="sxs-lookup"><span data-stu-id="66d20-229">Synchronous IO</span></span>

<span data-ttu-id="66d20-230"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> steuert, ob für Anforderung und Antwort synchrone E/A-Vorgänge zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="66d20-230"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="66d20-231">Der Standardwert ist `false`sein.</span><span class="sxs-lookup"><span data-stu-id="66d20-231">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="66d20-232">Sehr viele blockierende synchrone E/A-Vorgänge können zu einem Ressourcenmangel im Threadpool führen, wodurch die App nicht mehr reagiert.</span><span class="sxs-lookup"><span data-stu-id="66d20-232">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="66d20-233">Aktivieren Sie `AllowSynchronousIO` nur bei Verwendung einer Bibliothek, die asynchrone E/A-Vorgänge nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="66d20-233">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="66d20-234">Das folgende Beispiel aktiviert synchrone E/A-Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="66d20-234">The following example enables synchronous IO:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="66d20-235">Weitere Informationen zu anderen Kestrel-Optionen und -Einschränkungen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="66d20-235">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="66d20-236">Endpunktkonfiguration</span><span class="sxs-lookup"><span data-stu-id="66d20-236">Endpoint configuration</span></span>

<span data-ttu-id="66d20-237">Standardmäßig wird ASP.NET Core an Folgendes gebunden:</span><span class="sxs-lookup"><span data-stu-id="66d20-237">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="66d20-238">`https://localhost:5001` (wenn ein lokales Entwicklungszertifikat vorhanden ist)</span><span class="sxs-lookup"><span data-stu-id="66d20-238">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="66d20-239">Verwenden Sie Folgendes zum Angeben der URLs:</span><span class="sxs-lookup"><span data-stu-id="66d20-239">Specify URLs using the:</span></span>

* <span data-ttu-id="66d20-240">Die Umgebungsvariable `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="66d20-240">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="66d20-241">Das Befehlszeilenargument `--urls`</span><span class="sxs-lookup"><span data-stu-id="66d20-241">`--urls` command-line argument.</span></span>
* <span data-ttu-id="66d20-242">Den Hostkonfigurationsschlüssel `urls`</span><span class="sxs-lookup"><span data-stu-id="66d20-242">`urls` host configuration key.</span></span>
* <span data-ttu-id="66d20-243">Die Erweiterungsmethode `UseUrls`</span><span class="sxs-lookup"><span data-stu-id="66d20-243">`UseUrls` extension method.</span></span>

<span data-ttu-id="66d20-244">Der Wert, der mit diesen Ansätzen angegeben wird, kann mindestens ein HTTP- oder HTTPS-Endpunkt sein (HTTPS wenn ein Standardzertifikat verfügbar ist).</span><span class="sxs-lookup"><span data-stu-id="66d20-244">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="66d20-245">Konfigurieren Sie den Wert als eine durch Semikolons getrennte Liste (z.B. `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="66d20-245">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="66d20-246">Weitere Informationen zu diesen Ansätzen finden Sie unter [Server-URLs](xref:fundamentals/host/web-host#server-urls) und [Außerkraftsetzen der Konfiguration](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="66d20-246">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="66d20-247">Ein Entwicklungszertifikat wird erstellt:</span><span class="sxs-lookup"><span data-stu-id="66d20-247">A development certificate is created:</span></span>

* <span data-ttu-id="66d20-248">Wenn das [.NET Core SDK](/dotnet/core/sdk) installiert wird.</span><span class="sxs-lookup"><span data-stu-id="66d20-248">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="66d20-249">Das [dev-cert-Tool](xref:aspnetcore-2.1#https) wird zum Erstellen eines Zertifikats verwendet.</span><span class="sxs-lookup"><span data-stu-id="66d20-249">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="66d20-250">Einige Browser erfordern, dass Sie die explizite Berechtigung erteilen, dem lokalen Entwicklungszertifikat zu vertrauen.</span><span class="sxs-lookup"><span data-stu-id="66d20-250">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="66d20-251">Projektvorlagen konfigurieren Apps, damit sie standardmäßig auf HTTPS ausgeführt werden und die [HTTPS-Umleitung und HSTS-Unterstützung](xref:security/enforcing-ssl) enthalten.</span><span class="sxs-lookup"><span data-stu-id="66d20-251">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="66d20-252">Rufen Sie die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>- oder <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>-Methode unter <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> auf, um URL-Präfixe und Ports für Kestrel zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="66d20-252">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="66d20-253">`UseUrls`, das Befehlszeilenargument `--urls`, der Hostkonfigurationsschlüssel `urls` und die Umgebungsvariable `ASPNETCORE_URLS` funktionieren ebenfalls, verfügen jedoch über Einschränkungen, die im Verlauf dieses Abschnitts erläutert werden (Ein Standardzertifikat muss für die HTTPS-Endpunktkonfiguration verfügbar sein).</span><span class="sxs-lookup"><span data-stu-id="66d20-253">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="66d20-254">`KestrelServerOptions`-Konfiguration:</span><span class="sxs-lookup"><span data-stu-id="66d20-254">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="66d20-255">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="66d20-255">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="66d20-256">Gibt die Konfiguration von `Action` zum Ausführen von jedem angegebenen Endpunkt an.</span><span class="sxs-lookup"><span data-stu-id="66d20-256">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="66d20-257">Mehrmalige Aufrufe von `ConfigureEndpointDefaults` ersetzen vorherige Instanzen von `Action` mit der zuletzt angegebenen `Action`.</span><span class="sxs-lookup"><span data-stu-id="66d20-257">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="66d20-258">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="66d20-258">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="66d20-259">Gibt die Konfiguration von `Action` zum Ausführen von jedem HTTPS-Endpunkt an.</span><span class="sxs-lookup"><span data-stu-id="66d20-259">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="66d20-260">Mehrmalige Aufrufe von `ConfigureHttpsDefaults` ersetzen vorherige Instanzen von `Action` mit der zuletzt angegebenen `Action`.</span><span class="sxs-lookup"><span data-stu-id="66d20-260">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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

### <a name="configureiconfiguration"></a><span data-ttu-id="66d20-261">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="66d20-261">Configure(IConfiguration)</span></span>

<span data-ttu-id="66d20-262">Erstellt ein Konfigurationsladeprogramm für das Einrichten von Kestrel, was <xref:Microsoft.Extensions.Configuration.IConfiguration> als Eingabe erfordert.</span><span class="sxs-lookup"><span data-stu-id="66d20-262">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="66d20-263">Die Konfiguration muss auf den Konfigurationsabschnitt für Kestrel festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="66d20-263">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="66d20-264">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="66d20-264">ListenOptions.UseHttps</span></span>

<span data-ttu-id="66d20-265">Konfiguriert Kestrel zur Verwendung von HTTPS.</span><span class="sxs-lookup"><span data-stu-id="66d20-265">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="66d20-266">`ListenOptions.UseHttps`-Erweiterungen:</span><span class="sxs-lookup"><span data-stu-id="66d20-266">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="66d20-267">`UseHttps`: Konfiguriert Kestrel zur Verwendung von HTTPS mit dem Standardzertifikat.</span><span class="sxs-lookup"><span data-stu-id="66d20-267">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="66d20-268">Löst eine Ausnahme aus, wenn kein Standardzertifikat konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="66d20-268">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="66d20-269">`ListenOptions.UseHttps`-Parameter:</span><span class="sxs-lookup"><span data-stu-id="66d20-269">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="66d20-270">`filename` entspricht dem Pfad und Dateinamen einer Zertifikatdatei relativ zu dem Verzeichnis, das die Inhaltsdateien der App enthält.</span><span class="sxs-lookup"><span data-stu-id="66d20-270">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="66d20-271">`password` ist das für den Zugriff auf die X.509-Zertifikatsdaten erforderliche Kennwort.</span><span class="sxs-lookup"><span data-stu-id="66d20-271">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="66d20-272">`configureOptions` ist eine `Action` zum Konfigurieren von `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="66d20-272">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="66d20-273">Gibt `ListenOptions` zurück.</span><span class="sxs-lookup"><span data-stu-id="66d20-273">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="66d20-274">`storeName` ist der Zertifikatspeicher, aus dem das Zertifikat geladen wird.</span><span class="sxs-lookup"><span data-stu-id="66d20-274">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="66d20-275">`subject` ist der Name des Antragstellers für das Zertifikat.</span><span class="sxs-lookup"><span data-stu-id="66d20-275">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="66d20-276">`allowInvalid` gibt an, ob ungültige Zertifikate berücksichtigt werden sollten, z.B. selbstsignierte Zertifikate.</span><span class="sxs-lookup"><span data-stu-id="66d20-276">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="66d20-277">`location` ist der Speicherort, aus dem das Zertifikat geladen wird.</span><span class="sxs-lookup"><span data-stu-id="66d20-277">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="66d20-278">`serverCertificate` ist das X.509-Zertifikat.</span><span class="sxs-lookup"><span data-stu-id="66d20-278">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="66d20-279">In der Produktion muss HTTPS explizit konfiguriert sein.</span><span class="sxs-lookup"><span data-stu-id="66d20-279">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="66d20-280">Zumindest muss ein Standardzertifikat angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="66d20-280">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="66d20-281">Die im Folgenden beschriebenen unterstützten Konfigurationen:</span><span class="sxs-lookup"><span data-stu-id="66d20-281">Supported configurations described next:</span></span>

* <span data-ttu-id="66d20-282">Keine Konfiguration</span><span class="sxs-lookup"><span data-stu-id="66d20-282">No configuration</span></span>
* <span data-ttu-id="66d20-283">Ersetzen des Standardzertifikats aus der Konfiguration</span><span class="sxs-lookup"><span data-stu-id="66d20-283">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="66d20-284">Ändern des Standards im Code</span><span class="sxs-lookup"><span data-stu-id="66d20-284">Change the defaults in code</span></span>

<span data-ttu-id="66d20-285">*Keine Konfiguration*</span><span class="sxs-lookup"><span data-stu-id="66d20-285">*No configuration*</span></span>

<span data-ttu-id="66d20-286">Kestrel überwacht `http://localhost:5000` und `https://localhost:5001` (wenn ein Standardzertifikat verfügbar ist).</span><span class="sxs-lookup"><span data-stu-id="66d20-286">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="66d20-287">*Ersetzen des Standardzertifikats aus der Konfiguration*</span><span class="sxs-lookup"><span data-stu-id="66d20-287">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="66d20-288">`CreateDefaultBuilder` ruft `Configure(context.Configuration.GetSection("Kestrel"))` standardmäßig zum Laden der Kestrel-Konfiguration auf.</span><span class="sxs-lookup"><span data-stu-id="66d20-288">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="66d20-289">Ein Standardkonfigurationsschema für HTTPS-App-Einstellungen ist für Kestrel verfügbar.</span><span class="sxs-lookup"><span data-stu-id="66d20-289">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="66d20-290">Konfigurieren Sie mehrere Endpunkte, einschließlich der zu verwendenden URLs und Zertifikate aus einer Datei auf dem Datenträger oder einem Zertifikatspeicher.</span><span class="sxs-lookup"><span data-stu-id="66d20-290">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="66d20-291">Die Vorgehensweise im folgenden *appsettings.json*-Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66d20-291">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="66d20-292">Legen Sie **AllowInvalid** auf `true` fest, um die Verwendung von ungültigen Zertifikaten zu erlauben (z.B. selbstsignierte Zertifikate).</span><span class="sxs-lookup"><span data-stu-id="66d20-292">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="66d20-293">Jeder HTTPS-Endpunkt, der kein Zertifikat angibt (im folgenden Beispiel der Endpunkt **HttpsDefaultCert**), greift auf das unter **Zertifikate**>**Standard** festgelegte Zertifikat oder das Entwicklungszertifikat zurück.</span><span class="sxs-lookup"><span data-stu-id="66d20-293">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="66d20-294">Alternativ zur Verwendung von **Pfad** und **Kennwort** für alle Zertifikatknoten können Sie das Zertifikat mithilfe von Zertifikatspeicherfeldern angeben.</span><span class="sxs-lookup"><span data-stu-id="66d20-294">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="66d20-295">Das Zertifikat unter **Zertifikate** > **Standard** kann beispielweise wie folgt angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="66d20-295">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="66d20-296">Schema-Hinweise:</span><span class="sxs-lookup"><span data-stu-id="66d20-296">Schema notes:</span></span>

* <span data-ttu-id="66d20-297">Bei den Namen der Endpunkte wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="66d20-297">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="66d20-298">Zum Beispiel sind `HTTPS` und `Https` gültig.</span><span class="sxs-lookup"><span data-stu-id="66d20-298">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="66d20-299">Der Parameter `Url` ist für jeden Endpunkt erforderlich.</span><span class="sxs-lookup"><span data-stu-id="66d20-299">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="66d20-300">Das Format für diesen Parameter ist identisch mit dem allgemeinen Konfigurationsparameter `Urls`, mit der Ausnahme, dass er auf einen einzelnen Wert begrenzt ist.</span><span class="sxs-lookup"><span data-stu-id="66d20-300">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="66d20-301">Diese Endpunkte ersetzen die in der allgemeinen `Urls`-Konfiguration festgelegten Endpunkte, anstatt zu ihnen hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="66d20-301">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="66d20-302">Endpunkte, die über `Listen` in Code definiert werden, werden den im Konfigurationsabschnitt definierten Endpunkten hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="66d20-302">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="66d20-303">Der `Certificate`-Abschnitt ist optional.</span><span class="sxs-lookup"><span data-stu-id="66d20-303">The `Certificate` section is optional.</span></span> <span data-ttu-id="66d20-304">Wenn der `Certificate`-Abschnitt nicht angegeben ist, werden die in früheren Szenarios definierten Standardwerte verwendet.</span><span class="sxs-lookup"><span data-stu-id="66d20-304">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="66d20-305">Wenn keine Standardwerte verfügbar sind, löst der Server eine Ausnahme aus und startet nicht.</span><span class="sxs-lookup"><span data-stu-id="66d20-305">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="66d20-306">Der `Certificate`-Abschnitt unterstützt die Zertifikate **Pfad**&ndash;**Kennwort** und **Subject**&ndash;**Store** (Antragsteller–Speicher).</span><span class="sxs-lookup"><span data-stu-id="66d20-306">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="66d20-307">Auf diese Weise kann eine beliebige Anzahl von Endpunkten definiert werden, solange Sie keine Portkonflikte verursachen.</span><span class="sxs-lookup"><span data-stu-id="66d20-307">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="66d20-308">`options.Configure(context.Configuration.GetSection("{SECTION}"))` gibt `KestrelConfigurationLoader` mit der Methode `.Endpoint(string name, listenOptions => { })` zurück, die dazu verwendet werden kann, die Einstellungen eines Endpunkts zu ergänzen:</span><span class="sxs-lookup"><span data-stu-id="66d20-308">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="66d20-309">Auf `KestrelServerOptions.ConfigurationLoader` kann direkt zugegriffen werden, um die Iteration auf dem vorhandenen Ladeprogramm fortzusetzen, etwa auf dem von <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> bereitgestellten Ladeprogramm.</span><span class="sxs-lookup"><span data-stu-id="66d20-309">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="66d20-310">Der Konfigurationsabschnitt ist für jeden Endpunkt in den Optionen der Methode `Endpoint` verfügbar, sodass benutzerdefinierte Einstellungen gelesen werden können.</span><span class="sxs-lookup"><span data-stu-id="66d20-310">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="66d20-311">Mehrere Konfigurationen können durch erneutes Aufrufen von `options.Configure(context.Configuration.GetSection("{SECTION}"))` mit einem anderen Abschnitt geladen werden.</span><span class="sxs-lookup"><span data-stu-id="66d20-311">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="66d20-312">Sofern `Load` nicht in einer vorherigen Instanz explizit aufgerufen wird, wird nur die letzte Konfiguration verwendet.</span><span class="sxs-lookup"><span data-stu-id="66d20-312">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="66d20-313">Das Metapaket ruft `Load` nicht auf, sodass der Abschnitt mit der Standardkonfiguration ersetzt werden kann.</span><span class="sxs-lookup"><span data-stu-id="66d20-313">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="66d20-314">`KestrelConfigurationLoader` spiegelt die API-Familie `Listen` von `KestrelServerOptions` als `Endpoint`-Überladungen, weshalb Code und Konfigurationsendpunkte am selben Ort konfiguriert werden können.</span><span class="sxs-lookup"><span data-stu-id="66d20-314">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="66d20-315">Diese Überladungen verwenden keine Namen und nutzen nur Standardeinstellungen aus der Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="66d20-315">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="66d20-316">*Ändern des Standards im Code*</span><span class="sxs-lookup"><span data-stu-id="66d20-316">*Change the defaults in code*</span></span>

<span data-ttu-id="66d20-317">`ConfigureEndpointDefaults` und `ConfigureHttpsDefaults` können zum Ändern der Standardeinstellungen für `ListenOptions` und `HttpsConnectionAdapterOptions` verwendet werden, einschließlich der Standardzertifikate, die im vorherigen Szenario festgelegt wurden.</span><span class="sxs-lookup"><span data-stu-id="66d20-317">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="66d20-318">`ConfigureEndpointDefaults` und `ConfigureHttpsDefaults` sollten aufgerufen werden, bevor Endpunkte konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="66d20-318">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="66d20-319">*Kestrel-Unterstützung für SNI*</span><span class="sxs-lookup"><span data-stu-id="66d20-319">*Kestrel support for SNI*</span></span>

<span data-ttu-id="66d20-320">Die [Servernamensanzeige (SNI)](https://tools.ietf.org/html/rfc6066#section-3) kann zum Hosten mehrerer Domänen auf der gleichen IP-Adresse und dem gleichen Port verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="66d20-320">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="66d20-321">Damit die Servernamensanzeige funktioniert, sendet der Client während des TLS-Handshakes den Hostnamen für die sichere Sitzung an den Server, sodass der Server das richtige Zertifikat bereitstellen kann.</span><span class="sxs-lookup"><span data-stu-id="66d20-321">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="66d20-322">Der Client verwendet das beigestellte Zertifikat für die verschlüsselte Kommunikation mit dem Server während der sicheren Sitzung nach dem TLS-Handshake.</span><span class="sxs-lookup"><span data-stu-id="66d20-322">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="66d20-323">Kestrel unterstützt die Servernamensanzeige über den `ServerCertificateSelector`-Rückruf.</span><span class="sxs-lookup"><span data-stu-id="66d20-323">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="66d20-324">Der Rückruf wird für jede Verbindung einmal aufgerufen, um der App zu ermöglichen, den Hostnamen zu überprüfen und das entsprechende Zertifikat auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="66d20-324">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="66d20-325">Für die Unterstützung der Servernamensanzeige benötigen Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="66d20-325">SNI support requires:</span></span>

* <span data-ttu-id="66d20-326">Wird auf dem Zielframework `netcoreapp2.1` oder höher ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="66d20-326">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="66d20-327">In `net461` oder höher, wird der Rückruf aufgerufen, `name` ist aber immer `null`.</span><span class="sxs-lookup"><span data-stu-id="66d20-327">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="66d20-328">`name` ist auch `null`, wenn der Client den Hostnamenparameter nicht im TLS-Handshake angibt.</span><span class="sxs-lookup"><span data-stu-id="66d20-328">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="66d20-329">Alle Websites werden in derselben Kestrel-Instanz ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="66d20-329">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="66d20-330">Kestrel unterstützt ohne Reverseproxy keine gemeinsame IP-Adresse und keinen gemeinsamen Port für mehrere Instanzen.</span><span class="sxs-lookup"><span data-stu-id="66d20-330">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="66d20-331">Verbindungsprotokollierung</span><span class="sxs-lookup"><span data-stu-id="66d20-331">Connection logging</span></span>

<span data-ttu-id="66d20-332">Rufen Sie <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> auf, um Protokolle auf Debugebene für die Kommunikation auf Byteebene für eine Verbindung auszugeben.</span><span class="sxs-lookup"><span data-stu-id="66d20-332">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="66d20-333">Die Verbindungsprotokollierung ist beim Beheben von Problemen bei der Low-Level-Kommunikation hilfreich, wie z. B. bei der TLS-Verschlüsselung und bei Proxys.</span><span class="sxs-lookup"><span data-stu-id="66d20-333">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="66d20-334">Wenn `UseConnectionLogging` vor `UseHttps` platziert wird, wird der verschlüsselte Datenverkehr protokolliert.</span><span class="sxs-lookup"><span data-stu-id="66d20-334">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="66d20-335">Wenn `UseConnectionLogging` nach `UseHttps` platziert wird, wird der entschlüsselte Datenverkehr protokolliert.</span><span class="sxs-lookup"><span data-stu-id="66d20-335">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="66d20-336">Binden an einen TCP-Socket</span><span class="sxs-lookup"><span data-stu-id="66d20-336">Bind to a TCP socket</span></span>

<span data-ttu-id="66d20-337">Die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>-Methode wird an ein TCP-Socket gebunden, und ein Lambdaausdruck einer Option lässt die Konfiguration des X.509-Zertifikats zu:</span><span class="sxs-lookup"><span data-stu-id="66d20-337">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="66d20-338">Im Beispiel wird HTTPS für einen Endpunkt mit <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="66d20-338">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="66d20-339">Verwenden Sie die gleiche API zum Konfigurieren anderer Kestrel-Einstellungen für bestimmte Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="66d20-339">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="66d20-340">Binden an einen Unix-Socket</span><span class="sxs-lookup"><span data-stu-id="66d20-340">Bind to a Unix socket</span></span>

<span data-ttu-id="66d20-341">Wie in diesem Beispiel dargestellt, lauschen Sie an einem Unix-Socket mit <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>, um eine verbesserte Leistung mit Nginx zu erzielen:</span><span class="sxs-lookup"><span data-stu-id="66d20-341">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

### <a name="port-0"></a><span data-ttu-id="66d20-342">Port 0</span><span class="sxs-lookup"><span data-stu-id="66d20-342">Port 0</span></span>

<span data-ttu-id="66d20-343">Wenn die Portnummer `0` angegeben wird, wird Kestrel dynamisch an einen verfügbaren Port gebunden.</span><span class="sxs-lookup"><span data-stu-id="66d20-343">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="66d20-344">Im folgenden Beispiel wird veranschaulicht, wie bestimmt werden kann, für welchen Port Kestrel zur Laufzeit eine Bindung erstellt hat:</span><span class="sxs-lookup"><span data-stu-id="66d20-344">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="66d20-345">Wenn die App ausgeführt wird, gibt das Ausgabefenster der Konsole den dynamischen Port an, über den die App erreicht werden kann:</span><span class="sxs-lookup"><span data-stu-id="66d20-345">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="66d20-346">Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="66d20-346">Limitations</span></span>

<span data-ttu-id="66d20-347">Konfigurieren Sie Endpunkte mithilfe der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="66d20-347">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="66d20-348">Befehlszeilenargument `--urls`</span><span class="sxs-lookup"><span data-stu-id="66d20-348">`--urls` command-line argument</span></span>
* <span data-ttu-id="66d20-349">Hostkonfigurationsschlüssel `urls`</span><span class="sxs-lookup"><span data-stu-id="66d20-349">`urls` host configuration key</span></span>
* <span data-ttu-id="66d20-350">Umgebungsvariable `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="66d20-350">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="66d20-351">Diese Methoden sind nützlich, wenn Ihr Code mit anderen Servern als Kestrel funktionieren soll.</span><span class="sxs-lookup"><span data-stu-id="66d20-351">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="66d20-352">Beachten Sie jedoch die folgenden Einschränkungen:</span><span class="sxs-lookup"><span data-stu-id="66d20-352">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="66d20-353">HTTPS kann nicht mit diesen Ansätzen verwendet werden, außer ein Standardzertifikat wird in der HTTPS-Endpunktkonfiguration angegeben (z.B. wenn Sie wie zuvor in diesem Artikel gezeigt die `KestrelServerOptions`-Konfiguration oder eine Konfigurationsdatei verwenden).</span><span class="sxs-lookup"><span data-stu-id="66d20-353">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="66d20-354">Wenn die Ansätze `Listen` und `UseUrls` gleichzeitig verwendet werden, überschreiben die `Listen`-Endpunkte die `UseUrls`-Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="66d20-354">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="66d20-355">IIS-Endpunktkonfiguration</span><span class="sxs-lookup"><span data-stu-id="66d20-355">IIS endpoint configuration</span></span>

<span data-ttu-id="66d20-356">Bei der Verwendung von IIS werden die URL-Bindungen für IIS-Überschreibungsbindungen durch `Listen` oder `UseUrls` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="66d20-356">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="66d20-357">Weitere Informationen finden Sie im Artikel [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="66d20-357">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="66d20-358">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="66d20-358">ListenOptions.Protocols</span></span>

<span data-ttu-id="66d20-359">Die `Protocols`-Eigenschaft richtet die HTTP-Protokolle (`HttpProtocols`) ein, die für einen Verbindungsendpunkt oder für den Server aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="66d20-359">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="66d20-360">Weisen Sie der `Protocols`-Eigenschaft einen Wert aus der `HttpProtocols`-Enumeration zu.</span><span class="sxs-lookup"><span data-stu-id="66d20-360">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="66d20-361">`HttpProtocols`-Enumerationswert</span><span class="sxs-lookup"><span data-stu-id="66d20-361">`HttpProtocols` enum value</span></span> | <span data-ttu-id="66d20-362">Zulässiges Verbindungsprotokoll</span><span class="sxs-lookup"><span data-stu-id="66d20-362">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="66d20-363">Nur HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="66d20-363">HTTP/1.1 only.</span></span> <span data-ttu-id="66d20-364">Kann mit oder ohne TLS verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="66d20-364">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="66d20-365">Nur HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="66d20-365">HTTP/2 only.</span></span> <span data-ttu-id="66d20-366">Kann nur ohne TLS verwendet werden, wenn der Client einen [Vorabkenntnis-Modus](https://tools.ietf.org/html/rfc7540#section-3.4) unterstützt.</span><span class="sxs-lookup"><span data-stu-id="66d20-366">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="66d20-367">HTTP/1.1 und HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="66d20-367">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="66d20-368">Für HTTP/2 muss der Client HTTP/2 im TLS [ALPN-Handshake (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) auswählen, andernfalls wird standardmäßig eine HTTP/1.1 verwendet.</span><span class="sxs-lookup"><span data-stu-id="66d20-368">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="66d20-369">Der Standardwert von `ListenOptions.Protocols` ist für alle Endpunkte `HttpProtocols.Http1AndHttp2`.</span><span class="sxs-lookup"><span data-stu-id="66d20-369">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="66d20-370">TLS-Einschränkungen für HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="66d20-370">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="66d20-371">TLS Version 1.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="66d20-371">TLS version 1.2 or later</span></span>
* <span data-ttu-id="66d20-372">Erneute Aushandlung deaktiviert</span><span class="sxs-lookup"><span data-stu-id="66d20-372">Renegotiation disabled</span></span>
* <span data-ttu-id="66d20-373">Komprimierung deaktiviert</span><span class="sxs-lookup"><span data-stu-id="66d20-373">Compression disabled</span></span>
* <span data-ttu-id="66d20-374">Minimale Größen für Austausch von flüchtigen Schlüsseln:</span><span class="sxs-lookup"><span data-stu-id="66d20-374">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="66d20-375">ECDHE (Elliptic Curve Diffie-Hellman) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; mindestens 224 Bits</span><span class="sxs-lookup"><span data-stu-id="66d20-375">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits minimum</span></span>
  * <span data-ttu-id="66d20-376">DHE (Finite Field Diffie-Hellman) &lbrack;`TLS12`&rbrack; &ndash; mindestens 2.048 Bits</span><span class="sxs-lookup"><span data-stu-id="66d20-376">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bits minimum</span></span>
* <span data-ttu-id="66d20-377">Verschlüsselungssammlung nicht gesperrt</span><span class="sxs-lookup"><span data-stu-id="66d20-377">Cipher suite not blacklisted</span></span>

<span data-ttu-id="66d20-378">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; mit der elliptischen P-256-Kurve &lbrack;`FIPS186`&rbrack; wird standardmäßig unterstützt.</span><span class="sxs-lookup"><span data-stu-id="66d20-378">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="66d20-379">Das folgende Beispiel erlaubt HTTP/1.1- und HTTP/2-Verbindungen an Port 8000.</span><span class="sxs-lookup"><span data-stu-id="66d20-379">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="66d20-380">Die Verbindungen werden durch TLS mit einem bereitgestellten Zertifikat geschützt:</span><span class="sxs-lookup"><span data-stu-id="66d20-380">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="66d20-381">Verwenden Sie Verbindungsmiddleware, um TLS-Handshakes auf Verbindungsbasis für bestimmte Verschlüsselungen zu filtern, falls erforderlich.</span><span class="sxs-lookup"><span data-stu-id="66d20-381">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="66d20-382">Im folgenden Beispiel wird <xref:System.NotSupportedException> für jeden Verschlüsselungsalgorithmus ausgelöst, der von der App nicht unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="66d20-382">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="66d20-383">Alternativ können Sie [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) definieren und mit einer Liste zulässiger Verschlüsselungssammlungen vergleichen.</span><span class="sxs-lookup"><span data-stu-id="66d20-383">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="66d20-384">Es wird keine Verschlüsselung mit einem [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType)-Verschlüsselungsalgorithmus verwendet.</span><span class="sxs-lookup"><span data-stu-id="66d20-384">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

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

<span data-ttu-id="66d20-385">Die Verbindungsfilterung kann auch über einen <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder>-Lambdaausdruck konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="66d20-385">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

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

<span data-ttu-id="66d20-386">Unter Linux kann <xref:System.Net.Security.CipherSuitesPolicy> zum Filtern von TLS-Handshakes auf Verbindungsbasis verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="66d20-386">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

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

<span data-ttu-id="66d20-387">*Festlegen des Protokolls aus der Konfiguration*</span><span class="sxs-lookup"><span data-stu-id="66d20-387">*Set the protocol from configuration*</span></span>

<span data-ttu-id="66d20-388">`CreateDefaultBuilder` ruft `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` standardmäßig zum Laden der Kestrel-Konfiguration auf.</span><span class="sxs-lookup"><span data-stu-id="66d20-388">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="66d20-389">Das folgende *appsettings.json*-Beispiel richtet HTTP/1.1 als Standardverbindungsprotokoll für alle Endpunkte ein:</span><span class="sxs-lookup"><span data-stu-id="66d20-389">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="66d20-390">Das folgende *appsettings.json*-Beispiel richtet HTTP/1.1 als Verbindungsprotokoll für einen bestimmten Endpunkt ein:</span><span class="sxs-lookup"><span data-stu-id="66d20-390">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="66d20-391">Protokolle, die in Code angegeben werden, setzen Werte außer Kraft, der durch die Konfiguration festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="66d20-391">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="66d20-392">Transportkonfiguration</span><span class="sxs-lookup"><span data-stu-id="66d20-392">Transport configuration</span></span>

<span data-ttu-id="66d20-393">Für Projekte, die den Einsatz von Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>) erfordern:</span><span class="sxs-lookup"><span data-stu-id="66d20-393">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span></span>

* <span data-ttu-id="66d20-394">Fügen Sie für das [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/)-Paket eine Abhängigkeit auf die Projektdatei der App hinzu:</span><span class="sxs-lookup"><span data-stu-id="66d20-394">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* <span data-ttu-id="66d20-395"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> auf `IWebHostBuilder` aufrufen:</span><span class="sxs-lookup"><span data-stu-id="66d20-395">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> on the `IWebHostBuilder`:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="66d20-396">URL-Präfixe</span><span class="sxs-lookup"><span data-stu-id="66d20-396">URL prefixes</span></span>

<span data-ttu-id="66d20-397">Bei der Verwendung von `UseUrls`, dem Befehlszeilenargument `--urls`, dem Hostkonfigurationsschlüssel `urls` oder der Umgebungsvariable `ASPNETCORE_URLS` können die URL-Präfixe in den folgenden Formaten vorliegen.</span><span class="sxs-lookup"><span data-stu-id="66d20-397">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="66d20-398">Nur HTTP-URL-Präfixe sind gültig.</span><span class="sxs-lookup"><span data-stu-id="66d20-398">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="66d20-399">Kestrel unterstützt HTTPS nicht beim Konfigurieren von URL-Bindungen mit `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="66d20-399">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="66d20-400">IPv4-Adresse mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="66d20-400">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="66d20-401">Bei `0.0.0.0` handelt es sich um einen Sonderfall, für den eine Bindung an alle IPv4-Adressen erfolgt.</span><span class="sxs-lookup"><span data-stu-id="66d20-401">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="66d20-402">IPv6-Adresse mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="66d20-402">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="66d20-403">`[::]` stellt das Äquivalent von IPv6 zu `0.0.0.0` für IPv4 dar.</span><span class="sxs-lookup"><span data-stu-id="66d20-403">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="66d20-404">Hostname mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="66d20-404">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="66d20-405">Hostnamen, `*` und `+` sind nicht spezifisch.</span><span class="sxs-lookup"><span data-stu-id="66d20-405">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="66d20-406">Alle Elemente, die nicht als gültige IP-Adresse oder `localhost` erkannt werden, werden an alle IPv4- und IPv6-IP-Adressen gebunden.</span><span class="sxs-lookup"><span data-stu-id="66d20-406">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="66d20-407">Verwenden Sie [HTTP.sys](xref:fundamentals/servers/httpsys) oder einen Reverseproxyserver zum Binden verschiedener Hostnamen an verschiedene ASP.NET Core-Apps auf demselben Port, z.B. IIS, Nginx oder Apache.</span><span class="sxs-lookup"><span data-stu-id="66d20-407">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="66d20-408">Das Hosting in einer Reverseproxykonfiguration erfordert [Hostfilterung](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="66d20-408">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="66d20-409">`localhost`-Hostname mit Portnummer oder Loopback-IP mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="66d20-409">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="66d20-410">Wenn `localhost` angegeben ist, versucht Kestrel, eine Bindung zu IPv4- und IPv6-Loopback-Schnittstellen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="66d20-410">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="66d20-411">Wenn der erforderliche Port von einem anderen Dienst auf einer der Loopback-Schnittstellen verwendet wird, tritt beim Starten von Kestrel ein Fehler auf.</span><span class="sxs-lookup"><span data-stu-id="66d20-411">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="66d20-412">Wenn eine der Loopback-Schnittstellen aus anderen Gründen nicht verfügbar ist (meistens durch die fehlende Unterstützung von IPv6), protokolliert Kestrel eine Warnung.</span><span class="sxs-lookup"><span data-stu-id="66d20-412">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="66d20-413">Host-Filterung</span><span class="sxs-lookup"><span data-stu-id="66d20-413">Host filtering</span></span>

<span data-ttu-id="66d20-414">Obwohl Kestrel die Konfiguration basierend auf Präfixe wie `http://example.com:5000` unterstützt, ignoriert Kestrel den Hostnamen weitgehend.</span><span class="sxs-lookup"><span data-stu-id="66d20-414">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="66d20-415">Der Host `localhost` ist ein Sonderfall, der für die Bindung an Loopback-Adressen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="66d20-415">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="66d20-416">Jeder Host, der keine explizite IP-Adresse ist, wird an alle öffentlichen IP-Adressen gebunden.</span><span class="sxs-lookup"><span data-stu-id="66d20-416">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="66d20-417">`Host`-Header werden nicht überprüft.</span><span class="sxs-lookup"><span data-stu-id="66d20-417">`Host` headers aren't validated.</span></span>

<span data-ttu-id="66d20-418">Verwenden Sie Middleware zum Filtern von Hosts, um dieses Problem zu umgehen.</span><span class="sxs-lookup"><span data-stu-id="66d20-418">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="66d20-419">Die Middleware zum Filtern von Hosts wird durch das [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering)-Paket bereitgestellt, das implizit für ASP.NET Core-Apps zur Verfügung steht.</span><span class="sxs-lookup"><span data-stu-id="66d20-419">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="66d20-420">Die Middleware wird von <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> hinzugefügt, wodurch <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="66d20-420">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="66d20-421">Die Middleware zum Filtern von Hosts ist standardmäßig deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="66d20-421">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="66d20-422">Wenn Sie die Middleware aktivieren möchten, definieren Sie einen `AllowedHosts`-Schlüssel in *appsettings.json*/*appsettings.\<Umgebungsname>.json*.</span><span class="sxs-lookup"><span data-stu-id="66d20-422">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="66d20-423">Der Wert ist eine durch Semikolons getrennte Liste von Hostnamen ohne Portnummern:</span><span class="sxs-lookup"><span data-stu-id="66d20-423">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="66d20-424">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="66d20-424">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="66d20-425">[Middleware für weitergeleitete Header](xref:host-and-deploy/proxy-load-balancer) hat auch eine <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>-Option.</span><span class="sxs-lookup"><span data-stu-id="66d20-425">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="66d20-426">Middleware für weitergeleitete Header und Middleware zum Filtern von Hosts besitzen ähnliche Funktionen für unterschiedliche Szenarios.</span><span class="sxs-lookup"><span data-stu-id="66d20-426">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="66d20-427">Legen Sie `AllowedHosts` mit Middleware für weitergeleitete Header fest, wenn der `Host`-Header beim Weiterleiten von Anforderungen mit einem Reverseproxyserver oder einem Lastenausgleichsmodul nicht beibehalten wird.</span><span class="sxs-lookup"><span data-stu-id="66d20-427">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="66d20-428">Legen Sie `AllowedHosts` mit Middleware zum Filtern von Hosts fest, wenn Kestrel als öffentlicher Edgeserver verwendet oder der `Host`-Header direkt weitergeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="66d20-428">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="66d20-429">Weitere Informationen zu Middleware für weitergeleitete Header finden Sie unter <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="66d20-429">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="66d20-430">Einführung in Kestrel, dem plattformübergreifenden [Webserver für ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="66d20-430">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="66d20-431">Kestrel ist der Webserver, der standardmäßig in ASP.NET Core-Projektvorlagen enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="66d20-431">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="66d20-432">Kestrel unterstützt die folgenden Szenarios:</span><span class="sxs-lookup"><span data-stu-id="66d20-432">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="66d20-433">HTTPS</span><span class="sxs-lookup"><span data-stu-id="66d20-433">HTTPS</span></span>
* <span data-ttu-id="66d20-434">Nicht transparente Upgrades, die zum Aktivieren von [WebSockets](https://github.com/aspnet/websockets) verwendet werden</span><span class="sxs-lookup"><span data-stu-id="66d20-434">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="66d20-435">Unix-Sockets für eine hohe Leistung im Hintergrund von Nginx</span><span class="sxs-lookup"><span data-stu-id="66d20-435">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="66d20-436">HTTP/2 (außer unter macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="66d20-436">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="66d20-437">&dagger;HTTP/2 wird unter macOS in einem zukünftigen Release unterstützt.</span><span class="sxs-lookup"><span data-stu-id="66d20-437">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="66d20-438">Kestrel wird auf allen Plattformen und für alle Versionen unterstützt, die .NET Core unterstützt.</span><span class="sxs-lookup"><span data-stu-id="66d20-438">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="66d20-439">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="66d20-439">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="66d20-440">HTTP/2-Unterstützung</span><span class="sxs-lookup"><span data-stu-id="66d20-440">HTTP/2 support</span></span>

<span data-ttu-id="66d20-441">[HTTP/2](https://httpwg.org/specs/rfc7540.html) ist für ASP.NET Core-Apps verfügbar, wenn die folgenden Basisanforderungen erfüllt sind:</span><span class="sxs-lookup"><span data-stu-id="66d20-441">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="66d20-442">Betriebssystem&dagger;</span><span class="sxs-lookup"><span data-stu-id="66d20-442">Operating system&dagger;</span></span>
  * <span data-ttu-id="66d20-443">Windows Server 2016/Windows 10 oder höher&Dagger;</span><span class="sxs-lookup"><span data-stu-id="66d20-443">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="66d20-444">Linux mit OpenSSL 1.0.2 oder höher (z.B. Ubuntu 16.04 oder höher)</span><span class="sxs-lookup"><span data-stu-id="66d20-444">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="66d20-445">Zielframework: .NET Core 2.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="66d20-445">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="66d20-446">[ALPN](https://tools.ietf.org/html/rfc7301#section-3)-Verbindung (Application-Layer Protocol Negotiation)</span><span class="sxs-lookup"><span data-stu-id="66d20-446">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="66d20-447">TLS 1.2-Verbindung oder höher</span><span class="sxs-lookup"><span data-stu-id="66d20-447">TLS 1.2 or later connection</span></span>

<span data-ttu-id="66d20-448">&dagger;HTTP/2 wird unter macOS in einem zukünftigen Release unterstützt.</span><span class="sxs-lookup"><span data-stu-id="66d20-448">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="66d20-449">&Dagger;Kestrel bietet eingeschränkte Unterstützung für HTTP/2 unter Windows Server 2012 R2 und Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="66d20-449">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="66d20-450">Die Unterstützung ist eingeschränkt, weil die Liste der unterstützten TLS-Verschlüsselungssammlungen unter diesen Betriebssystemen begrenzt ist.</span><span class="sxs-lookup"><span data-stu-id="66d20-450">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="66d20-451">Zum Sichern von TLS-Verbindungen ist möglicherweise ein durch einen Elliptic Curve Digital Signature Algorithm (ECDSA) generiertes Zertifikat erforderlich.</span><span class="sxs-lookup"><span data-stu-id="66d20-451">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="66d20-452">Wenn eine HTTP/2-Verbindung hergestellt wurde, meldet [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="66d20-452">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="66d20-453">HTTP/2 ist standardmäßig deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="66d20-453">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="66d20-454">Weitere Informationen zur Konfiguration finden Sie in den Abschnitten [Kestrel-Optionen](#kestrel-options) und [ListenOptions.Protocols](#listenoptionsprotocols).</span><span class="sxs-lookup"><span data-stu-id="66d20-454">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="66d20-455">Verwenden von Kestrel mit einem Reverseproxy</span><span class="sxs-lookup"><span data-stu-id="66d20-455">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="66d20-456">Kestrel kann eigenständig oder mit einem *Reverseproxyserver* wie z.B. [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="66d20-456">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="66d20-457">Ein Reverseproxyserver empfängt HTTP-Anforderungen aus dem Netzwerk und leitet diese an Kestrel weiter.</span><span class="sxs-lookup"><span data-stu-id="66d20-457">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="66d20-458">Kestrel bei Verwendung als Webserver mit direkter Internetverbindung:</span><span class="sxs-lookup"><span data-stu-id="66d20-458">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel kommuniziert direkt und ohne Reverseproxyserver mit dem Internet](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="66d20-460">Kestrel bei Verwendung in einer Reverseproxykonfiguration:</span><span class="sxs-lookup"><span data-stu-id="66d20-460">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel kommuniziert indirekt mit dem Internet über einen Reverseproxyserver wie IIS, Nginx oder Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="66d20-462">Jede der beiden Konfigurationen – mit oder ohne einen Reverseproxyserver – stellt eine unterstützte Hostingkonfiguration dar.</span><span class="sxs-lookup"><span data-stu-id="66d20-462">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="66d20-463">Bei Verwendung als Edgeserver ohne Reverseproxyserver unterstützt Kestrel die gemeinsame Nutzung der gleichen IP-Adresse und des gleichen Ports durch mehrere Prozesse nicht.</span><span class="sxs-lookup"><span data-stu-id="66d20-463">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="66d20-464">Wenn Kestrel für das Lauschen an einem Port konfiguriert ist, verarbeitet Kestrel den gesamten Datenverkehr für diesen Port unabhängig von den `Host`-Headern der Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="66d20-464">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="66d20-465">Ein Reverseproxy, der Ports freigeben kann, kann Anforderungen an Kestrel über eine eindeutige IP und einen eindeutigen Port weiterleiten.</span><span class="sxs-lookup"><span data-stu-id="66d20-465">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="66d20-466">Auch wenn kein Reverseproxyserver erforderlich ist, kann die Verwendung eines solchen empfehlenswert sein.</span><span class="sxs-lookup"><span data-stu-id="66d20-466">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="66d20-467">Für einen Reverseproxy gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="66d20-467">A reverse proxy:</span></span>

* <span data-ttu-id="66d20-468">Er kann die verfügbar gemachten öffentlichen Oberflächen der von ihm gehosteten Apps einschränken.</span><span class="sxs-lookup"><span data-stu-id="66d20-468">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="66d20-469">Er stellt eine zusätzliche Ebene für Konfiguration und Schutz bereit.</span><span class="sxs-lookup"><span data-stu-id="66d20-469">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="66d20-470">Er lässt sich besser in die vorhandene Infrastruktur integrieren.</span><span class="sxs-lookup"><span data-stu-id="66d20-470">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="66d20-471">Er vereinfacht die Konfiguration von Lastenausgleich und sicherer Kommunikation (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="66d20-471">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="66d20-472">Nur der Reverseproxyserver erfordert ein X.509-Zertifikat, und dieser Server kann mit den Servern der App im internen Netzwerk über einfaches HTTP kommunizieren.</span><span class="sxs-lookup"><span data-stu-id="66d20-472">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="66d20-473">Das Hosting in einer Reverseproxykonfiguration erfordert [Hostfilterung](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="66d20-473">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="66d20-474">Verwenden von Kestrel in ASP.NET Core-Apps</span><span class="sxs-lookup"><span data-stu-id="66d20-474">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="66d20-475">Das Paket [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) ist im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) enthalten.</span><span class="sxs-lookup"><span data-stu-id="66d20-475">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="66d20-476">ASP.NET Core-Projektvorlagen verwenden Kestrel standardmäßig.</span><span class="sxs-lookup"><span data-stu-id="66d20-476">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="66d20-477">Der Vorlagencode in *Program.cs* ruft <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> auf, wodurch <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> im Hintergrund aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="66d20-477">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="66d20-478">Weitere Informationen zum `CreateDefaultBuilder` und zum Erstellen des Hosts finden Sie im Abschnitt *Einrichten eines Hosts* von <xref:fundamentals/host/web-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="66d20-478">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="66d20-479">Um zusätzliche Konfiguration nach dem Aufruf von `CreateDefaultBuilder` bereitzustellen, verwenden Sie `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="66d20-479">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="66d20-480">Wenn die App `CreateDefaultBuilder` nicht aufruft, um den Host einzurichten, rufen Sie <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **auf**, bevor Sie `ConfigureKestrel` aufrufen:</span><span class="sxs-lookup"><span data-stu-id="66d20-480">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="66d20-481">Kestrel-Optionen</span><span class="sxs-lookup"><span data-stu-id="66d20-481">Kestrel options</span></span>

<span data-ttu-id="66d20-482">Der Kestrel-Webserver verfügt über einschränkende Konfigurationsoptionen, die besonders nützlich bei Bereitstellungen mit Internetzugriff sind.</span><span class="sxs-lookup"><span data-stu-id="66d20-482">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="66d20-483">Legen Sie Einschränkungen für die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits>-Eigenschaft der <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>-Klasse fest.</span><span class="sxs-lookup"><span data-stu-id="66d20-483">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="66d20-484">Die `Limits`-Eigenschaft enthält eine Instanz der <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>-Klasse.</span><span class="sxs-lookup"><span data-stu-id="66d20-484">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="66d20-485">In den folgenden Beispielen wird der <xref:Microsoft.AspNetCore.Server.Kestrel.Core>-Namespace verwendet:</span><span class="sxs-lookup"><span data-stu-id="66d20-485">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="66d20-486">Kestrel-Optionen, die in den folgenden Beispielen in C#-Code konfiguriert sind, können auch mit einem [Konfigurationsanbieter](xref:fundamentals/configuration/index) festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="66d20-486">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="66d20-487">Beispielsweise kann der Dateikonfigurationsanbieter die Kestrel-Konfiguration aus einer *appsettings.json*- oder *appsettings.{Umgebung}.json*-Datei laden:</span><span class="sxs-lookup"><span data-stu-id="66d20-487">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="66d20-488">Verwenden Sie **einen** der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="66d20-488">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="66d20-489">Konfigurieren Sie Kestrel in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="66d20-489">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="66d20-490">Fügen Sie eine Instanz von `IConfiguration` in die `Startup`-Klasse ein.</span><span class="sxs-lookup"><span data-stu-id="66d20-490">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="66d20-491">Im folgenden Beispiel wird davon ausgegangen, dass die eingefügte Konfiguration der `Configuration`-Eigenschaft zugewiesen wird.</span><span class="sxs-lookup"><span data-stu-id="66d20-491">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="66d20-492">Laden Sie in `Startup.ConfigureServices` den Abschnitt `Kestrel` der Konfiguration in die Konfiguration von Kestrel.</span><span class="sxs-lookup"><span data-stu-id="66d20-492">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration.</span></span>

     ```csharp
     // using Microsoft.Extensions.Configuration

     public void ConfigureServices(IServiceCollection services)
     {
         services.Configure<KestrelServerOptions>(
             Configuration.GetSection("Kestrel"));
     }
     ```

* <span data-ttu-id="66d20-493">Konfigurieren Sie Kestrel beim Erstellen des Hosts:</span><span class="sxs-lookup"><span data-stu-id="66d20-493">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="66d20-494">Laden Sie in *Program.cs* den Abschnitt `Kestrel` der Konfiguration in die Konfiguration von Kestrel:</span><span class="sxs-lookup"><span data-stu-id="66d20-494">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="66d20-495">Beide vorangehenden Ansätze funktionieren mit jedem [Konfigurationsanbieter](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="66d20-495">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="66d20-496">Keep-Alive-Timeout</span><span class="sxs-lookup"><span data-stu-id="66d20-496">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="66d20-497">Ruft das [Keep-Alive-Timeout](https://tools.ietf.org/html/rfc7230#section-6.5) ab oder legt es fest.</span><span class="sxs-lookup"><span data-stu-id="66d20-497">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="66d20-498">Standardwert: 2 Minuten.</span><span class="sxs-lookup"><span data-stu-id="66d20-498">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="66d20-499">Maximale Anzahl der Clientverbindungen</span><span class="sxs-lookup"><span data-stu-id="66d20-499">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="66d20-500">Die maximale Anzahl von gleichzeitig geöffneten TCP-Verbindungen kann mithilfe von folgendem Code für die gesamte App festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="66d20-500">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="66d20-501">Es gibt einen separaten Grenzwert für Verbindungen, die von HTTP oder HTTPS auf ein anderes Protokoll aktualisiert wurden (z.B. auf eine WebSockets-Anforderung).</span><span class="sxs-lookup"><span data-stu-id="66d20-501">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="66d20-502">Nachdem eine Verbindung aktualisiert wurde, zählt diese nicht mehr für den `MaxConcurrentConnections`-Grenzwert.</span><span class="sxs-lookup"><span data-stu-id="66d20-502">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="66d20-503">Die maximale Anzahl von Verbindungen ist standardmäßig nicht begrenzt (NULL).</span><span class="sxs-lookup"><span data-stu-id="66d20-503">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="66d20-504">Maximale Größe des Anforderungstexts</span><span class="sxs-lookup"><span data-stu-id="66d20-504">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="66d20-505">Die maximale Größe des Anforderungstexts beträgt standardmäßig 30.000.000 Byte, also ungefähr 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="66d20-505">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="66d20-506">Die empfohlene Methode zur Außerkraftsetzung des Grenzwerts in einer ASP.NET Core-MVC-App besteht im Verwenden des <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>-Attributs in einer Aktionsmethode:</span><span class="sxs-lookup"><span data-stu-id="66d20-506">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="66d20-507">Im folgenden Beispiel wird veranschaulicht, wie die Einschränkungen auf jeder Anforderung für die App konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="66d20-507">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="66d20-508">Außer Kraft setzen der Einstellung für eine bestimmte Anforderung in Middleware:</span><span class="sxs-lookup"><span data-stu-id="66d20-508">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="66d20-509">Eine Ausnahme wird ausgelöst, wenn die App den Grenzwert einer Anforderung konfiguriert, nachdem die App bereits mit dem Lesen der Anforderung begonnen hat.</span><span class="sxs-lookup"><span data-stu-id="66d20-509">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="66d20-510">Es gibt eine `IsReadOnly`-Eigenschaft, die angibt, wenn sich die `MaxRequestBodySize`-Eigenschaft im schreibgeschützten Zustand befindet, also wenn der Grenzwert nicht mehr konfiguriert werden kann.</span><span class="sxs-lookup"><span data-stu-id="66d20-510">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="66d20-511">Wenn eine App [prozessextern](xref:host-and-deploy/iis/index#out-of-process-hosting-model) hinter dem [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) ausgeführt wird, ist das Größenlimit von Kestrel für Anforderungstext deaktiviert, weil IIS dieses Limit bereits festlegt.</span><span class="sxs-lookup"><span data-stu-id="66d20-511">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="66d20-512">Minimale Datenrate des Anforderungstexts</span><span class="sxs-lookup"><span data-stu-id="66d20-512">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="66d20-513">Kestrel überprüft sekündlich, ob Daten mit der angegebenen Rate in Bytes/Sekunde eingehen.</span><span class="sxs-lookup"><span data-stu-id="66d20-513">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="66d20-514">Wenn die Rate den mindestens erforderlichen Wert unterschreitet, wird für die Verbindung wegen Timeout getrennt. Bei der Toleranzperiode handelt es sich um die Zeitspanne, die Kestrel dem Client gewährt, um die Senderate auf den mindestens erforderlichen Wert zu erhöhen. Die Rate wird währenddessen nicht überprüft.</span><span class="sxs-lookup"><span data-stu-id="66d20-514">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="66d20-515">Diese Toleranzperiode beugt dem Trennen von Verbindungen vor, die Daten aufgrund eines langsamen TCP-Starts anfänglich mit einer niedrigen Rate senden.</span><span class="sxs-lookup"><span data-stu-id="66d20-515">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="66d20-516">Die mindestens erforderliche Rate beträgt standardmäßig 240 Bytes/Sekunde mit einer Toleranzperiode von 5 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="66d20-516">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="66d20-517">Für die Antwort gilt ebenfalls eine mindestens erforderliche Rate.</span><span class="sxs-lookup"><span data-stu-id="66d20-517">A minimum rate also applies to the response.</span></span> <span data-ttu-id="66d20-518">Der Code zum Festlegen des Grenzwerts für Anforderung und Antwort ist abgesehen von `RequestBody` oder `Response` in den Namen von Eigenschaften und Schnittstelle identisch.</span><span class="sxs-lookup"><span data-stu-id="66d20-518">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="66d20-519">In diesem Beispiel wird veranschaulicht, wie Sie die mindestens erforderlichen Datenraten in *Program.cs* konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="66d20-519">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="66d20-520">Außer Kraft setzen des minimalen Ratenlimits pro Anforderung in Middleware:</span><span class="sxs-lookup"><span data-stu-id="66d20-520">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="66d20-521">Keines dieser Ratenfeatures, auf die im vorherigen Beispiel verwiesen wird, ist in `HttpContext.Features` für HTTP/2-Anforderungen vorhanden, da die Änderung von Ratenlimits für jede Anforderung aufgrund der Unterstützung für Anforderungsmultiplexing des Protokolls nicht für HTTP/2 unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="66d20-521">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="66d20-522">Serverweite Ratenlimits, die über `KestrelServerOptions.Limits` konfiguriert sind, gelten auch für HTTP/1.x- und HTTP/2-Verbindungen.</span><span class="sxs-lookup"><span data-stu-id="66d20-522">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="66d20-523">Timeout für Anforderungsheader</span><span class="sxs-lookup"><span data-stu-id="66d20-523">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="66d20-524">Ruft die maximale Zeitspanne ab, während der der Server Anforderungsheader empfängt, oder legt diese fest.</span><span class="sxs-lookup"><span data-stu-id="66d20-524">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="66d20-525">Der Standardwert beträgt 30 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="66d20-525">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="66d20-526">Maximale Datenströme pro Verbindung</span><span class="sxs-lookup"><span data-stu-id="66d20-526">Maximum streams per connection</span></span>

<span data-ttu-id="66d20-527">`Http2.MaxStreamsPerConnection` schränkt die Anzahl gleichzeitiger Anforderungsdatenströme pro HTTP/2-Verbindung ein.</span><span class="sxs-lookup"><span data-stu-id="66d20-527">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="66d20-528">Überschüssige Datenströme werden zurückgewiesen.</span><span class="sxs-lookup"><span data-stu-id="66d20-528">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="66d20-529">Der Standardwert ist 100.</span><span class="sxs-lookup"><span data-stu-id="66d20-529">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="66d20-530">Größe der Headertabelle</span><span class="sxs-lookup"><span data-stu-id="66d20-530">Header table size</span></span>

<span data-ttu-id="66d20-531">Der HPACK-Decoder dekomprimiert HTTP-Header für HTTP/2-Verbindungen.</span><span class="sxs-lookup"><span data-stu-id="66d20-531">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="66d20-532">`Http2.HeaderTableSize` schränkt die Größe der Headerkomprimierungstabelle ein, die der HPACK-Decoder verwendet.</span><span class="sxs-lookup"><span data-stu-id="66d20-532">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="66d20-533">Der Wert wird in Oktetten bereitgestellt und muss größer als null (0) sein.</span><span class="sxs-lookup"><span data-stu-id="66d20-533">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="66d20-534">Der Standardwert ist 4096.</span><span class="sxs-lookup"><span data-stu-id="66d20-534">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="66d20-535">Maximale Framegröße</span><span class="sxs-lookup"><span data-stu-id="66d20-535">Maximum frame size</span></span>

<span data-ttu-id="66d20-536">`Http2.MaxFrameSize` gibt die maximale Größe der zu empfangenden HTTP/2-Verbindungsframenutzlast an.</span><span class="sxs-lookup"><span data-stu-id="66d20-536">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="66d20-537">Der Wert wird in Oktetten bereitgestellt und muss zwischen 2^14 (16.384) und 2^24-1 (16.777.215) liegen.</span><span class="sxs-lookup"><span data-stu-id="66d20-537">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="66d20-538">Der Standardwert ist 2^14 (16.384).</span><span class="sxs-lookup"><span data-stu-id="66d20-538">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="66d20-539">Maximale Größe des Anforderungsheaders</span><span class="sxs-lookup"><span data-stu-id="66d20-539">Maximum request header size</span></span>

<span data-ttu-id="66d20-540">`Http2.MaxRequestHeaderFieldSize` gibt die maximal zulässige Größe in Oktetten der Anforderungsheaderwerte an.</span><span class="sxs-lookup"><span data-stu-id="66d20-540">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="66d20-541">Dieser Grenzwert gilt zusammen für den Namen und Wert in komprimierten und nicht komprimierten Darstellungen.</span><span class="sxs-lookup"><span data-stu-id="66d20-541">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="66d20-542">Der Wert muss größer als 0 (null) sein.</span><span class="sxs-lookup"><span data-stu-id="66d20-542">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="66d20-543">Der Standardwert ist 8.192.</span><span class="sxs-lookup"><span data-stu-id="66d20-543">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="66d20-544">Anfangsfenstergröße der Verbindung</span><span class="sxs-lookup"><span data-stu-id="66d20-544">Initial connection window size</span></span>

<span data-ttu-id="66d20-545">`Http2.InitialConnectionWindowSize` gibt die maximalen Anforderungstextdaten in Byte an, die der Server auf einmal für alle Anforderungen (Streams) pro Verbindung aggregiert.</span><span class="sxs-lookup"><span data-stu-id="66d20-545">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="66d20-546">Anforderungen werden auch durch `Http2.InitialStreamWindowSize` beschränkt.</span><span class="sxs-lookup"><span data-stu-id="66d20-546">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="66d20-547">Der Wert muss größer als oder gleich 65.535 und kleiner als 2^31 (2.147.483.648) sein.</span><span class="sxs-lookup"><span data-stu-id="66d20-547">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="66d20-548">Der Standardwert ist 128 KB (131.072).</span><span class="sxs-lookup"><span data-stu-id="66d20-548">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="66d20-549">Anfangsfenstergröße des Streams</span><span class="sxs-lookup"><span data-stu-id="66d20-549">Initial stream window size</span></span>

<span data-ttu-id="66d20-550">`Http2.InitialStreamWindowSize` gibt die maximalen Anforderungstextdaten in Byte an, die der Server auf einmal pro Anforderung (Stream) puffert.</span><span class="sxs-lookup"><span data-stu-id="66d20-550">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="66d20-551">Anforderungen werden auch durch `Http2.InitialStreamWindowSize` beschränkt.</span><span class="sxs-lookup"><span data-stu-id="66d20-551">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="66d20-552">Der Wert muss größer als oder gleich 65.535 und kleiner als 2^31 (2.147.483.648) sein.</span><span class="sxs-lookup"><span data-stu-id="66d20-552">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="66d20-553">Der Standardwert ist 96 KB (98.304).</span><span class="sxs-lookup"><span data-stu-id="66d20-553">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="66d20-554">Synchrone E/A-Vorgänge</span><span class="sxs-lookup"><span data-stu-id="66d20-554">Synchronous IO</span></span>

<span data-ttu-id="66d20-555"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> steuert, ob für Anforderung und Antwort synchrone E/A-Vorgänge zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="66d20-555"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="66d20-556">Der Standardwert ist `true`.</span><span class="sxs-lookup"><span data-stu-id="66d20-556">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="66d20-557">Sehr viele blockierende synchrone E/A-Vorgänge können zu einem Ressourcenmangel im Threadpool führen, wodurch die App nicht mehr reagiert.</span><span class="sxs-lookup"><span data-stu-id="66d20-557">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="66d20-558">Aktivieren Sie `AllowSynchronousIO` nur bei Verwendung einer Bibliothek, die asynchrone E/A-Vorgänge nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="66d20-558">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="66d20-559">Das folgende Beispiel aktiviert synchrone E/A-Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="66d20-559">The following example enables synchronous IO:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="66d20-560">Weitere Informationen zu anderen Kestrel-Optionen und -Einschränkungen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="66d20-560">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="66d20-561">Endpunktkonfiguration</span><span class="sxs-lookup"><span data-stu-id="66d20-561">Endpoint configuration</span></span>

<span data-ttu-id="66d20-562">Standardmäßig wird ASP.NET Core an Folgendes gebunden:</span><span class="sxs-lookup"><span data-stu-id="66d20-562">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="66d20-563">`https://localhost:5001` (wenn ein lokales Entwicklungszertifikat vorhanden ist)</span><span class="sxs-lookup"><span data-stu-id="66d20-563">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="66d20-564">Verwenden Sie Folgendes zum Angeben der URLs:</span><span class="sxs-lookup"><span data-stu-id="66d20-564">Specify URLs using the:</span></span>

* <span data-ttu-id="66d20-565">Die Umgebungsvariable `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="66d20-565">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="66d20-566">Das Befehlszeilenargument `--urls`</span><span class="sxs-lookup"><span data-stu-id="66d20-566">`--urls` command-line argument.</span></span>
* <span data-ttu-id="66d20-567">Den Hostkonfigurationsschlüssel `urls`</span><span class="sxs-lookup"><span data-stu-id="66d20-567">`urls` host configuration key.</span></span>
* <span data-ttu-id="66d20-568">Die Erweiterungsmethode `UseUrls`</span><span class="sxs-lookup"><span data-stu-id="66d20-568">`UseUrls` extension method.</span></span>

<span data-ttu-id="66d20-569">Der Wert, der mit diesen Ansätzen angegeben wird, kann mindestens ein HTTP- oder HTTPS-Endpunkt sein (HTTPS wenn ein Standardzertifikat verfügbar ist).</span><span class="sxs-lookup"><span data-stu-id="66d20-569">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="66d20-570">Konfigurieren Sie den Wert als eine durch Semikolons getrennte Liste (z.B. `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="66d20-570">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="66d20-571">Weitere Informationen zu diesen Ansätzen finden Sie unter [Server-URLs](xref:fundamentals/host/web-host#server-urls) und [Außerkraftsetzen der Konfiguration](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="66d20-571">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="66d20-572">Ein Entwicklungszertifikat wird erstellt:</span><span class="sxs-lookup"><span data-stu-id="66d20-572">A development certificate is created:</span></span>

* <span data-ttu-id="66d20-573">Wenn das [.NET Core SDK](/dotnet/core/sdk) installiert wird.</span><span class="sxs-lookup"><span data-stu-id="66d20-573">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="66d20-574">Das [dev-cert-Tool](xref:aspnetcore-2.1#https) wird zum Erstellen eines Zertifikats verwendet.</span><span class="sxs-lookup"><span data-stu-id="66d20-574">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="66d20-575">Einige Browser erfordern, dass Sie die explizite Berechtigung erteilen, dem lokalen Entwicklungszertifikat zu vertrauen.</span><span class="sxs-lookup"><span data-stu-id="66d20-575">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="66d20-576">Projektvorlagen konfigurieren Apps, damit sie standardmäßig auf HTTPS ausgeführt werden und die [HTTPS-Umleitung und HSTS-Unterstützung](xref:security/enforcing-ssl) enthalten.</span><span class="sxs-lookup"><span data-stu-id="66d20-576">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="66d20-577">Rufen Sie die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>- oder <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>-Methode unter <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> auf, um URL-Präfixe und Ports für Kestrel zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="66d20-577">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="66d20-578">`UseUrls`, das Befehlszeilenargument `--urls`, der Hostkonfigurationsschlüssel `urls` und die Umgebungsvariable `ASPNETCORE_URLS` funktionieren ebenfalls, verfügen jedoch über Einschränkungen, die im Verlauf dieses Abschnitts erläutert werden (Ein Standardzertifikat muss für die HTTPS-Endpunktkonfiguration verfügbar sein).</span><span class="sxs-lookup"><span data-stu-id="66d20-578">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="66d20-579">`KestrelServerOptions`-Konfiguration:</span><span class="sxs-lookup"><span data-stu-id="66d20-579">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="66d20-580">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="66d20-580">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="66d20-581">Gibt die Konfiguration von `Action` zum Ausführen von jedem angegebenen Endpunkt an.</span><span class="sxs-lookup"><span data-stu-id="66d20-581">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="66d20-582">Mehrmalige Aufrufe von `ConfigureEndpointDefaults` ersetzen vorherige Instanzen von `Action` mit der zuletzt angegebenen `Action`.</span><span class="sxs-lookup"><span data-stu-id="66d20-582">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="66d20-583">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="66d20-583">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="66d20-584">Gibt die Konfiguration von `Action` zum Ausführen von jedem HTTPS-Endpunkt an.</span><span class="sxs-lookup"><span data-stu-id="66d20-584">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="66d20-585">Mehrmalige Aufrufe von `ConfigureHttpsDefaults` ersetzen vorherige Instanzen von `Action` mit der zuletzt angegebenen `Action`.</span><span class="sxs-lookup"><span data-stu-id="66d20-585">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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

### <a name="configureiconfiguration"></a><span data-ttu-id="66d20-586">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="66d20-586">Configure(IConfiguration)</span></span>

<span data-ttu-id="66d20-587">Erstellt ein Konfigurationsladeprogramm für das Einrichten von Kestrel, was <xref:Microsoft.Extensions.Configuration.IConfiguration> als Eingabe erfordert.</span><span class="sxs-lookup"><span data-stu-id="66d20-587">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="66d20-588">Die Konfiguration muss auf den Konfigurationsabschnitt für Kestrel festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="66d20-588">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="66d20-589">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="66d20-589">ListenOptions.UseHttps</span></span>

<span data-ttu-id="66d20-590">Konfiguriert Kestrel zur Verwendung von HTTPS.</span><span class="sxs-lookup"><span data-stu-id="66d20-590">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="66d20-591">`ListenOptions.UseHttps`-Erweiterungen:</span><span class="sxs-lookup"><span data-stu-id="66d20-591">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="66d20-592">`UseHttps`: Konfiguriert Kestrel zur Verwendung von HTTPS mit dem Standardzertifikat.</span><span class="sxs-lookup"><span data-stu-id="66d20-592">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="66d20-593">Löst eine Ausnahme aus, wenn kein Standardzertifikat konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="66d20-593">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="66d20-594">`ListenOptions.UseHttps`-Parameter:</span><span class="sxs-lookup"><span data-stu-id="66d20-594">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="66d20-595">`filename` entspricht dem Pfad und Dateinamen einer Zertifikatdatei relativ zu dem Verzeichnis, das die Inhaltsdateien der App enthält.</span><span class="sxs-lookup"><span data-stu-id="66d20-595">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="66d20-596">`password` ist das für den Zugriff auf die X.509-Zertifikatsdaten erforderliche Kennwort.</span><span class="sxs-lookup"><span data-stu-id="66d20-596">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="66d20-597">`configureOptions` ist eine `Action` zum Konfigurieren von `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="66d20-597">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="66d20-598">Gibt `ListenOptions` zurück.</span><span class="sxs-lookup"><span data-stu-id="66d20-598">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="66d20-599">`storeName` ist der Zertifikatspeicher, aus dem das Zertifikat geladen wird.</span><span class="sxs-lookup"><span data-stu-id="66d20-599">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="66d20-600">`subject` ist der Name des Antragstellers für das Zertifikat.</span><span class="sxs-lookup"><span data-stu-id="66d20-600">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="66d20-601">`allowInvalid` gibt an, ob ungültige Zertifikate berücksichtigt werden sollten, z.B. selbstsignierte Zertifikate.</span><span class="sxs-lookup"><span data-stu-id="66d20-601">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="66d20-602">`location` ist der Speicherort, aus dem das Zertifikat geladen wird.</span><span class="sxs-lookup"><span data-stu-id="66d20-602">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="66d20-603">`serverCertificate` ist das X.509-Zertifikat.</span><span class="sxs-lookup"><span data-stu-id="66d20-603">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="66d20-604">In der Produktion muss HTTPS explizit konfiguriert sein.</span><span class="sxs-lookup"><span data-stu-id="66d20-604">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="66d20-605">Zumindest muss ein Standardzertifikat angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="66d20-605">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="66d20-606">Die im Folgenden beschriebenen unterstützten Konfigurationen:</span><span class="sxs-lookup"><span data-stu-id="66d20-606">Supported configurations described next:</span></span>

* <span data-ttu-id="66d20-607">Keine Konfiguration</span><span class="sxs-lookup"><span data-stu-id="66d20-607">No configuration</span></span>
* <span data-ttu-id="66d20-608">Ersetzen des Standardzertifikats aus der Konfiguration</span><span class="sxs-lookup"><span data-stu-id="66d20-608">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="66d20-609">Ändern des Standards im Code</span><span class="sxs-lookup"><span data-stu-id="66d20-609">Change the defaults in code</span></span>

<span data-ttu-id="66d20-610">*Keine Konfiguration*</span><span class="sxs-lookup"><span data-stu-id="66d20-610">*No configuration*</span></span>

<span data-ttu-id="66d20-611">Kestrel überwacht `http://localhost:5000` und `https://localhost:5001` (wenn ein Standardzertifikat verfügbar ist).</span><span class="sxs-lookup"><span data-stu-id="66d20-611">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="66d20-612">*Ersetzen des Standardzertifikats aus der Konfiguration*</span><span class="sxs-lookup"><span data-stu-id="66d20-612">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="66d20-613">`CreateDefaultBuilder` ruft `Configure(context.Configuration.GetSection("Kestrel"))` standardmäßig zum Laden der Kestrel-Konfiguration auf.</span><span class="sxs-lookup"><span data-stu-id="66d20-613">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="66d20-614">Ein Standardkonfigurationsschema für HTTPS-App-Einstellungen ist für Kestrel verfügbar.</span><span class="sxs-lookup"><span data-stu-id="66d20-614">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="66d20-615">Konfigurieren Sie mehrere Endpunkte, einschließlich der zu verwendenden URLs und Zertifikate aus einer Datei auf dem Datenträger oder einem Zertifikatspeicher.</span><span class="sxs-lookup"><span data-stu-id="66d20-615">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="66d20-616">Die Vorgehensweise im folgenden *appsettings.json*-Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66d20-616">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="66d20-617">Legen Sie **AllowInvalid** auf `true` fest, um die Verwendung von ungültigen Zertifikaten zu erlauben (z.B. selbstsignierte Zertifikate).</span><span class="sxs-lookup"><span data-stu-id="66d20-617">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="66d20-618">Jeder HTTPS-Endpunkt, der kein Zertifikat angibt (im folgenden Beispiel der Endpunkt **HttpsDefaultCert**), greift auf das unter **Zertifikate**>**Standard** festgelegte Zertifikat oder das Entwicklungszertifikat zurück.</span><span class="sxs-lookup"><span data-stu-id="66d20-618">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="66d20-619">Alternativ zur Verwendung von **Pfad** und **Kennwort** für alle Zertifikatknoten können Sie das Zertifikat mithilfe von Zertifikatspeicherfeldern angeben.</span><span class="sxs-lookup"><span data-stu-id="66d20-619">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="66d20-620">Das Zertifikat unter **Zertifikate** > **Standard** kann beispielweise wie folgt angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="66d20-620">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="66d20-621">Schema-Hinweise:</span><span class="sxs-lookup"><span data-stu-id="66d20-621">Schema notes:</span></span>

* <span data-ttu-id="66d20-622">Bei den Namen der Endpunkte wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="66d20-622">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="66d20-623">Zum Beispiel sind `HTTPS` und `Https` gültig.</span><span class="sxs-lookup"><span data-stu-id="66d20-623">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="66d20-624">Der Parameter `Url` ist für jeden Endpunkt erforderlich.</span><span class="sxs-lookup"><span data-stu-id="66d20-624">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="66d20-625">Das Format für diesen Parameter ist identisch mit dem allgemeinen Konfigurationsparameter `Urls`, mit der Ausnahme, dass er auf einen einzelnen Wert begrenzt ist.</span><span class="sxs-lookup"><span data-stu-id="66d20-625">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="66d20-626">Diese Endpunkte ersetzen die in der allgemeinen `Urls`-Konfiguration festgelegten Endpunkte, anstatt zu ihnen hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="66d20-626">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="66d20-627">Endpunkte, die über `Listen` in Code definiert werden, werden den im Konfigurationsabschnitt definierten Endpunkten hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="66d20-627">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="66d20-628">Der `Certificate`-Abschnitt ist optional.</span><span class="sxs-lookup"><span data-stu-id="66d20-628">The `Certificate` section is optional.</span></span> <span data-ttu-id="66d20-629">Wenn der `Certificate`-Abschnitt nicht angegeben ist, werden die in früheren Szenarios definierten Standardwerte verwendet.</span><span class="sxs-lookup"><span data-stu-id="66d20-629">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="66d20-630">Wenn keine Standardwerte verfügbar sind, löst der Server eine Ausnahme aus und startet nicht.</span><span class="sxs-lookup"><span data-stu-id="66d20-630">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="66d20-631">Der `Certificate`-Abschnitt unterstützt die Zertifikate **Pfad**&ndash;**Kennwort** und **Subject**&ndash;**Store** (Antragsteller–Speicher).</span><span class="sxs-lookup"><span data-stu-id="66d20-631">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="66d20-632">Auf diese Weise kann eine beliebige Anzahl von Endpunkten definiert werden, solange Sie keine Portkonflikte verursachen.</span><span class="sxs-lookup"><span data-stu-id="66d20-632">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="66d20-633">`options.Configure(context.Configuration.GetSection("{SECTION}"))` gibt `KestrelConfigurationLoader` mit der Methode `.Endpoint(string name, listenOptions => { })` zurück, die dazu verwendet werden kann, die Einstellungen eines Endpunkts zu ergänzen:</span><span class="sxs-lookup"><span data-stu-id="66d20-633">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="66d20-634">Auf `KestrelServerOptions.ConfigurationLoader` kann direkt zugegriffen werden, um die Iteration auf dem vorhandenen Ladeprogramm fortzusetzen, etwa auf dem von <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> bereitgestellten Ladeprogramm.</span><span class="sxs-lookup"><span data-stu-id="66d20-634">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="66d20-635">Der Konfigurationsabschnitt ist für jeden Endpunkt in den Optionen der Methode `Endpoint` verfügbar, sodass benutzerdefinierte Einstellungen gelesen werden können.</span><span class="sxs-lookup"><span data-stu-id="66d20-635">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="66d20-636">Mehrere Konfigurationen können durch erneutes Aufrufen von `options.Configure(context.Configuration.GetSection("{SECTION}"))` mit einem anderen Abschnitt geladen werden.</span><span class="sxs-lookup"><span data-stu-id="66d20-636">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="66d20-637">Sofern `Load` nicht in einer vorherigen Instanz explizit aufgerufen wird, wird nur die letzte Konfiguration verwendet.</span><span class="sxs-lookup"><span data-stu-id="66d20-637">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="66d20-638">Das Metapaket ruft `Load` nicht auf, sodass der Abschnitt mit der Standardkonfiguration ersetzt werden kann.</span><span class="sxs-lookup"><span data-stu-id="66d20-638">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="66d20-639">`KestrelConfigurationLoader` spiegelt die API-Familie `Listen` von `KestrelServerOptions` als `Endpoint`-Überladungen, weshalb Code und Konfigurationsendpunkte am selben Ort konfiguriert werden können.</span><span class="sxs-lookup"><span data-stu-id="66d20-639">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="66d20-640">Diese Überladungen verwenden keine Namen und nutzen nur Standardeinstellungen aus der Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="66d20-640">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="66d20-641">*Ändern des Standards im Code*</span><span class="sxs-lookup"><span data-stu-id="66d20-641">*Change the defaults in code*</span></span>

<span data-ttu-id="66d20-642">`ConfigureEndpointDefaults` und `ConfigureHttpsDefaults` können zum Ändern der Standardeinstellungen für `ListenOptions` und `HttpsConnectionAdapterOptions` verwendet werden, einschließlich der Standardzertifikate, die im vorherigen Szenario festgelegt wurden.</span><span class="sxs-lookup"><span data-stu-id="66d20-642">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="66d20-643">`ConfigureEndpointDefaults` und `ConfigureHttpsDefaults` sollten aufgerufen werden, bevor Endpunkte konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="66d20-643">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="66d20-644">*Kestrel-Unterstützung für SNI*</span><span class="sxs-lookup"><span data-stu-id="66d20-644">*Kestrel support for SNI*</span></span>

<span data-ttu-id="66d20-645">Die [Servernamensanzeige (SNI)](https://tools.ietf.org/html/rfc6066#section-3) kann zum Hosten mehrerer Domänen auf der gleichen IP-Adresse und dem gleichen Port verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="66d20-645">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="66d20-646">Damit die Servernamensanzeige funktioniert, sendet der Client während des TLS-Handshakes den Hostnamen für die sichere Sitzung an den Server, sodass der Server das richtige Zertifikat bereitstellen kann.</span><span class="sxs-lookup"><span data-stu-id="66d20-646">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="66d20-647">Der Client verwendet das beigestellte Zertifikat für die verschlüsselte Kommunikation mit dem Server während der sicheren Sitzung nach dem TLS-Handshake.</span><span class="sxs-lookup"><span data-stu-id="66d20-647">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="66d20-648">Kestrel unterstützt die Servernamensanzeige über den `ServerCertificateSelector`-Rückruf.</span><span class="sxs-lookup"><span data-stu-id="66d20-648">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="66d20-649">Der Rückruf wird für jede Verbindung einmal aufgerufen, um der App zu ermöglichen, den Hostnamen zu überprüfen und das entsprechende Zertifikat auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="66d20-649">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="66d20-650">Für die Unterstützung der Servernamensanzeige benötigen Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="66d20-650">SNI support requires:</span></span>

* <span data-ttu-id="66d20-651">Wird auf dem Zielframework `netcoreapp2.1` oder höher ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="66d20-651">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="66d20-652">In `net461` oder höher, wird der Rückruf aufgerufen, `name` ist aber immer `null`.</span><span class="sxs-lookup"><span data-stu-id="66d20-652">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="66d20-653">`name` ist auch `null`, wenn der Client den Hostnamenparameter nicht im TLS-Handshake angibt.</span><span class="sxs-lookup"><span data-stu-id="66d20-653">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="66d20-654">Alle Websites werden in derselben Kestrel-Instanz ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="66d20-654">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="66d20-655">Kestrel unterstützt ohne Reverseproxy keine gemeinsame IP-Adresse und keinen gemeinsamen Port für mehrere Instanzen.</span><span class="sxs-lookup"><span data-stu-id="66d20-655">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="66d20-656">Verbindungsprotokollierung</span><span class="sxs-lookup"><span data-stu-id="66d20-656">Connection logging</span></span>

<span data-ttu-id="66d20-657">Rufen Sie <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> auf, um Protokolle auf Debugebene für die Kommunikation auf Byteebene für eine Verbindung auszugeben.</span><span class="sxs-lookup"><span data-stu-id="66d20-657">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="66d20-658">Die Verbindungsprotokollierung ist beim Beheben von Problemen bei der Low-Level-Kommunikation hilfreich, wie z. B. bei der TLS-Verschlüsselung und bei Proxys.</span><span class="sxs-lookup"><span data-stu-id="66d20-658">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="66d20-659">Wenn `UseConnectionLogging` vor `UseHttps` platziert wird, wird der verschlüsselte Datenverkehr protokolliert.</span><span class="sxs-lookup"><span data-stu-id="66d20-659">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="66d20-660">Wenn `UseConnectionLogging` nach `UseHttps` platziert wird, wird der entschlüsselte Datenverkehr protokolliert.</span><span class="sxs-lookup"><span data-stu-id="66d20-660">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="66d20-661">Binden an einen TCP-Socket</span><span class="sxs-lookup"><span data-stu-id="66d20-661">Bind to a TCP socket</span></span>

<span data-ttu-id="66d20-662">Die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>-Methode wird an ein TCP-Socket gebunden, und ein Lambdaausdruck einer Option lässt die Konfiguration des X.509-Zertifikats zu:</span><span class="sxs-lookup"><span data-stu-id="66d20-662">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="66d20-663">Im Beispiel wird HTTPS für einen Endpunkt mit <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="66d20-663">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="66d20-664">Verwenden Sie die gleiche API zum Konfigurieren anderer Kestrel-Einstellungen für bestimmte Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="66d20-664">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="66d20-665">Binden an einen Unix-Socket</span><span class="sxs-lookup"><span data-stu-id="66d20-665">Bind to a Unix socket</span></span>

<span data-ttu-id="66d20-666">Wie in diesem Beispiel dargestellt, lauschen Sie an einem Unix-Socket mit <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>, um eine verbesserte Leistung mit Nginx zu erzielen:</span><span class="sxs-lookup"><span data-stu-id="66d20-666">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

### <a name="port-0"></a><span data-ttu-id="66d20-667">Port 0</span><span class="sxs-lookup"><span data-stu-id="66d20-667">Port 0</span></span>

<span data-ttu-id="66d20-668">Wenn die Portnummer `0` angegeben wird, wird Kestrel dynamisch an einen verfügbaren Port gebunden.</span><span class="sxs-lookup"><span data-stu-id="66d20-668">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="66d20-669">Im folgenden Beispiel wird veranschaulicht, wie bestimmt werden kann, für welchen Port Kestrel zur Laufzeit eine Bindung erstellt hat:</span><span class="sxs-lookup"><span data-stu-id="66d20-669">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="66d20-670">Wenn die App ausgeführt wird, gibt das Ausgabefenster der Konsole den dynamischen Port an, über den die App erreicht werden kann:</span><span class="sxs-lookup"><span data-stu-id="66d20-670">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="66d20-671">Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="66d20-671">Limitations</span></span>

<span data-ttu-id="66d20-672">Konfigurieren Sie Endpunkte mithilfe der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="66d20-672">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="66d20-673">Befehlszeilenargument `--urls`</span><span class="sxs-lookup"><span data-stu-id="66d20-673">`--urls` command-line argument</span></span>
* <span data-ttu-id="66d20-674">Hostkonfigurationsschlüssel `urls`</span><span class="sxs-lookup"><span data-stu-id="66d20-674">`urls` host configuration key</span></span>
* <span data-ttu-id="66d20-675">Umgebungsvariable `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="66d20-675">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="66d20-676">Diese Methoden sind nützlich, wenn Ihr Code mit anderen Servern als Kestrel funktionieren soll.</span><span class="sxs-lookup"><span data-stu-id="66d20-676">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="66d20-677">Beachten Sie jedoch die folgenden Einschränkungen:</span><span class="sxs-lookup"><span data-stu-id="66d20-677">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="66d20-678">HTTPS kann nicht mit diesen Ansätzen verwendet werden, außer ein Standardzertifikat wird in der HTTPS-Endpunktkonfiguration angegeben (z.B. wenn Sie wie zuvor in diesem Artikel gezeigt die `KestrelServerOptions`-Konfiguration oder eine Konfigurationsdatei verwenden).</span><span class="sxs-lookup"><span data-stu-id="66d20-678">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="66d20-679">Wenn die Ansätze `Listen` und `UseUrls` gleichzeitig verwendet werden, überschreiben die `Listen`-Endpunkte die `UseUrls`-Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="66d20-679">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="66d20-680">IIS-Endpunktkonfiguration</span><span class="sxs-lookup"><span data-stu-id="66d20-680">IIS endpoint configuration</span></span>

<span data-ttu-id="66d20-681">Bei der Verwendung von IIS werden die URL-Bindungen für IIS-Überschreibungsbindungen durch `Listen` oder `UseUrls` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="66d20-681">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="66d20-682">Weitere Informationen finden Sie im Artikel [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="66d20-682">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="66d20-683">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="66d20-683">ListenOptions.Protocols</span></span>

<span data-ttu-id="66d20-684">Die `Protocols`-Eigenschaft richtet die HTTP-Protokolle (`HttpProtocols`) ein, die für einen Verbindungsendpunkt oder für den Server aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="66d20-684">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="66d20-685">Weisen Sie der `Protocols`-Eigenschaft einen Wert aus der `HttpProtocols`-Enumeration zu.</span><span class="sxs-lookup"><span data-stu-id="66d20-685">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="66d20-686">`HttpProtocols`-Enumerationswert</span><span class="sxs-lookup"><span data-stu-id="66d20-686">`HttpProtocols` enum value</span></span> | <span data-ttu-id="66d20-687">Zulässiges Verbindungsprotokoll</span><span class="sxs-lookup"><span data-stu-id="66d20-687">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="66d20-688">Nur HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="66d20-688">HTTP/1.1 only.</span></span> <span data-ttu-id="66d20-689">Kann mit oder ohne TLS verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="66d20-689">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="66d20-690">Nur HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="66d20-690">HTTP/2 only.</span></span> <span data-ttu-id="66d20-691">Kann nur ohne TLS verwendet werden, wenn der Client einen [Vorabkenntnis-Modus](https://tools.ietf.org/html/rfc7540#section-3.4) unterstützt.</span><span class="sxs-lookup"><span data-stu-id="66d20-691">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="66d20-692">HTTP/1.1 und HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="66d20-692">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="66d20-693">HTTP/2 erfordert eine TLS- und [ALPN](https://tools.ietf.org/html/rfc7301#section-3)-Verbindung (Application-Layer Protocol Negotiation). Andernfalls wird für die Verbindung standardmäßig HTTP/1.1 verwendet.</span><span class="sxs-lookup"><span data-stu-id="66d20-693">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="66d20-694">Das Standardprotokoll ist HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="66d20-694">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="66d20-695">TLS-Einschränkungen für HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="66d20-695">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="66d20-696">TLS Version 1.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="66d20-696">TLS version 1.2 or later</span></span>
* <span data-ttu-id="66d20-697">Erneute Aushandlung deaktiviert</span><span class="sxs-lookup"><span data-stu-id="66d20-697">Renegotiation disabled</span></span>
* <span data-ttu-id="66d20-698">Komprimierung deaktiviert</span><span class="sxs-lookup"><span data-stu-id="66d20-698">Compression disabled</span></span>
* <span data-ttu-id="66d20-699">Minimale Größen für Austausch von flüchtigen Schlüsseln:</span><span class="sxs-lookup"><span data-stu-id="66d20-699">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="66d20-700">ECDHE (Elliptic Curve Diffie-Hellman) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; mindestens 224 Bits</span><span class="sxs-lookup"><span data-stu-id="66d20-700">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits minimum</span></span>
  * <span data-ttu-id="66d20-701">DHE (Finite Field Diffie-Hellman) &lbrack;`TLS12`&rbrack; &ndash; mindestens 2.048 Bits</span><span class="sxs-lookup"><span data-stu-id="66d20-701">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bits minimum</span></span>
* <span data-ttu-id="66d20-702">Verschlüsselungssammlung nicht gesperrt</span><span class="sxs-lookup"><span data-stu-id="66d20-702">Cipher suite not blacklisted</span></span>

<span data-ttu-id="66d20-703">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; mit der elliptischen P-256-Kurve &lbrack;`FIPS186`&rbrack; wird standardmäßig unterstützt.</span><span class="sxs-lookup"><span data-stu-id="66d20-703">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="66d20-704">Das folgende Beispiel erlaubt HTTP/1.1- und HTTP/2-Verbindungen an Port 8000.</span><span class="sxs-lookup"><span data-stu-id="66d20-704">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="66d20-705">Die Verbindungen werden durch TLS mit einem bereitgestellten Zertifikat geschützt:</span><span class="sxs-lookup"><span data-stu-id="66d20-705">Connections are secured by TLS with a supplied certificate:</span></span>

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

<span data-ttu-id="66d20-706">Erstellen Sie optional eine `IConnectionAdapter`-Implementierung, um TLS-Handshakes auf Verbindungsbasis für bestimmte Verschlüsselungen zu filtern:</span><span class="sxs-lookup"><span data-stu-id="66d20-706">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

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

<span data-ttu-id="66d20-707">*Festlegen des Protokolls aus der Konfiguration*</span><span class="sxs-lookup"><span data-stu-id="66d20-707">*Set the protocol from configuration*</span></span>

<span data-ttu-id="66d20-708"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ruft `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` standardmäßig zum Laden der Kestrel-Konfiguration auf.</span><span class="sxs-lookup"><span data-stu-id="66d20-708"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="66d20-709">Im folgenden Beispiel *appsettings.json* wird für alle Endpunkte von Kestrel ein Standardverbindungsprotokoll (HTTP/1.1 und HTTP/2) eingerichtet:</span><span class="sxs-lookup"><span data-stu-id="66d20-709">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="66d20-710">Das folgende Beispiel einer Konfigurationsdatei richtet ein Verbindungsprotokoll für einen bestimmten Endpunkt ein:</span><span class="sxs-lookup"><span data-stu-id="66d20-710">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="66d20-711">Protokolle, die in Code angegeben werden, setzen Werte außer Kraft, der durch die Konfiguration festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="66d20-711">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="66d20-712">Transportkonfiguration</span><span class="sxs-lookup"><span data-stu-id="66d20-712">Transport configuration</span></span>

<span data-ttu-id="66d20-713">Mit dem Release von ASP.NET Core 2.1 basiert der Standardtransport von Kestrel nicht mehr auf Libuv, sondern auf verwalteten Sockets.</span><span class="sxs-lookup"><span data-stu-id="66d20-713">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="66d20-714">Dies stellt einen Breaking Change für ASP.NET Core 2.0-Apps dar, die auf Version 2.1 upgraden, <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> aufrufen und von einem der folgenden Pakete abhängig sind:</span><span class="sxs-lookup"><span data-stu-id="66d20-714">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="66d20-715">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direkter Paketverweis)</span><span class="sxs-lookup"><span data-stu-id="66d20-715">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="66d20-716">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="66d20-716">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="66d20-717">Für Projekte, die den Einsatz von Libuv erfordern:</span><span class="sxs-lookup"><span data-stu-id="66d20-717">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="66d20-718">Fügen Sie für das [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/)-Paket eine Abhängigkeit auf die Projektdatei der App hinzu:</span><span class="sxs-lookup"><span data-stu-id="66d20-718">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="66d20-719">Rufen Sie <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> auf:</span><span class="sxs-lookup"><span data-stu-id="66d20-719">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="66d20-720">URL-Präfixe</span><span class="sxs-lookup"><span data-stu-id="66d20-720">URL prefixes</span></span>

<span data-ttu-id="66d20-721">Bei der Verwendung von `UseUrls`, dem Befehlszeilenargument `--urls`, dem Hostkonfigurationsschlüssel `urls` oder der Umgebungsvariable `ASPNETCORE_URLS` können die URL-Präfixe in den folgenden Formaten vorliegen.</span><span class="sxs-lookup"><span data-stu-id="66d20-721">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="66d20-722">Nur HTTP-URL-Präfixe sind gültig.</span><span class="sxs-lookup"><span data-stu-id="66d20-722">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="66d20-723">Kestrel unterstützt HTTPS nicht beim Konfigurieren von URL-Bindungen mit `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="66d20-723">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="66d20-724">IPv4-Adresse mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="66d20-724">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="66d20-725">Bei `0.0.0.0` handelt es sich um einen Sonderfall, für den eine Bindung an alle IPv4-Adressen erfolgt.</span><span class="sxs-lookup"><span data-stu-id="66d20-725">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="66d20-726">IPv6-Adresse mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="66d20-726">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="66d20-727">`[::]` stellt das Äquivalent von IPv6 zu `0.0.0.0` für IPv4 dar.</span><span class="sxs-lookup"><span data-stu-id="66d20-727">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="66d20-728">Hostname mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="66d20-728">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="66d20-729">Hostnamen, `*` und `+` sind nicht spezifisch.</span><span class="sxs-lookup"><span data-stu-id="66d20-729">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="66d20-730">Alle Elemente, die nicht als gültige IP-Adresse oder `localhost` erkannt werden, werden an alle IPv4- und IPv6-IP-Adressen gebunden.</span><span class="sxs-lookup"><span data-stu-id="66d20-730">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="66d20-731">Verwenden Sie [HTTP.sys](xref:fundamentals/servers/httpsys) oder einen Reverseproxyserver zum Binden verschiedener Hostnamen an verschiedene ASP.NET Core-Apps auf demselben Port, z.B. IIS, Nginx oder Apache.</span><span class="sxs-lookup"><span data-stu-id="66d20-731">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="66d20-732">Das Hosting in einer Reverseproxykonfiguration erfordert [Hostfilterung](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="66d20-732">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="66d20-733">`localhost`-Hostname mit Portnummer oder Loopback-IP mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="66d20-733">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="66d20-734">Wenn `localhost` angegeben ist, versucht Kestrel, eine Bindung zu IPv4- und IPv6-Loopback-Schnittstellen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="66d20-734">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="66d20-735">Wenn der erforderliche Port von einem anderen Dienst auf einer der Loopback-Schnittstellen verwendet wird, tritt beim Starten von Kestrel ein Fehler auf.</span><span class="sxs-lookup"><span data-stu-id="66d20-735">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="66d20-736">Wenn eine der Loopback-Schnittstellen aus anderen Gründen nicht verfügbar ist (meistens durch die fehlende Unterstützung von IPv6), protokolliert Kestrel eine Warnung.</span><span class="sxs-lookup"><span data-stu-id="66d20-736">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="66d20-737">Host-Filterung</span><span class="sxs-lookup"><span data-stu-id="66d20-737">Host filtering</span></span>

<span data-ttu-id="66d20-738">Obwohl Kestrel die Konfiguration basierend auf Präfixe wie `http://example.com:5000` unterstützt, ignoriert Kestrel den Hostnamen weitgehend.</span><span class="sxs-lookup"><span data-stu-id="66d20-738">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="66d20-739">Der Host `localhost` ist ein Sonderfall, der für die Bindung an Loopback-Adressen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="66d20-739">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="66d20-740">Jeder Host, der keine explizite IP-Adresse ist, wird an alle öffentlichen IP-Adressen gebunden.</span><span class="sxs-lookup"><span data-stu-id="66d20-740">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="66d20-741">`Host`-Header werden nicht überprüft.</span><span class="sxs-lookup"><span data-stu-id="66d20-741">`Host` headers aren't validated.</span></span>

<span data-ttu-id="66d20-742">Verwenden Sie Middleware zum Filtern von Hosts, um dieses Problem zu umgehen.</span><span class="sxs-lookup"><span data-stu-id="66d20-742">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="66d20-743">Middleware zum Filtern von Hosts wird im Paket [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) bereitgestellt, das im [Metapaket Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) enthalten ist (ASP.NET Core 2.1 oder 2.2).</span><span class="sxs-lookup"><span data-stu-id="66d20-743">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="66d20-744">Die Middleware wird von <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> hinzugefügt, wodurch <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="66d20-744">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="66d20-745">Die Middleware zum Filtern von Hosts ist standardmäßig deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="66d20-745">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="66d20-746">Wenn Sie die Middleware aktivieren möchten, definieren Sie einen `AllowedHosts`-Schlüssel in *appsettings.json*/*appsettings.\<Umgebungsname>.json*.</span><span class="sxs-lookup"><span data-stu-id="66d20-746">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="66d20-747">Der Wert ist eine durch Semikolons getrennte Liste von Hostnamen ohne Portnummern:</span><span class="sxs-lookup"><span data-stu-id="66d20-747">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="66d20-748">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="66d20-748">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="66d20-749">[Middleware für weitergeleitete Header](xref:host-and-deploy/proxy-load-balancer) hat auch eine <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>-Option.</span><span class="sxs-lookup"><span data-stu-id="66d20-749">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="66d20-750">Middleware für weitergeleitete Header und Middleware zum Filtern von Hosts besitzen ähnliche Funktionen für unterschiedliche Szenarios.</span><span class="sxs-lookup"><span data-stu-id="66d20-750">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="66d20-751">Legen Sie `AllowedHosts` mit Middleware für weitergeleitete Header fest, wenn der `Host`-Header beim Weiterleiten von Anforderungen mit einem Reverseproxyserver oder einem Lastenausgleichsmodul nicht beibehalten wird.</span><span class="sxs-lookup"><span data-stu-id="66d20-751">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="66d20-752">Legen Sie `AllowedHosts` mit Middleware zum Filtern von Hosts fest, wenn Kestrel als öffentlicher Edgeserver verwendet oder der `Host`-Header direkt weitergeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="66d20-752">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="66d20-753">Weitere Informationen zu Middleware für weitergeleitete Header finden Sie unter <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="66d20-753">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="66d20-754">Einführung in Kestrel, dem plattformübergreifenden [Webserver für ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="66d20-754">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="66d20-755">Kestrel ist der Webserver, der standardmäßig in ASP.NET Core-Projektvorlagen enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="66d20-755">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="66d20-756">Kestrel unterstützt die folgenden Szenarios:</span><span class="sxs-lookup"><span data-stu-id="66d20-756">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="66d20-757">HTTPS</span><span class="sxs-lookup"><span data-stu-id="66d20-757">HTTPS</span></span>
* <span data-ttu-id="66d20-758">Nicht transparente Upgrades, die zum Aktivieren von [WebSockets](https://github.com/aspnet/websockets) verwendet werden</span><span class="sxs-lookup"><span data-stu-id="66d20-758">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="66d20-759">Unix-Sockets für eine hohe Leistung im Hintergrund von Nginx</span><span class="sxs-lookup"><span data-stu-id="66d20-759">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="66d20-760">Kestrel wird auf allen Plattformen und für alle Versionen unterstützt, die .NET Core unterstützt.</span><span class="sxs-lookup"><span data-stu-id="66d20-760">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="66d20-761">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="66d20-761">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="66d20-762">Verwenden von Kestrel mit einem Reverseproxy</span><span class="sxs-lookup"><span data-stu-id="66d20-762">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="66d20-763">Kestrel kann eigenständig oder mit einem *Reverseproxyserver* wie z.B. [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="66d20-763">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="66d20-764">Ein Reverseproxyserver empfängt HTTP-Anforderungen aus dem Netzwerk und leitet diese an Kestrel weiter.</span><span class="sxs-lookup"><span data-stu-id="66d20-764">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="66d20-765">Kestrel bei Verwendung als Webserver mit direkter Internetverbindung:</span><span class="sxs-lookup"><span data-stu-id="66d20-765">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel kommuniziert direkt und ohne Reverseproxyserver mit dem Internet](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="66d20-767">Kestrel bei Verwendung in einer Reverseproxykonfiguration:</span><span class="sxs-lookup"><span data-stu-id="66d20-767">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel kommuniziert indirekt mit dem Internet über einen Reverseproxyserver wie IIS, Nginx oder Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="66d20-769">Jede der beiden Konfigurationen – mit oder ohne einen Reverseproxyserver – stellt eine unterstützte Hostingkonfiguration dar.</span><span class="sxs-lookup"><span data-stu-id="66d20-769">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="66d20-770">Bei Verwendung als Edgeserver ohne Reverseproxyserver unterstützt Kestrel die gemeinsame Nutzung der gleichen IP-Adresse und des gleichen Ports durch mehrere Prozesse nicht.</span><span class="sxs-lookup"><span data-stu-id="66d20-770">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="66d20-771">Wenn Kestrel für das Lauschen an einem Port konfiguriert ist, verarbeitet Kestrel den gesamten Datenverkehr für diesen Port unabhängig von den `Host`-Headern der Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="66d20-771">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="66d20-772">Ein Reverseproxy, der Ports freigeben kann, kann Anforderungen an Kestrel über eine eindeutige IP und einen eindeutigen Port weiterleiten.</span><span class="sxs-lookup"><span data-stu-id="66d20-772">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="66d20-773">Auch wenn kein Reverseproxyserver erforderlich ist, kann die Verwendung eines solchen empfehlenswert sein.</span><span class="sxs-lookup"><span data-stu-id="66d20-773">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="66d20-774">Für einen Reverseproxy gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="66d20-774">A reverse proxy:</span></span>

* <span data-ttu-id="66d20-775">Er kann die verfügbar gemachten öffentlichen Oberflächen der von ihm gehosteten Apps einschränken.</span><span class="sxs-lookup"><span data-stu-id="66d20-775">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="66d20-776">Er stellt eine zusätzliche Ebene für Konfiguration und Schutz bereit.</span><span class="sxs-lookup"><span data-stu-id="66d20-776">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="66d20-777">Er lässt sich besser in die vorhandene Infrastruktur integrieren.</span><span class="sxs-lookup"><span data-stu-id="66d20-777">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="66d20-778">Er vereinfacht die Konfiguration von Lastenausgleich und sicherer Kommunikation (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="66d20-778">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="66d20-779">Nur der Reverseproxyserver erfordert ein X.509-Zertifikat, und dieser Server kann mit den Servern der App im internen Netzwerk über einfaches HTTP kommunizieren.</span><span class="sxs-lookup"><span data-stu-id="66d20-779">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="66d20-780">Das Hosting in einer Reverseproxykonfiguration erfordert [Hostfilterung](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="66d20-780">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="66d20-781">Verwenden von Kestrel in ASP.NET Core-Apps</span><span class="sxs-lookup"><span data-stu-id="66d20-781">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="66d20-782">Das Paket [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) ist im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) enthalten.</span><span class="sxs-lookup"><span data-stu-id="66d20-782">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="66d20-783">ASP.NET Core-Projektvorlagen verwenden Kestrel standardmäßig.</span><span class="sxs-lookup"><span data-stu-id="66d20-783">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="66d20-784">Der Vorlagencode in *Program.cs* ruft <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> auf, wodurch <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> im Hintergrund aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="66d20-784">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="66d20-785">Um zusätzliche Konfiguration nach dem Aufruf von `CreateDefaultBuilder` bereitzustellen, rufen Sie <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> auf:</span><span class="sxs-lookup"><span data-stu-id="66d20-785">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="66d20-786">Weitere Informationen zum `CreateDefaultBuilder` und zum Erstellen des Hosts finden Sie im Abschnitt *Einrichten eines Hosts* von <xref:fundamentals/host/web-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="66d20-786">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="66d20-787">Kestrel-Optionen</span><span class="sxs-lookup"><span data-stu-id="66d20-787">Kestrel options</span></span>

<span data-ttu-id="66d20-788">Der Kestrel-Webserver verfügt über einschränkende Konfigurationsoptionen, die besonders nützlich bei Bereitstellungen mit Internetzugriff sind.</span><span class="sxs-lookup"><span data-stu-id="66d20-788">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="66d20-789">Legen Sie Einschränkungen für die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits>-Eigenschaft der <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>-Klasse fest.</span><span class="sxs-lookup"><span data-stu-id="66d20-789">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="66d20-790">Die `Limits`-Eigenschaft enthält eine Instanz der <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>-Klasse.</span><span class="sxs-lookup"><span data-stu-id="66d20-790">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="66d20-791">In den folgenden Beispielen wird der <xref:Microsoft.AspNetCore.Server.Kestrel.Core>-Namespace verwendet:</span><span class="sxs-lookup"><span data-stu-id="66d20-791">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="66d20-792">Kestrel-Optionen, die in den folgenden Beispielen in C#-Code konfiguriert sind, können auch mit einem [Konfigurationsanbieter](xref:fundamentals/configuration/index) festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="66d20-792">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="66d20-793">Beispielsweise kann der Dateikonfigurationsanbieter die Kestrel-Konfiguration aus einer *appsettings.json*- oder *appsettings.{Umgebung}.json*-Datei laden:</span><span class="sxs-lookup"><span data-stu-id="66d20-793">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="66d20-794">Verwenden Sie **einen** der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="66d20-794">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="66d20-795">Konfigurieren Sie Kestrel in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="66d20-795">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="66d20-796">Fügen Sie eine Instanz von `IConfiguration` in die `Startup`-Klasse ein.</span><span class="sxs-lookup"><span data-stu-id="66d20-796">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="66d20-797">Im folgenden Beispiel wird davon ausgegangen, dass die eingefügte Konfiguration der `Configuration`-Eigenschaft zugewiesen wird.</span><span class="sxs-lookup"><span data-stu-id="66d20-797">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="66d20-798">Laden Sie in `Startup.ConfigureServices` den Abschnitt `Kestrel` der Konfiguration in die Konfiguration von Kestrel.</span><span class="sxs-lookup"><span data-stu-id="66d20-798">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration.</span></span>

     ```csharp
     // using Microsoft.Extensions.Configuration

     public void ConfigureServices(IServiceCollection services)
     {
         services.Configure<KestrelServerOptions>(
             Configuration.GetSection("Kestrel"));
     }
     ```

* <span data-ttu-id="66d20-799">Konfigurieren Sie Kestrel beim Erstellen des Hosts:</span><span class="sxs-lookup"><span data-stu-id="66d20-799">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="66d20-800">Laden Sie in *Program.cs* den Abschnitt `Kestrel` der Konfiguration in die Konfiguration von Kestrel:</span><span class="sxs-lookup"><span data-stu-id="66d20-800">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="66d20-801">Beide vorangehenden Ansätze funktionieren mit jedem [Konfigurationsanbieter](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="66d20-801">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="66d20-802">Keep-Alive-Timeout</span><span class="sxs-lookup"><span data-stu-id="66d20-802">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="66d20-803">Ruft das [Keep-Alive-Timeout](https://tools.ietf.org/html/rfc7230#section-6.5) ab oder legt es fest.</span><span class="sxs-lookup"><span data-stu-id="66d20-803">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="66d20-804">Standardwert: 2 Minuten.</span><span class="sxs-lookup"><span data-stu-id="66d20-804">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="66d20-805">Maximale Anzahl der Clientverbindungen</span><span class="sxs-lookup"><span data-stu-id="66d20-805">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="66d20-806">Die maximale Anzahl von gleichzeitig geöffneten TCP-Verbindungen kann mithilfe von folgendem Code für die gesamte App festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="66d20-806">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="66d20-807">Es gibt einen separaten Grenzwert für Verbindungen, die von HTTP oder HTTPS auf ein anderes Protokoll aktualisiert wurden (z.B. auf eine WebSockets-Anforderung).</span><span class="sxs-lookup"><span data-stu-id="66d20-807">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="66d20-808">Nachdem eine Verbindung aktualisiert wurde, zählt diese nicht mehr für den `MaxConcurrentConnections`-Grenzwert.</span><span class="sxs-lookup"><span data-stu-id="66d20-808">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="66d20-809">Die maximale Anzahl von Verbindungen ist standardmäßig nicht begrenzt (NULL).</span><span class="sxs-lookup"><span data-stu-id="66d20-809">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="66d20-810">Maximale Größe des Anforderungstexts</span><span class="sxs-lookup"><span data-stu-id="66d20-810">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="66d20-811">Die maximale Größe des Anforderungstexts beträgt standardmäßig 30.000.000 Byte, also ungefähr 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="66d20-811">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="66d20-812">Die empfohlene Methode zur Außerkraftsetzung des Grenzwerts in einer ASP.NET Core-MVC-App besteht im Verwenden des <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>-Attributs in einer Aktionsmethode:</span><span class="sxs-lookup"><span data-stu-id="66d20-812">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="66d20-813">Im folgenden Beispiel wird veranschaulicht, wie die Einschränkungen auf jeder Anforderung für die App konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="66d20-813">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="66d20-814">Außer Kraft setzen der Einstellung für eine bestimmte Anforderung in Middleware:</span><span class="sxs-lookup"><span data-stu-id="66d20-814">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="66d20-815">Eine Ausnahme wird ausgelöst, wenn die App den Grenzwert einer Anforderung konfiguriert, nachdem die App bereits mit dem Lesen der Anforderung begonnen hat.</span><span class="sxs-lookup"><span data-stu-id="66d20-815">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="66d20-816">Es gibt eine `IsReadOnly`-Eigenschaft, die angibt, wenn sich die `MaxRequestBodySize`-Eigenschaft im schreibgeschützten Zustand befindet, also wenn der Grenzwert nicht mehr konfiguriert werden kann.</span><span class="sxs-lookup"><span data-stu-id="66d20-816">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="66d20-817">Wenn eine App [prozessextern](xref:host-and-deploy/iis/index#out-of-process-hosting-model) hinter dem [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) ausgeführt wird, ist das Größenlimit von Kestrel für Anforderungstext deaktiviert, weil IIS dieses Limit bereits festlegt.</span><span class="sxs-lookup"><span data-stu-id="66d20-817">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="66d20-818">Minimale Datenrate des Anforderungstexts</span><span class="sxs-lookup"><span data-stu-id="66d20-818">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="66d20-819">Kestrel überprüft sekündlich, ob Daten mit der angegebenen Rate in Bytes/Sekunde eingehen.</span><span class="sxs-lookup"><span data-stu-id="66d20-819">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="66d20-820">Wenn die Rate den mindestens erforderlichen Wert unterschreitet, wird für die Verbindung wegen Timeout getrennt. Bei der Toleranzperiode handelt es sich um die Zeitspanne, die Kestrel dem Client gewährt, um die Senderate auf den mindestens erforderlichen Wert zu erhöhen. Die Rate wird währenddessen nicht überprüft.</span><span class="sxs-lookup"><span data-stu-id="66d20-820">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="66d20-821">Diese Toleranzperiode beugt dem Trennen von Verbindungen vor, die Daten aufgrund eines langsamen TCP-Starts anfänglich mit einer niedrigen Rate senden.</span><span class="sxs-lookup"><span data-stu-id="66d20-821">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="66d20-822">Die mindestens erforderliche Rate beträgt standardmäßig 240 Bytes/Sekunde mit einer Toleranzperiode von 5 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="66d20-822">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="66d20-823">Für die Antwort gilt ebenfalls eine mindestens erforderliche Rate.</span><span class="sxs-lookup"><span data-stu-id="66d20-823">A minimum rate also applies to the response.</span></span> <span data-ttu-id="66d20-824">Der Code zum Festlegen des Grenzwerts für Anforderung und Antwort ist abgesehen von `RequestBody` oder `Response` in den Namen von Eigenschaften und Schnittstelle identisch.</span><span class="sxs-lookup"><span data-stu-id="66d20-824">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="66d20-825">In diesem Beispiel wird veranschaulicht, wie Sie die mindestens erforderlichen Datenraten in *Program.cs* konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="66d20-825">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

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

### <a name="request-headers-timeout"></a><span data-ttu-id="66d20-826">Timeout für Anforderungsheader</span><span class="sxs-lookup"><span data-stu-id="66d20-826">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="66d20-827">Ruft die maximale Zeitspanne ab, während der der Server Anforderungsheader empfängt, oder legt diese fest.</span><span class="sxs-lookup"><span data-stu-id="66d20-827">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="66d20-828">Der Standardwert beträgt 30 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="66d20-828">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="66d20-829">Synchrone E/A-Vorgänge</span><span class="sxs-lookup"><span data-stu-id="66d20-829">Synchronous IO</span></span>

<span data-ttu-id="66d20-830"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> steuert, ob für Anforderung und Antwort synchrone E/A-Vorgänge zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="66d20-830"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="66d20-831">Der Standardwert ist `true`.</span><span class="sxs-lookup"><span data-stu-id="66d20-831">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="66d20-832">Sehr viele blockierende synchrone E/A-Vorgänge können zu einem Ressourcenmangel im Threadpool führen, wodurch die App nicht mehr reagiert.</span><span class="sxs-lookup"><span data-stu-id="66d20-832">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="66d20-833">Aktivieren Sie `AllowSynchronousIO` nur bei Verwendung einer Bibliothek, die asynchrone E/A-Vorgänge nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="66d20-833">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="66d20-834">Das folgende Beispiel deaktiviert synchrone E/A-Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="66d20-834">The following example disables synchronous IO:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="66d20-835">Weitere Informationen zu anderen Kestrel-Optionen und -Einschränkungen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="66d20-835">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="66d20-836">Endpunktkonfiguration</span><span class="sxs-lookup"><span data-stu-id="66d20-836">Endpoint configuration</span></span>

<span data-ttu-id="66d20-837">Standardmäßig wird ASP.NET Core an Folgendes gebunden:</span><span class="sxs-lookup"><span data-stu-id="66d20-837">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="66d20-838">`https://localhost:5001` (wenn ein lokales Entwicklungszertifikat vorhanden ist)</span><span class="sxs-lookup"><span data-stu-id="66d20-838">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="66d20-839">Verwenden Sie Folgendes zum Angeben der URLs:</span><span class="sxs-lookup"><span data-stu-id="66d20-839">Specify URLs using the:</span></span>

* <span data-ttu-id="66d20-840">Die Umgebungsvariable `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="66d20-840">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="66d20-841">Das Befehlszeilenargument `--urls`</span><span class="sxs-lookup"><span data-stu-id="66d20-841">`--urls` command-line argument.</span></span>
* <span data-ttu-id="66d20-842">Den Hostkonfigurationsschlüssel `urls`</span><span class="sxs-lookup"><span data-stu-id="66d20-842">`urls` host configuration key.</span></span>
* <span data-ttu-id="66d20-843">Die Erweiterungsmethode `UseUrls`</span><span class="sxs-lookup"><span data-stu-id="66d20-843">`UseUrls` extension method.</span></span>

<span data-ttu-id="66d20-844">Der Wert, der mit diesen Ansätzen angegeben wird, kann mindestens ein HTTP- oder HTTPS-Endpunkt sein (HTTPS wenn ein Standardzertifikat verfügbar ist).</span><span class="sxs-lookup"><span data-stu-id="66d20-844">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="66d20-845">Konfigurieren Sie den Wert als eine durch Semikolons getrennte Liste (z.B. `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="66d20-845">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="66d20-846">Weitere Informationen zu diesen Ansätzen finden Sie unter [Server-URLs](xref:fundamentals/host/web-host#server-urls) und [Außerkraftsetzen der Konfiguration](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="66d20-846">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="66d20-847">Ein Entwicklungszertifikat wird erstellt:</span><span class="sxs-lookup"><span data-stu-id="66d20-847">A development certificate is created:</span></span>

* <span data-ttu-id="66d20-848">Wenn das [.NET Core SDK](/dotnet/core/sdk) installiert wird.</span><span class="sxs-lookup"><span data-stu-id="66d20-848">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="66d20-849">Das [dev-cert-Tool](xref:aspnetcore-2.1#https) wird zum Erstellen eines Zertifikats verwendet.</span><span class="sxs-lookup"><span data-stu-id="66d20-849">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="66d20-850">Einige Browser erfordern, dass Sie die explizite Berechtigung erteilen, dem lokalen Entwicklungszertifikat zu vertrauen.</span><span class="sxs-lookup"><span data-stu-id="66d20-850">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="66d20-851">Projektvorlagen konfigurieren Apps, damit sie standardmäßig auf HTTPS ausgeführt werden und die [HTTPS-Umleitung und HSTS-Unterstützung](xref:security/enforcing-ssl) enthalten.</span><span class="sxs-lookup"><span data-stu-id="66d20-851">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="66d20-852">Rufen Sie die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>- oder <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>-Methode unter <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> auf, um URL-Präfixe und Ports für Kestrel zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="66d20-852">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="66d20-853">`UseUrls`, das Befehlszeilenargument `--urls`, der Hostkonfigurationsschlüssel `urls` und die Umgebungsvariable `ASPNETCORE_URLS` funktionieren ebenfalls, verfügen jedoch über Einschränkungen, die im Verlauf dieses Abschnitts erläutert werden (Ein Standardzertifikat muss für die HTTPS-Endpunktkonfiguration verfügbar sein).</span><span class="sxs-lookup"><span data-stu-id="66d20-853">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="66d20-854">`KestrelServerOptions`-Konfiguration:</span><span class="sxs-lookup"><span data-stu-id="66d20-854">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="66d20-855">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="66d20-855">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="66d20-856">Gibt die Konfiguration von `Action` zum Ausführen von jedem angegebenen Endpunkt an.</span><span class="sxs-lookup"><span data-stu-id="66d20-856">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="66d20-857">Mehrmalige Aufrufe von `ConfigureEndpointDefaults` ersetzen vorherige Instanzen von `Action` mit der zuletzt angegebenen `Action`.</span><span class="sxs-lookup"><span data-stu-id="66d20-857">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="66d20-858">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="66d20-858">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="66d20-859">Gibt die Konfiguration von `Action` zum Ausführen von jedem HTTPS-Endpunkt an.</span><span class="sxs-lookup"><span data-stu-id="66d20-859">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="66d20-860">Mehrmalige Aufrufe von `ConfigureHttpsDefaults` ersetzen vorherige Instanzen von `Action` mit der zuletzt angegebenen `Action`.</span><span class="sxs-lookup"><span data-stu-id="66d20-860">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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

### <a name="configureiconfiguration"></a><span data-ttu-id="66d20-861">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="66d20-861">Configure(IConfiguration)</span></span>

<span data-ttu-id="66d20-862">Erstellt ein Konfigurationsladeprogramm für das Einrichten von Kestrel, was <xref:Microsoft.Extensions.Configuration.IConfiguration> als Eingabe erfordert.</span><span class="sxs-lookup"><span data-stu-id="66d20-862">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="66d20-863">Die Konfiguration muss auf den Konfigurationsabschnitt für Kestrel festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="66d20-863">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="66d20-864">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="66d20-864">ListenOptions.UseHttps</span></span>

<span data-ttu-id="66d20-865">Konfiguriert Kestrel zur Verwendung von HTTPS.</span><span class="sxs-lookup"><span data-stu-id="66d20-865">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="66d20-866">`ListenOptions.UseHttps`-Erweiterungen:</span><span class="sxs-lookup"><span data-stu-id="66d20-866">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="66d20-867">`UseHttps`: Konfiguriert Kestrel zur Verwendung von HTTPS mit dem Standardzertifikat.</span><span class="sxs-lookup"><span data-stu-id="66d20-867">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="66d20-868">Löst eine Ausnahme aus, wenn kein Standardzertifikat konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="66d20-868">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="66d20-869">`ListenOptions.UseHttps`-Parameter:</span><span class="sxs-lookup"><span data-stu-id="66d20-869">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="66d20-870">`filename` entspricht dem Pfad und Dateinamen einer Zertifikatdatei relativ zu dem Verzeichnis, das die Inhaltsdateien der App enthält.</span><span class="sxs-lookup"><span data-stu-id="66d20-870">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="66d20-871">`password` ist das für den Zugriff auf die X.509-Zertifikatsdaten erforderliche Kennwort.</span><span class="sxs-lookup"><span data-stu-id="66d20-871">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="66d20-872">`configureOptions` ist eine `Action` zum Konfigurieren von `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="66d20-872">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="66d20-873">Gibt `ListenOptions` zurück.</span><span class="sxs-lookup"><span data-stu-id="66d20-873">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="66d20-874">`storeName` ist der Zertifikatspeicher, aus dem das Zertifikat geladen wird.</span><span class="sxs-lookup"><span data-stu-id="66d20-874">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="66d20-875">`subject` ist der Name des Antragstellers für das Zertifikat.</span><span class="sxs-lookup"><span data-stu-id="66d20-875">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="66d20-876">`allowInvalid` gibt an, ob ungültige Zertifikate berücksichtigt werden sollten, z.B. selbstsignierte Zertifikate.</span><span class="sxs-lookup"><span data-stu-id="66d20-876">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="66d20-877">`location` ist der Speicherort, aus dem das Zertifikat geladen wird.</span><span class="sxs-lookup"><span data-stu-id="66d20-877">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="66d20-878">`serverCertificate` ist das X.509-Zertifikat.</span><span class="sxs-lookup"><span data-stu-id="66d20-878">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="66d20-879">In der Produktion muss HTTPS explizit konfiguriert sein.</span><span class="sxs-lookup"><span data-stu-id="66d20-879">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="66d20-880">Zumindest muss ein Standardzertifikat angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="66d20-880">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="66d20-881">Die im Folgenden beschriebenen unterstützten Konfigurationen:</span><span class="sxs-lookup"><span data-stu-id="66d20-881">Supported configurations described next:</span></span>

* <span data-ttu-id="66d20-882">Keine Konfiguration</span><span class="sxs-lookup"><span data-stu-id="66d20-882">No configuration</span></span>
* <span data-ttu-id="66d20-883">Ersetzen des Standardzertifikats aus der Konfiguration</span><span class="sxs-lookup"><span data-stu-id="66d20-883">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="66d20-884">Ändern des Standards im Code</span><span class="sxs-lookup"><span data-stu-id="66d20-884">Change the defaults in code</span></span>

<span data-ttu-id="66d20-885">*Keine Konfiguration*</span><span class="sxs-lookup"><span data-stu-id="66d20-885">*No configuration*</span></span>

<span data-ttu-id="66d20-886">Kestrel überwacht `http://localhost:5000` und `https://localhost:5001` (wenn ein Standardzertifikat verfügbar ist).</span><span class="sxs-lookup"><span data-stu-id="66d20-886">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="66d20-887">*Ersetzen des Standardzertifikats aus der Konfiguration*</span><span class="sxs-lookup"><span data-stu-id="66d20-887">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="66d20-888">`CreateDefaultBuilder` ruft `Configure(context.Configuration.GetSection("Kestrel"))` standardmäßig zum Laden der Kestrel-Konfiguration auf.</span><span class="sxs-lookup"><span data-stu-id="66d20-888">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="66d20-889">Ein Standardkonfigurationsschema für HTTPS-App-Einstellungen ist für Kestrel verfügbar.</span><span class="sxs-lookup"><span data-stu-id="66d20-889">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="66d20-890">Konfigurieren Sie mehrere Endpunkte, einschließlich der zu verwendenden URLs und Zertifikate aus einer Datei auf dem Datenträger oder einem Zertifikatspeicher.</span><span class="sxs-lookup"><span data-stu-id="66d20-890">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="66d20-891">Die Vorgehensweise im folgenden *appsettings.json*-Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66d20-891">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="66d20-892">Legen Sie **AllowInvalid** auf `true` fest, um die Verwendung von ungültigen Zertifikaten zu erlauben (z.B. selbstsignierte Zertifikate).</span><span class="sxs-lookup"><span data-stu-id="66d20-892">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="66d20-893">Jeder HTTPS-Endpunkt, der kein Zertifikat angibt (im folgenden Beispiel der Endpunkt **HttpsDefaultCert**), greift auf das unter **Zertifikate**>**Standard** festgelegte Zertifikat oder das Entwicklungszertifikat zurück.</span><span class="sxs-lookup"><span data-stu-id="66d20-893">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="66d20-894">Alternativ zur Verwendung von **Pfad** und **Kennwort** für alle Zertifikatknoten können Sie das Zertifikat mithilfe von Zertifikatspeicherfeldern angeben.</span><span class="sxs-lookup"><span data-stu-id="66d20-894">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="66d20-895">Das Zertifikat unter **Zertifikate** > **Standard** kann beispielweise wie folgt angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="66d20-895">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="66d20-896">Schema-Hinweise:</span><span class="sxs-lookup"><span data-stu-id="66d20-896">Schema notes:</span></span>

* <span data-ttu-id="66d20-897">Bei den Namen der Endpunkte wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="66d20-897">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="66d20-898">Zum Beispiel sind `HTTPS` und `Https` gültig.</span><span class="sxs-lookup"><span data-stu-id="66d20-898">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="66d20-899">Der Parameter `Url` ist für jeden Endpunkt erforderlich.</span><span class="sxs-lookup"><span data-stu-id="66d20-899">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="66d20-900">Das Format für diesen Parameter ist identisch mit dem allgemeinen Konfigurationsparameter `Urls`, mit der Ausnahme, dass er auf einen einzelnen Wert begrenzt ist.</span><span class="sxs-lookup"><span data-stu-id="66d20-900">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="66d20-901">Diese Endpunkte ersetzen die in der allgemeinen `Urls`-Konfiguration festgelegten Endpunkte, anstatt zu ihnen hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="66d20-901">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="66d20-902">Endpunkte, die über `Listen` in Code definiert werden, werden den im Konfigurationsabschnitt definierten Endpunkten hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="66d20-902">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="66d20-903">Der `Certificate`-Abschnitt ist optional.</span><span class="sxs-lookup"><span data-stu-id="66d20-903">The `Certificate` section is optional.</span></span> <span data-ttu-id="66d20-904">Wenn der `Certificate`-Abschnitt nicht angegeben ist, werden die in früheren Szenarios definierten Standardwerte verwendet.</span><span class="sxs-lookup"><span data-stu-id="66d20-904">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="66d20-905">Wenn keine Standardwerte verfügbar sind, löst der Server eine Ausnahme aus und startet nicht.</span><span class="sxs-lookup"><span data-stu-id="66d20-905">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="66d20-906">Der `Certificate`-Abschnitt unterstützt die Zertifikate **Pfad**&ndash;**Kennwort** und **Subject**&ndash;**Store** (Antragsteller–Speicher).</span><span class="sxs-lookup"><span data-stu-id="66d20-906">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="66d20-907">Auf diese Weise kann eine beliebige Anzahl von Endpunkten definiert werden, solange Sie keine Portkonflikte verursachen.</span><span class="sxs-lookup"><span data-stu-id="66d20-907">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="66d20-908">`options.Configure(context.Configuration.GetSection("{SECTION}"))` gibt `KestrelConfigurationLoader` mit der Methode `.Endpoint(string name, listenOptions => { })` zurück, die dazu verwendet werden kann, die Einstellungen eines Endpunkts zu ergänzen:</span><span class="sxs-lookup"><span data-stu-id="66d20-908">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="66d20-909">Auf `KestrelServerOptions.ConfigurationLoader` kann direkt zugegriffen werden, um die Iteration auf dem vorhandenen Ladeprogramm fortzusetzen, etwa auf dem von <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> bereitgestellten Ladeprogramm.</span><span class="sxs-lookup"><span data-stu-id="66d20-909">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="66d20-910">Der Konfigurationsabschnitt ist für jeden Endpunkt in den Optionen der Methode `Endpoint` verfügbar, sodass benutzerdefinierte Einstellungen gelesen werden können.</span><span class="sxs-lookup"><span data-stu-id="66d20-910">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="66d20-911">Mehrere Konfigurationen können durch erneutes Aufrufen von `options.Configure(context.Configuration.GetSection("{SECTION}"))` mit einem anderen Abschnitt geladen werden.</span><span class="sxs-lookup"><span data-stu-id="66d20-911">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="66d20-912">Sofern `Load` nicht in einer vorherigen Instanz explizit aufgerufen wird, wird nur die letzte Konfiguration verwendet.</span><span class="sxs-lookup"><span data-stu-id="66d20-912">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="66d20-913">Das Metapaket ruft `Load` nicht auf, sodass der Abschnitt mit der Standardkonfiguration ersetzt werden kann.</span><span class="sxs-lookup"><span data-stu-id="66d20-913">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="66d20-914">`KestrelConfigurationLoader` spiegelt die API-Familie `Listen` von `KestrelServerOptions` als `Endpoint`-Überladungen, weshalb Code und Konfigurationsendpunkte am selben Ort konfiguriert werden können.</span><span class="sxs-lookup"><span data-stu-id="66d20-914">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="66d20-915">Diese Überladungen verwenden keine Namen und nutzen nur Standardeinstellungen aus der Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="66d20-915">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="66d20-916">*Ändern des Standards im Code*</span><span class="sxs-lookup"><span data-stu-id="66d20-916">*Change the defaults in code*</span></span>

<span data-ttu-id="66d20-917">`ConfigureEndpointDefaults` und `ConfigureHttpsDefaults` können zum Ändern der Standardeinstellungen für `ListenOptions` und `HttpsConnectionAdapterOptions` verwendet werden, einschließlich der Standardzertifikate, die im vorherigen Szenario festgelegt wurden.</span><span class="sxs-lookup"><span data-stu-id="66d20-917">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="66d20-918">`ConfigureEndpointDefaults` und `ConfigureHttpsDefaults` sollten aufgerufen werden, bevor Endpunkte konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="66d20-918">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="66d20-919">*Kestrel-Unterstützung für SNI*</span><span class="sxs-lookup"><span data-stu-id="66d20-919">*Kestrel support for SNI*</span></span>

<span data-ttu-id="66d20-920">Die [Servernamensanzeige (SNI)](https://tools.ietf.org/html/rfc6066#section-3) kann zum Hosten mehrerer Domänen auf der gleichen IP-Adresse und dem gleichen Port verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="66d20-920">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="66d20-921">Damit die Servernamensanzeige funktioniert, sendet der Client während des TLS-Handshakes den Hostnamen für die sichere Sitzung an den Server, sodass der Server das richtige Zertifikat bereitstellen kann.</span><span class="sxs-lookup"><span data-stu-id="66d20-921">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="66d20-922">Der Client verwendet das beigestellte Zertifikat für die verschlüsselte Kommunikation mit dem Server während der sicheren Sitzung nach dem TLS-Handshake.</span><span class="sxs-lookup"><span data-stu-id="66d20-922">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="66d20-923">Kestrel unterstützt die Servernamensanzeige über den `ServerCertificateSelector`-Rückruf.</span><span class="sxs-lookup"><span data-stu-id="66d20-923">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="66d20-924">Der Rückruf wird für jede Verbindung einmal aufgerufen, um der App zu ermöglichen, den Hostnamen zu überprüfen und das entsprechende Zertifikat auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="66d20-924">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="66d20-925">Für die Unterstützung der Servernamensanzeige benötigen Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="66d20-925">SNI support requires:</span></span>

* <span data-ttu-id="66d20-926">Wird auf dem Zielframework `netcoreapp2.1` oder höher ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="66d20-926">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="66d20-927">In `net461` oder höher, wird der Rückruf aufgerufen, `name` ist aber immer `null`.</span><span class="sxs-lookup"><span data-stu-id="66d20-927">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="66d20-928">`name` ist auch `null`, wenn der Client den Hostnamenparameter nicht im TLS-Handshake angibt.</span><span class="sxs-lookup"><span data-stu-id="66d20-928">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="66d20-929">Alle Websites werden in derselben Kestrel-Instanz ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="66d20-929">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="66d20-930">Kestrel unterstützt ohne Reverseproxy keine gemeinsame IP-Adresse und keinen gemeinsamen Port für mehrere Instanzen.</span><span class="sxs-lookup"><span data-stu-id="66d20-930">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="66d20-931">Verbindungsprotokollierung</span><span class="sxs-lookup"><span data-stu-id="66d20-931">Connection logging</span></span>

<span data-ttu-id="66d20-932">Rufen Sie <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> auf, um Protokolle auf Debugebene für die Kommunikation auf Byteebene für eine Verbindung auszugeben.</span><span class="sxs-lookup"><span data-stu-id="66d20-932">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="66d20-933">Die Verbindungsprotokollierung ist beim Beheben von Problemen bei der Low-Level-Kommunikation hilfreich, wie z. B. bei der TLS-Verschlüsselung und bei Proxys.</span><span class="sxs-lookup"><span data-stu-id="66d20-933">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="66d20-934">Wenn `UseConnectionLogging` vor `UseHttps` platziert wird, wird der verschlüsselte Datenverkehr protokolliert.</span><span class="sxs-lookup"><span data-stu-id="66d20-934">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="66d20-935">Wenn `UseConnectionLogging` nach `UseHttps` platziert wird, wird der entschlüsselte Datenverkehr protokolliert.</span><span class="sxs-lookup"><span data-stu-id="66d20-935">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="66d20-936">Binden an einen TCP-Socket</span><span class="sxs-lookup"><span data-stu-id="66d20-936">Bind to a TCP socket</span></span>

<span data-ttu-id="66d20-937">Die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>-Methode wird an ein TCP-Socket gebunden, und ein Lambdaausdruck einer Option lässt die Konfiguration des X.509-Zertifikats zu:</span><span class="sxs-lookup"><span data-stu-id="66d20-937">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

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

<span data-ttu-id="66d20-938">Im Beispiel wird HTTPS für einen Endpunkt mit <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="66d20-938">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="66d20-939">Verwenden Sie die gleiche API zum Konfigurieren anderer Kestrel-Einstellungen für bestimmte Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="66d20-939">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="66d20-940">Binden an einen Unix-Socket</span><span class="sxs-lookup"><span data-stu-id="66d20-940">Bind to a Unix socket</span></span>

<span data-ttu-id="66d20-941">Wie in diesem Beispiel dargestellt, lauschen Sie an einem Unix-Socket mit <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>, um eine verbesserte Leistung mit Nginx zu erzielen:</span><span class="sxs-lookup"><span data-stu-id="66d20-941">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

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

### <a name="port-0"></a><span data-ttu-id="66d20-942">Port 0</span><span class="sxs-lookup"><span data-stu-id="66d20-942">Port 0</span></span>

<span data-ttu-id="66d20-943">Wenn die Portnummer `0` angegeben wird, wird Kestrel dynamisch an einen verfügbaren Port gebunden.</span><span class="sxs-lookup"><span data-stu-id="66d20-943">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="66d20-944">Im folgenden Beispiel wird veranschaulicht, wie bestimmt werden kann, für welchen Port Kestrel zur Laufzeit eine Bindung erstellt hat:</span><span class="sxs-lookup"><span data-stu-id="66d20-944">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="66d20-945">Wenn die App ausgeführt wird, gibt das Ausgabefenster der Konsole den dynamischen Port an, über den die App erreicht werden kann:</span><span class="sxs-lookup"><span data-stu-id="66d20-945">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="66d20-946">Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="66d20-946">Limitations</span></span>

<span data-ttu-id="66d20-947">Konfigurieren Sie Endpunkte mithilfe der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="66d20-947">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="66d20-948">Befehlszeilenargument `--urls`</span><span class="sxs-lookup"><span data-stu-id="66d20-948">`--urls` command-line argument</span></span>
* <span data-ttu-id="66d20-949">Hostkonfigurationsschlüssel `urls`</span><span class="sxs-lookup"><span data-stu-id="66d20-949">`urls` host configuration key</span></span>
* <span data-ttu-id="66d20-950">Umgebungsvariable `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="66d20-950">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="66d20-951">Diese Methoden sind nützlich, wenn Ihr Code mit anderen Servern als Kestrel funktionieren soll.</span><span class="sxs-lookup"><span data-stu-id="66d20-951">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="66d20-952">Beachten Sie jedoch die folgenden Einschränkungen:</span><span class="sxs-lookup"><span data-stu-id="66d20-952">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="66d20-953">HTTPS kann nicht mit diesen Ansätzen verwendet werden, außer ein Standardzertifikat wird in der HTTPS-Endpunktkonfiguration angegeben (z.B. wenn Sie wie zuvor in diesem Artikel gezeigt die `KestrelServerOptions`-Konfiguration oder eine Konfigurationsdatei verwenden).</span><span class="sxs-lookup"><span data-stu-id="66d20-953">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="66d20-954">Wenn die Ansätze `Listen` und `UseUrls` gleichzeitig verwendet werden, überschreiben die `Listen`-Endpunkte die `UseUrls`-Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="66d20-954">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="66d20-955">IIS-Endpunktkonfiguration</span><span class="sxs-lookup"><span data-stu-id="66d20-955">IIS endpoint configuration</span></span>

<span data-ttu-id="66d20-956">Bei der Verwendung von IIS werden die URL-Bindungen für IIS-Überschreibungsbindungen durch `Listen` oder `UseUrls` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="66d20-956">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="66d20-957">Weitere Informationen finden Sie im Artikel [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="66d20-957">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="66d20-958">Transportkonfiguration</span><span class="sxs-lookup"><span data-stu-id="66d20-958">Transport configuration</span></span>

<span data-ttu-id="66d20-959">Mit dem Release von ASP.NET Core 2.1 basiert der Standardtransport von Kestrel nicht mehr auf Libuv, sondern auf verwalteten Sockets.</span><span class="sxs-lookup"><span data-stu-id="66d20-959">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="66d20-960">Dies stellt einen Breaking Change für ASP.NET Core 2.0-Apps dar, die auf Version 2.1 upgraden, <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> aufrufen und von einem der folgenden Pakete abhängig sind:</span><span class="sxs-lookup"><span data-stu-id="66d20-960">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="66d20-961">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direkter Paketverweis)</span><span class="sxs-lookup"><span data-stu-id="66d20-961">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="66d20-962">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="66d20-962">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="66d20-963">Für Projekte, die den Einsatz von Libuv erfordern:</span><span class="sxs-lookup"><span data-stu-id="66d20-963">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="66d20-964">Fügen Sie für das [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/)-Paket eine Abhängigkeit auf die Projektdatei der App hinzu:</span><span class="sxs-lookup"><span data-stu-id="66d20-964">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="66d20-965">Rufen Sie <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> auf:</span><span class="sxs-lookup"><span data-stu-id="66d20-965">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="66d20-966">URL-Präfixe</span><span class="sxs-lookup"><span data-stu-id="66d20-966">URL prefixes</span></span>

<span data-ttu-id="66d20-967">Bei der Verwendung von `UseUrls`, dem Befehlszeilenargument `--urls`, dem Hostkonfigurationsschlüssel `urls` oder der Umgebungsvariable `ASPNETCORE_URLS` können die URL-Präfixe in den folgenden Formaten vorliegen.</span><span class="sxs-lookup"><span data-stu-id="66d20-967">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="66d20-968">Nur HTTP-URL-Präfixe sind gültig.</span><span class="sxs-lookup"><span data-stu-id="66d20-968">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="66d20-969">Kestrel unterstützt HTTPS nicht beim Konfigurieren von URL-Bindungen mit `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="66d20-969">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="66d20-970">IPv4-Adresse mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="66d20-970">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="66d20-971">Bei `0.0.0.0` handelt es sich um einen Sonderfall, für den eine Bindung an alle IPv4-Adressen erfolgt.</span><span class="sxs-lookup"><span data-stu-id="66d20-971">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="66d20-972">IPv6-Adresse mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="66d20-972">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="66d20-973">`[::]` stellt das Äquivalent von IPv6 zu `0.0.0.0` für IPv4 dar.</span><span class="sxs-lookup"><span data-stu-id="66d20-973">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="66d20-974">Hostname mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="66d20-974">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="66d20-975">Hostnamen, `*` und `+` sind nicht spezifisch.</span><span class="sxs-lookup"><span data-stu-id="66d20-975">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="66d20-976">Alle Elemente, die nicht als gültige IP-Adresse oder `localhost` erkannt werden, werden an alle IPv4- und IPv6-IP-Adressen gebunden.</span><span class="sxs-lookup"><span data-stu-id="66d20-976">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="66d20-977">Verwenden Sie [HTTP.sys](xref:fundamentals/servers/httpsys) oder einen Reverseproxyserver zum Binden verschiedener Hostnamen an verschiedene ASP.NET Core-Apps auf demselben Port, z.B. IIS, Nginx oder Apache.</span><span class="sxs-lookup"><span data-stu-id="66d20-977">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="66d20-978">Das Hosting in einer Reverseproxykonfiguration erfordert [Hostfilterung](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="66d20-978">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="66d20-979">`localhost`-Hostname mit Portnummer oder Loopback-IP mit Portnummer</span><span class="sxs-lookup"><span data-stu-id="66d20-979">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="66d20-980">Wenn `localhost` angegeben ist, versucht Kestrel, eine Bindung zu IPv4- und IPv6-Loopback-Schnittstellen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="66d20-980">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="66d20-981">Wenn der erforderliche Port von einem anderen Dienst auf einer der Loopback-Schnittstellen verwendet wird, tritt beim Starten von Kestrel ein Fehler auf.</span><span class="sxs-lookup"><span data-stu-id="66d20-981">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="66d20-982">Wenn eine der Loopback-Schnittstellen aus anderen Gründen nicht verfügbar ist (meistens durch die fehlende Unterstützung von IPv6), protokolliert Kestrel eine Warnung.</span><span class="sxs-lookup"><span data-stu-id="66d20-982">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="66d20-983">Host-Filterung</span><span class="sxs-lookup"><span data-stu-id="66d20-983">Host filtering</span></span>

<span data-ttu-id="66d20-984">Obwohl Kestrel die Konfiguration basierend auf Präfixe wie `http://example.com:5000` unterstützt, ignoriert Kestrel den Hostnamen weitgehend.</span><span class="sxs-lookup"><span data-stu-id="66d20-984">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="66d20-985">Der Host `localhost` ist ein Sonderfall, der für die Bindung an Loopback-Adressen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="66d20-985">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="66d20-986">Jeder Host, der keine explizite IP-Adresse ist, wird an alle öffentlichen IP-Adressen gebunden.</span><span class="sxs-lookup"><span data-stu-id="66d20-986">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="66d20-987">`Host`-Header werden nicht überprüft.</span><span class="sxs-lookup"><span data-stu-id="66d20-987">`Host` headers aren't validated.</span></span>

<span data-ttu-id="66d20-988">Verwenden Sie Middleware zum Filtern von Hosts, um dieses Problem zu umgehen.</span><span class="sxs-lookup"><span data-stu-id="66d20-988">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="66d20-989">Middleware zum Filtern von Hosts wird im Paket [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) bereitgestellt, das im [Metapaket Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) enthalten ist (ASP.NET Core 2.1 oder 2.2).</span><span class="sxs-lookup"><span data-stu-id="66d20-989">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="66d20-990">Die Middleware wird von <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> hinzugefügt, wodurch <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="66d20-990">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="66d20-991">Die Middleware zum Filtern von Hosts ist standardmäßig deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="66d20-991">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="66d20-992">Wenn Sie die Middleware aktivieren möchten, definieren Sie einen `AllowedHosts`-Schlüssel in *appsettings.json*/*appsettings.\<Umgebungsname>.json*.</span><span class="sxs-lookup"><span data-stu-id="66d20-992">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="66d20-993">Der Wert ist eine durch Semikolons getrennte Liste von Hostnamen ohne Portnummern:</span><span class="sxs-lookup"><span data-stu-id="66d20-993">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="66d20-994">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="66d20-994">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="66d20-995">[Middleware für weitergeleitete Header](xref:host-and-deploy/proxy-load-balancer) hat auch eine <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>-Option.</span><span class="sxs-lookup"><span data-stu-id="66d20-995">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="66d20-996">Middleware für weitergeleitete Header und Middleware zum Filtern von Hosts besitzen ähnliche Funktionen für unterschiedliche Szenarios.</span><span class="sxs-lookup"><span data-stu-id="66d20-996">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="66d20-997">Legen Sie `AllowedHosts` mit Middleware für weitergeleitete Header fest, wenn der `Host`-Header beim Weiterleiten von Anforderungen mit einem Reverseproxyserver oder einem Lastenausgleichsmodul nicht beibehalten wird.</span><span class="sxs-lookup"><span data-stu-id="66d20-997">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="66d20-998">Legen Sie `AllowedHosts` mit Middleware zum Filtern von Hosts fest, wenn Kestrel als öffentlicher Edgeserver verwendet oder der `Host`-Header direkt weitergeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="66d20-998">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="66d20-999">Weitere Informationen zu Middleware für weitergeleitete Header finden Sie unter <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="66d20-999">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="66d20-1000">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="66d20-1000">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="66d20-1001">RFC 7230: Message Syntax and Routing (Abschnitt 5.4: Host)</span><span class="sxs-lookup"><span data-stu-id="66d20-1001">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)

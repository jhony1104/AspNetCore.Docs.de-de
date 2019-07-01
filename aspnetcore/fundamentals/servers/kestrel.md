---
title: Implementierung des Webservers Kestrel in ASP.NET Core
author: guardrex
description: Einführung in Kestrel, dem plattformübergreifenden Webserver für ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 06/24/2019
uid: fundamentals/servers/kestrel
ms.openlocfilehash: 7d66d04ec3b91d0ab1a67cacb2030cf52054454b
ms.sourcegitcommit: 763af2cbdab0da62d1f1cfef4bcf787f251dfb5c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/26/2019
ms.locfileid: "67394729"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a>Implementierung des Webservers Kestrel in ASP.NET Core

Von [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher) und [Stephen Halter](https://twitter.com/halter73)

Einführung in Kestrel, dem plattformübergreifenden [Webserver für ASP.NET Core](xref:fundamentals/servers/index). Kestrel ist der Webserver, der standardmäßig in ASP.NET Core-Projektvorlagen enthalten ist.

Kestrel unterstützt die folgenden Szenarios:

::: moniker range=">= aspnetcore-2.2"

* HTTPS
* Nicht transparente Upgrades, die zum Aktivieren von [WebSockets](https://github.com/aspnet/websockets) verwendet werden
* Unix-Sockets für eine hohe Leistung im Hintergrund von Nginx
* HTTP/2 (außer unter macOS&dagger;)

&dagger;HTTP/2 wird unter macOS in einem zukünftigen Release unterstützt.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* HTTPS
* Nicht transparente Upgrades, die zum Aktivieren von [WebSockets](https://github.com/aspnet/websockets) verwendet werden
* Unix-Sockets für eine hohe Leistung im Hintergrund von Nginx

::: moniker-end

Kestrel wird auf allen Plattformen und für alle Versionen unterstützt, die .NET Core unterstützt.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

::: moniker range=">= aspnetcore-2.2"

## <a name="http2-support"></a>HTTP/2-Unterstützung

[HTTP/2](https://httpwg.org/specs/rfc7540.html) ist für ASP.NET Core-Apps verfügbar, wenn die folgenden Basisanforderungen erfüllt sind:

* Betriebssystem&dagger;
  * Windows Server 2016/Windows 10 oder höher&Dagger;
  * Linux mit OpenSSL 1.0.2 oder höher (z.B. Ubuntu 16.04 oder höher)
* Zielframework: .NET Core 2.2 oder höher
* [ALPN](https://tools.ietf.org/html/rfc7301#section-3)-Verbindung (Application-Layer Protocol Negotiation)
* TLS 1.2-Verbindung oder höher

&dagger;HTTP/2 wird unter macOS in einem zukünftigen Release unterstützt.
&Dagger;Kestrel bietet eingeschränkte Unterstützung für HTTP/2 unter Windows Server 2012 R2 und Windows 8.1. Die Unterstützung ist eingeschränkt, weil die Liste der unterstützten TLS-Verschlüsselungssammlungen unter diesen Betriebssystemen begrenzt ist. Zum Sichern von TLS-Verbindungen ist möglicherweise ein durch einen Elliptic Curve Digital Signature Algorithm (ECDSA) generiertes Zertifikat erforderlich.

Wenn eine HTTP/2-Verbindung hergestellt wurde, meldet [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/2`.

HTTP/2 ist standardmäßig deaktiviert. Weitere Informationen zur Konfiguration finden Sie in den Abschnitten [Kestrel-Optionen](#kestrel-options) und [ListenOptions.Protocols](#listenoptionsprotocols).

::: moniker-end

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a>Verwenden von Kestrel mit einem Reverseproxy

Kestrel kann eigenständig oder mit einem *Reverseproxyserver* wie z.B. [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](http://nginx.org) oder [Apache](https://httpd.apache.org/) verwendet werden. Ein Reverseproxyserver empfängt HTTP-Anforderungen aus dem Netzwerk und leitet diese an Kestrel weiter.

Kestrel bei Verwendung als Webserver mit direkter Internetverbindung:

![Kestrel kommuniziert direkt und ohne Reverseproxyserver mit dem Internet](kestrel/_static/kestrel-to-internet2.png)

Kestrel bei Verwendung in einer Reverseproxykonfiguration:

![Kestrel kommuniziert indirekt mit dem Internet über einen Reverseproxyserver wie IIS, Nginx oder Apache](kestrel/_static/kestrel-to-internet.png)

Jede der beiden Konfigurationen – mit oder ohne Reverseproxyserver – ist eine unterstützte Hostingkonfiguration für mit ASP.NET Core 2.1 oder höher erstellte Apps, die Anforderungen aus dem Internet empfangen.

Bei Verwendung als Edgeserver ohne Reverseproxyserver unterstützt Kestrel die gemeinsame Nutzung der gleichen IP-Adresse und des gleichen Ports durch mehrere Prozesse nicht. Wenn Kestrel für das Lauschen an einem Port konfiguriert ist, verarbeitet Kestrel den gesamten Datenverkehr für diesen Port unabhängig von den `Host`-Headern der Anforderungen. Ein Reverseproxy, der Ports freigeben kann, kann Anforderungen an Kestrel über eine eindeutige IP und einen eindeutigen Port weiterleiten.

Auch wenn kein Reverseproxyserver erforderlich ist, kann die Verwendung eines solchen empfehlenswert sein.

Für einen Reverseproxy gilt Folgendes:

* Er kann die verfügbar gemachten öffentlichen Oberflächen der von ihm gehosteten Apps einschränken.
* Er stellt eine zusätzliche Ebene für Konfiguration und Schutz bereit.
* Er lässt sich besser in die vorhandene Infrastruktur integrieren.
* Er vereinfacht die Konfiguration von Lastenausgleich und sicherer Kommunikation (HTTPS). Nur der Reverseproxyserver erfordert ein X.509-Zertifikat., und dieser Server kann über einfaches HTTP mit Ihren App-Servern im internen Netzwerk kommunizieren.

> [!WARNING]
> Das Hosting in einer Reverseproxykonfiguration erfordert [Hostfilterung](#host-filtering).

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a>Verwenden von Kestrel in ASP.NET Core-Apps

Das Paket [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) ist im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 oder höher) enthalten.

ASP.NET Core-Projektvorlagen verwenden Kestrel standardmäßig. Der Vorlagencode in *Program.cs* ruft <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> auf, wodurch <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> im Hintergrund aufgerufen wird.

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

::: moniker range=">= aspnetcore-2.2"

Um zusätzliche Konfiguration nach dem Aufruf von `CreateDefaultBuilder` bereitzustellen, verwenden Sie `ConfigureKestrel`:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            // Set properties and call methods on options
        });
```

Wenn die App `CreateDefaultBuilder` nicht aufruft, um den Host einzurichten, rufen Sie <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **auf**, bevor Sie `ConfigureKestrel` aufrufen:

```csharp
public static void Main(string[] args)
{
    var host = new WebHostBuilder()
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseKestrel()
        .UseIISIntegration()
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            // Set properties and call methods on options
        })
        .Build();

    host.Run();
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Um zusätzliche Konfiguration nach dem Aufruf von `CreateDefaultBuilder` bereitzustellen, rufen Sie <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> auf:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            // Set properties and call methods on options
        });
```

::: moniker-end

## <a name="kestrel-options"></a>Kestrel-Optionen

Der Kestrel-Webserver verfügt über einschränkende Konfigurationsoptionen, die besonders nützlich bei Bereitstellungen mit Internetzugriff sind.

Legen Sie Einschränkungen für die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits>-Eigenschaft der <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>-Klasse fest. Die `Limits`-Eigenschaft enthält eine Instanz der <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>-Klasse.

In den folgenden Beispielen wird der <xref:Microsoft.AspNetCore.Server.Kestrel.Core>-Namespace verwendet:

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

### <a name="keep-alive-timeout"></a>Keep-Alive-Timeout

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

Ruft das [Keep-Alive-Timeout](https://tools.ietf.org/html/rfc7230#section-6.5) ab oder legt es fest. Standardwert: 2 Minuten.

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

::: moniker-end

### <a name="maximum-client-connections"></a>Maximale Anzahl der Clientverbindungen

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

Die maximale Anzahl von gleichzeitig geöffneten TCP-Verbindungen kann mithilfe von folgendem Code für die gesamte App festgelegt werden:

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.Limits.MaxConcurrentConnections = 100;
        });
```

::: moniker-end

Es gibt einen separaten Grenzwert für Verbindungen, die von HTTP oder HTTPS auf ein anderes Protokoll aktualisiert wurden (z.B. auf eine WebSockets-Anforderung). Nachdem eine Verbindung aktualisiert wurde, zählt diese nicht mehr für den `MaxConcurrentConnections`-Grenzwert.

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

::: moniker-end

Die maximale Anzahl von Verbindungen ist standardmäßig nicht begrenzt (NULL).

### <a name="maximum-request-body-size"></a>Maximale Größe des Anforderungstexts

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

Die maximale Größe des Anforderungstexts beträgt standardmäßig 30.000.000 Byte, also ungefähr 28,6 MB.

Die empfohlene Methode zur Außerkraftsetzung des Grenzwerts in einer ASP.NET Core-MVC-App besteht im Verwenden des <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>-Attributs in einer Aktionsmethode:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Im folgenden Beispiel wird veranschaulicht, wie die Einschränkungen auf jeder Anforderung für die App konfiguriert werden:

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

Sie können diese Einstellung für eine bestimmte Anforderung in Middleware außer Kraft setzen:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

::: moniker-end

Eine Ausnahme wird ausgelöst, wenn Sie versuchen, den Grenzwert einer Anforderung zu konfigurieren, nachdem die App bereits mit dem Lesen der Anforderung begonnen hat. Es gibt eine `IsReadOnly`-Eigenschaft, die angibt, wenn sich die `MaxRequestBodySize`-Eigenschaft im schreibgeschützten Zustand befindet, also wenn der Grenzwert nicht mehr konfiguriert werden kann.

Wenn eine App [prozessextern](xref:host-and-deploy/iis/index#out-of-process-hosting-model) hinter dem [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) ausgeführt wird, ist das Größenlimit von Kestrel für Anforderungstext deaktiviert, weil IIS dieses Limit bereits festlegt.

### <a name="minimum-request-body-data-rate"></a>Minimale Datenrate des Anforderungstexts

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

Kestrel überprüft sekündlich, ob Daten mit der angegebenen Rate in Bytes/Sekunde eingehen. Wenn die Rate den mindestens erforderlichen Wert unterschreitet, wird für die Verbindung wegen Timeout getrennt. Bei der Toleranzperiode handelt es sich um die Zeitspanne, die Kestrel dem Client gewährt, um die Senderate auf den mindestens erforderlichen Wert zu erhöhen. Die Rate wird währenddessen nicht überprüft. Diese Toleranzperiode beugt dem Trennen von Verbindungen vor, die Daten aufgrund eines langsamen TCP-Starts anfänglich mit einer niedrigen Rate senden.

Die mindestens erforderliche Rate beträgt standardmäßig 240 Bytes/Sekunde mit einer Toleranzperiode von 5 Sekunden.

Für die Antwort gilt ebenfalls eine mindestens erforderliche Rate. Der Code zum Festlegen des Grenzwerts für Anforderung und Antwort ist abgesehen von `RequestBody` oder `Response` in den Namen von Eigenschaften und Schnittstelle identisch.

In diesem Beispiel wird veranschaulicht, wie Sie die mindestens erforderlichen Datenraten in *Program.cs* konfigurieren:

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.Limits.MinRequestBodyDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
            options.Limits.MinResponseDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
        });
```

::: moniker-end

Sie können das minimale Ratenlimit pro Anforderung in Middleware außer Kraft setzen:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

::: moniker range=">= aspnetcore-3.0"

Das <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> aus dem vorherigen Beispiel ist in `HttpContext.Features` für HTTP/2-Anforderungen nicht vorhanden, weil die Änderung von Ratenlimits für jede Anforderung aufgrund der Unterstützung für Anforderungsmultiplexing von HTTP/2 von diesem Protokoll unterstützt wird. <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> ist jedoch in `HttpContext.Features` für HTTP/2-Anforderungen noch vorhanden, weil das Leseratenlimit weiterhin für jede Anforderung *vollständig deaktiviert* werden kann, indem `IHttpMinRequestBodyDataRateFeature.MinDataRate` auch für eine HTTP/2-Anforderung auf `null` festgelegt wird. Der Versuch, `IHttpMinRequestBodyDataRateFeature.MinDataRate` zu lesen oder auf einen anderen Wert als `null` festzulegen, führt dazu, dass bei einer HTTP/2-Anforderung eine `NotSupportedException` ausgelöst wird.

Serverweite Ratenlimits, die über `KestrelServerOptions.Limits` konfiguriert sind, gelten auch für HTTP/1.x- und HTTP/2-Verbindungen.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Keines dieser Ratenfeatures, auf die im vorherigen Beispiel verwiesen wird, ist in `HttpContext.Features` für HTTP/2-Anforderungen vorhanden, da die Änderung von Ratenlimits für jede Anforderung aufgrund der Unterstützung für Anforderungsmultiplexing des Protokolls nicht für HTTP/2 unterstützt wird. Serverweite Ratenlimits, die über `KestrelServerOptions.Limits` konfiguriert sind, gelten auch für HTTP/1.x- und HTTP/2-Verbindungen.

::: moniker-end

### <a name="request-headers-timeout"></a>Timeout für Anforderungsheader

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

Ruft die maximale Zeitspanne ab, während der der Server Anforderungsheader empfängt, oder legt diese fest. Der Standardwert beträgt 30 Sekunden.

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="maximum-streams-per-connection"></a>Maximale Datenströme pro Verbindung

`Http2.MaxStreamsPerConnection` schränkt die Anzahl gleichzeitiger Anforderungsdatenströme pro HTTP/2-Verbindung ein. Überschüssige Datenströme werden zurückgewiesen.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

Der Standardwert ist 100.

### <a name="header-table-size"></a>Größe der Headertabelle

Der HPACK-Decoder dekomprimiert HTTP-Header für HTTP/2-Verbindungen. `Http2.HeaderTableSize` schränkt die Größe der Headerkomprimierungstabelle ein, die der HPACK-Decoder verwendet. Der Wert wird in Oktetten bereitgestellt und muss größer als null (0) sein.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.Http2.HeaderTableSize = 4096;
        });
```

Der Standardwert ist 4096.

### <a name="maximum-frame-size"></a>Maximale Framegröße

`Http2.MaxFrameSize` gibt die maximale Größe der zu empfangenden HTTP/2-Verbindungsframenutzlast an. Der Wert wird in Oktetten bereitgestellt und muss zwischen 2^14 (16.384) und 2^24-1 (16.777.215) liegen.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.Http2.MaxFrameSize = 16384;
        });
```

Der Standardwert ist 2^14 (16.384).

### <a name="maximum-request-header-size"></a>Maximale Größe des Anforderungsheaders

`Http2.MaxRequestHeaderFieldSize` gibt die maximal zulässige Größe in Oktetten der Anforderungsheaderwerte an. Dieser Grenzwert gilt zusammen für den Namen und Wert in komprimierten und nicht komprimierten Darstellungen. Der Wert muss größer als 0 (null) sein.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

Der Standardwert ist 8.192.

### <a name="initial-connection-window-size"></a>Anfangsfenstergröße der Verbindung

`Http2.InitialConnectionWindowSize` gibt die maximalen Anforderungstextdaten in Byte an, die der Server auf einmal für alle Anforderungen (Streams) pro Verbindung aggregiert. Anforderungen werden auch durch `Http2.InitialStreamWindowSize` beschränkt. Der Wert muss größer als oder gleich 65.535 und kleiner als 2^31 (2.147.483.648) sein.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

Der Standardwert ist 128 KB (131.072).

### <a name="initial-stream-window-size"></a>Anfangsfenstergröße des Streams

`Http2.InitialStreamWindowSize` gibt die maximalen Anforderungstextdaten in Byte an, die der Server auf einmal pro Anforderung (Stream) puffert. Anforderungen werden auch durch `Http2.InitialStreamWindowSize` beschränkt. Der Wert muss größer als oder gleich 65.535 und kleiner als 2^31 (2.147.483.648) sein.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

Der Standardwert ist 96 KB (98.304).

::: moniker-end

### <a name="synchronous-io"></a>Synchrone E/A-Vorgänge

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> steuert, ob für Anforderung und Antwort synchrone E/A-Vorgänge zulässig sind. Der Standardwert ist `false`sein.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> steuert, ob für Anforderung und Antwort synchrone E/A-Vorgänge zulässig sind. Der Standardwert ist `true`.

::: moniker-end

> [!WARNING]
> Sehr viele blockierende synchrone E/A-Vorgänge können zu einem Ressourcenmangel im Threadpool führen, wodurch die App nicht mehr reagiert. Aktivieren Sie `AllowSynchronousIO` nur bei Verwendung einer Bibliothek, die asynchrone E/A-Vorgänge nicht unterstützt.

::: moniker range=">= aspnetcore-2.2"

Das folgende Beispiel aktiviert synchrone E/A-Vorgänge:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Das folgende Beispiel deaktiviert synchrone E/A-Vorgänge:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.AllowSynchronousIO = false;
        });
```

::: moniker-end

Weitere Informationen zu anderen Kestrel-Optionen und -Einschränkungen finden Sie unter:

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a>Endpunktkonfiguration

Standardmäßig wird ASP.NET Core an Folgendes gebunden:

* `http://localhost:5000`
* `https://localhost:5001` (wenn ein lokales Entwicklungszertifikat vorhanden ist)

Verwenden Sie Folgendes zum Angeben der URLs:

* Die Umgebungsvariable `ASPNETCORE_URLS`
* Das Befehlszeilenargument `--urls`
* Den Hostkonfigurationsschlüssel `urls`
* Die Erweiterungsmethode `UseUrls`

Der Wert, der mit diesen Ansätzen angegeben wird, kann mindestens ein HTTP- oder HTTPS-Endpunkt sein (HTTPS wenn ein Standardzertifikat verfügbar ist). Konfigurieren Sie den Wert als eine durch Semikolons getrennte Liste (z.B. `"Urls": "http://localhost:8000;http://localhost:8001"`).

Weitere Informationen zu diesen Ansätzen finden Sie unter [Server-URLs](xref:fundamentals/host/web-host#server-urls) und [Außerkraftsetzen der Konfiguration](xref:fundamentals/host/web-host#override-configuration).

Ein Entwicklungszertifikat wird erstellt:

* Wenn das [.NET Core SDK](/dotnet/core/sdk) installiert wird.
* Das [dev-cert-Tool](xref:aspnetcore-2.1#https) wird zum Erstellen eines Zertifikats verwendet.

Einige Browser erfordern, dass Sie dem Browser die explizite Berechtigung erteilen, dem lokalen Entwicklungszertifikat zu vertrauen.

ASP.NET Core 2.1 und spätere Projektvorlagen konfigurieren Apps, damit sie standardmäßig auf HTTPS ausgeführt werden und die [HTTPS-Umleitung und HSTS-Unterstützung](xref:security/enforcing-ssl) enthalten.

Rufen Sie die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>- oder <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>-Methode unter <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> auf, um URL-Präfixe und Ports für Kestrel zu konfigurieren.

`UseUrls`, das Befehlszeilenargument `--urls`, der Hostkonfigurationsschlüssel `urls` und die Umgebungsvariable `ASPNETCORE_URLS` funktionieren ebenfalls, verfügen jedoch über Einschränkungen, die im Verlauf dieses Abschnitts erläutert werden (Ein Standardzertifikat muss für die HTTPS-Endpunktkonfiguration verfügbar sein).

`KestrelServerOptions`-Konfiguration für ASP.NET Core 2.1 oder höher:

### <a name="configureendpointdefaultsactionltlistenoptionsgt"></a>ConfigureEndpointDefaults(Action&lt;ListenOptions&gt;)

Gibt die Konfiguration von `Action` zum Ausführen von jedem angegebenen Endpunkt an. Mehrmalige Aufrufe von `ConfigureEndpointDefaults` ersetzen vorherige Instanzen von `Action` mit der zuletzt angegebenen `Action`.

::: moniker range=">= aspnetcore-3.0"

```csharp
Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.ConfigureKestrel(serverOptions =>
        {
            serverOptions.ConfigureEndpointDefaults(configureOptions =>
            {
                configureOptions.NoDelay = true;
            });
        });
        webBuilder.UseStartup<Startup>();
    });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.ConfigureEndpointDefaults(configureOptions =>
            {
                configureOptions.NoDelay = true;
            });
        });
```

::: moniker-end

### <a name="configurehttpsdefaultsactionlthttpsconnectionadapteroptionsgt"></a>ConfigureHttpsDefaults(Action&lt;HttpsConnectionAdapterOptions&gt;)

Gibt die Konfiguration von `Action` zum Ausführen von jedem HTTPS-Endpunkt an. Mehrmalige Aufrufe von `ConfigureHttpsDefaults` ersetzen vorherige Instanzen von `Action` mit der zuletzt angegebenen `Action`.

::: moniker range=">= aspnetcore-3.0"

```csharp
Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.ConfigureKestrel(serverOptions =>
        {
            serverOptions.ConfigureHttpsDefaults(options =>
            {
                // certificate is an X509Certificate2
                options.ServerCertificate = certificate;
            });
        });
        webBuilder.UseStartup<Startup>();
    });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.ConfigureHttpsDefaults(httpsOptions =>
            {
                // certificate is an X509Certificate2
                httpsOptions.ServerCertificate = certificate;
            });
        });
```

::: moniker-end

### <a name="configureiconfiguration"></a>Configure(IConfiguration)

Erstellt ein Konfigurationsladeprogramm für das Einrichten von Kestrel, was <xref:Microsoft.Extensions.Configuration.IConfiguration> als Eingabe erfordert. Die Konfiguration muss auf den Konfigurationsabschnitt für Kestrel festgelegt werden.

### <a name="listenoptionsusehttps"></a>ListenOptions.UseHttps

Konfiguriert Kestrel zur Verwendung von HTTPS.

`ListenOptions.UseHttps`-Erweiterungen:

* `UseHttps`: Konfiguriert Kestrel zur Verwendung von HTTPS mit dem Standardzertifikat. Löst eine Ausnahme aus, wenn kein Standardzertifikat konfiguriert ist.
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

`ListenOptions.UseHttps`-Parameter:

* `filename` entspricht dem Pfad und Dateinamen einer Zertifikatdatei relativ zu dem Verzeichnis, das die Inhaltsdateien der App enthält.
* `password` ist das für den Zugriff auf die X.509-Zertifikatsdaten erforderliche Kennwort.
* `configureOptions` ist eine `Action` zum Konfigurieren von `HttpsConnectionAdapterOptions`. Gibt `ListenOptions` zurück.
* `storeName` ist der Zertifikatspeicher, aus dem das Zertifikat geladen wird.
* `subject` ist der Name des Antragstellers für das Zertifikat.
* `allowInvalid` gibt an, ob ungültige Zertifikate berücksichtigt werden sollten, z.B. selbstsignierte Zertifikate.
* `location` ist der Speicherort, aus dem das Zertifikat geladen wird.
* `serverCertificate` ist das X.509-Zertifikat.

In der Produktion muss HTTPS explizit konfiguriert sein. Zumindest muss ein Standardzertifikat angegeben werden.

Die im Folgenden beschriebenen unterstützten Konfigurationen:

* Keine Konfiguration
* Ersetzen des Standardzertifikats aus der Konfiguration
* Ändern des Standards im Code

*Keine Konfiguration*

Kestrel überwacht `http://localhost:5000` und `https://localhost:5001` (wenn ein Standardzertifikat verfügbar ist).

<a name="configuration"></a>

*Ersetzen des Standardzertifikats aus der Konfiguration*

<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ruft `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` standardmäßig zum Laden der Kestrel-Konfiguration auf. Ein Standardkonfigurationsschema für HTTPS-App-Einstellungen ist für Kestrel verfügbar. Konfigurieren Sie mehrere Endpunkte, einschließlich der zu verwendenden URLs und Zertifikate aus einer Datei auf dem Datenträger oder einem Zertifikatspeicher.

Die Vorgehensweise im folgenden *appsettings.json*-Beispiel:

* Legen Sie **AllowInvalid** auf `true` fest, um die Verwendung von ungültigen Zertifikaten zu erlauben (z.B. selbstsignierte Zertifikate).
* Jeder HTTPS-Endpunkt, der kein Zertifikat angibt (im folgenden Beispiel **HttpsDefaultCert**), greift auf das unter **Zertifikate** > **Standard** festgelegte Zertifikat oder das Entwicklungszertifikat zurück.

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

Alternativ zur Verwendung von **Pfad** und **Kennwort** für alle Zertifikatknoten können Sie das Zertifikat mithilfe von Zertifikatspeicherfeldern angeben. Das Zertifikat unter **Zertifikate** > **Standard** kann beispielweise wie folgt angegeben werden:

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

Schema-Hinweise:

* Bei den Namen der Endpunkte wird die Groß-/Kleinschreibung nicht beachtet. Zum Beispiel sind `HTTPS` und `Https` gültig.
* Der Parameter `Url` ist für jeden Endpunkt erforderlich. Das Format für diesen Parameter ist identisch mit dem allgemeinen Konfigurationsparameter `Urls`, mit der Ausnahme, dass er auf einen einzelnen Wert begrenzt ist.
* Diese Endpunkte ersetzen die in der allgemeinen `Urls`-Konfiguration festgelegten Endpunkte, anstatt zu ihnen hinzuzufügen. Endpunkte, die über `Listen` in Code definiert werden, werden den im Konfigurationsabschnitt definierten Endpunkten hinzugefügt.
* Der `Certificate`-Abschnitt ist optional. Wenn der `Certificate`-Abschnitt nicht angegeben ist, werden die in früheren Szenarios definierten Standardwerte verwendet. Wenn keine Standardwerte verfügbar sind, löst der Server eine Ausnahme aus und startet nicht.
* Der `Certificate`-Abschnitt unterstützt die Zertifikate **Pfad**&ndash;**Kennwort** und **Subject**&ndash;**Store** (Antragsteller–Speicher).
* Auf diese Weise kann eine beliebige Anzahl von Endpunkten definiert werden, solange Sie keine Portkonflikte verursachen.
* `options.Configure(context.Configuration.GetSection("Kestrel"))` gibt `KestrelConfigurationLoader` mit der Methode `.Endpoint(string name, options => { })` zurück, die dazu verwendet werden kann, die Einstellungen eines Endpunkts zu ergänzen:

  ```csharp
  options.Configure(context.Configuration.GetSection("Kestrel"))
      .Endpoint("HTTPS", opt =>
      {
          opt.HttpsOptions.SslProtocols = SslProtocols.Tls12;
      });
  ```

  Außerdem können Sie auch direkt auf `KestrelServerOptions.ConfigurationLoader` zugreifen, um auf einem vorhandenen Ladeprogramm zu iterieren, z.B. dem von <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> bereitgestellten Ladeprogramm.

* Der Konfigurationsabschnitt ist für jeden Endpunkt in den Optionen der Methode `Endpoint` verfügbar, sodass benutzerdefinierte Einstellungen gelesen werden können.
* Mehrere Konfigurationen können durch erneutes Aufrufen von `options.Configure(context.Configuration.GetSection("Kestrel"))` mit einem anderen Abschnitt geladen werden. Sofern `Load` nicht in einer vorherigen Instanz explizit aufgerufen wird, wird nur die letzte Konfiguration verwendet. Das Metapaket ruft `Load` nicht auf, sodass der Abschnitt mit der Standardkonfiguration ersetzt werden kann.
* `KestrelConfigurationLoader` spiegelt die API-Familie `Listen` von `KestrelServerOptions` als `Endpoint`-Überladungen, weshalb Code und Konfigurationsendpunkte am selben Ort konfiguriert werden können. Diese Überladungen verwenden keine Namen und nutzen nur Standardeinstellungen aus der Konfiguration.

*Ändern des Standards im Code*

`ConfigureEndpointDefaults` und `ConfigureHttpsDefaults` können zum Ändern der Standardeinstellungen für `ListenOptions` und `HttpsConnectionAdapterOptions` verwendet werden, einschließlich der Standardzertifikate, die im vorherigen Szenario festgelegt wurden. `ConfigureEndpointDefaults` und `ConfigureHttpsDefaults` sollten aufgerufen werden, bevor Endpunkte konfiguriert werden.

```csharp
options.ConfigureEndpointDefaults(opt =>
{
    opt.NoDelay = true;
});

options.ConfigureHttpsDefaults(httpsOptions =>
{
    httpsOptions.SslProtocols = SslProtocols.Tls12;
});
```

*Kestrel-Unterstützung für SNI*

Die [Servernamensanzeige (SNI)](https://tools.ietf.org/html/rfc6066#section-3) kann zum Hosten mehrerer Domänen auf der gleichen IP-Adresse und dem gleichen Port verwendet werden. Damit die Servernamensanzeige funktioniert, sendet der Client während des TLS-Handshakes den Hostnamen für die sichere Sitzung an den Server, sodass der Server das richtige Zertifikat bereitstellen kann. Der Client verwendet das beigestellte Zertifikat für die verschlüsselte Kommunikation mit dem Server während der sicheren Sitzung nach dem TLS-Handshake.

Kestrel unterstützt die Servernamensanzeige über den `ServerCertificateSelector`-Rückruf. Der Rückruf wird für jede Verbindung einmal aufgerufen, um der App zu ermöglichen, den Hostnamen zu überprüfen und das entsprechende Zertifikat auszuwählen.

Für die Unterstützung der Servernamensanzeige benötigen Sie Folgendes:

* Wird auf dem Zielframework `netcoreapp2.1` ausgeführt. Der Rückruf wird auf `netcoreapp2.0` und `net461` aufgerufen, `name` ist jedoch immer `null`. `name` ist auch `null`, wenn der Client den Hostnamenparameter nicht im TLS-Handshake angibt.
* Alle Websites werden in derselben Kestrel-Instanz ausgeführt. Kestrel unterstützt ohne Reverseproxy keine gemeinsame IP-Adresse und keinen gemeinsamen Port für mehrere Instanzen.

::: moniker range=">= aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.ListenAnyIP(5005, listenOptions =>
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

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, options) =>
        {
            options.ListenAnyIP(5005, listenOptions =>
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

::: moniker-end

### <a name="bind-to-a-tcp-socket"></a>Binden an einen TCP-Socket

Die <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>-Methode wird an ein TCP-Socket gebunden, und ein Lambdaausdruck einer Option lässt die Konfiguration des X.509-Zertifikats zu:

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.Listen(IPAddress.Loopback, 5000);
            options.Listen(IPAddress.Loopback, 5001, listenOptions =>
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
        .UseKestrel(options =>
        {
            options.Listen(IPAddress.Loopback, 5000);
            options.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

::: moniker-end

Im Beispiel wird HTTPS für einen Endpunkt mit <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> konfiguriert. Verwenden Sie die gleiche API zum Konfigurieren anderer Kestrel-Einstellungen für bestimmte Endpunkte.

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a>Binden an einen Unix-Socket

Wie in diesem Beispiel dargestellt, lauschen Sie an einem Unix-Socket mit <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>, um eine verbesserte Leistung mit Nginx zu erzielen:

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.ListenUnixSocket("/tmp/kestrel-test.sock");
            options.ListenUnixSocket("/tmp/kestrel-test.sock", listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testpassword");
            });
        });
```

::: moniker-end

### <a name="port-0"></a>Port 0

Wenn die Portnummer `0` angegeben wird, wird Kestrel dynamisch an einen verfügbaren Port gebunden. Im folgenden Beispiel wird veranschaulicht, wie bestimmt werden kann, für welchen Port Kestrel zur Laufzeit eine Bindung erstellt hat:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

Wenn die App ausgeführt wird, gibt das Ausgabefenster der Konsole den dynamischen Port an, über den die App erreicht werden kann:

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a>Einschränkungen

Konfigurieren Sie Endpunkte mithilfe der folgenden Ansätze:

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* Befehlszeilenargument `--urls`
* Hostkonfigurationsschlüssel `urls`
* Umgebungsvariable `ASPNETCORE_URLS`

Diese Methoden sind nützlich, wenn Ihr Code mit anderen Servern als Kestrel funktionieren soll. Beachten Sie jedoch die folgenden Einschränkungen:

* HTTPS kann nicht mit diesen Ansätzen verwendet werden, außer ein Standardzertifikat wird in der HTTPS-Endpunktkonfiguration angegeben (z.B. wenn Sie wie zuvor in diesem Artikel gezeigt die `KestrelServerOptions`-Konfiguration oder eine Konfigurationsdatei verwenden).
* Wenn die Ansätze `Listen` und `UseUrls` gleichzeitig verwendet werden, überschreiben die `Listen`-Endpunkte die `UseUrls`-Endpunkte.

### <a name="iis-endpoint-configuration"></a>IIS-Endpunktkonfiguration

Bei der Verwendung von IIS werden die URL-Bindungen für IIS-Überschreibungsbindungen durch `Listen` oder `UseUrls` festgelegt. Weitere Informationen finden Sie im Artikel [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module).

::: moniker range=">= aspnetcore-2.2"

### <a name="listenoptionsprotocols"></a>ListenOptions.Protocols

Die `Protocols`-Eigenschaft richtet die HTTP-Protokolle (`HttpProtocols`) ein, die für einen Verbindungsendpunkt oder für den Server aktiviert werden. Weisen Sie der `Protocols`-Eigenschaft einen Wert aus der `HttpProtocols`-Enumeration zu.

| `HttpProtocols`-Enumerationswert | Zulässiges Verbindungsprotokoll |
| -------------------------- | ----------------------------- |
| `Http1`                    | Nur HTTP/1.1. Kann mit oder ohne TLS verwendet werden. |
| `Http2`                    | Nur HTTP/2. Wird in erster Linie mit TLS verwendet. Kann nur ohne TLS verwendet werden, wenn der Client einen [Vorabkenntnis-Modus](https://tools.ietf.org/html/rfc7540#section-3.4) unterstützt. |
| `Http1AndHttp2`            | HTTP/1.1 und HTTP/2. Erfordert eine TLS- und [ALPN](https://tools.ietf.org/html/rfc7301#section-3)-Verbindung (Application-Layer Protocol Negotiation) zum Aushandeln von HTTP/2. Andernfalls wird für die Verbindung standardmäßig HTTP/1.1 verwendet. |

Das Standardprotokoll ist HTTP/1.1.

TLS-Einschränkungen für HTTP/2:

* TLS Version 1.2 oder höher
* Erneute Aushandlung deaktiviert
* Komprimierung deaktiviert
* Minimale Größen für Austausch von flüchtigen Schlüsseln:
  * ECDHE (Elliptic Curve Diffie-Hellman) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; mindestens 224 Bits
  * DHE (Finite Field Diffie-Hellman) &lbrack;`TLS12`&rbrack; &ndash; mindestens 2.048 Bits
* Verschlüsselungssammlung nicht gesperrt

`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; mit der elliptischen P-256-Kurve &lbrack;`FIPS186`&rbrack; wird standardmäßig unterstützt.

Das folgende Beispiel erlaubt HTTP/1.1- und HTTP/2-Verbindungen an Port 8000. Die Verbindungen werden durch TLS mit einem bereitgestellten Zertifikat geschützt:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Listen(IPAddress.Any, 8000, listenOptions =>
            {
                listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

Erstellen Sie optional eine `IConnectionAdapter`-Implementierung, um TLS-Handshakes auf Verbindungsbasis für bestimmte Verschlüsselungen zu filtern:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Listen(IPAddress.Any, 8000, listenOptions =>
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

        // Throw NotSupportedException for any cipher algorithm that you don't
        // wish to support. Alternatively, define and compare
        // ITlsHandshakeFeature.CipherAlgorithm to a list of acceptable cipher
        // suites.
        //
        // A ITlsHandshakeFeature.CipherAlgorithm of CipherAlgorithmType.Null
        // indicates that no cipher algorithm supported by Kestrel matches the
        // requested algorithm(s).
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

*Festlegen des Protokolls aus der Konfiguration*

<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ruft `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` standardmäßig zum Laden der Kestrel-Konfiguration auf.

Im folgenden Beispiel *appsettings.json* wird für alle Endpunkte von Kestrel ein Standardverbindungsprotokoll (HTTP/1.1 und HTTP/2) eingerichtet:

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

Das folgende Beispiel einer Konfigurationsdatei richtet ein Verbindungsprotokoll für einen bestimmten Endpunkt ein:

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

Protokolle, die in Code angegeben werden, setzen Werte außer Kraft, der durch die Konfiguration festgelegt werden.

::: moniker-end

## <a name="transport-configuration"></a>Transportkonfiguration

Mit dem Release von ASP.NET Core 2.1 basiert der Standardtransport von Kestrel nicht mehr auf Libuv, sondern auf verwalteten Sockets. Dies stellt einen Breaking Change für ASP.NET Core 2.0-Apps dar, die auf Version 2.1 upgraden, <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> aufrufen und von einem der folgenden Pakete abhängig sind:

* [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direkter Paketverweis)
* [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

Gehen Sie bei Projekten mit ASP.NET Core 2.1 oder höher, die das [Metapaket Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) verwenden und für die die Verwendung von Libuv erforderlich ist, wie folgt vor:

* Fügen Sie für das [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/)-Paket eine Abhängigkeit auf die Projektdatei der App hinzu:

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                      Version="<LATEST_VERSION>" />
    ```

* Rufen Sie <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> auf:

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

### <a name="url-prefixes"></a>URL-Präfixe

Bei der Verwendung von `UseUrls`, dem Befehlszeilenargument `--urls`, dem Hostkonfigurationsschlüssel `urls` oder der Umgebungsvariable `ASPNETCORE_URLS` können die URL-Präfixe in den folgenden Formaten vorliegen.

Nur HTTP-URL-Präfixe sind gültig. Kestrel unterstützt HTTPS nicht beim Konfigurieren von URL-Bindungen mit `UseUrls`.

* IPv4-Adresse mit Portnummer

  ```
  http://65.55.39.10:80/
  ```

  Bei `0.0.0.0` handelt es sich um einen Sonderfall, für den eine Bindung an alle IPv4-Adressen erfolgt.

* IPv6-Adresse mit Portnummer

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  `[::]` stellt das Äquivalent von IPv6 zu `0.0.0.0` für IPv4 dar.

* Hostname mit Portnummer

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  Hostnamen, `*` und `+` sind nicht spezifisch. Alle Elemente, die nicht als gültige IP-Adresse oder `localhost` erkannt werden, werden an alle IPv4- und IPv6-IP-Adressen gebunden. Verwenden Sie [HTTP.sys](xref:fundamentals/servers/httpsys) oder einen Reverseproxyserver zum Binden verschiedener Hostnamen an verschiedene ASP.NET Core-Apps auf demselben Port, z.B. IIS, Nginx oder Apache.

  > [!WARNING]
  > Das Hosting in einer Reverseproxykonfiguration erfordert [Hostfilterung](#host-filtering).

* `localhost`-Hostname mit Portnummer oder Loopback-IP mit Portnummer

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  Wenn `localhost` angegeben ist, versucht Kestrel, eine Bindung zu IPv4- und IPv6-Loopback-Schnittstellen zu erstellen. Wenn der erforderliche Port von einem anderen Dienst auf einer der Loopback-Schnittstellen verwendet wird, tritt beim Starten von Kestrel ein Fehler auf. Wenn eine der Loopback-Schnittstellen aus anderen Gründen nicht verfügbar ist (meistens durch die fehlende Unterstützung von IPv6), protokolliert Kestrel eine Warnung.

## <a name="host-filtering"></a>Host-Filterung

Obwohl Kestrel die Konfiguration basierend auf Präfixe wie `http://example.com:5000` unterstützt, ignoriert Kestrel den Hostnamen weitgehend. Der Host `localhost` ist ein Sonderfall, der für die Bindung an Loopback-Adressen verwendet wird. Jeder Host, der keine explizite IP-Adresse ist, wird an alle öffentlichen IP-Adressen gebunden. `Host`-Header werden nicht überprüft.

Verwenden Sie Middleware zum Filtern von Hosts, um dieses Problem zu umgehen. Middleware zum Filtern von Hosts wird im Paket [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) bereitgestellt, das im [Metapaket Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) enthalten ist (ASP.NET Core 2.1 oder höher). Die Middleware wird von <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> hinzugefügt, wodurch <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> aufgerufen wird:

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

Die Middleware zum Filtern von Hosts ist standardmäßig deaktiviert. Wenn Sie die Middleware aktivieren möchten, definieren Sie einen `AllowedHosts`-Schlüssel in *appsettings.json*/*appsettings.\<Umgebungsname>.json*. Der Wert ist eine durch Semikolons getrennte Liste von Hostnamen ohne Portnummern:

*appsettings.json*:

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> [Middleware für weitergeleitete Header](xref:host-and-deploy/proxy-load-balancer) hat auch eine <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>-Option. Middleware für weitergeleitete Header und Middleware zum Filtern von Hosts besitzen ähnliche Funktionen für unterschiedliche Szenarios. Legen Sie `AllowedHosts` mit Middleware für weitergeleitete Header fest, wenn der `Host`-Header beim Weiterleiten von Anforderungen mit einem Reverseproxyserver oder einem Lastenausgleichsmodul nicht beibehalten wird. Legen Sie `AllowedHosts` mit Middleware zum Filtern von Hosts fest, wenn Kestrel als öffentlicher Edgeserver verwendet oder der `Host`-Header direkt weitergeleitet wird.
>
> Weitere Informationen zu Middleware für weitergeleitete Header finden Sie unter <xref:host-and-deploy/proxy-load-balancer>.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [Kestrel-Quellcode](https://github.com/aspnet/AspNetCore/tree/master/src/Servers/Kestrel)
* [RFC 7230: Message Syntax and Routing (Abschnitt 5.4: Host)](https://tools.ietf.org/html/rfc7230#section-5.4)

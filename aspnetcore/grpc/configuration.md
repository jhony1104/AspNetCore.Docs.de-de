---
title: gRPC für ASP.NET Core-Konfiguration
author: jamesnk
description: Erfahren Sie, wie gRPC für ASP.NET Core-apps zu konfigurieren.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 04/09/2019
uid: grpc/configuration
ms.openlocfilehash: 66dfb9ec136616f10c1b7aaad766e18813b87de4
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087347"
---
# <a name="grpc-for-aspnet-core-configuration"></a>gRPC für ASP.NET Core-Konfiguration

## <a name="configure-services-options"></a>Konfigurieren Sie Optionen für Dienste

Die folgende Tabelle beschreibt die Optionen zum Konfigurieren der gRPC-Dienste:

| Option | Standardwert | Beschreibung |
| ------ | ------------- | ----------- |
| `SendMaxMessageSize` | `null` | Die maximale Nachrichtengröße in Bytes, die vom Server gesendet werden können. Es wird versucht, eine Nachricht zu senden, die überschreitet die konfigurierte maximale Nachricht Größe führt zu einer Ausnahme. |
| `ReceiveMaxMessageSize` | 4 MB | Die maximale Nachrichtengröße in Bytes, die vom Server empfangen werden können. Wenn der Server eine Nachricht, die diesen Grenzwert überschreitet empfängt, wird eine Ausnahme ausgelöst. Durch Erhöhen dieses Wertes können den Server, größere Nachrichten zu empfangen, aber arbeitsspeichernutzung negativ beeinträchtigt werden kann. |
| `EnableDetailedErrors` | `false` | Wenn `true`, detaillierte ausnahmemeldungen werden an Clients zurückgegeben, wenn eine Ausnahme in einer Dienstmethode ausgelöst wird. Die Standardeinstellung ist `false`. Wenn dies auf `true` kann Offenlegung von vertraulichen Informationen. |
| `CompressionProviders` | gzip | Eine Auflistung von Komprimierung-Anbietern, die zum Komprimieren und Dekomprimieren von Nachrichten verwendet werden soll. Benutzerdefinierte Komprimierung-Anbieter können erstellt und der Auflistung hinzugefügt werden. Standardmäßig konfiguriert der Anbieter unterstützt **Gzip** Komprimierung. |
| `ResponseCompressionAlgorithm` | `null` | Der Komprimierungsalgorithmus verwendet, um vom Server gesendeten Nachrichten zu komprimieren. Der Algorithmus muss einen Anbieter Komprimierung in übereinstimmen `CompressionProviders`. Für die Algorthm zum Komprimieren einer Antwort muss der Client angeben, senden Sie sie der Algorithmus unterstützt die **Grpc-accept-encoding** Header. |
| `ResponseCompressionLevel` | `null` | Die Compress-Ebene verwendet, um vom Server gesendeten Nachrichten zu komprimieren. |

Optionen für alle Dienste konfiguriert werden können, durch die Bereitstellung einer Optionen-Delegat, der die `AddGrpc` Aufrufen in `Startup.ConfigureServices`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.EnableDetailedErrors = true;
    });
}
```

Optionen für einen einzelnen Dienst außer Kraft setzen die globale Optionen im `AddGrpc` und kann konfiguriert werden, mithilfe von `AddServiceOptions<TService>`:

```csharp
services.AddGrpc().AddServiceOptions<MyService>(options =>
{
    options.ReceiveMaxMessageSize = 10 * 1024 * 1024; // 10 megabytes
});
```

## <a name="configure-kestrel-options"></a>Konfigurieren Sie Kestrel-Optionen

Kestrel-Server verfügt über Optionen für die Konfiguration, die das Verhalten der gRPC für ASP.NET zu beeinflussen.

### <a name="request-body-data-rate-limit"></a>Für die Anforderung Text Datenrate

Standardmäßig legt der Kestrel-Server eine [minimale Datenrate des Anforderungstexts](
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>). Für streaming-Client und Duplex-streaming-Aufrufe diese Rate kann nicht erfüllt werden, und möglicherweise werden Verbindungstimeout. Die minimale-Anforderungstext für die Datenrate muss deaktiviert werden, wenn der Dienst gRPC-streaming-Client und Duplex-streaming-Aufrufe enthält:

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
        webBuilder.UseStartup<Startup>();
        webBuilder.ConfigureKestrel((context, options) =>
        {
            options.Limits.MinRequestBodyDataRate = null;
        });
    });
}
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>

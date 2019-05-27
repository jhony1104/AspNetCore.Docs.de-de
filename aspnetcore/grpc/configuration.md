---
title: gRPC für ASP.NET Core-Konfiguration
author: jamesnk
description: Erfahren Sie, wie gRPC für ASP.NET Core-apps zu konfigurieren.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 04/09/2019
uid: grpc/configuration
ms.openlocfilehash: 851c9ca1f7d62f6f368df66bb38eb4bbaf64bf32
ms.sourcegitcommit: 5d384db2fa9373a93b5d15e985fb34430e49ad7a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66041892"
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

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>

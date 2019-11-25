---
title: Redis-Rückwand für ASP.net Core SignalR horizontales hochskalieren
author: bradygaster
description: Erfahren Sie, wie Sie eine redis-Rückwand einrichten, um das horizontale hochskalieren für eine ASP.net Core SignalR-APP zu aktivieren.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/redis-backplane
ms.openlocfilehash: 0461fc6a212ba78111bc2054cca74951721c5820
ms.sourcegitcommit: f40c9311058c9b1add4ec043ddc5629384af6c56
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74289036"
---
# <a name="set-up-a-redis-backplane-for-aspnet-core-opno-locsignalr-scale-out"></a>Einrichten einer redis-Rückwand für ASP.net Core SignalR horizontales hochskalieren

Von [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaester](https://twitter.com/bradygaster)und [Tom Dykstra](https://github.com/tdykstra)

In diesem Artikel werden SignalRspezifischen Aspekte der Einrichtung eines [redis](https://redis.io/) -Servers erläutert, der zum horizontalen hochskalieren einer ASP.net Core SignalR-App verwendet werden soll.

## <a name="set-up-a-redis-backplane"></a>Einrichten einer redis-Rückwand

* Stellen Sie einen redis-Server bereit.

  > [!IMPORTANT] 
  > Für den Einsatz in der Produktion wird eine redis-Rückwand nur dann empfohlen, wenn Sie im selben Rechenzentrum wie die SignalR-app ausgeführt wird. Andernfalls beeinträchtigt die Netzwerk Latenz die Leistung. Wenn Ihre SignalR-app in der Azure-Cloud ausgeführt wird, empfehlen wir den Azure-SignalR Dienst anstelle einer redis-Backplane. Sie können den Azure redis Cache-Dienst für Entwicklungs-und Testumgebungen verwenden.

  Weitere Informationen finden Sie in den folgenden Ressourcen:

  * <xref:signalr/scale>
  * [Redis-Dokumentation](https://redis.io/)
  * [Dokumentation zu Azure redis Cache](https://docs.microsoft.com/azure/redis-cache/)

::: moniker range="= aspnetcore-2.1"

* Installieren Sie in der SignalR-APP das `Microsoft.AspNetCore.SignalR.Redis` nuget-Paket.
* Nennen Sie in der `Startup.ConfigureServices`-Methode `AddRedis` nach `AddSignalR`:

  ```csharp
  services.AddSignalR().AddRedis("<your_Redis_connection_string>");
  ```

* Konfigurieren Sie die Optionen nach Bedarf:
 
  Die meisten Optionen können in der Verbindungs Zeichenfolge oder im [configurationoptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) -Objekt festgelegt werden. Die in `ConfigurationOptions` angegebenen Optionen überschreiben die in der Verbindungs Zeichenfolge festgelegten.

  Im folgenden Beispiel wird gezeigt, wie Optionen im `ConfigurationOptions`-Objekt festgelegt werden. In diesem Beispiel wird ein Kanal Präfix hinzugefügt, sodass mehrere apps dieselbe redis-Instanz gemeinsam nutzen können, wie im folgenden Schritt erläutert.

  ```csharp
  services.AddSignalR()
    .AddRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  Im vorangehenden Code wird `options.Configuration` mit allen in der Verbindungs Zeichenfolge angegebenen initialisiert.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* Installieren Sie in der SignalR-App eines der folgenden nuget-Pakete:

  * `Microsoft.AspNetCore.SignalR.StackExchangeRedis`: abhängig von stackexchange. redis 2. x Dies ist das empfohlene Paket für ASP.net Core 2,2 und höher.
  * `Microsoft.AspNetCore.SignalR.Redis`: abhängig von stackexchange. redis 1. x Dieses Paket ist nicht in ASP.net Core 3,0 und höher enthalten.

* Wenden Sie in der `Startup.ConfigureServices`-Methode <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>an:

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```

 Wenn Sie `Microsoft.AspNetCore.SignalR.Redis`verwenden, wenden Sie <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>an.

* Konfigurieren Sie die Optionen nach Bedarf:
 
  Die meisten Optionen können in der Verbindungs Zeichenfolge oder im [configurationoptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) -Objekt festgelegt werden. Die in `ConfigurationOptions` angegebenen Optionen überschreiben die in der Verbindungs Zeichenfolge festgelegten.

  Im folgenden Beispiel wird gezeigt, wie Optionen im `ConfigurationOptions`-Objekt festgelegt werden. In diesem Beispiel wird ein Kanal Präfix hinzugefügt, sodass mehrere apps dieselbe redis-Instanz gemeinsam nutzen können, wie im folgenden Schritt erläutert.

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

 Wenn Sie `Microsoft.AspNetCore.SignalR.Redis`verwenden, wenden Sie <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>an.

  Im vorangehenden Code wird `options.Configuration` mit allen in der Verbindungs Zeichenfolge angegebenen initialisiert.

  Weitere Informationen zu redis-Optionen finden Sie in der [stackexchange redis-Dokumentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* Installieren Sie in der SignalR-APP das folgende nuget-Paket:

  * `Microsoft.AspNetCore.SignalR.StackExchangeRedis`
  
* Wenden Sie in der `Startup.ConfigureServices`-Methode <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>an:

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```
  
* Konfigurieren Sie die Optionen nach Bedarf:
 
  Die meisten Optionen können in der Verbindungs Zeichenfolge oder im [configurationoptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) -Objekt festgelegt werden. Die in `ConfigurationOptions` angegebenen Optionen überschreiben die in der Verbindungs Zeichenfolge festgelegten.

  Im folgenden Beispiel wird gezeigt, wie Optionen im `ConfigurationOptions`-Objekt festgelegt werden. In diesem Beispiel wird ein Kanal Präfix hinzugefügt, sodass mehrere apps dieselbe redis-Instanz gemeinsam nutzen können, wie im folgenden Schritt erläutert.

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  Im vorangehenden Code wird `options.Configuration` mit allen in der Verbindungs Zeichenfolge angegebenen initialisiert.

  Weitere Informationen zu redis-Optionen finden Sie in der [stackexchange redis-Dokumentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).

::: moniker-end

* Wenn Sie einen redis-Server für mehrere SignalR-Apps verwenden, verwenden Sie für jede SignalR-App ein anderes channelpräfix.

  Wenn Sie ein Kanal Präfix festlegen, wird eine SignalR-APP von anderen isoliert, die unterschiedliche channelpräfixe verwenden. Wenn Sie keine unterschiedlichen Präfixe zuweisen, wird eine Nachricht, die von einer APP an alle Ihre eigenen Clients gesendet wird, an alle Clients aller apps gesendet, die den redis-Server als Rück Ebene verwenden.

* Konfigurieren Sie die Lasten Ausgleichs Software der Serverfarm für persistente Sitzungen. Im folgenden finden Sie einige Beispiele für die Vorgehensweise:

  * [IIS](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)
  * [HAProxy](https://www.haproxy.com/blog/load-balancing-affinity-persistence-sticky-sessions-what-you-need-to-know/)
  * [Nginx](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/#sticky)
  * [pfSense](https://www.netgate.com/docs/pfsense/loadbalancing/inbound-load-balancing.html#sticky-connections)

## <a name="redis-server-errors"></a>Redis-Server Fehler

Wenn ein redis-Server ausfällt, löst SignalR Ausnahmen aus, die darauf hinweisen, dass Nachrichten nicht übermittelt werden. Einige typische Ausnahme Meldungen:

* *Fehler beim Schreiben der Nachricht*
* *Fehler beim Aufrufen der Hub-Methode "MethodName".*
* *Fehler bei Verbindung mit redis.*

SignalR puffert keine Nachrichten, um Sie zu senden, wenn der Server wieder verfügbar ist. Alle Nachrichten, die gesendet werden, während der redis-Server ausfällt, gehen verloren.

SignalR automatisch erneut eine Verbindung her, wenn der redis-Server wieder verfügbar ist.

### <a name="custom-behavior-for-connection-failures"></a>Benutzerdefiniertes Verhalten bei Verbindungsfehlern

Es folgt ein Beispiel, das zeigt, wie redis-Verbindungsfehler Ereignisse behandelt werden.

::: moniker range="= aspnetcore-2.1"

```csharp
services.AddSignalR()
        .AddRedis(o =>
        {
            o.ConnectionFactory = async writer =>
            {
                var config = new ConfigurationOptions
                {
                    AbortOnConnectFail = false
                };
                config.EndPoints.Add(IPAddress.Loopback, 0);
                config.SetDefaultPorts();
                var connection = await ConnectionMultiplexer.ConnectAsync(config, writer);
                connection.ConnectionFailed += (_, e) =>
                {
                    Console.WriteLine("Connection to Redis failed.");
                };

                if (!connection.IsConnected)
                {
                    Console.WriteLine("Did not connect to Redis.");
                }

                return connection;
            };
        });
```

::: moniker-end

::: moniker range="> aspnetcore-2.1"

```csharp
services.AddSignalR()
        .AddMessagePackProtocol()
        .AddStackExchangeRedis(o =>
        {
            o.ConnectionFactory = async writer =>
            {
                var config = new ConfigurationOptions
                {
                    AbortOnConnectFail = false
                };
                config.EndPoints.Add(IPAddress.Loopback, 0);
                config.SetDefaultPorts();
                var connection = await ConnectionMultiplexer.ConnectAsync(config, writer);
                connection.ConnectionFailed += (_, e) =>
                {
                    Console.WriteLine("Connection to Redis failed.");
                };

                if (!connection.IsConnected)
                {
                    Console.WriteLine("Did not connect to Redis.");
                }

                return connection;
            };
        });
```

::: moniker-end

## <a name="redis-clustering"></a>Redis-Clustering

[Redis-Clustering](https://redis.io/topics/cluster-spec) ist eine Methode zum Erreichen von Hochverfügbarkeit mithilfe mehrerer redis-Server. Clustering wird nicht offiziell unterstützt, kann jedoch funktionieren.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Ressourcen:

* <xref:signalr/scale>
* [Redis-Dokumentation](https://redis.io/documentation)
* [Dokumentation zu stackexchange redis](https://stackexchange.github.io/StackExchange.Redis/)
* [Dokumentation zu Azure redis Cache](https://docs.microsoft.com/azure/redis-cache/)

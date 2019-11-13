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
ms.openlocfilehash: 379d46fcaabb8eb0d04e521a5ad698229f947b7c
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963915"
---
# <a name="set-up-a-redis-backplane-for-aspnet-core-opno-locsignalr-scale-out"></a><span data-ttu-id="b82e3-103">Einrichten einer redis-Rückwand für ASP.net Core SignalR horizontales hochskalieren</span><span class="sxs-lookup"><span data-stu-id="b82e3-103">Set up a Redis backplane for ASP.NET Core SignalR scale-out</span></span>

<span data-ttu-id="b82e3-104">Von [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaester](https://twitter.com/bradygaster)und [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="b82e3-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), and [Tom Dykstra](https://github.com/tdykstra),</span></span>

<span data-ttu-id="b82e3-105">In diesem Artikel werden SignalRspezifischen Aspekte der Einrichtung eines [redis](https://redis.io/) -Servers erläutert, der zum horizontalen hochskalieren einer ASP.net Core SignalR-App verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="b82e3-105">This article explains SignalR-specific aspects of setting up a [Redis](https://redis.io/) server to use for scaling out an ASP.NET Core SignalR app.</span></span>

## <a name="set-up-a-redis-backplane"></a><span data-ttu-id="b82e3-106">Einrichten einer redis-Rückwand</span><span class="sxs-lookup"><span data-stu-id="b82e3-106">Set up a Redis backplane</span></span>

* <span data-ttu-id="b82e3-107">Stellen Sie einen redis-Server bereit.</span><span class="sxs-lookup"><span data-stu-id="b82e3-107">Deploy a Redis server.</span></span>

  > [!IMPORTANT] 
  > <span data-ttu-id="b82e3-108">Für den Einsatz in der Produktion wird eine redis-Rückwand nur dann empfohlen, wenn Sie im selben Rechenzentrum wie die SignalR-app ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="b82e3-108">For production use, a Redis backplane is recommended only when it runs in the same data center as the SignalR app.</span></span> <span data-ttu-id="b82e3-109">Andernfalls beeinträchtigt die Netzwerk Latenz die Leistung.</span><span class="sxs-lookup"><span data-stu-id="b82e3-109">Otherwise, network latency degrades performance.</span></span> <span data-ttu-id="b82e3-110">Wenn Ihre SignalR-app in der Azure-Cloud ausgeführt wird, empfehlen wir den Azure-SignalR Dienst anstelle einer redis-Backplane.</span><span class="sxs-lookup"><span data-stu-id="b82e3-110">If your SignalR app is running in the Azure cloud, we recommend Azure SignalR Service instead of a Redis backplane.</span></span> <span data-ttu-id="b82e3-111">Sie können den Azure redis Cache-Dienst für Entwicklungs-und Testumgebungen verwenden.</span><span class="sxs-lookup"><span data-stu-id="b82e3-111">You can use the Azure Redis Cache Service for development and test environments.</span></span>

  <span data-ttu-id="b82e3-112">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="b82e3-112">For more information, see the following resources:</span></span>

  * <xref:signalr/scale>
  * [<span data-ttu-id="b82e3-113">Redis-Dokumentation</span><span class="sxs-lookup"><span data-stu-id="b82e3-113">Redis documentation</span></span>](https://redis.io/)
  * [<span data-ttu-id="b82e3-114">Dokumentation zu Azure redis Cache</span><span class="sxs-lookup"><span data-stu-id="b82e3-114">Azure Redis Cache documentation</span></span>](https://docs.microsoft.com/azure/redis-cache/)

::: moniker range="= aspnetcore-2.1"

* <span data-ttu-id="b82e3-115">Installieren Sie in der SignalR-APP das `Microsoft.AspNetCore.SignalR.Redis` nuget-Paket.</span><span class="sxs-lookup"><span data-stu-id="b82e3-115">In the SignalR app, install the `Microsoft.AspNetCore.SignalR.Redis` NuGet package.</span></span> <span data-ttu-id="b82e3-116">(Es gibt auch ein `Microsoft.AspNetCore.SignalR.StackExchangeRedis` Paket, aber dieses ist für ASP.net Core 2,2 und höher.)</span><span class="sxs-lookup"><span data-stu-id="b82e3-116">(There is also a `Microsoft.AspNetCore.SignalR.StackExchangeRedis` package, but that one is for ASP.NET Core 2.2 and later.)</span></span>

* <span data-ttu-id="b82e3-117">Nennen Sie in der `Startup.ConfigureServices`-Methode `AddRedis` nach `AddSignalR`:</span><span class="sxs-lookup"><span data-stu-id="b82e3-117">In the `Startup.ConfigureServices` method, call `AddRedis` after `AddSignalR`:</span></span>

  ```csharp
  services.AddSignalR().AddRedis("<your_Redis_connection_string>");
  ```

* <span data-ttu-id="b82e3-118">Konfigurieren Sie die Optionen nach Bedarf:</span><span class="sxs-lookup"><span data-stu-id="b82e3-118">Configure options as needed:</span></span>
 
  <span data-ttu-id="b82e3-119">Die meisten Optionen können in der Verbindungs Zeichenfolge oder im [configurationoptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) -Objekt festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="b82e3-119">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="b82e3-120">Die in `ConfigurationOptions` angegebenen Optionen überschreiben die in der Verbindungs Zeichenfolge festgelegten.</span><span class="sxs-lookup"><span data-stu-id="b82e3-120">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="b82e3-121">Im folgenden Beispiel wird gezeigt, wie Optionen im `ConfigurationOptions`-Objekt festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="b82e3-121">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="b82e3-122">In diesem Beispiel wird ein Kanal Präfix hinzugefügt, sodass mehrere apps dieselbe redis-Instanz gemeinsam nutzen können, wie im folgenden Schritt erläutert.</span><span class="sxs-lookup"><span data-stu-id="b82e3-122">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="b82e3-123">Im vorangehenden Code wird `options.Configuration` mit allen in der Verbindungs Zeichenfolge angegebenen initialisiert.</span><span class="sxs-lookup"><span data-stu-id="b82e3-123">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.1"

* <span data-ttu-id="b82e3-124">Installieren Sie in der SignalR-App eines der folgenden nuget-Pakete:</span><span class="sxs-lookup"><span data-stu-id="b82e3-124">In the SignalR app, install one of the following NuGet packages:</span></span>

  * <span data-ttu-id="b82e3-125">`Microsoft.AspNetCore.SignalR.StackExchangeRedis`: abhängig von stackexchange. redis 2. x</span><span class="sxs-lookup"><span data-stu-id="b82e3-125">`Microsoft.AspNetCore.SignalR.StackExchangeRedis` - Depends on StackExchange.Redis 2.X.X.</span></span> <span data-ttu-id="b82e3-126">Dies ist das empfohlene Paket für ASP.net Core 2,2 und höher.</span><span class="sxs-lookup"><span data-stu-id="b82e3-126">This is the recommended package for ASP.NET Core 2.2 and later.</span></span>
  * <span data-ttu-id="b82e3-127">`Microsoft.AspNetCore.SignalR.Redis`: abhängig von stackexchange. redis 1. x</span><span class="sxs-lookup"><span data-stu-id="b82e3-127">`Microsoft.AspNetCore.SignalR.Redis` - Depends on StackExchange.Redis 1.X.X.</span></span> <span data-ttu-id="b82e3-128">Dieses Paket wird nicht in ASP.net Core 3,0 ausgeliefert.</span><span class="sxs-lookup"><span data-stu-id="b82e3-128">This package will not be shipping in ASP.NET Core 3.0.</span></span>

* <span data-ttu-id="b82e3-129">Nennen Sie in der `Startup.ConfigureServices`-Methode `AddStackExchangeRedis` nach `AddSignalR`:</span><span class="sxs-lookup"><span data-stu-id="b82e3-129">In the `Startup.ConfigureServices` method, call `AddStackExchangeRedis` after `AddSignalR`:</span></span>

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```

* <span data-ttu-id="b82e3-130">Konfigurieren Sie die Optionen nach Bedarf:</span><span class="sxs-lookup"><span data-stu-id="b82e3-130">Configure options as needed:</span></span>
 
  <span data-ttu-id="b82e3-131">Die meisten Optionen können in der Verbindungs Zeichenfolge oder im [configurationoptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) -Objekt festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="b82e3-131">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="b82e3-132">Die in `ConfigurationOptions` angegebenen Optionen überschreiben die in der Verbindungs Zeichenfolge festgelegten.</span><span class="sxs-lookup"><span data-stu-id="b82e3-132">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="b82e3-133">Im folgenden Beispiel wird gezeigt, wie Optionen im `ConfigurationOptions`-Objekt festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="b82e3-133">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="b82e3-134">In diesem Beispiel wird ein Kanal Präfix hinzugefügt, sodass mehrere apps dieselbe redis-Instanz gemeinsam nutzen können, wie im folgenden Schritt erläutert.</span><span class="sxs-lookup"><span data-stu-id="b82e3-134">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="b82e3-135">Im vorangehenden Code wird `options.Configuration` mit allen in der Verbindungs Zeichenfolge angegebenen initialisiert.</span><span class="sxs-lookup"><span data-stu-id="b82e3-135">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

  <span data-ttu-id="b82e3-136">Weitere Informationen zu redis-Optionen finden Sie in der [stackexchange redis-Dokumentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span><span class="sxs-lookup"><span data-stu-id="b82e3-136">For information about Redis options, see the [StackExchange Redis documentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span></span>

::: moniker-end

* <span data-ttu-id="b82e3-137">Wenn Sie einen redis-Server für mehrere SignalR-Apps verwenden, verwenden Sie für jede SignalR-App ein anderes channelpräfix.</span><span class="sxs-lookup"><span data-stu-id="b82e3-137">If you're using one Redis server for multiple SignalR apps, use a different channel prefix for each SignalR app.</span></span>

  <span data-ttu-id="b82e3-138">Wenn Sie ein Kanal Präfix festlegen, wird eine SignalR-APP von anderen isoliert, die unterschiedliche channelpräfixe verwenden.</span><span class="sxs-lookup"><span data-stu-id="b82e3-138">Setting a channel prefix isolates one SignalR app from others that use different channel prefixes.</span></span> <span data-ttu-id="b82e3-139">Wenn Sie keine unterschiedlichen Präfixe zuweisen, wird eine Nachricht, die von einer APP an alle Ihre eigenen Clients gesendet wird, an alle Clients aller apps gesendet, die den redis-Server als Rück Ebene verwenden.</span><span class="sxs-lookup"><span data-stu-id="b82e3-139">If you don't assign different prefixes, a message sent from one app to all of its own clients will go to all clients of all apps that use the Redis server as a backplane.</span></span>

* <span data-ttu-id="b82e3-140">Konfigurieren Sie die Lasten Ausgleichs Software der Serverfarm für persistente Sitzungen.</span><span class="sxs-lookup"><span data-stu-id="b82e3-140">Configure your server farm load balancing software for sticky sessions.</span></span> <span data-ttu-id="b82e3-141">Im folgenden finden Sie einige Beispiele für die Vorgehensweise:</span><span class="sxs-lookup"><span data-stu-id="b82e3-141">Here are some examples of documentation on how to do that:</span></span>

  * [<span data-ttu-id="b82e3-142">IIS</span><span class="sxs-lookup"><span data-stu-id="b82e3-142">IIS</span></span>](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)
  * [<span data-ttu-id="b82e3-143">HAProxy</span><span class="sxs-lookup"><span data-stu-id="b82e3-143">HAProxy</span></span>](https://www.haproxy.com/blog/load-balancing-affinity-persistence-sticky-sessions-what-you-need-to-know/)
  * [<span data-ttu-id="b82e3-144">Nginx</span><span class="sxs-lookup"><span data-stu-id="b82e3-144">Nginx</span></span>](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/#sticky)
  * [<span data-ttu-id="b82e3-145">pfSense</span><span class="sxs-lookup"><span data-stu-id="b82e3-145">pfSense</span></span>](https://www.netgate.com/docs/pfsense/loadbalancing/inbound-load-balancing.html#sticky-connections)

## <a name="redis-server-errors"></a><span data-ttu-id="b82e3-146">Redis-Server Fehler</span><span class="sxs-lookup"><span data-stu-id="b82e3-146">Redis server errors</span></span>

<span data-ttu-id="b82e3-147">Wenn ein redis-Server ausfällt, löst SignalR Ausnahmen aus, die darauf hinweisen, dass Nachrichten nicht übermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="b82e3-147">When a Redis server goes down, SignalR throws exceptions that indicate messages won't be delivered.</span></span> <span data-ttu-id="b82e3-148">Einige typische Ausnahme Meldungen:</span><span class="sxs-lookup"><span data-stu-id="b82e3-148">Some typical exception messages:</span></span>

* <span data-ttu-id="b82e3-149">*Fehler beim Schreiben der Nachricht*</span><span class="sxs-lookup"><span data-stu-id="b82e3-149">*Failed writing message*</span></span>
* <span data-ttu-id="b82e3-150">*Fehler beim Aufrufen der Hub-Methode "MethodName".*</span><span class="sxs-lookup"><span data-stu-id="b82e3-150">*Failed to invoke hub method 'MethodName'*</span></span>
* <span data-ttu-id="b82e3-151">*Fehler bei Verbindung mit redis.*</span><span class="sxs-lookup"><span data-stu-id="b82e3-151">*Connection to Redis failed*</span></span>

SignalR<span data-ttu-id="b82e3-152"> puffert keine Nachrichten, um Sie zu senden, wenn der Server wieder verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="b82e3-152"> doesn't buffer messages to send them when the server comes back up.</span></span> <span data-ttu-id="b82e3-153">Alle Nachrichten, die gesendet werden, während der redis-Server ausfällt, gehen verloren.</span><span class="sxs-lookup"><span data-stu-id="b82e3-153">Any messages sent while the Redis server is down are lost.</span></span>

SignalR<span data-ttu-id="b82e3-154"> automatisch erneut eine Verbindung her, wenn der redis-Server wieder verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="b82e3-154"> automatically reconnects when the Redis server is available again.</span></span>

### <a name="custom-behavior-for-connection-failures"></a><span data-ttu-id="b82e3-155">Benutzerdefiniertes Verhalten bei Verbindungsfehlern</span><span class="sxs-lookup"><span data-stu-id="b82e3-155">Custom behavior for connection failures</span></span>

<span data-ttu-id="b82e3-156">Es folgt ein Beispiel, das zeigt, wie redis-Verbindungsfehler Ereignisse behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="b82e3-156">Here's an example that shows how to handle Redis connection failure events.</span></span>

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

## <a name="redis-clustering"></a><span data-ttu-id="b82e3-157">Redis-Clustering</span><span class="sxs-lookup"><span data-stu-id="b82e3-157">Redis Clustering</span></span>

<span data-ttu-id="b82e3-158">[Redis-Clustering](https://redis.io/topics/cluster-spec) ist eine Methode zum Erreichen von Hochverfügbarkeit mithilfe mehrerer redis-Server.</span><span class="sxs-lookup"><span data-stu-id="b82e3-158">[Redis Clustering](https://redis.io/topics/cluster-spec) is a method for achieving high availability by using multiple Redis servers.</span></span> <span data-ttu-id="b82e3-159">Clustering wird nicht offiziell unterstützt, kann jedoch funktionieren.</span><span class="sxs-lookup"><span data-stu-id="b82e3-159">Clustering isn't officially supported, but it might work.</span></span>

## <a name="next-steps"></a><span data-ttu-id="b82e3-160">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="b82e3-160">Next steps</span></span>

<span data-ttu-id="b82e3-161">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="b82e3-161">For more information, see the following resources:</span></span>

* <xref:signalr/scale>
* [<span data-ttu-id="b82e3-162">Redis-Dokumentation</span><span class="sxs-lookup"><span data-stu-id="b82e3-162">Redis documentation</span></span>](https://redis.io/documentation)
* [<span data-ttu-id="b82e3-163">Dokumentation zu stackexchange redis</span><span class="sxs-lookup"><span data-stu-id="b82e3-163">StackExchange Redis documentation</span></span>](https://stackexchange.github.io/StackExchange.Redis/)
* [<span data-ttu-id="b82e3-164">Dokumentation zu Azure redis Cache</span><span class="sxs-lookup"><span data-stu-id="b82e3-164">Azure Redis Cache documentation</span></span>](https://docs.microsoft.com/azure/redis-cache/)

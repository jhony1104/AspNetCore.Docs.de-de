---
title: Redis-Rückwand für SignalR ASP.net Core horizontales hochskalieren
author: bradygaster
description: Erfahren Sie, wie Sie eine redis-Rückwand einrichten, um das horizontale hochskalieren für eine ASP.net Core SignalR -APP zu aktivieren.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/redis-backplane
ms.openlocfilehash: 6068890f4089a13add05bf8cf8009367e343adce
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775348"
---
# <a name="set-up-a-redis-backplane-for-aspnet-core-signalr-scale-out"></a><span data-ttu-id="c7538-103">Einrichten einer redis-Rückwand für die SignalR ASP.net Core horizontales hochskalieren</span><span class="sxs-lookup"><span data-stu-id="c7538-103">Set up a Redis backplane for ASP.NET Core SignalR scale-out</span></span>

<span data-ttu-id="c7538-104">Von [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaester](https://twitter.com/bradygaster)und [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="c7538-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), and [Tom Dykstra](https://github.com/tdykstra),</span></span>

<span data-ttu-id="c7538-105">In diesem Artikel SignalRwerden spezifische Aspekte der Einrichtung eines [redis](https://redis.io/) -Servers erläutert, der zum horizontalen Skalieren SignalR einer ASP.net Core-App verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="c7538-105">This article explains SignalR-specific aspects of setting up a [Redis](https://redis.io/) server to use for scaling out an ASP.NET Core SignalR app.</span></span>

## <a name="set-up-a-redis-backplane"></a><span data-ttu-id="c7538-106">Einrichten einer redis-Rückwand</span><span class="sxs-lookup"><span data-stu-id="c7538-106">Set up a Redis backplane</span></span>

* <span data-ttu-id="c7538-107">Stellen Sie einen redis-Server bereit.</span><span class="sxs-lookup"><span data-stu-id="c7538-107">Deploy a Redis server.</span></span>

  > [!IMPORTANT] 
  > <span data-ttu-id="c7538-108">Für den Einsatz in der Produktion wird eine redis-Rückwand nur dann empfohlen, wenn Sie im selben Rechen SignalR Zentrum wie die app ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="c7538-108">For production use, a Redis backplane is recommended only when it runs in the same data center as the SignalR app.</span></span> <span data-ttu-id="c7538-109">Andernfalls beeinträchtigt die Netzwerk Latenz die Leistung.</span><span class="sxs-lookup"><span data-stu-id="c7538-109">Otherwise, network latency degrades performance.</span></span> <span data-ttu-id="c7538-110">Wenn Ihre SignalR app in der Azure-Cloud ausgeführt wird, empfehlen wir SignalR den Azure-Dienst anstelle einer redis-Backplane.</span><span class="sxs-lookup"><span data-stu-id="c7538-110">If your SignalR app is running in the Azure cloud, we recommend Azure SignalR Service instead of a Redis backplane.</span></span> <span data-ttu-id="c7538-111">Sie können den Azure redis Cache-Dienst für Entwicklungs-und Testumgebungen verwenden.</span><span class="sxs-lookup"><span data-stu-id="c7538-111">You can use the Azure Redis Cache Service for development and test environments.</span></span>

  <span data-ttu-id="c7538-112">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="c7538-112">For more information, see the following resources:</span></span>

  * <xref:signalr/scale>
  * [<span data-ttu-id="c7538-113">Redis-Dokumentation</span><span class="sxs-lookup"><span data-stu-id="c7538-113">Redis documentation</span></span>](https://redis.io/)
  * [<span data-ttu-id="c7538-114">Dokumentation zu Azure Redis Cache</span><span class="sxs-lookup"><span data-stu-id="c7538-114">Azure Redis Cache documentation</span></span>](https://docs.microsoft.com/azure/redis-cache/)

::: moniker range="= aspnetcore-2.1"

* <span data-ttu-id="c7538-115">Installieren Sie SignalR das `Microsoft.AspNetCore.SignalR.Redis` nuget-Paket in der app.</span><span class="sxs-lookup"><span data-stu-id="c7538-115">In the SignalR app, install the `Microsoft.AspNetCore.SignalR.Redis` NuGet package.</span></span>
* <span data-ttu-id="c7538-116">In der `Startup.ConfigureServices` -Methode wird `AddRedis` nach `AddSignalR`aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="c7538-116">In the `Startup.ConfigureServices` method, call `AddRedis` after `AddSignalR`:</span></span>

  ```csharp
  services.AddSignalR().AddRedis("<your_Redis_connection_string>");
  ```

* <span data-ttu-id="c7538-117">Konfigurieren Sie die Optionen nach Bedarf:</span><span class="sxs-lookup"><span data-stu-id="c7538-117">Configure options as needed:</span></span>
 
  <span data-ttu-id="c7538-118">Die meisten Optionen können in der Verbindungs Zeichenfolge oder im [configurationoptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) -Objekt festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="c7538-118">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="c7538-119">Die in `ConfigurationOptions` angegebenen Optionen überschreiben die in der Verbindungs Zeichenfolge festgelegten Optionen.</span><span class="sxs-lookup"><span data-stu-id="c7538-119">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="c7538-120">Im folgenden Beispiel wird gezeigt, wie Optionen im- `ConfigurationOptions` Objekt festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="c7538-120">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="c7538-121">In diesem Beispiel wird ein Kanal Präfix hinzugefügt, sodass mehrere apps dieselbe redis-Instanz gemeinsam nutzen können, wie im folgenden Schritt erläutert.</span><span class="sxs-lookup"><span data-stu-id="c7538-121">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="c7538-122">Im vorangehenden Code wird `options.Configuration` mit dem, was in der Verbindungs Zeichenfolge angegeben wurde, initialisiert.</span><span class="sxs-lookup"><span data-stu-id="c7538-122">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* <span data-ttu-id="c7538-123">Installieren Sie SignalR in der APP eines der folgenden nuget-Pakete:</span><span class="sxs-lookup"><span data-stu-id="c7538-123">In the SignalR app, install one of the following NuGet packages:</span></span>

  * <span data-ttu-id="c7538-124">`Microsoft.AspNetCore.SignalR.StackExchangeRedis`-Abhängig von stackexchange. redis 2. x</span><span class="sxs-lookup"><span data-stu-id="c7538-124">`Microsoft.AspNetCore.SignalR.StackExchangeRedis` - Depends on StackExchange.Redis 2.X.X.</span></span> <span data-ttu-id="c7538-125">Dies ist das empfohlene Paket für ASP.net Core 2,2 und höher.</span><span class="sxs-lookup"><span data-stu-id="c7538-125">This is the recommended package for ASP.NET Core 2.2 and later.</span></span>
  * <span data-ttu-id="c7538-126">`Microsoft.AspNetCore.SignalR.Redis`-Abhängig von stackexchange. redis 1. x</span><span class="sxs-lookup"><span data-stu-id="c7538-126">`Microsoft.AspNetCore.SignalR.Redis` - Depends on StackExchange.Redis 1.X.X.</span></span> <span data-ttu-id="c7538-127">Dieses Paket ist nicht in ASP.net Core 3,0 und höher enthalten.</span><span class="sxs-lookup"><span data-stu-id="c7538-127">This package isn't included in ASP.NET Core 3.0 and later.</span></span>

* <span data-ttu-id="c7538-128">In der `Startup.ConfigureServices` -Methode <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>:</span><span class="sxs-lookup"><span data-stu-id="c7538-128">In the `Startup.ConfigureServices` method, call <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>:</span></span>

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```

 <span data-ttu-id="c7538-129">Wenn Sie `Microsoft.AspNetCore.SignalR.Redis`verwenden, <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>wird aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="c7538-129">When using `Microsoft.AspNetCore.SignalR.Redis`, call <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>.</span></span>

* <span data-ttu-id="c7538-130">Konfigurieren Sie die Optionen nach Bedarf:</span><span class="sxs-lookup"><span data-stu-id="c7538-130">Configure options as needed:</span></span>
 
  <span data-ttu-id="c7538-131">Die meisten Optionen können in der Verbindungs Zeichenfolge oder im [configurationoptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) -Objekt festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="c7538-131">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="c7538-132">Die in `ConfigurationOptions` angegebenen Optionen überschreiben die in der Verbindungs Zeichenfolge festgelegten Optionen.</span><span class="sxs-lookup"><span data-stu-id="c7538-132">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="c7538-133">Im folgenden Beispiel wird gezeigt, wie Optionen im- `ConfigurationOptions` Objekt festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="c7538-133">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="c7538-134">In diesem Beispiel wird ein Kanal Präfix hinzugefügt, sodass mehrere apps dieselbe redis-Instanz gemeinsam nutzen können, wie im folgenden Schritt erläutert.</span><span class="sxs-lookup"><span data-stu-id="c7538-134">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

 <span data-ttu-id="c7538-135">Wenn Sie `Microsoft.AspNetCore.SignalR.Redis`verwenden, <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>wird aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="c7538-135">When using `Microsoft.AspNetCore.SignalR.Redis`, call <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>.</span></span>

  <span data-ttu-id="c7538-136">Im vorangehenden Code wird `options.Configuration` mit dem, was in der Verbindungs Zeichenfolge angegeben wurde, initialisiert.</span><span class="sxs-lookup"><span data-stu-id="c7538-136">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

  <span data-ttu-id="c7538-137">Weitere Informationen zu redis-Optionen finden Sie in der [stackexchange redis-Dokumentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span><span class="sxs-lookup"><span data-stu-id="c7538-137">For information about Redis options, see the [StackExchange Redis documentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="c7538-138">Installieren Sie SignalR in der APP das folgende nuget-Paket:</span><span class="sxs-lookup"><span data-stu-id="c7538-138">In the SignalR app, install the following NuGet package:</span></span>

  * `Microsoft.AspNetCore.SignalR.StackExchangeRedis`
  
* <span data-ttu-id="c7538-139">In der `Startup.ConfigureServices` -Methode <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>:</span><span class="sxs-lookup"><span data-stu-id="c7538-139">In the `Startup.ConfigureServices` method, call <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>:</span></span>

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```
  
* <span data-ttu-id="c7538-140">Konfigurieren Sie die Optionen nach Bedarf:</span><span class="sxs-lookup"><span data-stu-id="c7538-140">Configure options as needed:</span></span>
 
  <span data-ttu-id="c7538-141">Die meisten Optionen können in der Verbindungs Zeichenfolge oder im [configurationoptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) -Objekt festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="c7538-141">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="c7538-142">Die in `ConfigurationOptions` angegebenen Optionen überschreiben die in der Verbindungs Zeichenfolge festgelegten Optionen.</span><span class="sxs-lookup"><span data-stu-id="c7538-142">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="c7538-143">Im folgenden Beispiel wird gezeigt, wie Optionen im- `ConfigurationOptions` Objekt festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="c7538-143">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="c7538-144">In diesem Beispiel wird ein Kanal Präfix hinzugefügt, sodass mehrere apps dieselbe redis-Instanz gemeinsam nutzen können, wie im folgenden Schritt erläutert.</span><span class="sxs-lookup"><span data-stu-id="c7538-144">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="c7538-145">Im vorangehenden Code wird `options.Configuration` mit dem, was in der Verbindungs Zeichenfolge angegeben wurde, initialisiert.</span><span class="sxs-lookup"><span data-stu-id="c7538-145">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

  <span data-ttu-id="c7538-146">Weitere Informationen zu redis-Optionen finden Sie in der [stackexchange redis-Dokumentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span><span class="sxs-lookup"><span data-stu-id="c7538-146">For information about Redis options, see the [StackExchange Redis documentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span></span>

::: moniker-end

* <span data-ttu-id="c7538-147">Wenn Sie einen redis-Server für mehrere SignalR Apps verwenden, verwenden Sie für jede SignalR App ein anderes channelpräfix.</span><span class="sxs-lookup"><span data-stu-id="c7538-147">If you're using one Redis server for multiple SignalR apps, use a different channel prefix for each SignalR app.</span></span>

  <span data-ttu-id="c7538-148">Wenn Sie ein Kanal Präfix festlegen SignalR , wird eine APP von anderen Personen isoliert, die unterschiedliche channelpräfixe verwenden.</span><span class="sxs-lookup"><span data-stu-id="c7538-148">Setting a channel prefix isolates one SignalR app from others that use different channel prefixes.</span></span> <span data-ttu-id="c7538-149">Wenn Sie keine unterschiedlichen Präfixe zuweisen, wird eine Nachricht, die von einer APP an alle Ihre eigenen Clients gesendet wird, an alle Clients aller apps gesendet, die den redis-Server als Rück Ebene verwenden.</span><span class="sxs-lookup"><span data-stu-id="c7538-149">If you don't assign different prefixes, a message sent from one app to all of its own clients will go to all clients of all apps that use the Redis server as a backplane.</span></span>

* <span data-ttu-id="c7538-150">Konfigurieren Sie die Lasten Ausgleichs Software der Serverfarm für persistente Sitzungen.</span><span class="sxs-lookup"><span data-stu-id="c7538-150">Configure your server farm load balancing software for sticky sessions.</span></span> <span data-ttu-id="c7538-151">Im folgenden finden Sie einige Beispiele für die Vorgehensweise:</span><span class="sxs-lookup"><span data-stu-id="c7538-151">Here are some examples of documentation on how to do that:</span></span>

  * [<span data-ttu-id="c7538-152">IIS</span><span class="sxs-lookup"><span data-stu-id="c7538-152">IIS</span></span>](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)
  * [<span data-ttu-id="c7538-153">HAProxy</span><span class="sxs-lookup"><span data-stu-id="c7538-153">HAProxy</span></span>](https://www.haproxy.com/blog/load-balancing-affinity-persistence-sticky-sessions-what-you-need-to-know/)
  * [<span data-ttu-id="c7538-154">Nginx</span><span class="sxs-lookup"><span data-stu-id="c7538-154">Nginx</span></span>](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/#sticky)
  * [<span data-ttu-id="c7538-155">pfSense</span><span class="sxs-lookup"><span data-stu-id="c7538-155">pfSense</span></span>](https://www.netgate.com/docs/pfsense/loadbalancing/inbound-load-balancing.html#sticky-connections)

## <a name="redis-server-errors"></a><span data-ttu-id="c7538-156">Redis-Server Fehler</span><span class="sxs-lookup"><span data-stu-id="c7538-156">Redis server errors</span></span>

<span data-ttu-id="c7538-157">Wenn ein redis-Server ausfällt, SignalR löst Ausnahmen aus, die darauf hinweisen, dass Nachrichten nicht übermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="c7538-157">When a Redis server goes down, SignalR throws exceptions that indicate messages won't be delivered.</span></span> <span data-ttu-id="c7538-158">Einige typische Ausnahme Meldungen:</span><span class="sxs-lookup"><span data-stu-id="c7538-158">Some typical exception messages:</span></span>

* <span data-ttu-id="c7538-159">*Fehler beim Schreiben der Nachricht*</span><span class="sxs-lookup"><span data-stu-id="c7538-159">*Failed writing message*</span></span>
* <span data-ttu-id="c7538-160">*Fehler beim Aufrufen der Hub-Methode "MethodName".*</span><span class="sxs-lookup"><span data-stu-id="c7538-160">*Failed to invoke hub method 'MethodName'*</span></span>
* <span data-ttu-id="c7538-161">*Fehler bei Verbindung mit redis.*</span><span class="sxs-lookup"><span data-stu-id="c7538-161">*Connection to Redis failed*</span></span>

SignalR<span data-ttu-id="c7538-162">puffert Nachrichten nicht, um Sie zu senden, wenn der Server wieder verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="c7538-162"> doesn't buffer messages to send them when the server comes back up.</span></span> <span data-ttu-id="c7538-163">Alle Nachrichten, die gesendet werden, während der redis-Server ausfällt, gehen verloren.</span><span class="sxs-lookup"><span data-stu-id="c7538-163">Any messages sent while the Redis server is down are lost.</span></span>

SignalR<span data-ttu-id="c7538-164">die Verbindung wird automatisch erneut hergestellt, wenn der redis-Server wieder verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="c7538-164"> automatically reconnects when the Redis server is available again.</span></span>

### <a name="custom-behavior-for-connection-failures"></a><span data-ttu-id="c7538-165">Benutzerdefiniertes Verhalten bei Verbindungsfehlern</span><span class="sxs-lookup"><span data-stu-id="c7538-165">Custom behavior for connection failures</span></span>

<span data-ttu-id="c7538-166">Es folgt ein Beispiel, das zeigt, wie redis-Verbindungsfehler Ereignisse behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="c7538-166">Here's an example that shows how to handle Redis connection failure events.</span></span>

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

## <a name="redis-clustering"></a><span data-ttu-id="c7538-167">Redis-Clustering</span><span class="sxs-lookup"><span data-stu-id="c7538-167">Redis Clustering</span></span>

<span data-ttu-id="c7538-168">[Redis-Clustering](https://redis.io/topics/cluster-spec) ist eine Methode zum Erreichen von Hochverfügbarkeit mithilfe mehrerer redis-Server.</span><span class="sxs-lookup"><span data-stu-id="c7538-168">[Redis Clustering](https://redis.io/topics/cluster-spec) is a method for achieving high availability by using multiple Redis servers.</span></span> <span data-ttu-id="c7538-169">Clustering wird nicht offiziell unterstützt, kann jedoch funktionieren.</span><span class="sxs-lookup"><span data-stu-id="c7538-169">Clustering isn't officially supported, but it might work.</span></span>

## <a name="next-steps"></a><span data-ttu-id="c7538-170">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="c7538-170">Next steps</span></span>

<span data-ttu-id="c7538-171">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="c7538-171">For more information, see the following resources:</span></span>

* <xref:signalr/scale>
* [<span data-ttu-id="c7538-172">Redis-Dokumentation</span><span class="sxs-lookup"><span data-stu-id="c7538-172">Redis documentation</span></span>](https://redis.io/documentation)
* [<span data-ttu-id="c7538-173">Dokumentation zu stackexchange redis</span><span class="sxs-lookup"><span data-stu-id="c7538-173">StackExchange Redis documentation</span></span>](https://stackexchange.github.io/StackExchange.Redis/)
* [<span data-ttu-id="c7538-174">Dokumentation zu Azure Redis Cache</span><span class="sxs-lookup"><span data-stu-id="c7538-174">Azure Redis Cache documentation</span></span>](https://docs.microsoft.com/azure/redis-cache/)

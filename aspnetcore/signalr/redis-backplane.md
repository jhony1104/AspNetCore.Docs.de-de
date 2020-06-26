---
title: Redis-Rückwand für ASP.net Core horizontales hoch SignalR skalieren
author: bradygaster
description: Erfahren Sie, wie Sie eine redis-Rückwand einrichten, um das horizontale hochskalieren für eine ASP.net Core-APP zu aktivieren SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/redis-backplane
ms.openlocfilehash: 58c1ff2c9334e75535f6e5f0f418976176822724
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408473"
---
# <a name="set-up-a-redis-backplane-for-aspnet-core-signalr-scale-out"></a><span data-ttu-id="adff4-103">Einrichten einer redis-Rückwand für die ASP.net Core horizontales hoch SignalR skalieren</span><span class="sxs-lookup"><span data-stu-id="adff4-103">Set up a Redis backplane for ASP.NET Core SignalR scale-out</span></span>

<span data-ttu-id="adff4-104">Von [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaester](https://twitter.com/bradygaster)und [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="adff4-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), and [Tom Dykstra](https://github.com/tdykstra),</span></span>

<span data-ttu-id="adff4-105">In diesem Artikel SignalR werden spezifische Aspekte der Einrichtung eines [redis](https://redis.io/) -Servers erläutert, der zum horizontalen Skalieren einer ASP.net Core-App verwendet werden soll SignalR .</span><span class="sxs-lookup"><span data-stu-id="adff4-105">This article explains SignalR-specific aspects of setting up a [Redis](https://redis.io/) server to use for scaling out an ASP.NET Core SignalR app.</span></span>

## <a name="set-up-a-redis-backplane"></a><span data-ttu-id="adff4-106">Einrichten einer redis-Rückwand</span><span class="sxs-lookup"><span data-stu-id="adff4-106">Set up a Redis backplane</span></span>

* <span data-ttu-id="adff4-107">Stellen Sie einen redis-Server bereit.</span><span class="sxs-lookup"><span data-stu-id="adff4-107">Deploy a Redis server.</span></span>

  > [!IMPORTANT] 
  > <span data-ttu-id="adff4-108">Für den Einsatz in der Produktion wird eine redis-Rückwand nur dann empfohlen, wenn Sie im selben Rechenzentrum wie die app ausgeführt wird SignalR .</span><span class="sxs-lookup"><span data-stu-id="adff4-108">For production use, a Redis backplane is recommended only when it runs in the same data center as the SignalR app.</span></span> <span data-ttu-id="adff4-109">Andernfalls beeinträchtigt die Netzwerk Latenz die Leistung.</span><span class="sxs-lookup"><span data-stu-id="adff4-109">Otherwise, network latency degrades performance.</span></span> <span data-ttu-id="adff4-110">Wenn Ihre SignalR app in der Azure-Cloud ausgeführt wird, empfehlen wir den Azure- SignalR Dienst anstelle einer redis-Backplane.</span><span class="sxs-lookup"><span data-stu-id="adff4-110">If your SignalR app is running in the Azure cloud, we recommend Azure SignalR Service instead of a Redis backplane.</span></span> <span data-ttu-id="adff4-111">Sie können den Azure redis Cache-Dienst für Entwicklungs-und Testumgebungen verwenden.</span><span class="sxs-lookup"><span data-stu-id="adff4-111">You can use the Azure Redis Cache Service for development and test environments.</span></span>

  <span data-ttu-id="adff4-112">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="adff4-112">For more information, see the following resources:</span></span>

  * <xref:signalr/scale>
  * [<span data-ttu-id="adff4-113">Redis-Dokumentation</span><span class="sxs-lookup"><span data-stu-id="adff4-113">Redis documentation</span></span>](https://redis.io/)
  * [<span data-ttu-id="adff4-114">Dokumentation zu Azure Redis Cache</span><span class="sxs-lookup"><span data-stu-id="adff4-114">Azure Redis Cache documentation</span></span>](https://docs.microsoft.com/azure/redis-cache/)

::: moniker range="= aspnetcore-2.1"

* <span data-ttu-id="adff4-115">Installieren Sie SignalR das nuget-Paket in der APP `Microsoft.AspNetCore.SignalR.Redis` .</span><span class="sxs-lookup"><span data-stu-id="adff4-115">In the SignalR app, install the `Microsoft.AspNetCore.SignalR.Redis` NuGet package.</span></span>
* <span data-ttu-id="adff4-116">In der- `Startup.ConfigureServices` Methode wird `AddRedis` nach aufgerufen `AddSignalR` :</span><span class="sxs-lookup"><span data-stu-id="adff4-116">In the `Startup.ConfigureServices` method, call `AddRedis` after `AddSignalR`:</span></span>

  ```csharp
  services.AddSignalR().AddRedis("<your_Redis_connection_string>");
  ```

* <span data-ttu-id="adff4-117">Konfigurieren Sie die Optionen nach Bedarf:</span><span class="sxs-lookup"><span data-stu-id="adff4-117">Configure options as needed:</span></span>
 
  <span data-ttu-id="adff4-118">Die meisten Optionen können in der Verbindungs Zeichenfolge oder im [configurationoptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) -Objekt festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="adff4-118">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="adff4-119">Die in angegebenen Optionen über `ConfigurationOptions` schreiben die in der Verbindungs Zeichenfolge festgelegten Optionen.</span><span class="sxs-lookup"><span data-stu-id="adff4-119">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="adff4-120">Im folgenden Beispiel wird gezeigt, wie Optionen im-Objekt festgelegt werden `ConfigurationOptions` .</span><span class="sxs-lookup"><span data-stu-id="adff4-120">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="adff4-121">In diesem Beispiel wird ein Kanal Präfix hinzugefügt, sodass mehrere apps dieselbe redis-Instanz gemeinsam nutzen können, wie im folgenden Schritt erläutert.</span><span class="sxs-lookup"><span data-stu-id="adff4-121">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="adff4-122">Im vorangehenden Code `options.Configuration` wird mit dem, was in der Verbindungs Zeichenfolge angegeben wurde, initialisiert.</span><span class="sxs-lookup"><span data-stu-id="adff4-122">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* <span data-ttu-id="adff4-123">Installieren Sie in der SignalR App eines der folgenden nuget-Pakete:</span><span class="sxs-lookup"><span data-stu-id="adff4-123">In the SignalR app, install one of the following NuGet packages:</span></span>

  * <span data-ttu-id="adff4-124">`Microsoft.AspNetCore.SignalR.StackExchangeRedis`-Abhängig von stackexchange. redis 2. x</span><span class="sxs-lookup"><span data-stu-id="adff4-124">`Microsoft.AspNetCore.SignalR.StackExchangeRedis` - Depends on StackExchange.Redis 2.X.X.</span></span> <span data-ttu-id="adff4-125">Dies ist das empfohlene Paket für ASP.net Core 2,2 und höher.</span><span class="sxs-lookup"><span data-stu-id="adff4-125">This is the recommended package for ASP.NET Core 2.2 and later.</span></span>
  * <span data-ttu-id="adff4-126">`Microsoft.AspNetCore.SignalR.Redis`-Abhängig von stackexchange. redis 1. x</span><span class="sxs-lookup"><span data-stu-id="adff4-126">`Microsoft.AspNetCore.SignalR.Redis` - Depends on StackExchange.Redis 1.X.X.</span></span> <span data-ttu-id="adff4-127">Dieses Paket ist nicht in ASP.net Core 3,0 und höher enthalten.</span><span class="sxs-lookup"><span data-stu-id="adff4-127">This package isn't included in ASP.NET Core 3.0 and later.</span></span>

* <span data-ttu-id="adff4-128">In der- `Startup.ConfigureServices` Methode <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*> :</span><span class="sxs-lookup"><span data-stu-id="adff4-128">In the `Startup.ConfigureServices` method, call <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>:</span></span>

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```

 <span data-ttu-id="adff4-129">Wenn Sie verwenden `Microsoft.AspNetCore.SignalR.Redis` , wird aufgerufen <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*> .</span><span class="sxs-lookup"><span data-stu-id="adff4-129">When using `Microsoft.AspNetCore.SignalR.Redis`, call <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>.</span></span>

* <span data-ttu-id="adff4-130">Konfigurieren Sie die Optionen nach Bedarf:</span><span class="sxs-lookup"><span data-stu-id="adff4-130">Configure options as needed:</span></span>
 
  <span data-ttu-id="adff4-131">Die meisten Optionen können in der Verbindungs Zeichenfolge oder im [configurationoptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) -Objekt festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="adff4-131">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="adff4-132">Die in angegebenen Optionen über `ConfigurationOptions` schreiben die in der Verbindungs Zeichenfolge festgelegten Optionen.</span><span class="sxs-lookup"><span data-stu-id="adff4-132">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="adff4-133">Im folgenden Beispiel wird gezeigt, wie Optionen im-Objekt festgelegt werden `ConfigurationOptions` .</span><span class="sxs-lookup"><span data-stu-id="adff4-133">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="adff4-134">In diesem Beispiel wird ein Kanal Präfix hinzugefügt, sodass mehrere apps dieselbe redis-Instanz gemeinsam nutzen können, wie im folgenden Schritt erläutert.</span><span class="sxs-lookup"><span data-stu-id="adff4-134">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

 <span data-ttu-id="adff4-135">Wenn Sie verwenden `Microsoft.AspNetCore.SignalR.Redis` , wird aufgerufen <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*> .</span><span class="sxs-lookup"><span data-stu-id="adff4-135">When using `Microsoft.AspNetCore.SignalR.Redis`, call <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>.</span></span>

  <span data-ttu-id="adff4-136">Im vorangehenden Code `options.Configuration` wird mit dem, was in der Verbindungs Zeichenfolge angegeben wurde, initialisiert.</span><span class="sxs-lookup"><span data-stu-id="adff4-136">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

  <span data-ttu-id="adff4-137">Weitere Informationen zu redis-Optionen finden Sie in der [stackexchange redis-Dokumentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span><span class="sxs-lookup"><span data-stu-id="adff4-137">For information about Redis options, see the [StackExchange Redis documentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="adff4-138">Installieren Sie in der SignalR App das folgende nuget-Paket:</span><span class="sxs-lookup"><span data-stu-id="adff4-138">In the SignalR app, install the following NuGet package:</span></span>

  * `Microsoft.AspNetCore.SignalR.StackExchangeRedis`
  
* <span data-ttu-id="adff4-139">In der- `Startup.ConfigureServices` Methode <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*> :</span><span class="sxs-lookup"><span data-stu-id="adff4-139">In the `Startup.ConfigureServices` method, call <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>:</span></span>

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```
  
* <span data-ttu-id="adff4-140">Konfigurieren Sie die Optionen nach Bedarf:</span><span class="sxs-lookup"><span data-stu-id="adff4-140">Configure options as needed:</span></span>
 
  <span data-ttu-id="adff4-141">Die meisten Optionen können in der Verbindungs Zeichenfolge oder im [configurationoptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) -Objekt festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="adff4-141">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="adff4-142">Die in angegebenen Optionen über `ConfigurationOptions` schreiben die in der Verbindungs Zeichenfolge festgelegten Optionen.</span><span class="sxs-lookup"><span data-stu-id="adff4-142">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="adff4-143">Im folgenden Beispiel wird gezeigt, wie Optionen im-Objekt festgelegt werden `ConfigurationOptions` .</span><span class="sxs-lookup"><span data-stu-id="adff4-143">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="adff4-144">In diesem Beispiel wird ein Kanal Präfix hinzugefügt, sodass mehrere apps dieselbe redis-Instanz gemeinsam nutzen können, wie im folgenden Schritt erläutert.</span><span class="sxs-lookup"><span data-stu-id="adff4-144">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="adff4-145">Im vorangehenden Code `options.Configuration` wird mit dem, was in der Verbindungs Zeichenfolge angegeben wurde, initialisiert.</span><span class="sxs-lookup"><span data-stu-id="adff4-145">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

  <span data-ttu-id="adff4-146">Weitere Informationen zu redis-Optionen finden Sie in der [stackexchange redis-Dokumentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span><span class="sxs-lookup"><span data-stu-id="adff4-146">For information about Redis options, see the [StackExchange Redis documentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span></span>

::: moniker-end

* <span data-ttu-id="adff4-147">Wenn Sie einen redis-Server für mehrere SignalR Apps verwenden, verwenden Sie für jede APP ein anderes channelpräfix SignalR .</span><span class="sxs-lookup"><span data-stu-id="adff4-147">If you're using one Redis server for multiple SignalR apps, use a different channel prefix for each SignalR app.</span></span>

  <span data-ttu-id="adff4-148">Wenn Sie ein Kanal Präfix festlegen SignalR , wird eine APP von anderen Personen isoliert, die unterschiedliche channelpräfixe verwenden.</span><span class="sxs-lookup"><span data-stu-id="adff4-148">Setting a channel prefix isolates one SignalR app from others that use different channel prefixes.</span></span> <span data-ttu-id="adff4-149">Wenn Sie keine unterschiedlichen Präfixe zuweisen, wird eine Nachricht, die von einer APP an alle Ihre eigenen Clients gesendet wird, an alle Clients aller apps gesendet, die den redis-Server als Rück Ebene verwenden.</span><span class="sxs-lookup"><span data-stu-id="adff4-149">If you don't assign different prefixes, a message sent from one app to all of its own clients will go to all clients of all apps that use the Redis server as a backplane.</span></span>

* <span data-ttu-id="adff4-150">Konfigurieren Sie die Lasten Ausgleichs Software der Serverfarm für persistente Sitzungen.</span><span class="sxs-lookup"><span data-stu-id="adff4-150">Configure your server farm load balancing software for sticky sessions.</span></span> <span data-ttu-id="adff4-151">Im folgenden finden Sie einige Beispiele für die Vorgehensweise:</span><span class="sxs-lookup"><span data-stu-id="adff4-151">Here are some examples of documentation on how to do that:</span></span>

  * [<span data-ttu-id="adff4-152">IIS</span><span class="sxs-lookup"><span data-stu-id="adff4-152">IIS</span></span>](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)
  * [<span data-ttu-id="adff4-153">HAProxy</span><span class="sxs-lookup"><span data-stu-id="adff4-153">HAProxy</span></span>](https://www.haproxy.com/blog/load-balancing-affinity-persistence-sticky-sessions-what-you-need-to-know/)
  * [<span data-ttu-id="adff4-154">Nginx</span><span class="sxs-lookup"><span data-stu-id="adff4-154">Nginx</span></span>](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/#sticky)
  * [<span data-ttu-id="adff4-155">pfSense</span><span class="sxs-lookup"><span data-stu-id="adff4-155">pfSense</span></span>](https://www.netgate.com/docs/pfsense/loadbalancing/inbound-load-balancing.html#sticky-connections)

## <a name="redis-server-errors"></a><span data-ttu-id="adff4-156">Redis-Server Fehler</span><span class="sxs-lookup"><span data-stu-id="adff4-156">Redis server errors</span></span>

<span data-ttu-id="adff4-157">Wenn ein redis-Server ausfällt, löst Ausnahmen aus, SignalR die darauf hinweisen, dass Nachrichten nicht übermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="adff4-157">When a Redis server goes down, SignalR throws exceptions that indicate messages won't be delivered.</span></span> <span data-ttu-id="adff4-158">Einige typische Ausnahme Meldungen:</span><span class="sxs-lookup"><span data-stu-id="adff4-158">Some typical exception messages:</span></span>

* <span data-ttu-id="adff4-159">*Fehler beim Schreiben der Nachricht*</span><span class="sxs-lookup"><span data-stu-id="adff4-159">*Failed writing message*</span></span>
* <span data-ttu-id="adff4-160">*Fehler beim Aufrufen der Hub-Methode "MethodName".*</span><span class="sxs-lookup"><span data-stu-id="adff4-160">*Failed to invoke hub method 'MethodName'*</span></span>
* <span data-ttu-id="adff4-161">*Fehler bei Verbindung mit redis.*</span><span class="sxs-lookup"><span data-stu-id="adff4-161">*Connection to Redis failed*</span></span>

SignalR<span data-ttu-id="adff4-162">puffert Nachrichten nicht, um Sie zu senden, wenn der Server wieder verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="adff4-162"> doesn't buffer messages to send them when the server comes back up.</span></span> <span data-ttu-id="adff4-163">Alle Nachrichten, die gesendet werden, während der redis-Server ausfällt, gehen verloren.</span><span class="sxs-lookup"><span data-stu-id="adff4-163">Any messages sent while the Redis server is down are lost.</span></span>

SignalR<span data-ttu-id="adff4-164">die Verbindung wird automatisch erneut hergestellt, wenn der redis-Server wieder verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="adff4-164"> automatically reconnects when the Redis server is available again.</span></span>

### <a name="custom-behavior-for-connection-failures"></a><span data-ttu-id="adff4-165">Benutzerdefiniertes Verhalten bei Verbindungsfehlern</span><span class="sxs-lookup"><span data-stu-id="adff4-165">Custom behavior for connection failures</span></span>

<span data-ttu-id="adff4-166">Es folgt ein Beispiel, das zeigt, wie redis-Verbindungsfehler Ereignisse behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="adff4-166">Here's an example that shows how to handle Redis connection failure events.</span></span>

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

## <a name="redis-clustering"></a><span data-ttu-id="adff4-167">Redis-Clustering</span><span class="sxs-lookup"><span data-stu-id="adff4-167">Redis Clustering</span></span>

<span data-ttu-id="adff4-168">[Redis-Clustering](https://redis.io/topics/cluster-spec) ist eine Methode zum Erreichen von Hochverfügbarkeit mithilfe mehrerer redis-Server.</span><span class="sxs-lookup"><span data-stu-id="adff4-168">[Redis Clustering](https://redis.io/topics/cluster-spec) is a method for achieving high availability by using multiple Redis servers.</span></span> <span data-ttu-id="adff4-169">Clustering wird nicht offiziell unterstützt, kann jedoch funktionieren.</span><span class="sxs-lookup"><span data-stu-id="adff4-169">Clustering isn't officially supported, but it might work.</span></span>

## <a name="next-steps"></a><span data-ttu-id="adff4-170">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="adff4-170">Next steps</span></span>

<span data-ttu-id="adff4-171">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="adff4-171">For more information, see the following resources:</span></span>

* <xref:signalr/scale>
* [<span data-ttu-id="adff4-172">Redis-Dokumentation</span><span class="sxs-lookup"><span data-stu-id="adff4-172">Redis documentation</span></span>](https://redis.io/documentation)
* [<span data-ttu-id="adff4-173">Dokumentation zu stackexchange redis</span><span class="sxs-lookup"><span data-stu-id="adff4-173">StackExchange Redis documentation</span></span>](https://stackexchange.github.io/StackExchange.Redis/)
* [<span data-ttu-id="adff4-174">Dokumentation zu Azure Redis Cache</span><span class="sxs-lookup"><span data-stu-id="adff4-174">Azure Redis Cache documentation</span></span>](https://docs.microsoft.com/azure/redis-cache/)

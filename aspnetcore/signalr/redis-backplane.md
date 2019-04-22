---
title: Redis-Backplane für ASP.NET Core SignalR-Skalierung
author: bradygaster
description: Erfahren Sie, wie Sie eine Redis-Rückwandplatine einrichten, um die horizontale Skalierung für eine ASP.NET Core SignalR-app zu aktivieren.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/28/2018
uid: signalr/redis-backplane
ms.openlocfilehash: 9d2a942dba6abe669126efee7f2b3cdd6560658e
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59012642"
---
# <a name="set-up-a-redis-backplane-for-aspnet-core-signalr-scale-out"></a><span data-ttu-id="66acb-103">Richten Sie einen Redis-Backplane für ASP.NET Core SignalR-Skalierung</span><span class="sxs-lookup"><span data-stu-id="66acb-103">Set up a Redis backplane for ASP.NET Core SignalR scale-out</span></span>

<span data-ttu-id="66acb-104">Durch [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), und [Tom Dykstra](https://github.com/tdykstra),</span><span class="sxs-lookup"><span data-stu-id="66acb-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), and [Tom Dykstra](https://github.com/tdykstra),</span></span>

<span data-ttu-id="66acb-105">In diesem Artikel wird erläutert, SignalR-spezifische Aspekte der Einrichtung einer [Redis](https://redis.io/) Server für das horizontale Skalieren einer ASP.NET Core SignalR-app verwenden.</span><span class="sxs-lookup"><span data-stu-id="66acb-105">This article explains SignalR-specific aspects of setting up a [Redis](https://redis.io/) server to use for scaling out an ASP.NET Core SignalR app.</span></span>

## <a name="set-up-a-redis-backplane"></a><span data-ttu-id="66acb-106">Richten Sie eine Redis-Rückwandplatine</span><span class="sxs-lookup"><span data-stu-id="66acb-106">Set up a Redis backplane</span></span>

* <span data-ttu-id="66acb-107">Bereitstellen eines Redis-Servers.</span><span class="sxs-lookup"><span data-stu-id="66acb-107">Deploy a Redis server.</span></span>

  > [!IMPORTANT] 
  > <span data-ttu-id="66acb-108">Für die Produktion wird eine Redis-Rückwandplatine empfohlen, nur, wenn er im selben Rechenzentrum wie die SignalR-app ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="66acb-108">For production use, a Redis backplane is recommended only when it runs in the same data center as the SignalR app.</span></span> <span data-ttu-id="66acb-109">Andernfalls wird die Netzwerklatenz die Leistung beeinträchtigt.</span><span class="sxs-lookup"><span data-stu-id="66acb-109">Otherwise, network latency degrades performance.</span></span> <span data-ttu-id="66acb-110">Wenn Ihre SignalR-app in der Azure-Cloud ausgeführt wird, empfehlen wir anstelle einer Redis-Rückwandplatine Azure SignalR Service.</span><span class="sxs-lookup"><span data-stu-id="66acb-110">If your SignalR app is running in the Azure cloud, we recommend Azure SignalR Service instead of a Redis backplane.</span></span> <span data-ttu-id="66acb-111">Sie können den Azure Redis Cache-Dienst verwenden, für die Entwicklung und testumgebungen.</span><span class="sxs-lookup"><span data-stu-id="66acb-111">You can use the Azure Redis Cache Service for development and test environments.</span></span>

  <span data-ttu-id="66acb-112">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="66acb-112">For more information, see the following resources:</span></span>

  * <xref:signalr/scale>
  * [<span data-ttu-id="66acb-113">Dokumentation zu redis</span><span class="sxs-lookup"><span data-stu-id="66acb-113">Redis documentation</span></span>](https://redis.io/)
  * [<span data-ttu-id="66acb-114">Azure Redis Cache-Dokumentation</span><span class="sxs-lookup"><span data-stu-id="66acb-114">Azure Redis Cache documentation</span></span>](https://docs.microsoft.com/azure/redis-cache/)

::: moniker range="= aspnetcore-2.1"

* <span data-ttu-id="66acb-115">Installieren Sie in der SignalR-app die `Microsoft.AspNetCore.SignalR.Redis` NuGet-Paket.</span><span class="sxs-lookup"><span data-stu-id="66acb-115">In the SignalR app, install the `Microsoft.AspNetCore.SignalR.Redis` NuGet package.</span></span> <span data-ttu-id="66acb-116">(Es gibt auch eine `Microsoft.AspNetCore.SignalR.StackExchangeRedis` Verpacken, aber, dass für ASP.NET Core 2.2 und höher ist.)</span><span class="sxs-lookup"><span data-stu-id="66acb-116">(There is also a `Microsoft.AspNetCore.SignalR.StackExchangeRedis` package, but that one is for ASP.NET Core 2.2 and later.)</span></span>

* <span data-ttu-id="66acb-117">In der `Startup.ConfigureServices` -Methode, rufen `AddRedis` nach `AddSignalR`:</span><span class="sxs-lookup"><span data-stu-id="66acb-117">In the `Startup.ConfigureServices` method, call `AddRedis` after `AddSignalR`:</span></span>

  ```csharp
  services.AddSignalR().AddRedis("<your_Redis_connection_string>");
  ```

* <span data-ttu-id="66acb-118">Konfigurieren Sie Optionen aus, je nach Bedarf:</span><span class="sxs-lookup"><span data-stu-id="66acb-118">Configure options as needed:</span></span>
 
  <span data-ttu-id="66acb-119">Die meisten Optionen können festgelegt werden, in der Verbindungszeichenfolge oder in der [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) Objekt.</span><span class="sxs-lookup"><span data-stu-id="66acb-119">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="66acb-120">Optionen, die im angegebenen `ConfigurationOptions` legen Sie in der Verbindungszeichenfolge überschreiben.</span><span class="sxs-lookup"><span data-stu-id="66acb-120">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="66acb-121">Das folgende Beispiel zeigt, wie Sie Optionen festlegen, der `ConfigurationOptions` Objekt.</span><span class="sxs-lookup"><span data-stu-id="66acb-121">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="66acb-122">Dieses Beispiel fügt eine Kanalpräfix, damit mehrere Anwendungen dieselbe Redis-Instanz freigeben können wie im nächsten Schritt beschrieben.</span><span class="sxs-lookup"><span data-stu-id="66acb-122">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="66acb-123">Im vorangehenden Code `options.Configuration` wird initialisiert, indem Sie den Inhalt in der Verbindungszeichenfolge angegeben wurde.</span><span class="sxs-lookup"><span data-stu-id="66acb-123">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.1"

* <span data-ttu-id="66acb-124">Installieren Sie eine der folgenden NuGet-Pakete an, in der SignalR-app:</span><span class="sxs-lookup"><span data-stu-id="66acb-124">In the SignalR app, install one of the following NuGet packages:</span></span>

  * <span data-ttu-id="66acb-125">`Microsoft.AspNetCore.SignalR.StackExchangeRedis` – Hängt von "stackexchange.redis" 2.X.X ab.</span><span class="sxs-lookup"><span data-stu-id="66acb-125">`Microsoft.AspNetCore.SignalR.StackExchangeRedis` - Depends on StackExchange.Redis 2.X.X.</span></span> <span data-ttu-id="66acb-126">Dies ist das empfohlene Paket für ASP.NET Core 2.2 und höher.</span><span class="sxs-lookup"><span data-stu-id="66acb-126">This is the recommended package for ASP.NET Core 2.2 and later.</span></span>
  * <span data-ttu-id="66acb-127">`Microsoft.AspNetCore.SignalR.Redis` – Hängt von "stackexchange.redis" 1.X.X ab.</span><span class="sxs-lookup"><span data-stu-id="66acb-127">`Microsoft.AspNetCore.SignalR.Redis` - Depends on StackExchange.Redis 1.X.X.</span></span> <span data-ttu-id="66acb-128">Dieses Paket wird nicht in ASP.NET Core 3.0 schicken.</span><span class="sxs-lookup"><span data-stu-id="66acb-128">This package will not be shipping in ASP.NET Core 3.0.</span></span>

* <span data-ttu-id="66acb-129">In der `Startup.ConfigureServices` -Methode, rufen `AddStackExchangeRedis` nach `AddSignalR`:</span><span class="sxs-lookup"><span data-stu-id="66acb-129">In the `Startup.ConfigureServices` method, call `AddStackExchangeRedis` after `AddSignalR`:</span></span>

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```

* <span data-ttu-id="66acb-130">Konfigurieren Sie Optionen aus, je nach Bedarf:</span><span class="sxs-lookup"><span data-stu-id="66acb-130">Configure options as needed:</span></span>
 
  <span data-ttu-id="66acb-131">Die meisten Optionen können festgelegt werden, in der Verbindungszeichenfolge oder in der [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) Objekt.</span><span class="sxs-lookup"><span data-stu-id="66acb-131">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="66acb-132">Optionen, die im angegebenen `ConfigurationOptions` legen Sie in der Verbindungszeichenfolge überschreiben.</span><span class="sxs-lookup"><span data-stu-id="66acb-132">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="66acb-133">Das folgende Beispiel zeigt, wie Sie Optionen festlegen, der `ConfigurationOptions` Objekt.</span><span class="sxs-lookup"><span data-stu-id="66acb-133">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="66acb-134">Dieses Beispiel fügt eine Kanalpräfix, damit mehrere Anwendungen dieselbe Redis-Instanz freigeben können wie im nächsten Schritt beschrieben.</span><span class="sxs-lookup"><span data-stu-id="66acb-134">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="66acb-135">Im vorangehenden Code `options.Configuration` wird initialisiert, indem Sie den Inhalt in der Verbindungszeichenfolge angegeben wurde.</span><span class="sxs-lookup"><span data-stu-id="66acb-135">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

  <span data-ttu-id="66acb-136">Weitere Informationen zu Redis-Optionen finden Sie unter den [StackExchange Redis-Dokumentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span><span class="sxs-lookup"><span data-stu-id="66acb-136">For information about Redis options, see the [StackExchange Redis documentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span></span>

::: moniker-end

* <span data-ttu-id="66acb-137">Wenn Sie einen Redis-Server für mehrere SignalR-apps verwenden, verwenden Sie einen anderen Kanal-Präfix für die einzelnen SignalR-Apps.</span><span class="sxs-lookup"><span data-stu-id="66acb-137">If you're using one Redis server for multiple SignalR apps, use a different channel prefix for each SignalR app.</span></span>

  <span data-ttu-id="66acb-138">Festlegen von einem Kanalpräfix isoliert eine SignalR-app von anderen Benutzern, die Präfixe der anderen Kanal zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="66acb-138">Setting a channel prefix isolates one SignalR app from others that use different channel prefixes.</span></span> <span data-ttu-id="66acb-139">Wenn Sie keine unterschiedliche Präfixen zuweisen, geht aus einer app an alle seine Clients gesendete Nachrichten an alle Clients für alle apps, die den Redis-Server als eine Rückwandplatine verwenden.</span><span class="sxs-lookup"><span data-stu-id="66acb-139">If you don't assign different prefixes, a message sent from one app to all of its own clients will go to all clients of all apps that use the Redis server as a backplane.</span></span>

* <span data-ttu-id="66acb-140">Konfigurieren Sie Ihre Server Webfarm-Lastenausgleichs Software für persistente Sitzungen.</span><span class="sxs-lookup"><span data-stu-id="66acb-140">Configure your server farm load balancing software for sticky sessions.</span></span> <span data-ttu-id="66acb-141">Hier sind einige Beispiele der Dokumentation zur Vorgehensweise, die ein:</span><span class="sxs-lookup"><span data-stu-id="66acb-141">Here are some examples of documentation on how to do that:</span></span>

  * [<span data-ttu-id="66acb-142">IIS</span><span class="sxs-lookup"><span data-stu-id="66acb-142">IIS</span></span>](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)
  * [<span data-ttu-id="66acb-143">HAProxy</span><span class="sxs-lookup"><span data-stu-id="66acb-143">HAProxy</span></span>](https://www.haproxy.com/blog/load-balancing-affinity-persistence-sticky-sessions-what-you-need-to-know/)
  * [<span data-ttu-id="66acb-144">Nginx</span><span class="sxs-lookup"><span data-stu-id="66acb-144">Nginx</span></span>](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/#sticky)
  * [<span data-ttu-id="66acb-145">pfSense</span><span class="sxs-lookup"><span data-stu-id="66acb-145">pfSense</span></span>](https://www.netgate.com/docs/pfsense/loadbalancing/inbound-load-balancing.html#sticky-connections)

## <a name="redis-server-errors"></a><span data-ttu-id="66acb-146">Redis-Server-Fehler</span><span class="sxs-lookup"><span data-stu-id="66acb-146">Redis server errors</span></span>

<span data-ttu-id="66acb-147">Wenn ein Redis-Server ausfällt, löst SignalR Ausnahmen, die angeben, dass die Nachricht übermittelt werden, wird nicht aus.</span><span class="sxs-lookup"><span data-stu-id="66acb-147">When a Redis server goes down, SignalR throws exceptions that indicate messages won't be delivered.</span></span> <span data-ttu-id="66acb-148">Einige typische ausnahmemeldungen:</span><span class="sxs-lookup"><span data-stu-id="66acb-148">Some typical exception messages:</span></span>

* <span data-ttu-id="66acb-149">*Fehler beim Schreiben einer Nachricht*</span><span class="sxs-lookup"><span data-stu-id="66acb-149">*Failed writing message*</span></span>
* <span data-ttu-id="66acb-150">*Fehler beim Aufrufen der hubmethode 'Methodenname'*</span><span class="sxs-lookup"><span data-stu-id="66acb-150">*Failed to invoke hub method 'MethodName'*</span></span>
* <span data-ttu-id="66acb-151">*Fehler bei der Verbindung mit Redis*</span><span class="sxs-lookup"><span data-stu-id="66acb-151">*Connection to Redis failed*</span></span>

<span data-ttu-id="66acb-152">SignalR anforderungsinhaltsdatenstrom nicht puffert, Nachrichten an diese senden, wenn der Server wieder verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="66acb-152">SignalR doesn't buffer messages to send them when the server comes back up.</span></span> <span data-ttu-id="66acb-153">Alle Nachrichten, die gesendet, während sich der Redis-Server ausgefallen ist, gehen verloren.</span><span class="sxs-lookup"><span data-stu-id="66acb-153">Any messages sent while the Redis server is down are lost.</span></span>

<span data-ttu-id="66acb-154">SignalR stellt automatisch wieder her, wenn der Redis-Server wieder verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="66acb-154">SignalR automatically reconnects when the Redis server is available again.</span></span>

### <a name="custom-behavior-for-connection-failures"></a><span data-ttu-id="66acb-155">Benutzerdefiniertes Verhalten für Verbindungsfehler</span><span class="sxs-lookup"><span data-stu-id="66acb-155">Custom behavior for connection failures</span></span>

<span data-ttu-id="66acb-156">Hier ist ein Beispiel, das zeigt, wie Ereignisse durch Fehler beim Redis-Verbindung behandelt.</span><span class="sxs-lookup"><span data-stu-id="66acb-156">Here's an example that shows how to handle Redis connection failure events.</span></span>

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

## <a name="clustering"></a><span data-ttu-id="66acb-157">Clusterbildung</span><span class="sxs-lookup"><span data-stu-id="66acb-157">Clustering</span></span>

<span data-ttu-id="66acb-158">Clustering ist eine Methode zum Erreichen der hochverfügbarkeit mit mehreren Redis-Server.</span><span class="sxs-lookup"><span data-stu-id="66acb-158">Clustering is a method for achieving high availability by using multiple Redis servers.</span></span> <span data-ttu-id="66acb-159">Clustering wird offiziell nicht unterstützt, aber es funktioniert.</span><span class="sxs-lookup"><span data-stu-id="66acb-159">Clustering isn't officially supported, but it might work.</span></span>

## <a name="next-steps"></a><span data-ttu-id="66acb-160">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="66acb-160">Next steps</span></span>

<span data-ttu-id="66acb-161">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="66acb-161">For more information, see the following resources:</span></span>

* <xref:signalr/scale>
* [<span data-ttu-id="66acb-162">Dokumentation zu redis</span><span class="sxs-lookup"><span data-stu-id="66acb-162">Redis documentation</span></span>](https://redis.io/documentation)
* [<span data-ttu-id="66acb-163">StackExchange Redis-Dokumentation</span><span class="sxs-lookup"><span data-stu-id="66acb-163">StackExchange Redis documentation</span></span>](https://stackexchange.github.io/StackExchange.Redis/)
* [<span data-ttu-id="66acb-164">Azure Redis Cache-Dokumentation</span><span class="sxs-lookup"><span data-stu-id="66acb-164">Azure Redis Cache documentation</span></span>](https://docs.microsoft.com/azure/redis-cache/)

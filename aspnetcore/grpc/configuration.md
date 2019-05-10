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
# <a name="grpc-for-aspnet-core-configuration"></a><span data-ttu-id="5b911-103">gRPC für ASP.NET Core-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="5b911-103">gRPC for ASP.NET Core configuration</span></span>

## <a name="configure-services-options"></a><span data-ttu-id="5b911-104">Konfigurieren Sie Optionen für Dienste</span><span class="sxs-lookup"><span data-stu-id="5b911-104">Configure services options</span></span>

<span data-ttu-id="5b911-105">Die folgende Tabelle beschreibt die Optionen zum Konfigurieren der gRPC-Dienste:</span><span class="sxs-lookup"><span data-stu-id="5b911-105">The following table describes options for configuring gRPC services:</span></span>

| <span data-ttu-id="5b911-106">Option</span><span class="sxs-lookup"><span data-stu-id="5b911-106">Option</span></span> | <span data-ttu-id="5b911-107">Standardwert</span><span class="sxs-lookup"><span data-stu-id="5b911-107">Default Value</span></span> | <span data-ttu-id="5b911-108">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="5b911-108">Description</span></span> |
| ------ | ------------- | ----------- |
| `SendMaxMessageSize` | `null` | <span data-ttu-id="5b911-109">Die maximale Nachrichtengröße in Bytes, die vom Server gesendet werden können.</span><span class="sxs-lookup"><span data-stu-id="5b911-109">The maximum message size in bytes that can be sent from the server.</span></span> <span data-ttu-id="5b911-110">Es wird versucht, eine Nachricht zu senden, die überschreitet die konfigurierte maximale Nachricht Größe führt zu einer Ausnahme.</span><span class="sxs-lookup"><span data-stu-id="5b911-110">Attempting to send a message that exceeds the configured maximum message size results in an exception.</span></span> |
| `ReceiveMaxMessageSize` | <span data-ttu-id="5b911-111">4 MB</span><span class="sxs-lookup"><span data-stu-id="5b911-111">4 MB</span></span> | <span data-ttu-id="5b911-112">Die maximale Nachrichtengröße in Bytes, die vom Server empfangen werden können.</span><span class="sxs-lookup"><span data-stu-id="5b911-112">The maximum message size in bytes that can be received by the server.</span></span> <span data-ttu-id="5b911-113">Wenn der Server eine Nachricht, die diesen Grenzwert überschreitet empfängt, wird eine Ausnahme ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="5b911-113">If the server receives a message that exceeds this limit, it throws an exception.</span></span> <span data-ttu-id="5b911-114">Durch Erhöhen dieses Wertes können den Server, größere Nachrichten zu empfangen, aber arbeitsspeichernutzung negativ beeinträchtigt werden kann.</span><span class="sxs-lookup"><span data-stu-id="5b911-114">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="5b911-115">Wenn `true`, detaillierte ausnahmemeldungen werden an Clients zurückgegeben, wenn eine Ausnahme in einer Dienstmethode ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="5b911-115">If `true`, detailed exception messages are returned to clients when an exception is thrown in a service method.</span></span> <span data-ttu-id="5b911-116">Die Standardeinstellung ist `false`.</span><span class="sxs-lookup"><span data-stu-id="5b911-116">The default is `false`.</span></span> <span data-ttu-id="5b911-117">Wenn dies auf `true` kann Offenlegung von vertraulichen Informationen.</span><span class="sxs-lookup"><span data-stu-id="5b911-117">Setting this to `true` can leak sensitive information.</span></span> |
| `CompressionProviders` | <span data-ttu-id="5b911-118">gzip</span><span class="sxs-lookup"><span data-stu-id="5b911-118">gzip</span></span> | <span data-ttu-id="5b911-119">Eine Auflistung von Komprimierung-Anbietern, die zum Komprimieren und Dekomprimieren von Nachrichten verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="5b911-119">A collection of compression providers used to compress and decompress messages.</span></span> <span data-ttu-id="5b911-120">Benutzerdefinierte Komprimierung-Anbieter können erstellt und der Auflistung hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="5b911-120">Custom compression providers can be created and added to the collection.</span></span> <span data-ttu-id="5b911-121">Standardmäßig konfiguriert der Anbieter unterstützt **Gzip** Komprimierung.</span><span class="sxs-lookup"><span data-stu-id="5b911-121">The default configured provider supports **gzip** compression.</span></span> |
| `ResponseCompressionAlgorithm` | `null` | <span data-ttu-id="5b911-122">Der Komprimierungsalgorithmus verwendet, um vom Server gesendeten Nachrichten zu komprimieren.</span><span class="sxs-lookup"><span data-stu-id="5b911-122">The compression algorithm used to compress messages sent from the server.</span></span> <span data-ttu-id="5b911-123">Der Algorithmus muss einen Anbieter Komprimierung in übereinstimmen `CompressionProviders`.</span><span class="sxs-lookup"><span data-stu-id="5b911-123">The algorithm must match a compression provider in `CompressionProviders`.</span></span> <span data-ttu-id="5b911-124">Für die Algorthm zum Komprimieren einer Antwort muss der Client angeben, senden Sie sie der Algorithmus unterstützt die **Grpc-accept-encoding** Header.</span><span class="sxs-lookup"><span data-stu-id="5b911-124">For the algorthm to compress a response the client must indicate it supports the algorithm by sending it in the **grpc-accept-encoding** header.</span></span> |
| `ResponseCompressionLevel` | `null` | <span data-ttu-id="5b911-125">Die Compress-Ebene verwendet, um vom Server gesendeten Nachrichten zu komprimieren.</span><span class="sxs-lookup"><span data-stu-id="5b911-125">The compress level used to compress messages sent from the server.</span></span> |

<span data-ttu-id="5b911-126">Optionen für alle Dienste konfiguriert werden können, durch die Bereitstellung einer Optionen-Delegat, der die `AddGrpc` Aufrufen in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="5b911-126">Options can be configured for all services by providing an options delegate to the `AddGrpc` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.EnableDetailedErrors = true;
    });
}
```

<span data-ttu-id="5b911-127">Optionen für einen einzelnen Dienst außer Kraft setzen die globale Optionen im `AddGrpc` und kann konfiguriert werden, mithilfe von `AddServiceOptions<TService>`:</span><span class="sxs-lookup"><span data-stu-id="5b911-127">Options for a single service override the global options provided in `AddGrpc` and can be configured using `AddServiceOptions<TService>`:</span></span>

```csharp
services.AddGrpc().AddServiceOptions<MyService>(options =>
{
    options.ReceiveMaxMessageSize = 10 * 1024 * 1024; // 10 megabytes
});
```

## <a name="configure-kestrel-options"></a><span data-ttu-id="5b911-128">Konfigurieren Sie Kestrel-Optionen</span><span class="sxs-lookup"><span data-stu-id="5b911-128">Configure Kestrel options</span></span>

<span data-ttu-id="5b911-129">Kestrel-Server verfügt über Optionen für die Konfiguration, die das Verhalten der gRPC für ASP.NET zu beeinflussen.</span><span class="sxs-lookup"><span data-stu-id="5b911-129">Kestrel server has configuration options that affect the behavior of gRPC for ASP.NET.</span></span>

### <a name="request-body-data-rate-limit"></a><span data-ttu-id="5b911-130">Für die Anforderung Text Datenrate</span><span class="sxs-lookup"><span data-stu-id="5b911-130">Request body data rate limit</span></span>

<span data-ttu-id="5b911-131">Standardmäßig legt der Kestrel-Server eine [minimale Datenrate des Anforderungstexts](
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>).</span><span class="sxs-lookup"><span data-stu-id="5b911-131">By default, the Kestrel server imposes a [minimum request body data rate](
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>).</span></span> <span data-ttu-id="5b911-132">Für streaming-Client und Duplex-streaming-Aufrufe diese Rate kann nicht erfüllt werden, und möglicherweise werden Verbindungstimeout. Die minimale-Anforderungstext für die Datenrate muss deaktiviert werden, wenn der Dienst gRPC-streaming-Client und Duplex-streaming-Aufrufe enthält:</span><span class="sxs-lookup"><span data-stu-id="5b911-132">For client streaming and duplex streaming calls, this rate may not be satisfied and the connection may be timed out. The minimum request body data rate limit must be disabled when the gRPC service includes client streaming and duplex streaming calls:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="5b911-133">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="5b911-133">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>

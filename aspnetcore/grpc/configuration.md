---
title: gRPC für ASP.NET Core-Konfiguration
author: jamesnk
description: Erfahren Sie, wie gRPC für ASP.NET Core-apps zu konfigurieren.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 5/30/2019
uid: grpc/configuration
ms.openlocfilehash: 1f8250dc9aa8b82da384ee28287011baa19dc11f
ms.sourcegitcommit: a1364109d11d414121a6337b611bee61d6e489e9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66491232"
---
# <a name="grpc-for-aspnet-core-configuration"></a><span data-ttu-id="36bf2-103">gRPC für ASP.NET Core-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="36bf2-103">gRPC for ASP.NET Core configuration</span></span>

## <a name="configure-services-options"></a><span data-ttu-id="36bf2-104">Konfigurieren Sie Optionen für Dienste</span><span class="sxs-lookup"><span data-stu-id="36bf2-104">Configure services options</span></span>

<span data-ttu-id="36bf2-105">Die folgende Tabelle beschreibt die Optionen zum Konfigurieren der gRPC-Dienste:</span><span class="sxs-lookup"><span data-stu-id="36bf2-105">The following table describes options for configuring gRPC services:</span></span>

| <span data-ttu-id="36bf2-106">Option</span><span class="sxs-lookup"><span data-stu-id="36bf2-106">Option</span></span> | <span data-ttu-id="36bf2-107">Standardwert</span><span class="sxs-lookup"><span data-stu-id="36bf2-107">Default Value</span></span> | <span data-ttu-id="36bf2-108">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="36bf2-108">Description</span></span> |
| ------ | ------------- | ----------- |
| `SendMaxMessageSize` | `null` | <span data-ttu-id="36bf2-109">Die maximale Nachrichtengröße in Bytes, die vom Server gesendet werden können.</span><span class="sxs-lookup"><span data-stu-id="36bf2-109">The maximum message size in bytes that can be sent from the server.</span></span> <span data-ttu-id="36bf2-110">Es wird versucht, eine Nachricht zu senden, die überschreitet die konfigurierte maximale Nachricht Größe führt zu einer Ausnahme.</span><span class="sxs-lookup"><span data-stu-id="36bf2-110">Attempting to send a message that exceeds the configured maximum message size results in an exception.</span></span> |
| `ReceiveMaxMessageSize` | <span data-ttu-id="36bf2-111">4 MB</span><span class="sxs-lookup"><span data-stu-id="36bf2-111">4 MB</span></span> | <span data-ttu-id="36bf2-112">Die maximale Nachrichtengröße in Bytes, die vom Server empfangen werden können.</span><span class="sxs-lookup"><span data-stu-id="36bf2-112">The maximum message size in bytes that can be received by the server.</span></span> <span data-ttu-id="36bf2-113">Wenn der Server eine Nachricht, die diesen Grenzwert überschreitet empfängt, wird eine Ausnahme ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="36bf2-113">If the server receives a message that exceeds this limit, it throws an exception.</span></span> <span data-ttu-id="36bf2-114">Durch Erhöhen dieses Wertes können den Server, größere Nachrichten zu empfangen, aber arbeitsspeichernutzung negativ beeinträchtigt werden kann.</span><span class="sxs-lookup"><span data-stu-id="36bf2-114">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="36bf2-115">Wenn `true`, detaillierte ausnahmemeldungen werden an Clients zurückgegeben, wenn eine Ausnahme in einer Dienstmethode ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="36bf2-115">If `true`, detailed exception messages are returned to clients when an exception is thrown in a service method.</span></span> <span data-ttu-id="36bf2-116">Die Standardeinstellung ist `false`.</span><span class="sxs-lookup"><span data-stu-id="36bf2-116">The default is `false`.</span></span> <span data-ttu-id="36bf2-117">Festlegen von `EnableDetailedErrors` zu `true` kann Offenlegung von vertraulichen Informationen.</span><span class="sxs-lookup"><span data-stu-id="36bf2-117">Setting `EnableDetailedErrors` to `true` can leak sensitive information.</span></span> |
| `CompressionProviders` | <span data-ttu-id="36bf2-118">gzip</span><span class="sxs-lookup"><span data-stu-id="36bf2-118">gzip</span></span> | <span data-ttu-id="36bf2-119">Eine Auflistung von Komprimierung-Anbietern, die zum Komprimieren und Dekomprimieren von Nachrichten verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="36bf2-119">A collection of compression providers used to compress and decompress messages.</span></span> <span data-ttu-id="36bf2-120">Benutzerdefinierte Komprimierung-Anbieter können erstellt und der Auflistung hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="36bf2-120">Custom compression providers can be created and added to the collection.</span></span> <span data-ttu-id="36bf2-121">Standardmäßig konfiguriert der Anbieter unterstützt **Gzip** Komprimierung.</span><span class="sxs-lookup"><span data-stu-id="36bf2-121">The default configured provider supports **gzip** compression.</span></span> |
| `ResponseCompressionAlgorithm` | `null` | <span data-ttu-id="36bf2-122">Der Komprimierungsalgorithmus verwendet, um vom Server gesendeten Nachrichten zu komprimieren.</span><span class="sxs-lookup"><span data-stu-id="36bf2-122">The compression algorithm used to compress messages sent from the server.</span></span> <span data-ttu-id="36bf2-123">Der Algorithmus muss einen Anbieter Komprimierung in übereinstimmen `CompressionProviders`.</span><span class="sxs-lookup"><span data-stu-id="36bf2-123">The algorithm must match a compression provider in `CompressionProviders`.</span></span> <span data-ttu-id="36bf2-124">Für den Algorithmus, um eine Antwort zu komprimieren, muss der Client angeben, senden Sie sie der Algorithmus unterstützt die **Grpc-accept-encoding** Header.</span><span class="sxs-lookup"><span data-stu-id="36bf2-124">For the algorithm to compress a response, the client must indicate it supports the algorithm by sending it in the **grpc-accept-encoding** header.</span></span> |
| `ResponseCompressionLevel` | `null` | <span data-ttu-id="36bf2-125">Die Compress-Ebene verwendet, um vom Server gesendeten Nachrichten zu komprimieren.</span><span class="sxs-lookup"><span data-stu-id="36bf2-125">The compress level used to compress messages sent from the server.</span></span> |

<span data-ttu-id="36bf2-126">Optionen für alle Dienste konfiguriert werden können, durch die Bereitstellung einer Optionen-Delegat, der die `AddGrpc` Aufrufen in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="36bf2-126">Options can be configured for all services by providing an options delegate to the `AddGrpc` call in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

<span data-ttu-id="36bf2-127">Optionen für einen einzelnen Dienst außer Kraft setzen die globale Optionen im `AddGrpc` und kann konfiguriert werden, mithilfe von `AddServiceOptions<TService>`:</span><span class="sxs-lookup"><span data-stu-id="36bf2-127">Options for a single service override the global options provided in `AddGrpc` and can be configured using `AddServiceOptions<TService>`:</span></span>

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a><span data-ttu-id="36bf2-128">Konfigurieren Sie Clientoptionen</span><span class="sxs-lookup"><span data-stu-id="36bf2-128">Configure client options</span></span>

<span data-ttu-id="36bf2-129">Der folgende Code legt die maximale Client senden und Empfangen der Nachrichtengröße:</span><span class="sxs-lookup"><span data-stu-id="36bf2-129">The following code sets the client maximum send and receive message size:</span></span>

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-6)]

## <a name="additional-resources"></a><span data-ttu-id="36bf2-130">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="36bf2-130">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>

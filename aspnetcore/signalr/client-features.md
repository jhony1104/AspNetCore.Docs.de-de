---
title: SignalR-Clientfunktionen
author: bradygaster
description: Erfahren Sie, welche Funktionen von den verschiedenen ASP.net Core signalr-Clients unterstützt werden.
monikerRange: '>= aspnetcore-3.0'
ms.author: bradyg
ms.custom: mvc
ms.date: 09/18/2019
uid: signalr/client-features
ms.openlocfilehash: 2d6759a5484c37aee6db3d22b3127414231605ae
ms.sourcegitcommit: 14b25156e34c82ed0495b4aff5776ac5b1950b5e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/26/2019
ms.locfileid: "71301188"
---
# <a name="aspnet-core-signalr-client-features"></a><span data-ttu-id="629b1-103">ASP.net Core signalr-Client Funktionen</span><span class="sxs-lookup"><span data-stu-id="629b1-103">ASP.NET Core SignalR client features</span></span>

## <a name="feature-distribution"></a><span data-ttu-id="629b1-104">Funktionsverteilung</span><span class="sxs-lookup"><span data-stu-id="629b1-104">Feature distribution</span></span>

<span data-ttu-id="629b1-105">In der folgenden Tabelle sind die Features und die Unterstützung für Clients aufgeführt, die Echtzeitunterstützung anbieten.</span><span class="sxs-lookup"><span data-stu-id="629b1-105">The table below shows the features and support for the clients that offer real-time support.</span></span>

| <span data-ttu-id="629b1-106">Feature</span><span class="sxs-lookup"><span data-stu-id="629b1-106">Feature</span></span> | <span data-ttu-id="629b1-107">.NET</span><span class="sxs-lookup"><span data-stu-id="629b1-107">.NET</span></span> | <span data-ttu-id="629b1-108">JavaScript</span><span class="sxs-lookup"><span data-stu-id="629b1-108">JavaScript</span></span> | <span data-ttu-id="629b1-109">Java</span><span class="sxs-lookup"><span data-stu-id="629b1-109">Java</span></span> |
| ---- | :-: | :-: | :-: |
| <span data-ttu-id="629b1-110">Azure signalr Service-Unterstützung</span><span class="sxs-lookup"><span data-stu-id="629b1-110">Azure SignalR Service Support</span></span> |<span data-ttu-id="629b1-111">✔</span><span class="sxs-lookup"><span data-stu-id="629b1-111">✔</span></span>|<span data-ttu-id="629b1-112">✔</span><span class="sxs-lookup"><span data-stu-id="629b1-112">✔</span></span>|<span data-ttu-id="629b1-113">✔</span><span class="sxs-lookup"><span data-stu-id="629b1-113">✔</span></span>|
| [<span data-ttu-id="629b1-114">Streaming zwischen Server und Client</span><span class="sxs-lookup"><span data-stu-id="629b1-114">Server-to-client Streaming</span></span>](xref:signalr/streaming)          |<span data-ttu-id="629b1-115">✔</span><span class="sxs-lookup"><span data-stu-id="629b1-115">✔</span></span>|<span data-ttu-id="629b1-116">✔</span><span class="sxs-lookup"><span data-stu-id="629b1-116">✔</span></span>|<span data-ttu-id="629b1-117">✔</span><span class="sxs-lookup"><span data-stu-id="629b1-117">✔</span></span>|
| [<span data-ttu-id="629b1-118">Client-zu-Server-Streaming</span><span class="sxs-lookup"><span data-stu-id="629b1-118">Client-to-server Streaming</span></span>](xref:signalr/streaming)          |<span data-ttu-id="629b1-119">✔</span><span class="sxs-lookup"><span data-stu-id="629b1-119">✔</span></span>|<span data-ttu-id="629b1-120">✔</span><span class="sxs-lookup"><span data-stu-id="629b1-120">✔</span></span>|<span data-ttu-id="629b1-121">✔</span><span class="sxs-lookup"><span data-stu-id="629b1-121">✔</span></span>|
| <span data-ttu-id="629b1-122">Automatische erneute Verbindung ([.net](/aspnet/core/signalr/dotnet-client?view=aspnetcore-3.0&tabs=visual-studio#handle-lost-connection), [JavaScript](/aspnet/core/signalr/javascript-client?view=aspnetcore-3.0#reconnect-clients))</span><span class="sxs-lookup"><span data-stu-id="629b1-122">Automatic Reconnection ([.NET](/aspnet/core/signalr/dotnet-client?view=aspnetcore-3.0&tabs=visual-studio#handle-lost-connection), [JavaScript](/aspnet/core/signalr/javascript-client?view=aspnetcore-3.0#reconnect-clients))</span></span>          |<span data-ttu-id="629b1-123">✔</span><span class="sxs-lookup"><span data-stu-id="629b1-123">✔</span></span>|<span data-ttu-id="629b1-124">✔</span><span class="sxs-lookup"><span data-stu-id="629b1-124">✔</span></span>| |
| <span data-ttu-id="629b1-125">Websockets-Transport</span><span class="sxs-lookup"><span data-stu-id="629b1-125">WebSockets Transport</span></span> |<span data-ttu-id="629b1-126">✔</span><span class="sxs-lookup"><span data-stu-id="629b1-126">✔</span></span>|<span data-ttu-id="629b1-127">✔</span><span class="sxs-lookup"><span data-stu-id="629b1-127">✔</span></span>|<span data-ttu-id="629b1-128">✔</span><span class="sxs-lookup"><span data-stu-id="629b1-128">✔</span></span>|
| <span data-ttu-id="629b1-129">Transport von Server gesendeten Ereignissen</span><span class="sxs-lookup"><span data-stu-id="629b1-129">Server-Sent Events Transport</span></span> |<span data-ttu-id="629b1-130">✔</span><span class="sxs-lookup"><span data-stu-id="629b1-130">✔</span></span>|<span data-ttu-id="629b1-131">✔</span><span class="sxs-lookup"><span data-stu-id="629b1-131">✔</span></span>| |
| <span data-ttu-id="629b1-132">Langer Abruf Transport</span><span class="sxs-lookup"><span data-stu-id="629b1-132">Long Polling Transport</span></span> |<span data-ttu-id="629b1-133">✔</span><span class="sxs-lookup"><span data-stu-id="629b1-133">✔</span></span>|<span data-ttu-id="629b1-134">✔</span><span class="sxs-lookup"><span data-stu-id="629b1-134">✔</span></span>|<span data-ttu-id="629b1-135">✔</span><span class="sxs-lookup"><span data-stu-id="629b1-135">✔</span></span>|
| <span data-ttu-id="629b1-136">JSON Hub-Protokoll</span><span class="sxs-lookup"><span data-stu-id="629b1-136">JSON Hub Protocol</span></span> |<span data-ttu-id="629b1-137">✔</span><span class="sxs-lookup"><span data-stu-id="629b1-137">✔</span></span>|<span data-ttu-id="629b1-138">✔</span><span class="sxs-lookup"><span data-stu-id="629b1-138">✔</span></span>|<span data-ttu-id="629b1-139">✔</span><span class="sxs-lookup"><span data-stu-id="629b1-139">✔</span></span>|
| <span data-ttu-id="629b1-140">Hub-Protokoll „MessagePack“</span><span class="sxs-lookup"><span data-stu-id="629b1-140">MessagePack Hub Protocol</span></span> |<span data-ttu-id="629b1-141">✔</span><span class="sxs-lookup"><span data-stu-id="629b1-141">✔</span></span>|<span data-ttu-id="629b1-142">✔</span><span class="sxs-lookup"><span data-stu-id="629b1-142">✔</span></span>| |

<span data-ttu-id="629b1-143">Die Unterstützung für die automatische erneute Verbindungs Herstellung im Java-Client wird in [unserer Problem](https://github.com/aspnet/AspNetCore/issues/8711)Verfolgung nachverfolgt.</span><span class="sxs-lookup"><span data-stu-id="629b1-143">Support for automatic reconnect in the Java client is tracked in [our issue tracker](https://github.com/aspnet/AspNetCore/issues/8711).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="629b1-144">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="629b1-144">Additional resources</span></span>

* [<span data-ttu-id="629b1-145">Beginnen Sie mit signalr für ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="629b1-145">Get started with SignalR for ASP.NET Core</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="629b1-146">Unterstützte Plattformen</span><span class="sxs-lookup"><span data-stu-id="629b1-146">Supported platforms</span></span>](xref:signalr/supported-platforms)
* [<span data-ttu-id="629b1-147">Hubs</span><span class="sxs-lookup"><span data-stu-id="629b1-147">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="629b1-148">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="629b1-148">JavaScript client</span></span>](xref:signalr/javascript-client)

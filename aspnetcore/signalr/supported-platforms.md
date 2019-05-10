---
title: ASP.NET Core SignalR unterstützte Plattformen
author: bradygaster
description: Informationen Sie zu den unterstützten Plattformen für ASP.NET Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/06/2019
uid: signalr/supported-platforms
ms.openlocfilehash: fefaaf97de3f1fabf8f3154bf5b4ccb37195ccff
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64892017"
---
# <a name="aspnet-core-signalr-supported-platforms"></a><span data-ttu-id="e0bd3-103">ASP.NET Core SignalR unterstützte Plattformen</span><span class="sxs-lookup"><span data-stu-id="e0bd3-103">ASP.NET Core SignalR supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="e0bd3-104">Server – Systemanforderungen</span><span class="sxs-lookup"><span data-stu-id="e0bd3-104">Server system requirements</span></span>

<span data-ttu-id="e0bd3-105">SignalR für ASP.NET Core unterstützt Serverplattform, die ASP.NET Core unterstützt.</span><span class="sxs-lookup"><span data-stu-id="e0bd3-105">SignalR for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="e0bd3-106">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="e0bd3-106">JavaScript client</span></span>

<span data-ttu-id="e0bd3-107">Die [JavaScript-Client](https://www.npmjs.com/package/@aspnet/signalr) auf NodeJS-8 und höheren Versionen und den folgenden Browsern ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="e0bd3-107">The [JavaScript client](https://www.npmjs.com/package/@aspnet/signalr) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="e0bd3-108">Browser</span><span class="sxs-lookup"><span data-stu-id="e0bd3-108">Browser</span></span>                         | <span data-ttu-id="e0bd3-109">Version</span><span class="sxs-lookup"><span data-stu-id="e0bd3-109">Version</span></span> |
| ------------------------------- | ------- |
| <span data-ttu-id="e0bd3-110">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="e0bd3-110">Microsoft Edge</span></span>                  | <span data-ttu-id="e0bd3-111">Aktuell</span><span class="sxs-lookup"><span data-stu-id="e0bd3-111">current</span></span> |
| <span data-ttu-id="e0bd3-112">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="e0bd3-112">Mozilla Firefox</span></span>                 | <span data-ttu-id="e0bd3-113">Aktuell</span><span class="sxs-lookup"><span data-stu-id="e0bd3-113">current</span></span> |
| <span data-ttu-id="e0bd3-114">Google Chrome; Android enthält</span><span class="sxs-lookup"><span data-stu-id="e0bd3-114">Google Chrome; includes Android</span></span> | <span data-ttu-id="e0bd3-115">Aktuell</span><span class="sxs-lookup"><span data-stu-id="e0bd3-115">current</span></span> |
| <span data-ttu-id="e0bd3-116">Safari; iOS enthält</span><span class="sxs-lookup"><span data-stu-id="e0bd3-116">Safari; includes iOS</span></span>            | <span data-ttu-id="e0bd3-117">Aktuell</span><span class="sxs-lookup"><span data-stu-id="e0bd3-117">current</span></span> |
| <span data-ttu-id="e0bd3-118">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="e0bd3-118">Microsoft Internet Explorer</span></span>     | <span data-ttu-id="e0bd3-119">11</span><span class="sxs-lookup"><span data-stu-id="e0bd3-119">11</span></span>      |
 
## <a name="net-client"></a><span data-ttu-id="e0bd3-120">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="e0bd3-120">.NET client</span></span>

<span data-ttu-id="e0bd3-121">Die [.NET Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) ausgeführt, die auf jeder Plattform, die von ASP.NET Core unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="e0bd3-121">The [.NET client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="e0bd3-122">Z. B. [Xamarin-Entwickler können SignalR](https://github.com/aspnet/Announcements/issues/305) zum Erstellen von Android-apps mithilfe von Xamarin.Android 8.4.0.1 und höher sowie iOS-apps unter Verwendung von Xamarin.iOS 11.14.0.4 und höher.</span><span class="sxs-lookup"><span data-stu-id="e0bd3-122">For example, [Xamarin developers can use SignalR](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="e0bd3-123">Wenn der Server mit IIS ausgeführt wird, ist die WebSockets-Übertragung IIS 8.0 oder höher auf Windows Server 2012 oder höher erforderlich.</span><span class="sxs-lookup"><span data-stu-id="e0bd3-123">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="e0bd3-124">Andere Transporte werden auf allen Plattformen unterstützt.</span><span class="sxs-lookup"><span data-stu-id="e0bd3-124">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="e0bd3-125">Java-Client</span><span class="sxs-lookup"><span data-stu-id="e0bd3-125">Java client</span></span>

<span data-ttu-id="e0bd3-126">Die [Java-Client](https://search.maven.org/artifact/com.microsoft.aspnet/signalr) Java 8 und höheren Versionen unterstützt.</span><span class="sxs-lookup"><span data-stu-id="e0bd3-126">The [Java client](https://search.maven.org/artifact/com.microsoft.aspnet/signalr) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="e0bd3-127">Nicht unterstützte clients</span><span class="sxs-lookup"><span data-stu-id="e0bd3-127">Unsupported clients</span></span>

<span data-ttu-id="e0bd3-128">Die folgenden Clients sind verfügbar, aber es sind experimentelle oder inoffizielle.</span><span class="sxs-lookup"><span data-stu-id="e0bd3-128">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="e0bd3-129">Sie werden derzeit nicht unterstützt und möglicherweise nie sein.</span><span class="sxs-lookup"><span data-stu-id="e0bd3-129">They aren't currently supported and may never be.</span></span>

* [<span data-ttu-id="e0bd3-130">C++-client</span><span class="sxs-lookup"><span data-stu-id="e0bd3-130">C++ client</span></span>](https://github.com/aspnet/SignalR/tree/master/clients/cpp)

* [<span data-ttu-id="e0bd3-131">SWIFT-client</span><span class="sxs-lookup"><span data-stu-id="e0bd3-131">Swift client</span></span>](https://github.com/moozzyk/SignalR-Client-Swift)

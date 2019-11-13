---
title: ASP.net Core SignalR unterstützten Plattformen
author: bradygaster
description: Erfahren Sie mehr über die unterstützten Plattformen für ASP.net Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/supported-platforms
ms.openlocfilehash: 86ba5b1aec230d78c1a0e1709187e129df6cb4cc
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963728"
---
# <a name="aspnet-core-opno-locsignalr-supported-platforms"></a><span data-ttu-id="e0f3e-103">ASP.net Core SignalR unterstützten Plattformen</span><span class="sxs-lookup"><span data-stu-id="e0f3e-103">ASP.NET Core SignalR supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="e0f3e-104">Systemanforderungen für Server</span><span class="sxs-lookup"><span data-stu-id="e0f3e-104">Server system requirements</span></span>

SignalR<span data-ttu-id="e0f3e-105"> für ASP.net Core unterstützt jede Server Plattform, die ASP.net Core unterstützt.</span><span class="sxs-lookup"><span data-stu-id="e0f3e-105"> for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="e0f3e-106">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="e0f3e-106">JavaScript client</span></span>

<span data-ttu-id="e0f3e-107">Der [JavaScript-Client](https://www.npmjs.com/package/@aspnet/signalr) wird auf nodejs 8 und höheren Versionen und den folgenden Browsern ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="e0f3e-107">The [JavaScript client](https://www.npmjs.com/package/@aspnet/signalr) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="e0f3e-108">Browser</span><span class="sxs-lookup"><span data-stu-id="e0f3e-108">Browser</span></span>                         | <span data-ttu-id="e0f3e-109">Version</span><span class="sxs-lookup"><span data-stu-id="e0f3e-109">Version</span></span>         |
| ------------------------------- | --------------- |
| <span data-ttu-id="e0f3e-110">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="e0f3e-110">Microsoft Edge</span></span>                  | <span data-ttu-id="e0f3e-111">Aktuelle&dagger;</span><span class="sxs-lookup"><span data-stu-id="e0f3e-111">Current&dagger;</span></span> |
| <span data-ttu-id="e0f3e-112">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="e0f3e-112">Mozilla Firefox</span></span>                 | <span data-ttu-id="e0f3e-113">Aktuelle&dagger;</span><span class="sxs-lookup"><span data-stu-id="e0f3e-113">Current&dagger;</span></span> |
| <span data-ttu-id="e0f3e-114">Google Chrome; umfasst Android</span><span class="sxs-lookup"><span data-stu-id="e0f3e-114">Google Chrome; includes Android</span></span> | <span data-ttu-id="e0f3e-115">Aktuelle&dagger;</span><span class="sxs-lookup"><span data-stu-id="e0f3e-115">Current&dagger;</span></span> |
| <span data-ttu-id="e0f3e-116">SK umfasst IOS</span><span class="sxs-lookup"><span data-stu-id="e0f3e-116">Safari; includes iOS</span></span>            | <span data-ttu-id="e0f3e-117">Aktuelle&dagger;</span><span class="sxs-lookup"><span data-stu-id="e0f3e-117">Current&dagger;</span></span> |
| <span data-ttu-id="e0f3e-118">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="e0f3e-118">Microsoft Internet Explorer</span></span>     | <span data-ttu-id="e0f3e-119">11</span><span class="sxs-lookup"><span data-stu-id="e0f3e-119">11</span></span>              |

<span data-ttu-id="e0f3e-120">&dagger;*aktuelle* bezieht sich auf die neueste Version des Browsers.</span><span class="sxs-lookup"><span data-stu-id="e0f3e-120">&dagger;*Current* refers to the latest version of the browser.</span></span>

## <a name="net-client"></a><span data-ttu-id="e0f3e-121">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="e0f3e-121">.NET client</span></span>

<span data-ttu-id="e0f3e-122">Der [.NET-Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) wird auf jeder von ASP.net Core unterstützten Plattform ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="e0f3e-122">The [.NET client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="e0f3e-123">[Xamarin-Entwickler können](https://github.com/aspnet/Announcements/issues/305) z. b. SignalRzum Entwickeln von Android-Apps mit xamarin. Android 8.4.0.1 und höher und IOS-Apps verwenden, die xamarin. IOS 11.14.0.4 und höher verwenden.</span><span class="sxs-lookup"><span data-stu-id="e0f3e-123">For example, [Xamarin developers can use SignalR](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="e0f3e-124">Wenn der Server IIS ausführt, erfordert der websockets-Transport IIS 8,0 oder höher unter Windows Server 2012 oder höher.</span><span class="sxs-lookup"><span data-stu-id="e0f3e-124">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="e0f3e-125">Andere Transporte werden auf allen Plattformen unterstützt.</span><span class="sxs-lookup"><span data-stu-id="e0f3e-125">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="e0f3e-126">Java-Client</span><span class="sxs-lookup"><span data-stu-id="e0f3e-126">Java client</span></span>

<span data-ttu-id="e0f3e-127">Der [Java-Client](https://search.maven.org/artifact/com.microsoft.aspnet/signalr) unterstützt Java 8 und höhere Versionen.</span><span class="sxs-lookup"><span data-stu-id="e0f3e-127">The [Java client](https://search.maven.org/artifact/com.microsoft.aspnet/signalr) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="e0f3e-128">Nicht unterstützte Clients</span><span class="sxs-lookup"><span data-stu-id="e0f3e-128">Unsupported clients</span></span>

<span data-ttu-id="e0f3e-129">Die folgenden Clients sind verfügbar, aber experimentell oder inoffiziell.</span><span class="sxs-lookup"><span data-stu-id="e0f3e-129">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="e0f3e-130">Sie werden derzeit nicht unterstützt und sind möglicherweise nie.</span><span class="sxs-lookup"><span data-stu-id="e0f3e-130">They aren't currently supported and may never be.</span></span>

* <span data-ttu-id="e0f3e-131">[C++ent](https://github.com/aspnet/SignalR/tree/master/clients/cpp)</span><span class="sxs-lookup"><span data-stu-id="e0f3e-131">[C++ client](https://github.com/aspnet/SignalR/tree/master/clients/cpp)</span></span>

* <span data-ttu-id="e0f3e-132">[SWIFT-Client](https://github.com/moozzyk/SignalR-Client-Swift)</span><span class="sxs-lookup"><span data-stu-id="e0f3e-132">[Swift client](https://github.com/moozzyk/SignalR-Client-Swift)</span></span>

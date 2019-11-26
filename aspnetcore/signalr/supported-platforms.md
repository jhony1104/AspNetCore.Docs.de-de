---
title: ASP.net Core SignalR unterstützten Plattformen
author: bradygaster
description: Erfahren Sie mehr über die unterstützten Plattformen für ASP.net Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/21/2019
no-loc:
- SignalR
uid: signalr/supported-platforms
ms.openlocfilehash: 9b9cf1d57d61c333c485f23b7ab952c66814d2aa
ms.sourcegitcommit: 3e503ef510008e77be6dd82ee79213c9f7b97607
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74317470"
---
# <a name="aspnet-core-opno-locsignalr-supported-platforms"></a><span data-ttu-id="8ee46-103">ASP.net Core SignalR unterstützten Plattformen</span><span class="sxs-lookup"><span data-stu-id="8ee46-103">ASP.NET Core SignalR supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="8ee46-104">Systemanforderungen an Server</span><span class="sxs-lookup"><span data-stu-id="8ee46-104">Server system requirements</span></span>

SignalR<span data-ttu-id="8ee46-105"> für ASP.net Core unterstützt jede Server Plattform, die ASP.net Core unterstützt.</span><span class="sxs-lookup"><span data-stu-id="8ee46-105"> for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="8ee46-106">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="8ee46-106">JavaScript client</span></span>

<span data-ttu-id="8ee46-107">Der [JavaScript-Client](xref:signalr/javascript-client) wird auf nodejs 8 und höheren Versionen und den folgenden Browsern ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="8ee46-107">The [JavaScript client](xref:signalr/javascript-client) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="8ee46-108">Browser</span><span class="sxs-lookup"><span data-stu-id="8ee46-108">Browser</span></span>                         | <span data-ttu-id="8ee46-109">Version</span><span class="sxs-lookup"><span data-stu-id="8ee46-109">Version</span></span>         |
| ------------------------------- | --------------- |
| <span data-ttu-id="8ee46-110">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="8ee46-110">Microsoft Edge</span></span>                  | <span data-ttu-id="8ee46-111">Aktuelle&dagger;</span><span class="sxs-lookup"><span data-stu-id="8ee46-111">Current&dagger;</span></span> |
| <span data-ttu-id="8ee46-112">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="8ee46-112">Mozilla Firefox</span></span>                 | <span data-ttu-id="8ee46-113">Aktuelle&dagger;</span><span class="sxs-lookup"><span data-stu-id="8ee46-113">Current&dagger;</span></span> |
| <span data-ttu-id="8ee46-114">Google Chrome; umfasst Android</span><span class="sxs-lookup"><span data-stu-id="8ee46-114">Google Chrome; includes Android</span></span> | <span data-ttu-id="8ee46-115">Aktuelle&dagger;</span><span class="sxs-lookup"><span data-stu-id="8ee46-115">Current&dagger;</span></span> |
| <span data-ttu-id="8ee46-116">SK umfasst IOS</span><span class="sxs-lookup"><span data-stu-id="8ee46-116">Safari; includes iOS</span></span>            | <span data-ttu-id="8ee46-117">Aktuelle&dagger;</span><span class="sxs-lookup"><span data-stu-id="8ee46-117">Current&dagger;</span></span> |
| <span data-ttu-id="8ee46-118">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="8ee46-118">Microsoft Internet Explorer</span></span>     | <span data-ttu-id="8ee46-119">11</span><span class="sxs-lookup"><span data-stu-id="8ee46-119">11</span></span>              |

<span data-ttu-id="8ee46-120">&dagger;*aktuelle* bezieht sich auf die neueste Version des Browsers.</span><span class="sxs-lookup"><span data-stu-id="8ee46-120">&dagger;*Current* refers to the latest version of the browser.</span></span>

## <a name="net-client"></a><span data-ttu-id="8ee46-121">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="8ee46-121">.NET client</span></span>

<span data-ttu-id="8ee46-122">Der [.NET-Client](xref:signalr/dotnet-client) wird auf jeder von ASP.net Core unterstützten Plattform ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="8ee46-122">The [.NET client](xref:signalr/dotnet-client) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="8ee46-123">[Xamarin-Entwickler können](https://github.com/aspnet/Announcements/issues/305) z. b. SignalRzum Entwickeln von Android-Apps mit xamarin. Android 8.4.0.1 und höher und IOS-Apps verwenden, die xamarin. IOS 11.14.0.4 und höher verwenden.</span><span class="sxs-lookup"><span data-stu-id="8ee46-123">For example, [Xamarin developers can use SignalR](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="8ee46-124">Wenn der Server IIS ausführt, erfordert der websockets-Transport IIS 8,0 oder höher unter Windows Server 2012 oder höher.</span><span class="sxs-lookup"><span data-stu-id="8ee46-124">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="8ee46-125">Andere Transporte werden auf allen Plattformen unterstützt.</span><span class="sxs-lookup"><span data-stu-id="8ee46-125">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="8ee46-126">Java-Client</span><span class="sxs-lookup"><span data-stu-id="8ee46-126">Java client</span></span>

<span data-ttu-id="8ee46-127">Der [Java-Client](xref:signalr/java-client) unterstützt Java 8 und höhere Versionen.</span><span class="sxs-lookup"><span data-stu-id="8ee46-127">The [Java client](xref:signalr/java-client) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="8ee46-128">Nicht unterstützte Clients</span><span class="sxs-lookup"><span data-stu-id="8ee46-128">Unsupported clients</span></span>

<span data-ttu-id="8ee46-129">Die folgenden Clients sind verfügbar, aber experimentell oder inoffiziell.</span><span class="sxs-lookup"><span data-stu-id="8ee46-129">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="8ee46-130">Sie werden derzeit nicht unterstützt und sind möglicherweise nie.</span><span class="sxs-lookup"><span data-stu-id="8ee46-130">They aren't currently supported and may never be.</span></span>

* <span data-ttu-id="8ee46-131">[C++ent](https://github.com/aspnet/SignalR/tree/master/clients/cpp)</span><span class="sxs-lookup"><span data-stu-id="8ee46-131">[C++ client](https://github.com/aspnet/SignalR/tree/master/clients/cpp)</span></span>

* <span data-ttu-id="8ee46-132">[SWIFT-Client](https://github.com/moozzyk/SignalR-Client-Swift)</span><span class="sxs-lookup"><span data-stu-id="8ee46-132">[Swift client](https://github.com/moozzyk/SignalR-Client-Swift)</span></span>

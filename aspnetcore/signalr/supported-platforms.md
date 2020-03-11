---
title: ASP.net Core SignalR unterstützten Plattformen
author: bradygaster
description: Erfahren Sie mehr über die unterstützten Plattformen für ASP.net Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- SignalR
uid: signalr/supported-platforms
ms.openlocfilehash: 054965921c87c1a9be27e5ddaa8a87b0fa1f4113
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78655147"
---
# <a name="aspnet-core-signalr-supported-platforms"></a><span data-ttu-id="36eb3-103">ASP.net Core von signalr unterstützte Plattformen</span><span class="sxs-lookup"><span data-stu-id="36eb3-103">ASP.NET Core SignalR supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="36eb3-104">Systemanforderungen an Server</span><span class="sxs-lookup"><span data-stu-id="36eb3-104">Server system requirements</span></span>

<span data-ttu-id="36eb3-105">Signalr für ASP.net Core unterstützt jede Server Plattform, die von ASP.net Core unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="36eb3-105">SignalR for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="36eb3-106">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="36eb3-106">JavaScript client</span></span>

<span data-ttu-id="36eb3-107">Der [JavaScript-Client](xref:signalr/javascript-client) wird auf nodejs 8 und höheren Versionen und den folgenden Browsern ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="36eb3-107">The [JavaScript client](xref:signalr/javascript-client) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="36eb3-108">Browser</span><span class="sxs-lookup"><span data-stu-id="36eb3-108">Browser</span></span>                         | <span data-ttu-id="36eb3-109">Version</span><span class="sxs-lookup"><span data-stu-id="36eb3-109">Version</span></span>         |
| ------------------------------- | --------------- |
| <span data-ttu-id="36eb3-110">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="36eb3-110">Microsoft Edge</span></span>                  | <span data-ttu-id="36eb3-111">Aktuelle&dagger;</span><span class="sxs-lookup"><span data-stu-id="36eb3-111">Current&dagger;</span></span> |
| <span data-ttu-id="36eb3-112">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="36eb3-112">Mozilla Firefox</span></span>                 | <span data-ttu-id="36eb3-113">Aktuelle&dagger;</span><span class="sxs-lookup"><span data-stu-id="36eb3-113">Current&dagger;</span></span> |
| <span data-ttu-id="36eb3-114">Google Chrome; umfasst Android</span><span class="sxs-lookup"><span data-stu-id="36eb3-114">Google Chrome; includes Android</span></span> | <span data-ttu-id="36eb3-115">Aktuelle&dagger;</span><span class="sxs-lookup"><span data-stu-id="36eb3-115">Current&dagger;</span></span> |
| <span data-ttu-id="36eb3-116">SK umfasst IOS</span><span class="sxs-lookup"><span data-stu-id="36eb3-116">Safari; includes iOS</span></span>            | <span data-ttu-id="36eb3-117">Aktuelle&dagger;</span><span class="sxs-lookup"><span data-stu-id="36eb3-117">Current&dagger;</span></span> |
| <span data-ttu-id="36eb3-118">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="36eb3-118">Microsoft Internet Explorer</span></span>     | <span data-ttu-id="36eb3-119">11</span><span class="sxs-lookup"><span data-stu-id="36eb3-119">11</span></span>              |

<span data-ttu-id="36eb3-120">&dagger;*aktuelle* bezieht sich auf die neueste Version des Browsers.</span><span class="sxs-lookup"><span data-stu-id="36eb3-120">&dagger;*Current* refers to the latest version of the browser.</span></span>

## <a name="net-client"></a><span data-ttu-id="36eb3-121">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="36eb3-121">.NET client</span></span>

<span data-ttu-id="36eb3-122">Der [.NET-Client](xref:signalr/dotnet-client) wird auf jeder von ASP.net Core unterstützten Plattform ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="36eb3-122">The [.NET client](xref:signalr/dotnet-client) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="36eb3-123">[Xamarin-Entwickler können](https://github.com/aspnet/Announcements/issues/305) z. b. SignalRzum Entwickeln von Android-Apps mit xamarin. Android 8.4.0.1 und höher und IOS-Apps verwenden, die xamarin. IOS 11.14.0.4 und höher verwenden.</span><span class="sxs-lookup"><span data-stu-id="36eb3-123">For example, [Xamarin developers can use SignalR](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="36eb3-124">Wenn der Server IIS ausführt, erfordert der websockets-Transport IIS 8,0 oder höher unter Windows Server 2012 oder höher.</span><span class="sxs-lookup"><span data-stu-id="36eb3-124">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="36eb3-125">Andere Transporte werden auf allen Plattformen unterstützt.</span><span class="sxs-lookup"><span data-stu-id="36eb3-125">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="36eb3-126">Java-Client</span><span class="sxs-lookup"><span data-stu-id="36eb3-126">Java client</span></span>

<span data-ttu-id="36eb3-127">Der [Java-Client](xref:signalr/java-client) unterstützt Java 8 und höhere Versionen.</span><span class="sxs-lookup"><span data-stu-id="36eb3-127">The [Java client](xref:signalr/java-client) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="36eb3-128">Nicht unterstützte Clients</span><span class="sxs-lookup"><span data-stu-id="36eb3-128">Unsupported clients</span></span>

<span data-ttu-id="36eb3-129">Die folgenden Clients sind verfügbar, aber experimentell oder inoffiziell.</span><span class="sxs-lookup"><span data-stu-id="36eb3-129">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="36eb3-130">Sie werden derzeit nicht unterstützt und sind möglicherweise nie.</span><span class="sxs-lookup"><span data-stu-id="36eb3-130">They aren't currently supported and may never be.</span></span>

* <span data-ttu-id="36eb3-131">[C++ent](https://github.com/aspnet/SignalR-Client-Cpp)</span><span class="sxs-lookup"><span data-stu-id="36eb3-131">[C++ client](https://github.com/aspnet/SignalR-Client-Cpp)</span></span>

* <span data-ttu-id="36eb3-132">[SWIFT-Client](https://github.com/moozzyk/SignalR-Client-Swift)</span><span class="sxs-lookup"><span data-stu-id="36eb3-132">[Swift client](https://github.com/moozzyk/SignalR-Client-Swift)</span></span>

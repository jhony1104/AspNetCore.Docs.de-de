---
title: Unter SignalR stützte Plattformen ASP.net Core
author: bradygaster
description: Erfahren Sie mehr über die unterstützten SignalRPlattformen für ASP.net Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/supported-platforms
ms.openlocfilehash: 70a05dabb95aaf561aa78d5c8b24b430c51bd973
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772604"
---
# <a name="aspnet-core-signalr-supported-platforms"></a><span data-ttu-id="11433-103">ASP.net Core von signalr unterstützte Plattformen</span><span class="sxs-lookup"><span data-stu-id="11433-103">ASP.NET Core SignalR supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="11433-104">Systemanforderungen an Server</span><span class="sxs-lookup"><span data-stu-id="11433-104">Server system requirements</span></span>

<span data-ttu-id="11433-105">Signalr für ASP.net Core unterstützt jede Server Plattform, die von ASP.net Core unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="11433-105">SignalR for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="11433-106">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="11433-106">JavaScript client</span></span>

<span data-ttu-id="11433-107">Der [JavaScript-Client](xref:signalr/javascript-client) wird auf nodejs 8 und höheren Versionen und den folgenden Browsern ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="11433-107">The [JavaScript client](xref:signalr/javascript-client) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="11433-108">Browser</span><span class="sxs-lookup"><span data-stu-id="11433-108">Browser</span></span>                         | <span data-ttu-id="11433-109">Version</span><span class="sxs-lookup"><span data-stu-id="11433-109">Version</span></span>         |
| ------------------------------- | --------------- |
| <span data-ttu-id="11433-110">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="11433-110">Microsoft Edge</span></span>                  | <span data-ttu-id="11433-111">Strömung&dagger;</span><span class="sxs-lookup"><span data-stu-id="11433-111">Current&dagger;</span></span> |
| <span data-ttu-id="11433-112">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="11433-112">Mozilla Firefox</span></span>                 | <span data-ttu-id="11433-113">Strömung&dagger;</span><span class="sxs-lookup"><span data-stu-id="11433-113">Current&dagger;</span></span> |
| <span data-ttu-id="11433-114">Google Chrome; umfasst Android</span><span class="sxs-lookup"><span data-stu-id="11433-114">Google Chrome; includes Android</span></span> | <span data-ttu-id="11433-115">Strömung&dagger;</span><span class="sxs-lookup"><span data-stu-id="11433-115">Current&dagger;</span></span> |
| <span data-ttu-id="11433-116">SK umfasst IOS</span><span class="sxs-lookup"><span data-stu-id="11433-116">Safari; includes iOS</span></span>            | <span data-ttu-id="11433-117">Strömung&dagger;</span><span class="sxs-lookup"><span data-stu-id="11433-117">Current&dagger;</span></span> |
| <span data-ttu-id="11433-118">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="11433-118">Microsoft Internet Explorer</span></span>     | <span data-ttu-id="11433-119">11</span><span class="sxs-lookup"><span data-stu-id="11433-119">11</span></span>              |

<span data-ttu-id="11433-120">&dagger;*Current* bezieht sich auf die neueste Version des Browsers.</span><span class="sxs-lookup"><span data-stu-id="11433-120">&dagger;*Current* refers to the latest version of the browser.</span></span>

## <a name="net-client"></a><span data-ttu-id="11433-121">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="11433-121">.NET client</span></span>

<span data-ttu-id="11433-122">Der [.NET-Client](xref:signalr/dotnet-client) wird auf jeder von ASP.net Core unterstützten Plattform ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="11433-122">The [.NET client](xref:signalr/dotnet-client) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="11433-123">[Xamarin- SignalR Entwickler können](https://github.com/aspnet/Announcements/issues/305) z. b. zum Entwickeln von Android-Apps mit xamarin. Android 8.4.0.1 und höher und IOS-Apps verwenden, die xamarin. IOS 11.14.0.4 und höher verwenden.</span><span class="sxs-lookup"><span data-stu-id="11433-123">For example, [Xamarin developers can use SignalR](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="11433-124">Wenn der Server IIS ausführt, erfordert der websockets-Transport IIS 8,0 oder höher unter Windows Server 2012 oder höher.</span><span class="sxs-lookup"><span data-stu-id="11433-124">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="11433-125">Andere Transporte werden auf allen Plattformen unterstützt.</span><span class="sxs-lookup"><span data-stu-id="11433-125">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="11433-126">Java-Client</span><span class="sxs-lookup"><span data-stu-id="11433-126">Java client</span></span>

<span data-ttu-id="11433-127">Der [Java-Client](xref:signalr/java-client) unterstützt Java 8 und höhere Versionen.</span><span class="sxs-lookup"><span data-stu-id="11433-127">The [Java client](xref:signalr/java-client) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="11433-128">Nicht unterstützte Clients</span><span class="sxs-lookup"><span data-stu-id="11433-128">Unsupported clients</span></span>

<span data-ttu-id="11433-129">Die folgenden Clients sind verfügbar, aber experimentell oder inoffiziell.</span><span class="sxs-lookup"><span data-stu-id="11433-129">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="11433-130">Sie werden derzeit nicht unterstützt und sind möglicherweise nie.</span><span class="sxs-lookup"><span data-stu-id="11433-130">They aren't currently supported and may never be.</span></span>

* <span data-ttu-id="11433-131">[C++-Client](https://github.com/aspnet/SignalR-Client-Cpp)</span><span class="sxs-lookup"><span data-stu-id="11433-131">[C++ client](https://github.com/aspnet/SignalR-Client-Cpp)</span></span>

* <span data-ttu-id="11433-132">[SWIFT-Client](https://github.com/moozzyk/SignalR-Client-Swift)</span><span class="sxs-lookup"><span data-stu-id="11433-132">[Swift client](https://github.com/moozzyk/SignalR-Client-Swift)</span></span>

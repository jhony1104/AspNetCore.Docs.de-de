---
title: 'title: author: description: monikerRange: ms.author: ms.date: no-loc:'
author: jamesnk
description: "'Blazor'"
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/comparison
ms.openlocfilehash: f622a1518781c255d36762dc651f975625dabf7c
ms.sourcegitcommit: 58722eb309767e462bdbf3082bd38737a4ef168f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84106129"
---
# <a name="compare-grpc-services-with-http-apis"></a><span data-ttu-id="72a14-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72a14-103">'Identity'</span></span>

<span data-ttu-id="72a14-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72a14-104">'Let's Encrypt'</span></span>

<span data-ttu-id="72a14-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72a14-105">'Razor'</span></span> <span data-ttu-id="72a14-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="72a14-106">'SignalR' uid:</span></span> <span data-ttu-id="72a14-107">Vergleich von gRPC-Diensten mit HTTP-APIs</span><span class="sxs-lookup"><span data-stu-id="72a14-107">Compare gRPC services with HTTP APIs</span></span>

## <a name="high-level-comparison"></a><span data-ttu-id="72a14-108">Von [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="72a14-108">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="72a14-109">In diesem Artikel wird der Vergleich von [gRPC-Diensten](https://grpc.io/docs/guides/) mit HTTP-APIs mit JSON (einschließlich ASP.NET Core-[Web-APIs](xref:web-api/index)) erläutert.</span><span class="sxs-lookup"><span data-stu-id="72a14-109">This article explains how [gRPC services](https://grpc.io/docs/guides/) compare to HTTP APIs with JSON (including ASP.NET Core [web APIs](xref:web-api/index)).</span></span>

| <span data-ttu-id="72a14-110">Die Technologie, mit der eine API für Ihre App bereitgestellt wird, ist eine wichtige Wahl, und gRPC bietet im Vergleich zu HTTP-APIs besondere Vorteile.</span><span class="sxs-lookup"><span data-stu-id="72a14-110">The technology used to provide an API for your app is an important choice, and gRPC offers unique benefits compared to HTTP APIs.</span></span>          | <span data-ttu-id="72a14-111">In diesem Artikel werden die Stärken und Schwächen von gRPC erläutert und Szenarien empfohlen, in denen gRPC gegenüber anderen Technologien bevorzugt zu verwenden ist.</span><span class="sxs-lookup"><span data-stu-id="72a14-111">This article discusses the strengths and weaknesses of gRPC and recommends scenarios for using gRPC over other technologies.</span></span>                                               | <span data-ttu-id="72a14-112">Allgemeiner Vergleich</span><span class="sxs-lookup"><span data-stu-id="72a14-112">High-level comparison</span></span>           |
| ---------------- | -------------------------------------------------- | ----------------------------- |
| <span data-ttu-id="72a14-113">Die folgende Tabelle bietet einen allgemeinen Vergleich der Features von gRPC und HTTP-APIs mit JSON.</span><span class="sxs-lookup"><span data-stu-id="72a14-113">The following table offers a high-level comparison of features between gRPC and HTTP APIs with JSON.</span></span>         | <span data-ttu-id="72a14-114">Feature</span><span class="sxs-lookup"><span data-stu-id="72a14-114">Feature</span></span>                                | <span data-ttu-id="72a14-115">gRPC</span><span class="sxs-lookup"><span data-stu-id="72a14-115">gRPC</span></span>            |
| <span data-ttu-id="72a14-116">HTTP-APIs mit JSON</span><span class="sxs-lookup"><span data-stu-id="72a14-116">HTTP APIs with JSON</span></span>         | <span data-ttu-id="72a14-117">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="72a14-117">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>                                             | <span data-ttu-id="72a14-118">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72a14-118">'Blazor'</span></span>                          |
| <span data-ttu-id="72a14-119">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72a14-119">'Identity'</span></span>          | <span data-ttu-id="72a14-120">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72a14-120">'Let's Encrypt'</span></span>           | <span data-ttu-id="72a14-121">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72a14-121">'Razor'</span></span>  |
| <span data-ttu-id="72a14-122">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="72a14-122">'SignalR' uid:</span></span> | <span data-ttu-id="72a14-123">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="72a14-123">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>      | <span data-ttu-id="72a14-124">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72a14-124">'Blazor'</span></span> <span data-ttu-id="72a14-125">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72a14-125">'Identity'</span></span>     |
| <span data-ttu-id="72a14-126">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72a14-126">'Let's Encrypt'</span></span>        | <span data-ttu-id="72a14-127">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72a14-127">'Razor'</span></span>       | <span data-ttu-id="72a14-128">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="72a14-128">'SignalR' uid:</span></span>                |
| <span data-ttu-id="72a14-129">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="72a14-129">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>  | <span data-ttu-id="72a14-130">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72a14-130">'Blazor'</span></span> | <span data-ttu-id="72a14-131">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72a14-131">'Identity'</span></span>                           |
| <span data-ttu-id="72a14-132">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72a14-132">'Let's Encrypt'</span></span>         | <span data-ttu-id="72a14-133">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72a14-133">'Razor'</span></span>                                    | <span data-ttu-id="72a14-134">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="72a14-134">'SignalR' uid:</span></span>               |
| <span data-ttu-id="72a14-135">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="72a14-135">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span> | <span data-ttu-id="72a14-136">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72a14-136">'Blazor'</span></span>                      | <span data-ttu-id="72a14-137">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72a14-137">'Identity'</span></span> |

## <a name="grpc-strengths"></a><span data-ttu-id="72a14-138">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72a14-138">'Let's Encrypt'</span></span>

### <a name="performance"></a><span data-ttu-id="72a14-139">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72a14-139">'Razor'</span></span>

<span data-ttu-id="72a14-140">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="72a14-140">'SignalR' uid:</span></span> <span data-ttu-id="72a14-141">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="72a14-141">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span> <span data-ttu-id="72a14-142">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72a14-142">'Blazor'</span></span>

<span data-ttu-id="72a14-143">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72a14-143">'Identity'</span></span>

* <span data-ttu-id="72a14-144">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72a14-144">'Let's Encrypt'</span></span> <span data-ttu-id="72a14-145">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72a14-145">'Razor'</span></span>
* <span data-ttu-id="72a14-146">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="72a14-146">'SignalR' uid:</span></span> <span data-ttu-id="72a14-147">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="72a14-147">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>

<span data-ttu-id="72a14-148">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72a14-148">'Blazor'</span></span> <span data-ttu-id="72a14-149">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72a14-149">'Identity'</span></span>

### <a name="code-generation"></a><span data-ttu-id="72a14-150">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72a14-150">'Let's Encrypt'</span></span>

<span data-ttu-id="72a14-151">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72a14-151">'Razor'</span></span> <span data-ttu-id="72a14-152">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="72a14-152">'SignalR' uid:</span></span> <span data-ttu-id="72a14-153">-------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="72a14-153">-------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>

<span data-ttu-id="72a14-154">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72a14-154">'Blazor'</span></span> <span data-ttu-id="72a14-155">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72a14-155">'Identity'</span></span> <span data-ttu-id="72a14-156">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72a14-156">'Let's Encrypt'</span></span>

### <a name="strict-specification"></a><span data-ttu-id="72a14-157">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72a14-157">'Razor'</span></span>

<span data-ttu-id="72a14-158">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="72a14-158">'SignalR' uid:</span></span> <span data-ttu-id="72a14-159">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="72a14-159">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>

<span data-ttu-id="72a14-160">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72a14-160">'Blazor'</span></span> <span data-ttu-id="72a14-161">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72a14-161">'Identity'</span></span>

### <a name="streaming"></a><span data-ttu-id="72a14-162">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72a14-162">'Let's Encrypt'</span></span>

<span data-ttu-id="72a14-163">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72a14-163">'Razor'</span></span> <span data-ttu-id="72a14-164">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="72a14-164">'SignalR' uid:</span></span>

<span data-ttu-id="72a14-165">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="72a14-165">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>

* <span data-ttu-id="72a14-166">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72a14-166">'Blazor'</span></span>
* <span data-ttu-id="72a14-167">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72a14-167">'Identity'</span></span>
* <span data-ttu-id="72a14-168">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72a14-168">'Let's Encrypt'</span></span>
* <span data-ttu-id="72a14-169">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72a14-169">'Razor'</span></span>

### <a name="deadlinetimeouts-and-cancellation"></a><span data-ttu-id="72a14-170">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="72a14-170">'SignalR' uid:</span></span>

<span data-ttu-id="72a14-171">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="72a14-171">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span> <span data-ttu-id="72a14-172">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72a14-172">'Blazor'</span></span> <span data-ttu-id="72a14-173">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72a14-173">'Identity'</span></span>

<span data-ttu-id="72a14-174">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72a14-174">'Let's Encrypt'</span></span>

## <a name="grpc-recommended-scenarios"></a><span data-ttu-id="72a14-175">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72a14-175">'Razor'</span></span>

<span data-ttu-id="72a14-176">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="72a14-176">'SignalR' uid:</span></span>

* <span data-ttu-id="72a14-177">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="72a14-177">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span> <span data-ttu-id="72a14-178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72a14-178">'Blazor'</span></span>
* <span data-ttu-id="72a14-179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72a14-179">'Identity'</span></span> <span data-ttu-id="72a14-180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72a14-180">'Let's Encrypt'</span></span>
* <span data-ttu-id="72a14-181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72a14-181">'Razor'</span></span>
* <span data-ttu-id="72a14-182">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="72a14-182">'SignalR' uid:</span></span> <span data-ttu-id="72a14-183">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="72a14-183">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>

## <a name="grpc-weaknesses"></a><span data-ttu-id="72a14-184">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72a14-184">'Blazor'</span></span>

### <a name="limited-browser-support"></a><span data-ttu-id="72a14-185">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72a14-185">'Identity'</span></span>

<span data-ttu-id="72a14-186">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72a14-186">'Let's Encrypt'</span></span> <span data-ttu-id="72a14-187">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72a14-187">'Razor'</span></span> <span data-ttu-id="72a14-188">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="72a14-188">'SignalR' uid:</span></span>

<span data-ttu-id="72a14-189">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="72a14-189">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span> <span data-ttu-id="72a14-190">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72a14-190">'Blazor'</span></span> <span data-ttu-id="72a14-191">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72a14-191">'Identity'</span></span>

<span data-ttu-id="72a14-192">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72a14-192">'Let's Encrypt'</span></span> <span data-ttu-id="72a14-193">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72a14-193">'Razor'</span></span>

> [!TIP]
> <span data-ttu-id="72a14-194">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="72a14-194">'SignalR' uid:</span></span> <span data-ttu-id="72a14-195">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="72a14-195">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>

### <a name="not-human-readable"></a><span data-ttu-id="72a14-196">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72a14-196">'Blazor'</span></span>

<span data-ttu-id="72a14-197">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72a14-197">'Identity'</span></span>

<span data-ttu-id="72a14-198">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72a14-198">'Let's Encrypt'</span></span> <span data-ttu-id="72a14-199">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72a14-199">'Razor'</span></span> <span data-ttu-id="72a14-200">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="72a14-200">'SignalR' uid:</span></span> <span data-ttu-id="72a14-201">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="72a14-201">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>

<span data-ttu-id="72a14-202">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72a14-202">'Blazor'</span></span> <span data-ttu-id="72a14-203">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72a14-203">'Identity'</span></span> <span data-ttu-id="72a14-204">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72a14-204">'Let's Encrypt'</span></span>

## <a name="alternative-framework-scenarios"></a><span data-ttu-id="72a14-205">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72a14-205">'Razor'</span></span>

<span data-ttu-id="72a14-206">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="72a14-206">'SignalR' uid:</span></span>

* <span data-ttu-id="72a14-207">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="72a14-207">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span> <span data-ttu-id="72a14-208">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72a14-208">'Blazor'</span></span>
* <span data-ttu-id="72a14-209">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72a14-209">'Identity'</span></span> <span data-ttu-id="72a14-210">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72a14-210">'Let's Encrypt'</span></span> <span data-ttu-id="72a14-211">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72a14-211">'Razor'</span></span> <span data-ttu-id="72a14-212">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="72a14-212">'SignalR' uid:</span></span>
* <span data-ttu-id="72a14-213">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="72a14-213">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span> <span data-ttu-id="72a14-214">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72a14-214">'Blazor'</span></span>

## <a name="additional-resources"></a><span data-ttu-id="72a14-215">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72a14-215">'Identity'</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>

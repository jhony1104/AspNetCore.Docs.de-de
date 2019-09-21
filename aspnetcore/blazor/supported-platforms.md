---
title: ASP.net Core von blazor unterstützten Plattformen
author: guardrex
description: Erfahren Sie mehr über die unterstützten Plattformen für ASP.net Core blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/01/2019
uid: blazor/supported-platforms
ms.openlocfilehash: 8730417f772c84ebcccc449a5826126aa5c64abb
ms.sourcegitcommit: e5a74f882c14eaa0e5639ff082355e130559ba83
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/20/2019
ms.locfileid: "71168169"
---
# <a name="aspnet-core-blazor-supported-platforms"></a><span data-ttu-id="926da-103">ASP.net Core von blazor unterstützten Plattformen</span><span class="sxs-lookup"><span data-stu-id="926da-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="926da-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="926da-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="browser-requirements"></a><span data-ttu-id="926da-105">Browseranforderungen</span><span class="sxs-lookup"><span data-stu-id="926da-105">Browser requirements</span></span>

### <a name="blazor-webassembly"></a><span data-ttu-id="926da-106">Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="926da-106">Blazor WebAssembly</span></span>

| <span data-ttu-id="926da-107">Browser</span><span class="sxs-lookup"><span data-stu-id="926da-107">Browser</span></span>                          | <span data-ttu-id="926da-108">Version</span><span class="sxs-lookup"><span data-stu-id="926da-108">Version</span></span>               |
| -------------------------------- | :-------------------: |
| <span data-ttu-id="926da-109">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="926da-109">Microsoft Edge</span></span>                   | <span data-ttu-id="926da-110">Aktuell</span><span class="sxs-lookup"><span data-stu-id="926da-110">Current</span></span>               |
| <span data-ttu-id="926da-111">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="926da-111">Mozilla Firefox</span></span>                  | <span data-ttu-id="926da-112">Aktuell</span><span class="sxs-lookup"><span data-stu-id="926da-112">Current</span></span>               |
| <span data-ttu-id="926da-113">Google Chrome, einschließlich Android</span><span class="sxs-lookup"><span data-stu-id="926da-113">Google Chrome, including Android</span></span> | <span data-ttu-id="926da-114">Aktuell</span><span class="sxs-lookup"><span data-stu-id="926da-114">Current</span></span>               |
| <span data-ttu-id="926da-115">Safari, einschließlich IOS</span><span class="sxs-lookup"><span data-stu-id="926da-115">Safari, including iOS</span></span>            | <span data-ttu-id="926da-116">Aktuell</span><span class="sxs-lookup"><span data-stu-id="926da-116">Current</span></span>               |
| <span data-ttu-id="926da-117">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="926da-117">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="926da-118">Nicht unterstützt&dagger;</span><span class="sxs-lookup"><span data-stu-id="926da-118">Not Supported&dagger;</span></span> |

<span data-ttu-id="926da-119">&dagger;[Webassembly](https://webassembly.org)wird von Microsoft Internet Explorer nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="926da-119">&dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

### <a name="blazor-server"></a><span data-ttu-id="926da-120">Blazor Server</span><span class="sxs-lookup"><span data-stu-id="926da-120">Blazor Server</span></span>

| <span data-ttu-id="926da-121">Browser</span><span class="sxs-lookup"><span data-stu-id="926da-121">Browser</span></span>                          | <span data-ttu-id="926da-122">Version</span><span class="sxs-lookup"><span data-stu-id="926da-122">Version</span></span>    |
| -------------------------------- | :--------: |
| <span data-ttu-id="926da-123">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="926da-123">Microsoft Edge</span></span>                   | <span data-ttu-id="926da-124">Aktuell</span><span class="sxs-lookup"><span data-stu-id="926da-124">Current</span></span>    |
| <span data-ttu-id="926da-125">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="926da-125">Mozilla Firefox</span></span>                  | <span data-ttu-id="926da-126">Aktuell</span><span class="sxs-lookup"><span data-stu-id="926da-126">Current</span></span>    |
| <span data-ttu-id="926da-127">Google Chrome, einschließlich Android</span><span class="sxs-lookup"><span data-stu-id="926da-127">Google Chrome, including Android</span></span> | <span data-ttu-id="926da-128">Aktuell</span><span class="sxs-lookup"><span data-stu-id="926da-128">Current</span></span>    |
| <span data-ttu-id="926da-129">Safari, einschließlich IOS</span><span class="sxs-lookup"><span data-stu-id="926da-129">Safari, including iOS</span></span>            | <span data-ttu-id="926da-130">Aktuell</span><span class="sxs-lookup"><span data-stu-id="926da-130">Current</span></span>    |
| <span data-ttu-id="926da-131">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="926da-131">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="926da-132">11:&dagger;</span><span class="sxs-lookup"><span data-stu-id="926da-132">11&dagger;</span></span> |

<span data-ttu-id="926da-133">&dagger;Zusätzliche polyfills sind erforderlich (Zusagen können z. b. über ein [Polyfill.IO](https://polyfill.io/v3/) -Bundle hinzugefügt werden).</span><span class="sxs-lookup"><span data-stu-id="926da-133">&dagger;Additional polyfills are required (for example, promises can be added via a [Polyfill.io](https://polyfill.io/v3/) bundle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="926da-134">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="926da-134">Additional resources</span></span>

* <xref:blazor/hosting-models>

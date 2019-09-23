---
title: Authentifizierungs Beispiele für ASP.net Core
author: rick-anderson
description: Enthält Links zu den Authentifizierungs Beispielen im ASP.net Core Repository.
ms.author: riande
ms.date: 01/31/2019
uid: security/authentication/samples
ms.openlocfilehash: d49aef198e926d88f1a6727f84b06f0861c8812d
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2019
ms.locfileid: "71187295"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="c6fab-103">Authentifizierungs Beispiele für ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="c6fab-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="c6fab-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c6fab-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c6fab-105">Das [ASP.net Core-Repository](https://github.com/aspnet/AspNetCore) enthält die folgenden Authentifizierungs Beispiele im Ordner " *aspnetcore/src/Security/Samples* ":</span><span class="sxs-lookup"><span data-stu-id="c6fab-105">The [ASP.NET Core repository](https://github.com/aspnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="c6fab-106">Anspruchs Transformation</span><span class="sxs-lookup"><span data-stu-id="c6fab-106">Claims transformation</span></span>](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="c6fab-107">Cookie-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="c6fab-107">Cookie authentication</span></span>](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/Cookies)
* [<span data-ttu-id="c6fab-108">Benutzerdefinierter Richtlinien Anbieter-iauthorizationpolicyprovider</span><span class="sxs-lookup"><span data-stu-id="c6fab-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="c6fab-109">Dynamische Authentifizierungs Schemas und-Optionen</span><span class="sxs-lookup"><span data-stu-id="c6fab-109">Dynamic authentication schemes and options</span></span>](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="c6fab-110">Externe Ansprüche</span><span class="sxs-lookup"><span data-stu-id="c6fab-110">External claims</span></span>](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="c6fab-111">Auswählen zwischen Cookie und einem anderen Authentifizierungsschema basierend auf der Anforderung</span><span class="sxs-lookup"><span data-stu-id="c6fab-111">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="c6fab-112">Schränkt den Zugriff auf statische Dateien ein.</span><span class="sxs-lookup"><span data-stu-id="c6fab-112">Restricts access to static files</span></span>](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="c6fab-113">Ausführen der Beispiele</span><span class="sxs-lookup"><span data-stu-id="c6fab-113">Run the samples</span></span>

* <span data-ttu-id="c6fab-114">Wählen Sie eine [Verzweigung](https://github.com/aspnet/AspNetCore) aus.</span><span class="sxs-lookup"><span data-stu-id="c6fab-114">Select a [branch](https://github.com/aspnet/AspNetCore).</span></span> <span data-ttu-id="c6fab-115">Beispiel: `Tag:v3.0.0`</span><span class="sxs-lookup"><span data-stu-id="c6fab-115">For example, `Tag:v3.0.0`</span></span>
* <span data-ttu-id="c6fab-116">Klonen Sie das [ASP.net Core Repository](https://github.com/aspnet/AspNetCore), oder laden Sie es herunter.</span><span class="sxs-lookup"><span data-stu-id="c6fab-116">Clone or download the [ASP.NET Core repository](https://github.com/aspnet/AspNetCore).</span></span>
* <span data-ttu-id="c6fab-117">Vergewissern Sie sich, dass Sie die [.net Core SDK](https://www.microsoft.com/net/download/all) Version installiert haben, die mit dem Klon des ASP.net Core Repository übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="c6fab-117">Verify you have installed the [.NET Core SDK](https://www.microsoft.com/net/download/all) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="c6fab-118">Navigieren Sie in *aspnetcore/src/Security/Samples* zu einem Beispiel, und führen Sie `dotnet run`das Beispiel mit aus.</span><span class="sxs-lookup"><span data-stu-id="c6fab-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c6fab-119">Das [ASP.net Core-Repository](https://github.com/aspnet/AspNetCore) enthält die folgenden Authentifizierungs Beispiele im Ordner " *aspnetcore/src/Security/Samples* ":</span><span class="sxs-lookup"><span data-stu-id="c6fab-119">The [ASP.NET Core repository](https://github.com/aspnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="c6fab-120">Anspruchs Transformation</span><span class="sxs-lookup"><span data-stu-id="c6fab-120">Claims transformation</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="c6fab-121">Cookie-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="c6fab-121">Cookie authentication</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [<span data-ttu-id="c6fab-122">Benutzerdefinierter Richtlinien Anbieter-iauthorizationpolicyprovider</span><span class="sxs-lookup"><span data-stu-id="c6fab-122">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="c6fab-123">Dynamische Authentifizierungs Schemas und-Optionen</span><span class="sxs-lookup"><span data-stu-id="c6fab-123">Dynamic authentication schemes and options</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="c6fab-124">Externe Ansprüche</span><span class="sxs-lookup"><span data-stu-id="c6fab-124">External claims</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="c6fab-125">Auswählen zwischen Cookie und einem anderen Authentifizierungsschema basierend auf der Anforderung</span><span class="sxs-lookup"><span data-stu-id="c6fab-125">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="c6fab-126">Schränkt den Zugriff auf statische Dateien ein.</span><span class="sxs-lookup"><span data-stu-id="c6fab-126">Restricts access to static files</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="c6fab-127">Ausführen der Beispiele</span><span class="sxs-lookup"><span data-stu-id="c6fab-127">Run the samples</span></span>

* <span data-ttu-id="c6fab-128">Wählen Sie eine [Verzweigung](https://github.com/aspnet/AspNetCore) aus.</span><span class="sxs-lookup"><span data-stu-id="c6fab-128">Select a [branch](https://github.com/aspnet/AspNetCore).</span></span> <span data-ttu-id="c6fab-129">Beispiel: `release/2.2`</span><span class="sxs-lookup"><span data-stu-id="c6fab-129">For example, `release/2.2`</span></span>
* <span data-ttu-id="c6fab-130">Klonen Sie das [ASP.net Core Repository](https://github.com/aspnet/AspNetCore), oder laden Sie es herunter.</span><span class="sxs-lookup"><span data-stu-id="c6fab-130">Clone or download the [ASP.NET Core repository](https://github.com/aspnet/AspNetCore).</span></span>
* <span data-ttu-id="c6fab-131">Vergewissern Sie sich, dass Sie die [.net Core SDK](https://www.microsoft.com/net/download/all) Version installiert haben, die mit dem Klon des ASP.net Core Repository übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="c6fab-131">Verify you have installed the [.NET Core SDK](https://www.microsoft.com/net/download/all) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="c6fab-132">Navigieren Sie in *aspnetcore/src/Security/Samples* zu einem Beispiel, und führen Sie `dotnet run`das Beispiel mit aus.</span><span class="sxs-lookup"><span data-stu-id="c6fab-132">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

---
title: Authentifizierungs Beispiele für ASP.net Core
author: rick-anderson
description: Enthält Links zu den Authentifizierungs Beispielen im ASP.net Core Repository.
ms.author: riande
ms.date: 01/31/2019
uid: security/authentication/samples
ms.openlocfilehash: b013d02a65e752bbb61a87a0bf502785125378a2
ms.sourcegitcommit: d64ef143c64ee4fdade8f9ea0b753b16752c5998
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511612"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="bce91-103">Authentifizierungs Beispiele für ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="bce91-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="bce91-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bce91-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bce91-105">Das [ASP.net Core-Repository](https://github.com/dotnet/AspNetCore) enthält die folgenden Authentifizierungs Beispiele im Ordner " *aspnetcore/src/Security/Samples* ":</span><span class="sxs-lookup"><span data-stu-id="bce91-105">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="bce91-106">Anspruchs Transformation</span><span class="sxs-lookup"><span data-stu-id="bce91-106">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="bce91-107">Cookie-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="bce91-107">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/Cookies)
* [<span data-ttu-id="bce91-108">Benutzerdefinierter Richtlinien Anbieter-iauthorizationpolicyprovider</span><span class="sxs-lookup"><span data-stu-id="bce91-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="bce91-109">Dynamische Authentifizierungs Schemas und-Optionen</span><span class="sxs-lookup"><span data-stu-id="bce91-109">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="bce91-110">Externe Ansprüche</span><span class="sxs-lookup"><span data-stu-id="bce91-110">External claims</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="bce91-111">Auswählen zwischen Cookie und einem anderen Authentifizierungsschema basierend auf der Anforderung</span><span class="sxs-lookup"><span data-stu-id="bce91-111">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="bce91-112">Schränkt den Zugriff auf statische Dateien ein.</span><span class="sxs-lookup"><span data-stu-id="bce91-112">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="bce91-113">Ausführen der Beispiele</span><span class="sxs-lookup"><span data-stu-id="bce91-113">Run the samples</span></span>

* <span data-ttu-id="bce91-114">Wählen Sie eine [Verzweigung](https://github.com/dotnet/AspNetCore)aus.</span><span class="sxs-lookup"><span data-stu-id="bce91-114">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="bce91-115">Zum Beispiel, `Tag:v3.0.0`</span><span class="sxs-lookup"><span data-stu-id="bce91-115">For example, `Tag:v3.0.0`</span></span>
* <span data-ttu-id="bce91-116">Klonen Sie das [ASP.net Core Repository](https://github.com/dotnet/AspNetCore), oder laden Sie es herunter.</span><span class="sxs-lookup"><span data-stu-id="bce91-116">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="bce91-117">Vergewissern Sie sich, dass Sie die [.net Core SDK](https://dotnet.microsoft.com/download/dotnet-core) Version installiert haben, die mit dem Klon des ASP.net Core Repository übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="bce91-117">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="bce91-118">Navigieren Sie in *aspnetcore/src/Security/Samples* zu einem Beispiel, und führen Sie das Beispiel mit `dotnet run`aus.</span><span class="sxs-lookup"><span data-stu-id="bce91-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bce91-119">Das [ASP.net Core-Repository](https://github.com/dotnet/AspNetCore) enthält die folgenden Authentifizierungs Beispiele im Ordner " *aspnetcore/src/Security/Samples* ":</span><span class="sxs-lookup"><span data-stu-id="bce91-119">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="bce91-120">Anspruchs Transformation</span><span class="sxs-lookup"><span data-stu-id="bce91-120">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="bce91-121">Cookie-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="bce91-121">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [<span data-ttu-id="bce91-122">Benutzerdefinierter Richtlinien Anbieter-iauthorizationpolicyprovider</span><span class="sxs-lookup"><span data-stu-id="bce91-122">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="bce91-123">Dynamische Authentifizierungs Schemas und-Optionen</span><span class="sxs-lookup"><span data-stu-id="bce91-123">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="bce91-124">Externe Ansprüche</span><span class="sxs-lookup"><span data-stu-id="bce91-124">External claims</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="bce91-125">Auswählen zwischen Cookie und einem anderen Authentifizierungsschema basierend auf der Anforderung</span><span class="sxs-lookup"><span data-stu-id="bce91-125">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="bce91-126">Schränkt den Zugriff auf statische Dateien ein.</span><span class="sxs-lookup"><span data-stu-id="bce91-126">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="bce91-127">Ausführen der Beispiele</span><span class="sxs-lookup"><span data-stu-id="bce91-127">Run the samples</span></span>

* <span data-ttu-id="bce91-128">Wählen Sie eine [Verzweigung](https://github.com/dotnet/AspNetCore)aus.</span><span class="sxs-lookup"><span data-stu-id="bce91-128">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="bce91-129">Zum Beispiel, `release/2.2`</span><span class="sxs-lookup"><span data-stu-id="bce91-129">For example, `release/2.2`</span></span>
* <span data-ttu-id="bce91-130">Klonen Sie das [ASP.net Core Repository](https://github.com/dotnet/AspNetCore), oder laden Sie es herunter.</span><span class="sxs-lookup"><span data-stu-id="bce91-130">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="bce91-131">Vergewissern Sie sich, dass Sie die [.net Core SDK](https://dotnet.microsoft.com/download/dotnet-core) Version installiert haben, die mit dem Klon des ASP.net Core Repository übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="bce91-131">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="bce91-132">Navigieren Sie in *aspnetcore/src/Security/Samples* zu einem Beispiel, und führen Sie das Beispiel mit `dotnet run`aus.</span><span class="sxs-lookup"><span data-stu-id="bce91-132">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

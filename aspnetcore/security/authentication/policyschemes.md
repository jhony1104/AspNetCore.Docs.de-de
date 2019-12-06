---
title: Richtlinien Schemas in ASP.net Core
author: rick-anderson
description: Authentifizierungs Richtlinien Schemas vereinfachen die Erstellung eines einzelnen logischen Authentifizierungs Schemas.
ms.author: riande
ms.date: 12/05/2019
uid: security/authentication/policyschemes
ms.openlocfilehash: f02d8e5cac20a9b60c5eddbd28253efacf682ea1
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74880717"
---
# <a name="policy-schemes-in-aspnet-core"></a><span data-ttu-id="9e7fc-103">Richtlinien Schemas in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="9e7fc-103">Policy schemes in ASP.NET Core</span></span>

<span data-ttu-id="9e7fc-104">Authentifizierungs Richtlinien Schemas vereinfachen die Verwendung eines einzelnen logischen Authentifizierungs Schemas, das möglicherweise mehrere Ansätze verwendet.</span><span class="sxs-lookup"><span data-stu-id="9e7fc-104">Authentication policy schemes make it easier to have a single logical authentication scheme potentially use multiple approaches.</span></span> <span data-ttu-id="9e7fc-105">Beispielsweise kann ein Richtlinien Schema die Google-Authentifizierung für Herausforderungen und die Cookie-Authentifizierung für alles andere verwenden.</span><span class="sxs-lookup"><span data-stu-id="9e7fc-105">For example, a policy scheme might use Google authentication for challenges, and cookie authentication for everything else.</span></span> <span data-ttu-id="9e7fc-106">Die Authentifizierungs Richtlinien Schemas machen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="9e7fc-106">Authentication policy schemes make it:</span></span>

* <span data-ttu-id="9e7fc-107">Einfache Weiterleiten beliebiger Authentifizierungs Aktionen an ein anderes Schema.</span><span class="sxs-lookup"><span data-stu-id="9e7fc-107">Easy to forward any authentication action to another scheme.</span></span>
* <span data-ttu-id="9e7fc-108">Basierend auf der Anforderung dynamisch weiterleiten.</span><span class="sxs-lookup"><span data-stu-id="9e7fc-108">Forward dynamically based on the request.</span></span>

<span data-ttu-id="9e7fc-109">Alle Authentifizierungs Schemas, die abgeleitete <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> und die zugehörigen [authenticationhandler-\<toptions >](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1)verwenden:</span><span class="sxs-lookup"><span data-stu-id="9e7fc-109">All authentication schemes that use derived <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> and the associated [AuthenticationHandler\<TOptions>](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):</span></span>

* <span data-ttu-id="9e7fc-110">Sind automatisch Richtlinien Schemas in ASP.net Core 2,1 und höher.</span><span class="sxs-lookup"><span data-stu-id="9e7fc-110">Are automatically policy schemes in ASP.NET Core 2.1 and later.</span></span>
* <span data-ttu-id="9e7fc-111">Kann durch Konfigurieren der Schema Optionen aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="9e7fc-111">Can be enabled via configuring the scheme's options.</span></span>

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a><span data-ttu-id="9e7fc-112">Beispiele</span><span class="sxs-lookup"><span data-stu-id="9e7fc-112">Examples</span></span>

<span data-ttu-id="9e7fc-113">Das folgende Beispiel zeigt ein Schema höherer Ebene, das Schemata auf niedrigerer Ebene kombiniert.</span><span class="sxs-lookup"><span data-stu-id="9e7fc-113">The following example shows a higher level scheme that combines lower level schemes.</span></span> <span data-ttu-id="9e7fc-114">Die Google-Authentifizierung wird für Herausforderungen verwendet, und die Cookie-Authentifizierung wird für alles andere verwendet:</span><span class="sxs-lookup"><span data-stu-id="9e7fc-114">Google authentication is used for challenges, and cookie authentication is used for everything else:</span></span>

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

<span data-ttu-id="9e7fc-115">Im folgenden Beispiel wird die dynamische Auswahl von Schemas pro Anforderung aktiviert.</span><span class="sxs-lookup"><span data-stu-id="9e7fc-115">The following example enables dynamic selection of schemes on a per request basis.</span></span> <span data-ttu-id="9e7fc-116">Das heißt, es wird erläutert, wie Cookies und API-Authentifizierung gemischt werden:</span><span class="sxs-lookup"><span data-stu-id="9e7fc-116">That is, how to mix cookies and API authentication:</span></span>

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]

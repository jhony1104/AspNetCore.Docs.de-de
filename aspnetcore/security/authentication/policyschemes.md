---
title: Richtlinie für Schemas, die in ASP.NET Core
author: rick-anderson
description: Richtlinie Authentifizierungsschemas erleichtern es, eine einzelne logische Authentifizierungsschema haben
ms.author: riande
ms.date: 02/28/2019
uid: security/authentication/policyschemes
ms.openlocfilehash: be03f349455c673b0739935ad20e596325c8cb74
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815285"
---
# <a name="policy-schemes-in-aspnet-core"></a><span data-ttu-id="77836-103">Richtlinie für Schemas, die in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="77836-103">Policy schemes in ASP.NET Core</span></span>

<span data-ttu-id="77836-104">Richtlinie Authentifizierungsschemas erleichtern es, damit eine einzelne logische-Authentifizierungsschema, die potenziell mehrere Ansätze verwenden.</span><span class="sxs-lookup"><span data-stu-id="77836-104">Authentication policy schemes make it easier to have a single logical authentication scheme potentially use multiple approaches.</span></span> <span data-ttu-id="77836-105">Beispielsweise kann ein Schema für die Richtlinie für alle anderen Google-Authentifizierung für die Herausforderungen und Cookie-Authentifizierung verwenden.</span><span class="sxs-lookup"><span data-stu-id="77836-105">For example, a policy scheme might use Google authentication for challenges, and cookie authentication for everything else.</span></span> <span data-ttu-id="77836-106">Richtlinie Authentifizierungsschemas erleichtern:</span><span class="sxs-lookup"><span data-stu-id="77836-106">Authentication policy schemes make it:</span></span>

* <span data-ttu-id="77836-107">Einfache Maßnahmen Authentifizierung zu einem anderen Schema weiterleiten.</span><span class="sxs-lookup"><span data-stu-id="77836-107">Easy to forward any authentication action to another scheme.</span></span>
* <span data-ttu-id="77836-108">Vorlauf dynamisch basierend auf der Anforderung.</span><span class="sxs-lookup"><span data-stu-id="77836-108">Forward dynamically based on the request.</span></span>

<span data-ttu-id="77836-109">Alle Authentifizierungsschemas, die verwenden abgeleiteter <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> und den zugehörigen [ `AuthenticationHandler<TOptions>` ](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):</span><span class="sxs-lookup"><span data-stu-id="77836-109">All authentication schemes that use derived <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> and the associated [`AuthenticationHandler<TOptions>`](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):</span></span>

* <span data-ttu-id="77836-110">Werden automatisch Richtlinie Schemas in ASP.NET Core 2.1 und höher.</span><span class="sxs-lookup"><span data-stu-id="77836-110">Are automatically policy schemes in ASP.NET Core 2.1 and later.</span></span>
* <span data-ttu-id="77836-111">Kann über das Konfigurieren von Optionen für das Schema der aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="77836-111">Can be enabled via configuring the scheme's options.</span></span>

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a><span data-ttu-id="77836-112">Beispiele</span><span class="sxs-lookup"><span data-stu-id="77836-112">Examples</span></span>

<span data-ttu-id="77836-113">Das folgende Beispiel zeigt eine höhere Ebene Schema, in der unteren Ebene Schemas kombiniert.</span><span class="sxs-lookup"><span data-stu-id="77836-113">The following example shows a higher level scheme that combines lower level schemes.</span></span> <span data-ttu-id="77836-114">Google-Authentifizierung wird verwendet, für die Herausforderungen und Cookieauthentifizierung für alle anderen verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="77836-114">Google authentication is used for challenges, and cookie authentication is used for everything else:</span></span>

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

<span data-ttu-id="77836-115">Im folgenden Beispiel wird die dynamische Auswahl der Schemas auf Grundlage individueller Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="77836-115">The following example enables dynamic selection of schemes on a per request basis.</span></span> <span data-ttu-id="77836-116">D. h. wie zum Mischen von Cookies und API-Authentifizierung:</span><span class="sxs-lookup"><span data-stu-id="77836-116">That is, how to mix cookies and API authentication:</span></span>

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]

---
title: Client-IP-Listen sicherer Adressen für ASP.NET Core
author: damienbod
description: Erfahren Sie, wie Middleware oder Aktion Schreibfilter zum remote-IP-Adressen mit einer Liste der zulässigen IP-Adressen zu überprüfen.
ms.author: tdykstra
ms.custom: mvc
ms.date: 08/31/2018
uid: security/ip-safelist
ms.openlocfilehash: ca05989efabea3a71c6912e98055a6746e0f5966
ms.sourcegitcommit: 1bf80f4acd62151ff8cce517f03f6fa891136409
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/15/2019
ms.locfileid: "68223933"
---
# <a name="client-ip-safelist-for-aspnet-core"></a><span data-ttu-id="09524-103">Client-IP-Listen sicherer Adressen für ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="09524-103">Client IP safelist for ASP.NET Core</span></span>

<span data-ttu-id="09524-104">Durch [Damien Bowden](https://twitter.com/damien_bod) und [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="09524-104">By [Damien Bowden](https://twitter.com/damien_bod) and [Tom Dykstra](https://github.com/tdykstra)</span></span>
 
<span data-ttu-id="09524-105">In diesem Artikel zeigt drei Möglichkeiten, eine IP-Listen sicherer Adressen (auch bekannt als eine Whitelist) in einer ASP.NET Core-app zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="09524-105">This article shows three ways to implement an IP safelist (also known as a whitelist) in an ASP.NET Core app.</span></span> <span data-ttu-id="09524-106">Sie können Folgendes verwenden:</span><span class="sxs-lookup"><span data-stu-id="09524-106">You can use:</span></span>

* <span data-ttu-id="09524-107">Die Middleware die remote IP-Adresse jeder Anforderung überprüfen.</span><span class="sxs-lookup"><span data-stu-id="09524-107">Middleware to check the remote IP address of every request.</span></span>
* <span data-ttu-id="09524-108">Aktionsfilter, um die Anforderungen für bestimmte Controller oder Aktionsmethoden remote IP-Adresse zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="09524-108">Action filters to check the remote IP address of requests for specific controllers or action methods.</span></span>
* <span data-ttu-id="09524-109">Razor-Seiten-Filter, überprüfen Sie die remote IP-Adresse von Anforderungen für Razor-Seiten.</span><span class="sxs-lookup"><span data-stu-id="09524-109">Razor Pages filters to check the remote IP address of requests for Razor pages.</span></span>

<span data-ttu-id="09524-110">In jedem Fall wird eine Zeichenfolge, die genehmigte Client-IP-Adressen in einer app-Einstellung gespeichert.</span><span class="sxs-lookup"><span data-stu-id="09524-110">In each case, a string containing approved client IP addresses is stored in an app setting.</span></span> <span data-ttu-id="09524-111">Die Middleware oder Filter analysiert die Zeichenfolge in eine Liste, und überprüft, ob die remote-IP in der Liste ist.</span><span class="sxs-lookup"><span data-stu-id="09524-111">The middleware or filter parses the string into a list and checks if the remote IP is in the list.</span></span> <span data-ttu-id="09524-112">Wenn dies nicht der Fall ist, ein HTTP 403 Verboten-Statuscode zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="09524-112">If not, an HTTP 403 Forbidden status code is returned.</span></span>

<span data-ttu-id="09524-113">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples/2.x/ClientIpAspNetCore) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="09524-113">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples/2.x/ClientIpAspNetCore) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="the-safelist"></a><span data-ttu-id="09524-114">Die Listen sicherer Adressen</span><span class="sxs-lookup"><span data-stu-id="09524-114">The safelist</span></span>

<span data-ttu-id="09524-115">Die Liste ist so konfiguriert, der *"appSettings.JSON"* Datei.</span><span class="sxs-lookup"><span data-stu-id="09524-115">The list is configured in the *appsettings.json* file.</span></span> <span data-ttu-id="09524-116">Dabei handelt es sich eine durch Semikolons getrennte Liste kann IPv4 und IPv6-Adressen enthalten.</span><span class="sxs-lookup"><span data-stu-id="09524-116">It's a semicolon-delimited list and can contain IPv4 and IPv6 addresses.</span></span>

[!code-json[](ip-safelist/samples/2.x/ClientIpAspNetCore/appsettings.json?highlight=2)]

## <a name="middleware"></a><span data-ttu-id="09524-117">Middleware</span><span class="sxs-lookup"><span data-stu-id="09524-117">Middleware</span></span>

<span data-ttu-id="09524-118">Die `Configure` Methode fügt die Middleware und die Zeichenfolge von Listen sicherer Adressen in einem Konstruktorparameter an diese übergibt.</span><span class="sxs-lookup"><span data-stu-id="09524-118">The `Configure` method adds the middleware and passes the safelist string to it in a constructor parameter.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="09524-119">Die Middleware analysiert die Zeichenfolge in ein Array und sucht nach der remote-IP-Adresse in das Array.</span><span class="sxs-lookup"><span data-stu-id="09524-119">The middleware parses the string into an array and looks for the remote IP address in the array.</span></span> <span data-ttu-id="09524-120">Wenn die remote-IP-Adresse nicht gefunden wird, gibt die Middleware zurück, die HTTP-401 nicht zulässig.</span><span class="sxs-lookup"><span data-stu-id="09524-120">If the remote IP address is not found, the middleware returns HTTP 401 Forbidden.</span></span> <span data-ttu-id="09524-121">Dieser Überprüfungsvorgang wird umgangen, um die HTTP Get-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="09524-121">This validation process is bypassed for HTTP Get requests.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a><span data-ttu-id="09524-122">Action-filter</span><span class="sxs-lookup"><span data-stu-id="09524-122">Action filter</span></span>

<span data-ttu-id="09524-123">Wenn Sie eine von Listen sicherer Adressen nur für bestimmte Controller oder Aktionsmethoden anwenden möchten, verwenden Sie einen Aktionsfilter.</span><span class="sxs-lookup"><span data-stu-id="09524-123">If you want a safelist only for specific controllers or action methods, use an action filter.</span></span> <span data-ttu-id="09524-124">Im Folgenden ein Beispiel:</span><span class="sxs-lookup"><span data-stu-id="09524-124">Here's an example:</span></span> 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIdCheckFilter.cs)]

<span data-ttu-id="09524-125">Der Action-Filter wird zum Dienstcontainer hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="09524-125">The action filter is added to the services container.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

<span data-ttu-id="09524-126">Der Filter kann dann für eine Methode Controller bzw. die Aktionsmethode verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="09524-126">The filter can then be used on a controller or action method.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_Filter&highlight=1)]

<span data-ttu-id="09524-127">In der Beispiel-app der Filter angewendet wird, auf die `Get` Methode.</span><span class="sxs-lookup"><span data-stu-id="09524-127">In the sample app, the filter is applied to the `Get` method.</span></span> <span data-ttu-id="09524-128">Wenn die app zu, indem Sie senden testen also eine `Get` API anfordern, die das Attribut ist die Client-IP-Adresse überprüfen.</span><span class="sxs-lookup"><span data-stu-id="09524-128">So when you test the app by sending a `Get` API request, the attribute is validating the client IP address.</span></span> <span data-ttu-id="09524-129">Wenn Sie testen, indem Sie die API mit anderen HTTP-Methode aufrufen, ist die Middleware die Client-IP-Adresse überprüfen.</span><span class="sxs-lookup"><span data-stu-id="09524-129">When you test by calling the API with any other HTTP method, the middleware is validating the client IP.</span></span>

## <a name="razor-pages-filter"></a><span data-ttu-id="09524-130">Razor-Seiten zu filtern.</span><span class="sxs-lookup"><span data-stu-id="09524-130">Razor Pages filter</span></span> 

<span data-ttu-id="09524-131">Wenn Sie eine von Listen sicherer Adressen für eine Razor-Seiten-app möchten, verwenden Sie einen Razor-Seiten-Filter.</span><span class="sxs-lookup"><span data-stu-id="09524-131">If you want a safelist for a Razor Pages app, use a Razor Pages filter.</span></span> <span data-ttu-id="09524-132">Im Folgenden ein Beispiel:</span><span class="sxs-lookup"><span data-stu-id="09524-132">Here's an example:</span></span> 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIdCheckPageFilter.cs)]

<span data-ttu-id="09524-133">Dieser Filter ist aktiviert, indem sie auf die Auflistung der MVC-Filter hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="09524-133">This filter is enabled by adding it to the MVC Filters collection.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

<span data-ttu-id="09524-134">Wenn Sie die app ausführen und eine Razor-Seite anfordern, ist der Filter für die Razor-Seiten die Client-IP-Adresse überprüfen.</span><span class="sxs-lookup"><span data-stu-id="09524-134">When you run the app and request a Razor page, the Razor Pages filter is validating the client IP.</span></span>

## <a name="next-steps"></a><span data-ttu-id="09524-135">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="09524-135">Next steps</span></span>

<span data-ttu-id="09524-136">[Erfahren Sie mehr über ASP.NET Core-Middleware](xref:fundamentals/middleware/index).</span><span class="sxs-lookup"><span data-stu-id="09524-136">[Learn more about ASP.NET Core Middleware](xref:fundamentals/middleware/index).</span></span>

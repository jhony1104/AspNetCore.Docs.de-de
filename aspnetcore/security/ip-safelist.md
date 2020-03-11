---
title: Client-IP-SafeList für ASP.net Core
author: damienbod
description: Erfahren Sie, wie Sie Middleware oder Aktionsfilter schreiben, um Remote-IP-Adressen anhand einer Liste genehmigter IP-Adressen zu überprüfen.
ms.author: riande
ms.custom: mvc
ms.date: 08/31/2018
uid: security/ip-safelist
ms.openlocfilehash: d25c375f7e659168ab8cc9d8e11753cb7dfde831
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652051"
---
# <a name="client-ip-safelist-for-aspnet-core"></a><span data-ttu-id="f3290-103">Client-IP-SafeList für ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="f3290-103">Client IP safelist for ASP.NET Core</span></span>

<span data-ttu-id="f3290-104">Von [Damien Bowder](https://twitter.com/damien_bod) und [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="f3290-104">By [Damien Bowden](https://twitter.com/damien_bod) and [Tom Dykstra](https://github.com/tdykstra)</span></span>
 
<span data-ttu-id="f3290-105">In diesem Artikel werden drei Möglichkeiten beschrieben, wie Sie eine IP-Safelist (auch als Whitelist bezeichnet) in einer ASP.net Core-App implementieren.</span><span class="sxs-lookup"><span data-stu-id="f3290-105">This article shows three ways to implement an IP safelist (also known as a whitelist) in an ASP.NET Core app.</span></span> <span data-ttu-id="f3290-106">Verwenden Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="f3290-106">You can use:</span></span>

* <span data-ttu-id="f3290-107">Middleware zum Überprüfen der Remote-IP-Adresse für jede Anforderung.</span><span class="sxs-lookup"><span data-stu-id="f3290-107">Middleware to check the remote IP address of every request.</span></span>
* <span data-ttu-id="f3290-108">Aktionsfilter zum Überprüfen der Remote-IP-Adresse von Anforderungen für bestimmte Controller oder Aktionsmethoden.</span><span class="sxs-lookup"><span data-stu-id="f3290-108">Action filters to check the remote IP address of requests for specific controllers or action methods.</span></span>
* <span data-ttu-id="f3290-109">Razor Pages Filter zum Überprüfen der Remote-IP-Adresse von Anforderungen für Razor pages.</span><span class="sxs-lookup"><span data-stu-id="f3290-109">Razor Pages filters to check the remote IP address of requests for Razor pages.</span></span>

<span data-ttu-id="f3290-110">In jedem Fall wird eine Zeichenfolge mit genehmigten Client-IP-Adressen in einer APP-Einstellung gespeichert.</span><span class="sxs-lookup"><span data-stu-id="f3290-110">In each case, a string containing approved client IP addresses is stored in an app setting.</span></span> <span data-ttu-id="f3290-111">Die Middleware oder der Filter analysiert die Zeichenfolge in eine Liste und überprüft, ob die Remote-IP-Adresse in der Liste enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="f3290-111">The middleware or filter parses the string into a list and checks if the remote IP is in the list.</span></span> <span data-ttu-id="f3290-112">Wenn dies nicht der Wert ist, wird der Statuscode HTTP 403 verboten zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="f3290-112">If not, an HTTP 403 Forbidden status code is returned.</span></span>

<span data-ttu-id="f3290-113">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples/2.x/ClientIpAspNetCore) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f3290-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples/2.x/ClientIpAspNetCore) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="the-safelist"></a><span data-ttu-id="f3290-114">Die SafeList</span><span class="sxs-lookup"><span data-stu-id="f3290-114">The safelist</span></span>

<span data-ttu-id="f3290-115">Die Liste wird in der Datei " *appSettings. JSON* " konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="f3290-115">The list is configured in the *appsettings.json* file.</span></span> <span data-ttu-id="f3290-116">Dabei handelt es sich um eine durch Semikolons getrennte Liste, die IPv4-und IPv6-Adressen enthalten kann.</span><span class="sxs-lookup"><span data-stu-id="f3290-116">It's a semicolon-delimited list and can contain IPv4 and IPv6 addresses.</span></span>

[!code-json[](ip-safelist/samples/2.x/ClientIpAspNetCore/appsettings.json?highlight=2)]

## <a name="middleware"></a><span data-ttu-id="f3290-117">Middleware</span><span class="sxs-lookup"><span data-stu-id="f3290-117">Middleware</span></span>

<span data-ttu-id="f3290-118">Die `Configure`-Methode fügt die Middleware hinzu und übergibt die SafeList-Zeichenfolge in einem Konstruktorparameter.</span><span class="sxs-lookup"><span data-stu-id="f3290-118">The `Configure` method adds the middleware and passes the safelist string to it in a constructor parameter.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="f3290-119">Die Middleware analysiert die Zeichenfolge in ein Array und sucht im Array nach der Remote-IP-Adresse.</span><span class="sxs-lookup"><span data-stu-id="f3290-119">The middleware parses the string into an array and looks for the remote IP address in the array.</span></span> <span data-ttu-id="f3290-120">Wenn die Remote-IP-Adresse nicht gefunden wird, gibt die Middleware HTTP 401 unzulässig zurück.</span><span class="sxs-lookup"><span data-stu-id="f3290-120">If the remote IP address is not found, the middleware returns HTTP 401 Forbidden.</span></span> <span data-ttu-id="f3290-121">Dieser Überprüfungs Vorgang wird für HTTP GET-Anforderungen umgangen.</span><span class="sxs-lookup"><span data-stu-id="f3290-121">This validation process is bypassed for HTTP Get requests.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a><span data-ttu-id="f3290-122">Aktionsfilter</span><span class="sxs-lookup"><span data-stu-id="f3290-122">Action filter</span></span>

<span data-ttu-id="f3290-123">Wenn Sie eine SafeList nur für bestimmte Controller oder Aktionsmethoden verwenden möchten, verwenden Sie einen Aktionsfilter.</span><span class="sxs-lookup"><span data-stu-id="f3290-123">If you want a safelist only for specific controllers or action methods, use an action filter.</span></span> <span data-ttu-id="f3290-124">Hier sehen Sie ein Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f3290-124">Here's an example:</span></span> 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIpCheckFilter.cs)]

<span data-ttu-id="f3290-125">Der Aktionsfilter wird dem Dienst Container hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="f3290-125">The action filter is added to the services container.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

<span data-ttu-id="f3290-126">Der Filter kann dann für einen Controller oder eine Aktionsmethode verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="f3290-126">The filter can then be used on a controller or action method.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_Filter&highlight=1)]

<span data-ttu-id="f3290-127">In der Beispiel-APP wird der Filter auf die `Get`-Methode angewendet.</span><span class="sxs-lookup"><span data-stu-id="f3290-127">In the sample app, the filter is applied to the `Get` method.</span></span> <span data-ttu-id="f3290-128">Wenn Sie also die APP testen, indem Sie eine `Get` API-Anforderung senden, überprüft das Attribut die Client-IP-Adresse.</span><span class="sxs-lookup"><span data-stu-id="f3290-128">So when you test the app by sending a `Get` API request, the attribute is validating the client IP address.</span></span> <span data-ttu-id="f3290-129">Wenn Sie den Test durch Aufrufen der API mit einer anderen HTTP-Methode durchlaufen, überprüft die Middleware die Client-IP-Adresse.</span><span class="sxs-lookup"><span data-stu-id="f3290-129">When you test by calling the API with any other HTTP method, the middleware is validating the client IP.</span></span>

## <a name="razor-pages-filter"></a><span data-ttu-id="f3290-130">Razor Pages Filter</span><span class="sxs-lookup"><span data-stu-id="f3290-130">Razor Pages filter</span></span> 

<span data-ttu-id="f3290-131">Wenn Sie eine SafeList für eine Razor Pages-App verwenden möchten, verwenden Sie einen Razor Pages Filter.</span><span class="sxs-lookup"><span data-stu-id="f3290-131">If you want a safelist for a Razor Pages app, use a Razor Pages filter.</span></span> <span data-ttu-id="f3290-132">Hier sehen Sie ein Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f3290-132">Here's an example:</span></span> 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIpCheckPageFilter.cs)]

<span data-ttu-id="f3290-133">Dieser Filter wird aktiviert, indem er der MVC-Filter Auflistung hinzugefügt wird.</span><span class="sxs-lookup"><span data-stu-id="f3290-133">This filter is enabled by adding it to the MVC Filters collection.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

<span data-ttu-id="f3290-134">Wenn Sie die app ausführen und eine Razor Page anfordern, überprüft der Razor Pages Filter die Client-IP-Adresse.</span><span class="sxs-lookup"><span data-stu-id="f3290-134">When you run the app and request a Razor page, the Razor Pages filter is validating the client IP.</span></span>

## <a name="next-steps"></a><span data-ttu-id="f3290-135">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="f3290-135">Next steps</span></span>

<span data-ttu-id="f3290-136">[Erfahren Sie mehr über ASP.net Core Middleware](xref:fundamentals/middleware/index).</span><span class="sxs-lookup"><span data-stu-id="f3290-136">[Learn more about ASP.NET Core Middleware](xref:fundamentals/middleware/index).</span></span>

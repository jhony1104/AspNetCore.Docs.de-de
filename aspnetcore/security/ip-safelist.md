---
title: Client-IP-Listen sicherer Adressen für ASP.NET Core
author: damienbod
description: Erfahren Sie, wie Middleware oder Aktion Schreibfilter zum remote-IP-Adressen mit einer Liste der zulässigen IP-Adressen zu überprüfen.
ms.author: tdykstra
ms.custom: mvc
ms.date: 08/31/2018
uid: security/ip-safelist
ms.openlocfilehash: cfbb50ea33ae3af577f13b00bccc75fe0be57f79
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64898147"
---
# <a name="client-ip-safelist-for-aspnet-core"></a><span data-ttu-id="af196-103">Client-IP-Listen sicherer Adressen für ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="af196-103">Client IP safelist for ASP.NET Core</span></span>

<span data-ttu-id="af196-104">Durch [Damien Bowden](https://twitter.com/damien_bod) und [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="af196-104">By [Damien Bowden](https://twitter.com/damien_bod) and [Tom Dykstra](https://github.com/tdykstra)</span></span>
 
<span data-ttu-id="af196-105">In diesem Artikel zeigt drei Möglichkeiten, eine IP-Listen sicherer Adressen (auch bekannt als eine Whitelist) in einer ASP.NET Core-app zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="af196-105">This article shows three ways to implement an IP safelist (also known as a whitelist) in an ASP.NET Core app.</span></span> <span data-ttu-id="af196-106">Sie können Folgendes verwenden:</span><span class="sxs-lookup"><span data-stu-id="af196-106">You can use:</span></span>

* <span data-ttu-id="af196-107">Die Middleware die remote IP-Adresse jeder Anforderung überprüfen.</span><span class="sxs-lookup"><span data-stu-id="af196-107">Middleware to check the remote IP address of every request.</span></span>
* <span data-ttu-id="af196-108">Aktionsfilter, um die Anforderungen für bestimmte Controller oder Aktionsmethoden remote IP-Adresse zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="af196-108">Action filters to check the remote IP address of requests for specific controllers or action methods.</span></span>
* <span data-ttu-id="af196-109">Razor-Seiten-Filter, überprüfen Sie die remote IP-Adresse von Anforderungen für Razor-Seiten.</span><span class="sxs-lookup"><span data-stu-id="af196-109">Razor Pages filters to check the remote IP address of requests for Razor pages.</span></span>

<span data-ttu-id="af196-110">Die Beispiel-app zeigt beide Ansätze.</span><span class="sxs-lookup"><span data-stu-id="af196-110">The sample app illustrates both approaches.</span></span> <span data-ttu-id="af196-111">In jedem Fall wird eine Zeichenfolge, die genehmigte Client-IP-Adressen in einer app-Einstellung gespeichert.</span><span class="sxs-lookup"><span data-stu-id="af196-111">In each case, a string containing approved client IP addresses is stored in an app setting.</span></span> <span data-ttu-id="af196-112">Die Middleware oder Filter analysiert die Zeichenfolge in eine Liste, und überprüft, ob die remote-IP in der Liste ist.</span><span class="sxs-lookup"><span data-stu-id="af196-112">The middleware or filter parses the string into a list and  checks if the remote IP is in the list.</span></span> <span data-ttu-id="af196-113">Wenn dies nicht der Fall ist, ein HTTP 403 Verboten-Statuscode zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="af196-113">If not, an HTTP 403 Forbidden status code is returned.</span></span>

<span data-ttu-id="af196-114">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples/2.x/ClientIpAspNetCore) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="af196-114">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples/2.x/ClientIpAspNetCore) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="the-safelist"></a><span data-ttu-id="af196-115">Die Listen sicherer Adressen</span><span class="sxs-lookup"><span data-stu-id="af196-115">The safelist</span></span>

<span data-ttu-id="af196-116">Die Liste ist so konfiguriert, der *"appSettings.JSON"* Datei.</span><span class="sxs-lookup"><span data-stu-id="af196-116">The list is configured in the *appsettings.json* file.</span></span> <span data-ttu-id="af196-117">Dabei handelt es sich eine durch Semikolons getrennte Liste kann IPv4 und IPv6-Adressen enthalten.</span><span class="sxs-lookup"><span data-stu-id="af196-117">It's a semicolon-delimited list and can contain IPv4 and IPv6 addresses.</span></span>

[!code-json[](ip-safelist/samples/2.x/ClientIpAspNetCore/appsettings.json?highlight=2)]

## <a name="middleware"></a><span data-ttu-id="af196-118">Middleware</span><span class="sxs-lookup"><span data-stu-id="af196-118">Middleware</span></span>

<span data-ttu-id="af196-119">Die `Configure` Methode fügt die Middleware und die Zeichenfolge von Listen sicherer Adressen in einem Konstruktorparameter an diese übergibt.</span><span class="sxs-lookup"><span data-stu-id="af196-119">The `Configure` method adds the middleware and passes the safelist string to it in a constructor parameter.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_Configure&highlight=7)]

<span data-ttu-id="af196-120">Die Middleware analysiert die Zeichenfolge in ein Array und sucht nach der remote-IP-Adresse in das Array.</span><span class="sxs-lookup"><span data-stu-id="af196-120">The middleware parses the string into an array and looks for the remote IP address in the array.</span></span> <span data-ttu-id="af196-121">Wenn die remote-IP-Adresse nicht gefunden wird, gibt die Middleware zurück, die HTTP-401 nicht zulässig.</span><span class="sxs-lookup"><span data-stu-id="af196-121">If the remote IP address is not found, the middleware returns HTTP 401 Forbidden.</span></span> <span data-ttu-id="af196-122">Dieser Überprüfungsvorgang wird umgangen, um die HTTP Get-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="af196-122">This validation process is bypassed for HTTP Get requests.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a><span data-ttu-id="af196-123">Action-filter</span><span class="sxs-lookup"><span data-stu-id="af196-123">Action filter</span></span>

<span data-ttu-id="af196-124">Wenn Sie eine von Listen sicherer Adressen nur für bestimmte Controller oder Aktionsmethoden anwenden möchten, verwenden Sie einen Aktionsfilter.</span><span class="sxs-lookup"><span data-stu-id="af196-124">If you want a safelist only for specific controllers or action methods, use an action filter.</span></span> <span data-ttu-id="af196-125">Im Folgenden ein Beispiel:</span><span class="sxs-lookup"><span data-stu-id="af196-125">Here's an example:</span></span> 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIdCheckFilter.cs)]

<span data-ttu-id="af196-126">Der Action-Filter wird zum Dienstcontainer hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="af196-126">The action filter is added to the services container.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

<span data-ttu-id="af196-127">Der Filter kann dann für eine Methode Controller bzw. die Aktionsmethode verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="af196-127">The filter can then be used on a controller or action method.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_Filter&highlight=1)]

<span data-ttu-id="af196-128">In der Beispiel-app der Filter angewendet wird, auf die `Get` Methode.</span><span class="sxs-lookup"><span data-stu-id="af196-128">In the sample app, the filter is applied to the `Get` method.</span></span> <span data-ttu-id="af196-129">Wenn die app zu, indem Sie senden testen also eine `Get` API anfordern, die das Attribut ist die Client-IP-Adresse überprüfen.</span><span class="sxs-lookup"><span data-stu-id="af196-129">So when you test the app by sending a `Get` API request, the attribute is validating the client IP address.</span></span> <span data-ttu-id="af196-130">Wenn Sie testen, indem Sie die API mit anderen HTTP-Methode aufrufen, ist die Middleware die Client-IP-Adresse überprüfen.</span><span class="sxs-lookup"><span data-stu-id="af196-130">When you test by calling the API with any other HTTP method, the middleware is validating the client IP.</span></span>

## <a name="razor-pages-filter"></a><span data-ttu-id="af196-131">Razor-Seiten zu filtern.</span><span class="sxs-lookup"><span data-stu-id="af196-131">Razor Pages filter</span></span> 

<span data-ttu-id="af196-132">Wenn Sie eine von Listen sicherer Adressen für eine Razor-Seiten-app möchten, verwenden Sie einen Razor-Seiten-Filter.</span><span class="sxs-lookup"><span data-stu-id="af196-132">If you want a safelist for a Razor Pages app, use a Razor Pages filter.</span></span> <span data-ttu-id="af196-133">Im Folgenden ein Beispiel:</span><span class="sxs-lookup"><span data-stu-id="af196-133">Here's an example:</span></span> 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIdCheckPageFilter.cs)]

<span data-ttu-id="af196-134">Dieser Filter ist aktiviert, indem sie auf die Auflistung der MVC-Filter hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="af196-134">This filter is enabled by adding it to the MVC Filters collection.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

<span data-ttu-id="af196-135">Wenn Sie die app ausführen und eine Razor-Seite anfordern, ist der Filter für die Razor-Seiten die Client-IP-Adresse überprüfen.</span><span class="sxs-lookup"><span data-stu-id="af196-135">When you run the app and request a Razor page, the Razor Pages filter is validating the client IP.</span></span>

## <a name="next-steps"></a><span data-ttu-id="af196-136">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="af196-136">Next steps</span></span>

<span data-ttu-id="af196-137">[Erfahren Sie mehr über ASP.NET Core-Middleware](xref:fundamentals/middleware/index).</span><span class="sxs-lookup"><span data-stu-id="af196-137">[Learn more about ASP.NET Core Middleware](xref:fundamentals/middleware/index).</span></span>

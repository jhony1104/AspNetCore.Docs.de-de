---
title: Client IP-Safelist für ASP.NET Core
author: damienbod
description: Erfahren Sie, wie Sie Middleware oder Aktionsfilter schreiben, um Remote-IP-Adressen anhand einer Liste genehmigter IP-Adressen zu überprüfen.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/12/2020
uid: security/ip-safelist
ms.openlocfilehash: 2db879a6918245cbacff8b1a5dc15786ffab6a34
ms.sourcegitcommit: 196e4a36df5be5b04fedcff484a4261f8046ec57
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80471804"
---
# <a name="client-ip-safelist-for-aspnet-core"></a><span data-ttu-id="fb5a0-103">Client IP-Safelist für ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fb5a0-103">Client IP safelist for ASP.NET Core</span></span>

<span data-ttu-id="fb5a0-104">Von [Damien Bowden](https://twitter.com/damien_bod) und [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="fb5a0-104">By [Damien Bowden](https://twitter.com/damien_bod) and [Tom Dykstra](https://github.com/tdykstra)</span></span>
 
<span data-ttu-id="fb5a0-105">Dieser Artikel zeigt drei Möglichkeiten zum Implementieren einer IP-Adress-Safelist (auch als Zulassungsliste bezeichnet) in einer ASP.NET Core-App.</span><span class="sxs-lookup"><span data-stu-id="fb5a0-105">This article shows three ways to implement an IP address safelist (also known as an allow list) in an ASP.NET Core app.</span></span> <span data-ttu-id="fb5a0-106">Eine begleitende Beispiel-App zeigt alle drei Ansätze.</span><span class="sxs-lookup"><span data-stu-id="fb5a0-106">An accompanying sample app demonstrates all three approaches.</span></span> <span data-ttu-id="fb5a0-107">Verwenden Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="fb5a0-107">You can use:</span></span>

* <span data-ttu-id="fb5a0-108">Middleware, um die Remote-IP-Adresse jeder Anforderung zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="fb5a0-108">Middleware to check the remote IP address of every request.</span></span>
* <span data-ttu-id="fb5a0-109">MVC-Aktionsfilter, um die Remote-IP-Adresse von Anforderungen für bestimmte Controller oder Aktionsmethoden zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="fb5a0-109">MVC action filters to check the remote IP address of requests for specific controllers or action methods.</span></span>
* <span data-ttu-id="fb5a0-110">Razor Pages filtert, um die Remote-IP-Adresse von Anforderungen für Razor-Seiten zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="fb5a0-110">Razor Pages filters to check the remote IP address of requests for Razor pages.</span></span>

<span data-ttu-id="fb5a0-111">In jedem Fall wird eine Zeichenfolge mit genehmigten Client-IP-Adressen in einer App-Einstellung gespeichert.</span><span class="sxs-lookup"><span data-stu-id="fb5a0-111">In each case, a string containing approved client IP addresses is stored in an app setting.</span></span> <span data-ttu-id="fb5a0-112">Die Middleware oder der Filter:</span><span class="sxs-lookup"><span data-stu-id="fb5a0-112">The middleware or filter:</span></span>

* <span data-ttu-id="fb5a0-113">Analysiert die Zeichenfolge in einem Array.</span><span class="sxs-lookup"><span data-stu-id="fb5a0-113">Parses the string into an array.</span></span> 
* <span data-ttu-id="fb5a0-114">Überprüft, ob die Remote-IP-Adresse im Array vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="fb5a0-114">Checks if the remote IP address exists in the array.</span></span>

<span data-ttu-id="fb5a0-115">Der Zugriff ist zulässig, wenn das Array die IP-Adresse enthält.</span><span class="sxs-lookup"><span data-stu-id="fb5a0-115">Access is allowed if the array contains the IP address.</span></span> <span data-ttu-id="fb5a0-116">Andernfalls wird ein HTTP 403 Forbidden Statuscode zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="fb5a0-116">Otherwise, an HTTP 403 Forbidden status code is returned.</span></span>

<span data-ttu-id="fb5a0-117">[Beispielcode anzeigen oder herunterladen](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) ([downloaden](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fb5a0-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ip-address-safelist"></a><span data-ttu-id="fb5a0-118">IP-Adresse Safelist</span><span class="sxs-lookup"><span data-stu-id="fb5a0-118">IP address safelist</span></span>

<span data-ttu-id="fb5a0-119">In der Beispiel-App lautet die IP-Adress-Safelist:</span><span class="sxs-lookup"><span data-stu-id="fb5a0-119">In the sample app, the IP address safelist is:</span></span>

* <span data-ttu-id="fb5a0-120">Definiert durch `AdminSafeList` die Eigenschaft in der Datei *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="fb5a0-120">Defined by the `AdminSafeList` property in the *appsettings.json* file.</span></span>
* <span data-ttu-id="fb5a0-121">Eine durch Semikolons getrennte Zeichenfolge, die sowohl Adressen des [Internetprotokolls Version 4 (IPv4) als](https://wikipedia.org/wiki/IPv4) auch des [Internetprotokolls Version 6 (IPv6)](https://wikipedia.org/wiki/IPv6) enthalten kann.</span><span class="sxs-lookup"><span data-stu-id="fb5a0-121">A semicolon-delimited string that may contain both [Internet Protocol version 4 (IPv4)](https://wikipedia.org/wiki/IPv4) and [Internet Protocol version 6 (IPv6)](https://wikipedia.org/wiki/IPv6) addresses.</span></span>

[!code-json[](ip-safelist/samples/3.x/ClientIpAspNetCore/appsettings.json?range=1-3&highlight=2)]

<span data-ttu-id="fb5a0-122">Im `127.0.0.1` vorherigen Beispiel sind die IPv4-Adressen von und `192.168.1.5` die `::1` IPv6-Loopback-Adresse von (komprimiertes Format für `0:0:0:0:0:0:0:1`) zulässig.</span><span class="sxs-lookup"><span data-stu-id="fb5a0-122">In the preceding example, the IPv4 addresses of `127.0.0.1` and `192.168.1.5` and the IPv6 loopback address of `::1` (compressed format for `0:0:0:0:0:0:0:1`) are allowed.</span></span>

## <a name="middleware"></a><span data-ttu-id="fb5a0-123">Middleware</span><span class="sxs-lookup"><span data-stu-id="fb5a0-123">Middleware</span></span>

<span data-ttu-id="fb5a0-124">Die `Startup.Configure` Methode fügt `AdminSafeListMiddleware` der Anforderungspipeline der App den benutzerdefinierten Middlewaretyp hinzu.</span><span class="sxs-lookup"><span data-stu-id="fb5a0-124">The `Startup.Configure` method adds the custom `AdminSafeListMiddleware` middleware type to the app's request pipeline.</span></span> <span data-ttu-id="fb5a0-125">Die Safelist wird mit dem .NET Core-Konfigurationsanbieter abgerufen und als Konstruktorparameter übergeben.</span><span class="sxs-lookup"><span data-stu-id="fb5a0-125">The safelist is retrieved with the .NET Core configuration provider and is passed as a constructor parameter.</span></span>

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureAddMiddleware)]

<span data-ttu-id="fb5a0-126">Die Middleware analysiert die Zeichenfolge in ein Array und sucht nach der Remote-IP-Adresse im Array.</span><span class="sxs-lookup"><span data-stu-id="fb5a0-126">The middleware parses the string into an array and searches for the remote IP address in the array.</span></span> <span data-ttu-id="fb5a0-127">Wenn die Remote-IP-Adresse nicht gefunden wird, gibt die Middleware HTTP 403 Forbidden zurück.</span><span class="sxs-lookup"><span data-stu-id="fb5a0-127">If the remote IP address isn't found, the middleware returns HTTP 403 Forbidden.</span></span> <span data-ttu-id="fb5a0-128">Dieser Validierungsprozess wird für HTTP GET-Anforderungen umgangen.</span><span class="sxs-lookup"><span data-stu-id="fb5a0-128">This validation process is bypassed for HTTP GET requests.</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Middlewares/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a><span data-ttu-id="fb5a0-129">Aktionsfilter</span><span class="sxs-lookup"><span data-stu-id="fb5a0-129">Action filter</span></span>

<span data-ttu-id="fb5a0-130">Wenn Sie eine sicherlistengesteuerte Zugriffssteuerung für bestimmte MVC-Controller oder Aktionsmethoden wünschen, verwenden Sie einen Aktionsfilter.</span><span class="sxs-lookup"><span data-stu-id="fb5a0-130">If you want safelist-driven access control for specific MVC controllers or action methods, use an action filter.</span></span> <span data-ttu-id="fb5a0-131">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="fb5a0-131">For example:</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckActionFilter.cs?name=snippet_ClassOnly)]

<span data-ttu-id="fb5a0-132">Fügen `Startup.ConfigureServices`Sie den Aktionsfilter der MVC-Filterauflistung hinzu.</span><span class="sxs-lookup"><span data-stu-id="fb5a0-132">In `Startup.ConfigureServices`, add the action filter to the MVC filters collection.</span></span> <span data-ttu-id="fb5a0-133">Im folgenden Beispiel `ClientIpCheckActionFilter` wird ein Aktionsfilter hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="fb5a0-133">In the following example, a `ClientIpCheckActionFilter` action filter is added.</span></span> <span data-ttu-id="fb5a0-134">Eine Safelist und eine Konsolenprotokollierungsinstanz werden als Konstruktorparameter übergeben.</span><span class="sxs-lookup"><span data-stu-id="fb5a0-134">A safelist and a console logger instance are passed as constructor parameters.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

<span data-ttu-id="fb5a0-135">Der Aktionsfilter kann dann auf einen Controller oder eine Aktionsmethode mit dem [Attribut [ServiceFilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="fb5a0-135">The action filter can then be applied to a controller or action method with the [[ServiceFilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) attribute:</span></span>

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_ActionFilter&highlight=1)]

<span data-ttu-id="fb5a0-136">In der Beispiel-App wird der Aktionsfilter `Get` auf die Aktionsmethode des Controllers angewendet.</span><span class="sxs-lookup"><span data-stu-id="fb5a0-136">In the sample app, the action filter is applied to the controller's `Get` action method.</span></span> <span data-ttu-id="fb5a0-137">Wenn Sie die App testen, indem Sie folgende Daten senden:</span><span class="sxs-lookup"><span data-stu-id="fb5a0-137">When you test the app by sending:</span></span>

* <span data-ttu-id="fb5a0-138">Eine HTTP GET-Anforderung, das `[ServiceFilter]` Attribut überprüft die Client-IP-Adresse.</span><span class="sxs-lookup"><span data-stu-id="fb5a0-138">An HTTP GET request, the `[ServiceFilter]` attribute validates the client IP address.</span></span> <span data-ttu-id="fb5a0-139">Wenn der Zugriff `Get` auf die Aktionsmethode zulässig ist, wird eine Variation der folgenden Konsolenausgabe durch den Aktionsfilter und die Aktionsmethode erzeugt:</span><span class="sxs-lookup"><span data-stu-id="fb5a0-139">If access is allowed to the `Get` action method, a variation of the following console output is produced by the action filter and action method:</span></span>

    ```
    dbug: ClientIpSafelistComponents.Filters.ClientIpCheckActionFilter[0]
          Remote IpAddress: ::1
    dbug: ClientIpAspNetCore.Controllers.ValuesController[0]
          successful HTTP GET    
    ```

* <span data-ttu-id="fb5a0-140">Ein anderes HTTP-Anforderungsverb `AdminSafeListMiddleware` als GET, die Middleware überprüft die Client-IP-Adresse.</span><span class="sxs-lookup"><span data-stu-id="fb5a0-140">An HTTP request verb other than GET, the `AdminSafeListMiddleware` middleware validates the client IP address.</span></span>

## <a name="razor-pages-filter"></a><span data-ttu-id="fb5a0-141">Razor Pages Filter</span><span class="sxs-lookup"><span data-stu-id="fb5a0-141">Razor Pages filter</span></span>

<span data-ttu-id="fb5a0-142">Wenn Sie eine sicherauflistgesteuerte Zugriffssteuerung für eine Razor Pages-App wünschen, verwenden Sie einen Razor Pages-Filter.</span><span class="sxs-lookup"><span data-stu-id="fb5a0-142">If you want safelist-driven access control for a Razor Pages app, use a Razor Pages filter.</span></span> <span data-ttu-id="fb5a0-143">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="fb5a0-143">For example:</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckPageFilter.cs?name=snippet_ClassOnly)]

<span data-ttu-id="fb5a0-144">Aktivieren `Startup.ConfigureServices`Sie den Filter Razor Pages, indem Sie ihn der MVC-Filterauflistung hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="fb5a0-144">In `Startup.ConfigureServices`, enable the Razor Pages filter by adding it to the MVC filters collection.</span></span> <span data-ttu-id="fb5a0-145">Im folgenden Beispiel `ClientIpCheckPageFilter` wird ein Razor Pages-Filter hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="fb5a0-145">In the following example, a `ClientIpCheckPageFilter` Razor Pages filter is added.</span></span> <span data-ttu-id="fb5a0-146">Eine Safelist und eine Konsolenprotokollierungsinstanz werden als Konstruktorparameter übergeben.</span><span class="sxs-lookup"><span data-stu-id="fb5a0-146">A safelist and a console logger instance are passed as constructor parameters.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

<span data-ttu-id="fb5a0-147">Wenn die *Index* Razor-Seite der Beispiel-App angefordert wird, überprüft der Filter Razor Pages die Client-IP-Adresse.</span><span class="sxs-lookup"><span data-stu-id="fb5a0-147">When the sample app's *Index* Razor page is requested, the Razor Pages filter validates the client IP address.</span></span> <span data-ttu-id="fb5a0-148">Der Filter erzeugt eine Variation der folgenden Konsolenausgabe:</span><span class="sxs-lookup"><span data-stu-id="fb5a0-148">The filter produces a variation of the following console output:</span></span>

```
dbug: ClientIpSafelistComponents.Filters.ClientIpCheckPageFilter[0]
      Remote IpAddress: ::1
```

## <a name="additional-resources"></a><span data-ttu-id="fb5a0-149">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="fb5a0-149">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="fb5a0-150">Aktionsfilter</span><span class="sxs-lookup"><span data-stu-id="fb5a0-150">Action filters</span></span>](xref:mvc/controllers/filters#action-filters)
* <xref:razor-pages/filter>

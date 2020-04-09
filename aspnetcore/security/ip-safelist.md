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
# <a name="client-ip-safelist-for-aspnet-core"></a>Client IP-Safelist für ASP.NET Core

Von [Damien Bowden](https://twitter.com/damien_bod) und [Tom Dykstra](https://github.com/tdykstra)
 
Dieser Artikel zeigt drei Möglichkeiten zum Implementieren einer IP-Adress-Safelist (auch als Zulassungsliste bezeichnet) in einer ASP.NET Core-App. Eine begleitende Beispiel-App zeigt alle drei Ansätze. Verwenden Sie Folgendes:

* Middleware, um die Remote-IP-Adresse jeder Anforderung zu überprüfen.
* MVC-Aktionsfilter, um die Remote-IP-Adresse von Anforderungen für bestimmte Controller oder Aktionsmethoden zu überprüfen.
* Razor Pages filtert, um die Remote-IP-Adresse von Anforderungen für Razor-Seiten zu überprüfen.

In jedem Fall wird eine Zeichenfolge mit genehmigten Client-IP-Adressen in einer App-Einstellung gespeichert. Die Middleware oder der Filter:

* Analysiert die Zeichenfolge in einem Array. 
* Überprüft, ob die Remote-IP-Adresse im Array vorhanden ist.

Der Zugriff ist zulässig, wenn das Array die IP-Adresse enthält. Andernfalls wird ein HTTP 403 Forbidden Statuscode zurückgegeben.

[Beispielcode anzeigen oder herunterladen](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) ([downloaden](xref:index#how-to-download-a-sample))

## <a name="ip-address-safelist"></a>IP-Adresse Safelist

In der Beispiel-App lautet die IP-Adress-Safelist:

* Definiert durch `AdminSafeList` die Eigenschaft in der Datei *appsettings.json.*
* Eine durch Semikolons getrennte Zeichenfolge, die sowohl Adressen des [Internetprotokolls Version 4 (IPv4) als](https://wikipedia.org/wiki/IPv4) auch des [Internetprotokolls Version 6 (IPv6)](https://wikipedia.org/wiki/IPv6) enthalten kann.

[!code-json[](ip-safelist/samples/3.x/ClientIpAspNetCore/appsettings.json?range=1-3&highlight=2)]

Im `127.0.0.1` vorherigen Beispiel sind die IPv4-Adressen von und `192.168.1.5` die `::1` IPv6-Loopback-Adresse von (komprimiertes Format für `0:0:0:0:0:0:0:1`) zulässig.

## <a name="middleware"></a>Middleware

Die `Startup.Configure` Methode fügt `AdminSafeListMiddleware` der Anforderungspipeline der App den benutzerdefinierten Middlewaretyp hinzu. Die Safelist wird mit dem .NET Core-Konfigurationsanbieter abgerufen und als Konstruktorparameter übergeben.

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureAddMiddleware)]

Die Middleware analysiert die Zeichenfolge in ein Array und sucht nach der Remote-IP-Adresse im Array. Wenn die Remote-IP-Adresse nicht gefunden wird, gibt die Middleware HTTP 403 Forbidden zurück. Dieser Validierungsprozess wird für HTTP GET-Anforderungen umgangen.

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Middlewares/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a>Aktionsfilter

Wenn Sie eine sicherlistengesteuerte Zugriffssteuerung für bestimmte MVC-Controller oder Aktionsmethoden wünschen, verwenden Sie einen Aktionsfilter. Beispiel:

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckActionFilter.cs?name=snippet_ClassOnly)]

Fügen `Startup.ConfigureServices`Sie den Aktionsfilter der MVC-Filterauflistung hinzu. Im folgenden Beispiel `ClientIpCheckActionFilter` wird ein Aktionsfilter hinzugefügt. Eine Safelist und eine Konsolenprotokollierungsinstanz werden als Konstruktorparameter übergeben.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

Der Aktionsfilter kann dann auf einen Controller oder eine Aktionsmethode mit dem [Attribut [ServiceFilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) angewendet werden:

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_ActionFilter&highlight=1)]

In der Beispiel-App wird der Aktionsfilter `Get` auf die Aktionsmethode des Controllers angewendet. Wenn Sie die App testen, indem Sie folgende Daten senden:

* Eine HTTP GET-Anforderung, das `[ServiceFilter]` Attribut überprüft die Client-IP-Adresse. Wenn der Zugriff `Get` auf die Aktionsmethode zulässig ist, wird eine Variation der folgenden Konsolenausgabe durch den Aktionsfilter und die Aktionsmethode erzeugt:

    ```
    dbug: ClientIpSafelistComponents.Filters.ClientIpCheckActionFilter[0]
          Remote IpAddress: ::1
    dbug: ClientIpAspNetCore.Controllers.ValuesController[0]
          successful HTTP GET    
    ```

* Ein anderes HTTP-Anforderungsverb `AdminSafeListMiddleware` als GET, die Middleware überprüft die Client-IP-Adresse.

## <a name="razor-pages-filter"></a>Razor Pages Filter

Wenn Sie eine sicherauflistgesteuerte Zugriffssteuerung für eine Razor Pages-App wünschen, verwenden Sie einen Razor Pages-Filter. Beispiel:

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckPageFilter.cs?name=snippet_ClassOnly)]

Aktivieren `Startup.ConfigureServices`Sie den Filter Razor Pages, indem Sie ihn der MVC-Filterauflistung hinzufügen. Im folgenden Beispiel `ClientIpCheckPageFilter` wird ein Razor Pages-Filter hinzugefügt. Eine Safelist und eine Konsolenprotokollierungsinstanz werden als Konstruktorparameter übergeben.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

Wenn die *Index* Razor-Seite der Beispiel-App angefordert wird, überprüft der Filter Razor Pages die Client-IP-Adresse. Der Filter erzeugt eine Variation der folgenden Konsolenausgabe:

```
dbug: ClientIpSafelistComponents.Filters.ClientIpCheckPageFilter[0]
      Remote IpAddress: ::1
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/middleware/index>
* [Aktionsfilter](xref:mvc/controllers/filters#action-filters)
* <xref:razor-pages/filter>

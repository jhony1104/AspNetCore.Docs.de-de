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
# <a name="client-ip-safelist-for-aspnet-core"></a>Client-IP-SafeList für ASP.net Core

Von [Damien Bowder](https://twitter.com/damien_bod) und [Tom Dykstra](https://github.com/tdykstra)
 
In diesem Artikel werden drei Möglichkeiten beschrieben, wie Sie eine IP-Safelist (auch als Whitelist bezeichnet) in einer ASP.net Core-App implementieren. Verwenden Sie Folgendes:

* Middleware zum Überprüfen der Remote-IP-Adresse für jede Anforderung.
* Aktionsfilter zum Überprüfen der Remote-IP-Adresse von Anforderungen für bestimmte Controller oder Aktionsmethoden.
* Razor Pages Filter zum Überprüfen der Remote-IP-Adresse von Anforderungen für Razor pages.

In jedem Fall wird eine Zeichenfolge mit genehmigten Client-IP-Adressen in einer APP-Einstellung gespeichert. Die Middleware oder der Filter analysiert die Zeichenfolge in eine Liste und überprüft, ob die Remote-IP-Adresse in der Liste enthalten ist. Wenn dies nicht der Wert ist, wird der Statuscode HTTP 403 verboten zurückgegeben.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples/2.x/ClientIpAspNetCore) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="the-safelist"></a>Die SafeList

Die Liste wird in der Datei " *appSettings. JSON* " konfiguriert. Dabei handelt es sich um eine durch Semikolons getrennte Liste, die IPv4-und IPv6-Adressen enthalten kann.

[!code-json[](ip-safelist/samples/2.x/ClientIpAspNetCore/appsettings.json?highlight=2)]

## <a name="middleware"></a>Middleware

Die `Configure`-Methode fügt die Middleware hinzu und übergibt die SafeList-Zeichenfolge in einem Konstruktorparameter.

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_Configure&highlight=10)]

Die Middleware analysiert die Zeichenfolge in ein Array und sucht im Array nach der Remote-IP-Adresse. Wenn die Remote-IP-Adresse nicht gefunden wird, gibt die Middleware HTTP 401 unzulässig zurück. Dieser Überprüfungs Vorgang wird für HTTP GET-Anforderungen umgangen.

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a>Aktionsfilter

Wenn Sie eine SafeList nur für bestimmte Controller oder Aktionsmethoden verwenden möchten, verwenden Sie einen Aktionsfilter. Hier sehen Sie ein Beispiel: 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIpCheckFilter.cs)]

Der Aktionsfilter wird dem Dienst Container hinzugefügt.

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

Der Filter kann dann für einen Controller oder eine Aktionsmethode verwendet werden.

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_Filter&highlight=1)]

In der Beispiel-APP wird der Filter auf die `Get`-Methode angewendet. Wenn Sie also die APP testen, indem Sie eine `Get` API-Anforderung senden, überprüft das Attribut die Client-IP-Adresse. Wenn Sie den Test durch Aufrufen der API mit einer anderen HTTP-Methode durchlaufen, überprüft die Middleware die Client-IP-Adresse.

## <a name="razor-pages-filter"></a>Razor Pages Filter 

Wenn Sie eine SafeList für eine Razor Pages-App verwenden möchten, verwenden Sie einen Razor Pages Filter. Hier sehen Sie ein Beispiel: 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIpCheckPageFilter.cs)]

Dieser Filter wird aktiviert, indem er der MVC-Filter Auflistung hinzugefügt wird.

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

Wenn Sie die app ausführen und eine Razor Page anfordern, überprüft der Razor Pages Filter die Client-IP-Adresse.

## <a name="next-steps"></a>Nächste Schritte

[Erfahren Sie mehr über ASP.net Core Middleware](xref:fundamentals/middleware/index).

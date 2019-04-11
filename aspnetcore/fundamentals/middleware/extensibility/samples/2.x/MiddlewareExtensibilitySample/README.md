---
ms.openlocfilehash: 41021e30ae85dd0ae42cbe6f1606727e21bd7707
ms.sourcegitcommit: 5995f44e9e13d7e7aa8d193e2825381c42184e47
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/02/2019
ms.locfileid: "58809383"
---
# <a name="aspnet-core-middleware-extensibility-sample"></a>Beispiel: ASP.NET Core-Middleware-Erweiterbarkeit

Dieses Beispiel veranschaulicht die unter [Factorybezogene Middlewareaktivierung in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/middleware-extensibility) beschriebenen Szenarios.

Die Beispiel-App stellt Middleware dar, die wie folgt aktiviert wurde:

* Per Konvention Weitere Informationen zur konventionellen Middlewareaktivierung finden Sie im Artikel [Middleware](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/).
* Durch die Implementierung von [IMiddleware](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddleware) Die Standard-[IMiddlewareFactory-Klasse](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) aktiviert die Middleware.

Die Middlewareimplementierungen funktionieren auf identische Weise und erfassen den Wert, der von einem Abfragezeichenfolgenparameter (`key`) bereitgestellt wird. Die Middlewares verwenden einen eingefügten Datenbankkontext (einen bereichsbezogenen Dienst), um den Abfragezeichenwert in einer In-Memory Database zu erfassen.

---
title: Kompatibilitätsversion für ASP.NET Core MVC
author: rick-anderson
description: Informationen zur Vorgehensweise der Startklasse in ASP.NET Core bei der Konfiguration von Diensten und der Anforderungspipeline einer App.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/15/2019
uid: mvc/compatibility-version
ms.openlocfilehash: 7c4189db435088e0803b35add82fa0eb9372e664
ms.sourcegitcommit: d75d8eb26c2cce19876c8d5b65ac8a4b21f625ef
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/19/2019
ms.locfileid: "56410144"
---
# <a name="compatibility-version-for-aspnet-core-mvc"></a>Kompatibilitätsversion für ASP.NET Core MVC

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

Durch die Methode <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> kann eine App Änderungen im Verhalten annehmen oder ablehnen, die in ASP.NET Core MVC 2.1 und höher eingeführt werden und potentiell Fehler verursachen. Diese potentiell Fehler verursachenden Änderungen im Verhalten betreffen generell das Verhalten des MVC-Subsystems und die Art, wie **Ihr Code** von der Runtime aufgerufen wird. Wenn Sie sich für die Änderungen entscheiden, erhalten Sie das aktuelle Verhalten und das langfristige Verhalten von ASP.NET Core.

Der folgende Code legt den Kompatibilitätsmodus auf ASP.NET Core 2.2 fest:

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup.cs?name=snippet1)]

Es wird empfohlen, Ihre App mit der aktuellen Version zu testen (`CompatibilityVersion.Version_2_2`). Wir erwarten, dass bei den meisten Apps mit der aktuellen Version keine Fehler verursachenden Verhaltensänderungen auftreten werden.

Apps, die `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)` aufrufen, sind vor potentiell Fehler verursachenden Änderungen im Verhalten geschützt, die in ASP.NET Core 2.1 MVC und höheren 2.x-Versionen eingeführt wurden. Dieser Schutz:

* Gilt nicht für alle Änderungen in 2.1 und höher. Das Ziel sind potentiell Fehler verursachende Änderungen im Verhalten der ASP.NET Core-Runtime im MVC-Subsystem.
* Erstreckt sich nicht auf die nächste Hauptversion.

Die Standard-Kompatibilität für ASP.NET Core 2.1 und höhere 2.x-Aps, die **nicht** `SetCompatibilityVersion` aufrufen, ist 2.0-Kompatibilität. Das bedeutet, `SetCompatibilityVersion` nicht aufzurufen entspricht dem Aufrufen von `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)`.

Der folgende Code legt den Kompatibilitätsmodus auf ASP.NET Core 2.2 fest, außer für die folgenden Verhaltensweisen:

* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowCombiningAuthorizeFilters>
* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.InputFormatterExceptionPolicy>

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup2.cs?name=snippet1)]

Bei Apps, bei denen Fehler verursachende Änderungen auftreten, können Sie die geeigneten Kompatibilitätsoptionen verwenden, um Folgendes zu erreichen:

* Sie können das aktuelle Release verwenden und spezifische Fehler verursachende Änderungen im Verhalten vermeiden.
* Sie bekommen die Gelegenheit, Ihre App zu aktualisieren, damit Sie mit den neuesten Änderungen funktioniert.

In den Quellkommentaren für die Klasse [MvcOptions](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Mvc/Mvc.Core/src/MvcOptions.cs) finden Sie eine gute Erklärung, was sich geändert hat und welche Änderungen eine Verbesserung für die meisten Benutzer darstellen.

Zu einem späteren Zeitpunkt wird es eine [ASP.NET Core 3.0-Version](https://github.com/aspnet/Home/wiki/Roadmap) geben. Altes Verhalten, das von Kompatibilitätsoptionen unterstützt wird, wird in der 3.0-Version entfernt. Beinahe alle Benutzer werden von diesen positiven Änderungen profitieren. Dadurch, dass die Änderungen bereits jetzt eingeführt werden, können die meisten Apps auch jetzt davon profitieren, und die anderen bekommen Zeit, ihre Apps zu aktualisieren.

---
title: Dependency Injection in Controller in ASP.NET Core
author: ardalis
description: Erfahren Sie, wie ASP.NET Core MVC-Controller Abhängigkeiten mit Dependency Injection in ASP.NET Core explizit über Konstruktoren anfordern.
ms.author: riande
ms.date: 02/24/2019
uid: mvc/controllers/dependency-injection
ms.openlocfilehash: 6b08c321f4cae1f4efd8ea40300eaf4dfc2f63a1
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64890935"
---
# <a name="dependency-injection-into-controllers-in-aspnet-core"></a>Dependency Injection in Controller in ASP.NET Core

<a name="dependency-injection-controllers"></a>

Von [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT) und [Steve Smith](https://github.com/ardalis)

ASP.NET Core MVC-Controller fordern Abhängigkeiten explizit über Konstruktoren an. ASP.NET Core verfügt über integrierte Unterstützung für [Dependency Injection ( DI)](xref:fundamentals/dependency-injection). DI erleichtert das Testen und die Wartung von Apps.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="constructor-injection"></a>Constructor Injection

Dienste werden als Konstruktorparameter hinzugefügt, und die Runtime löst den Dienst aus dem Dienstcontainer auf. Dienste werden meist mithilfe von Schnittstellen definiert. Stellen Sich sich z.B. eine App vor, für die die aktuelle Uhrzeit erforderlich ist. Die folgende Schnittstelle macht den `IDateTime`-Dienst verfügbar:

[!code-csharp[](dependency-injection/sample/ControllerDI/Interfaces/IDateTime.cs?name=snippet)]

Im folgenden Code wird die `IDateTime`-Schnittstelle implementiert:

[!code-csharp[](dependency-injection/sample/ControllerDI/Services/SystemDateTime.cs?name=snippet)]

Fügen Sie anschließend dem Dienstcontainer den Dienst hinzu:

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup1.cs?name=snippet&highlight=3)]

Weitere Informationen zu <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> finden Sie unter [Dependency Injection in ASP.NET Core](xref:fundamentals/dependency-injection#service-lifetimes).

Im folgenden Code sehen Sie eine Begrüßung des Benutzers, die sich an der jeweiligen Tageszeit orientiert:

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet)]

Führen Sie die App aus, und eine Nachricht wird angezeigt, die von der Tageszeit abhängt.

## <a name="action-injection-with-fromservices"></a>Action Injection mit FromServices

Mit dem <xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> ist es möglich, einen Dienst direkt in eine Aktionsmethode einzufügen, ohne eine Constructor Injection verwenden zu müssen:

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet2)]

## <a name="access-settings-from-a-controller"></a>Zugreifen auf Einstellungen von einem Controller

Das Zugreifen auf App- oder Konfigurationseinstellungen von einem Controller aus ist ein häufiges Szenario. Das in <xref:fundamentals/configuration/options> beschriebene *Optionsmuster* ist der bevorzugte Ansatz, um Einstellungen zu verwalten. Fügen Sie generell <xref:Microsoft.Extensions.Configuration.IConfiguration> nicht direkt in einen Controller ein.

Erstellen Sie eine Klasse, die die Optionen darstellt. Beispiel:

[!code-csharp[](dependency-injection/sample/ControllerDI/Models/SampleWebSettings.cs?name=snippet)]

Hinzufügen der Konfigurationsklasse in die Dienstauflistung:

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup.cs?highlight=4&name=snippet1)]

Konfigurieren Sie die App, sodass sie die Einstellungen aus einer Datei mit JSON-Formatierung liest:

[!code-csharp[](dependency-injection/sample/ControllerDI/Program.cs?name=snippet&range=10-15)]

Der folgende Code fordert die `IOptions<SampleWebSettings>`-Einstellungen aus dem Dienstcontainer an, und verwendet sie in der `Index`-Methode:

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/SettingsController.cs?name=snippet)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* Informationen zum einfacheren Testen von Code mithilfe der expliziten Anforderung von Abhängigkeiten in Controllern finden Sie unter <xref:mvc/controllers/testing>.

* Informationen zum [Ersetzen von Standarddienstcontainern](xref:fundamentals/dependency-injection#default-service-container-replacement) mit einer Drittanbieterimplementierung.

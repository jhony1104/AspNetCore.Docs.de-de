---
title: Dependency Injection in Ansichten in ASP.NET Core
author: ardalis
description: Erfahren Sie mehr über die Unterstützung von Dependency Injection in Ansichten in ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: mvc/views/dependency-injection
ms.openlocfilehash: dfadafe9ebb5799b45ef68653f20c5fc1a2506b5
ms.sourcegitcommit: d75d8eb26c2cce19876c8d5b65ac8a4b21f625ef
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/19/2019
ms.locfileid: "56410559"
---
# <a name="dependency-injection-into-views-in-aspnet-core"></a>Dependency Injection in Ansichten in ASP.NET Core

Von [Steve Smith](https://ardalis.com/)

ASP.NET Core unterstützt [Dependency Injection](xref:fundamentals/dependency-injection) in Ansichten. Dies kann besonders für ansichtsspezifische Dienste nützlich sein – z.B. für die Lokalisierung oder für Daten, die nur zum Auffüllen von Ansichtselementen erforderlich sind. Sie sollten versuchen, das Prinzip [Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) zwischen Controllern und Ansichten beizubehalten. Die meisten Daten, die in Ihren Ansichten angezeigt werden, sollten vom Controller übergeben worden sein.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/dependency-injection/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="a-simple-example"></a>Ein einfaches Beispiel

Sie können mithilfe der `@inject`-Anweisung einen Dienst in eine Ansicht einfügen. `@inject` fügt Ihrer Ansicht eine Eigenschaft hinzu und füllt diese Eigenschaft mittels Dependency Injection auf.

Die Syntax für `@inject`: `@inject <type> <name>`

Im Folgenden finden Sie ein Beispiel für die Verwendung von `@inject`:

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/ToDo/Index.cshtml?highlight=4,5,15,16,17)]

In dieser Ansicht wird neben einer Zusammenfassung von allgemeinen Statistiken eine Liste von `ToDoItem`-Instanzen angezeigt. Die Zusammenfassung wird über den eingefügten `StatisticsService`-Dienst aufgefüllt. Dieser Dienst ist für Dependency Injection in `ConfigureServices` in *Startup.cs* registriert:

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Startup.cs?highlight=6,7&range=15-22)]

Die `StatisticsService` führt Berechnungen für die `ToDoItem`-Instanzen durch, auf die er über ein Repository zugreift:

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/StatisticsService.cs?highlight=15,20,25)]

Das Beispielrepository verwendet eine speicherinterne Auflistung. Die obenstehend dargestellte Implementierung (die auf allen Daten im Arbeitsspeicher ausgeführt wird) wird nicht für große Datasets empfohlen, auf die über eine Remoteverbindung zugegriffen wird.

Das Beispiel stellt Daten aus dem Modell, die an die Ansicht gebunden sind, und den in die Ansicht eingefügten Dienst dar:

![Die „Zu erledigen“-Ansicht, die die Gesamtelemente, fertiggestellte Elemente, durchschnittliche Priorität und eine Liste mit Aufgaben enthält, deren Prioritätsstufen und boolesche Werte auf die Fertigstellung hindeuten.](dependency-injection/_static/screenshot.png)

## <a name="populating-lookup-data"></a>Auffüllen von Nachschlagedaten

Die Ansichtsinjektion kann nützlich sein, wenn Sie Optionen in Benutzeroberflächenelemente wie Dropdownlisten einfügen möchten. Verwenden Sie ggf. ein Benutzerprofilformular, das Optionen zum Festlegen des Geschlechts, des Staats und anderer Vorlieben beinhaltet. Wenn Sie ein solches Formular über einen Standard-MVC-Ansatz rendern, muss der Controller Datenzugriffsdienste für all diese Optionen anfordern, und dann ein Modell oder `ViewBag` mit den Optionen auffüllen, die gebunden werden sollen.

Über einen alternativen Ansatz werden Dienste direkt in die Ansicht eingefügt, um die Optionen abzurufen. Dadurch ist weniger Code für den Controller erforderlich, denn die Konstruktionsebene dieses Ansichtselements wird in die Ansicht verschoben. Die Controlleraktion, die das Formular zur Profilbearbeitung anzeigen soll, muss das Formular nur an die Profilinstanz übergeben:

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Controllers/ProfileController.cs?highlight=9,19)]

Das HTML-Formular, das verwendet wird, um diese Vorlieben zu aktualisieren, umfasst Dropdownlisten für drei Eigenschaften:

![Die „Profil aktualisieren“-Ansicht mit einem Formular, in das der Name, das Geschlecht, der Staat und die Lieblingsfarbe eingetragen werden kann.](dependency-injection/_static/updateprofile.png)

Die Listen werden über einen Dienst aufgefüllt, der in die Ansicht eingefügt wurde:

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Profile/Index.cshtml?highlight=4,16,17,21,22,26,27)]

`ProfileOptionsService` ist ein Dienst auf Benutzeroberflächenebene, der nur die für das Formular benötigten Daten einfügt:

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/ProfileOptionsService.cs?highlight=7,13,24)]

> [!IMPORTANT]
> Denken Sie daran, Typen zu registrieren, die Sie über Dependency Injection in `Startup.ConfigureServices` anfordern. Ein nicht registrierter Typ löst eine Ausnahme zur Laufzeit aus, weil der Dienstanbieter intern über [GetRequiredService](/dotnet/api/microsoft.extensions.dependencyinjection.serviceproviderserviceextensions.getrequiredservice) abgefragt wird.

## <a name="overriding-services"></a>Überschreiben von Diensten

Sie können über diese Technik nicht nur neue Dienste einfügen, sondern auch zuvor auf einer Seite eingefügte Dienste überschreiben. In der nachfolgenden Abbildung werden alle Felder angezeigt, die auf der Seite verfügbar sind, die im ersten Beispiel verwendet wird:

![Kontextmenü von IntelliSense, in dem das @-Symbol eingegeben wurde und „HTML“, „Komponente“, „StatsService“ und „URL-Felder“ angezeigt werden](dependency-injection/_static/razor-fields.png)

Die Standardfelder umfassen also `Html`, `Component` und `Url` sowie den `StatsService`-Dienst, den Sie eingefügt haben. Wenn Sie beispielsweise die Standard-HTML-Hilfsprogramme durch ihre eigenen ersetzen möchten, können Sie dafür ganz einfach `@inject` verwenden:

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Helper/Index.cshtml?highlight=3,11)]

Wenn Sie vorhandene Dienste erweitern möchten, können Sie diese Technik verwenden und aus der vorhanden Implementierung erben bzw. diese mit Ihrer eigenen umschließen.

## <a name="see-also"></a>Siehe auch

* Blog von Simon Timms: [Getting Lookup Data Into Your View (Übertragen von Nachschlagedaten in Ihre Ansicht)](http://blog.simontimms.com/2015/06/09/getting-lookup-data-into-you-view/)

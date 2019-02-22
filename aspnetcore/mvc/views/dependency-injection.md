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
# <a name="dependency-injection-into-views-in-aspnet-core"></a><span data-ttu-id="5f064-103">Dependency Injection in Ansichten in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5f064-103">Dependency injection into views in ASP.NET Core</span></span>

<span data-ttu-id="5f064-104">Von [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="5f064-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="5f064-105">ASP.NET Core unterstützt [Dependency Injection](xref:fundamentals/dependency-injection) in Ansichten.</span><span class="sxs-lookup"><span data-stu-id="5f064-105">ASP.NET Core supports [dependency injection](xref:fundamentals/dependency-injection) into views.</span></span> <span data-ttu-id="5f064-106">Dies kann besonders für ansichtsspezifische Dienste nützlich sein – z.B. für die Lokalisierung oder für Daten, die nur zum Auffüllen von Ansichtselementen erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="5f064-106">This can be useful for view-specific services, such as localization or data required only for populating view elements.</span></span> <span data-ttu-id="5f064-107">Sie sollten versuchen, das Prinzip [Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) zwischen Controllern und Ansichten beizubehalten.</span><span class="sxs-lookup"><span data-stu-id="5f064-107">You should try to maintain [separation of concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) between your controllers and views.</span></span> <span data-ttu-id="5f064-108">Die meisten Daten, die in Ihren Ansichten angezeigt werden, sollten vom Controller übergeben worden sein.</span><span class="sxs-lookup"><span data-stu-id="5f064-108">Most of the data your views display should be passed in from the controller.</span></span>

<span data-ttu-id="5f064-109">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/dependency-injection/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5f064-109">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="a-simple-example"></a><span data-ttu-id="5f064-110">Ein einfaches Beispiel</span><span class="sxs-lookup"><span data-stu-id="5f064-110">A Simple Example</span></span>

<span data-ttu-id="5f064-111">Sie können mithilfe der `@inject`-Anweisung einen Dienst in eine Ansicht einfügen.</span><span class="sxs-lookup"><span data-stu-id="5f064-111">You can inject a service into a view using the `@inject` directive.</span></span> <span data-ttu-id="5f064-112">`@inject` fügt Ihrer Ansicht eine Eigenschaft hinzu und füllt diese Eigenschaft mittels Dependency Injection auf.</span><span class="sxs-lookup"><span data-stu-id="5f064-112">You can think of `@inject` as adding a property to your view, and populating the property using DI.</span></span>

<span data-ttu-id="5f064-113">Die Syntax für `@inject`: `@inject <type> <name>`</span><span class="sxs-lookup"><span data-stu-id="5f064-113">The syntax for `@inject`: `@inject <type> <name>`</span></span>

<span data-ttu-id="5f064-114">Im Folgenden finden Sie ein Beispiel für die Verwendung von `@inject`:</span><span class="sxs-lookup"><span data-stu-id="5f064-114">An example of `@inject` in action:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/ToDo/Index.cshtml?highlight=4,5,15,16,17)]

<span data-ttu-id="5f064-115">In dieser Ansicht wird neben einer Zusammenfassung von allgemeinen Statistiken eine Liste von `ToDoItem`-Instanzen angezeigt.</span><span class="sxs-lookup"><span data-stu-id="5f064-115">This view displays a list of `ToDoItem` instances, along with a summary showing overall statistics.</span></span> <span data-ttu-id="5f064-116">Die Zusammenfassung wird über den eingefügten `StatisticsService`-Dienst aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="5f064-116">The summary is populated from the injected `StatisticsService`.</span></span> <span data-ttu-id="5f064-117">Dieser Dienst ist für Dependency Injection in `ConfigureServices` in *Startup.cs* registriert:</span><span class="sxs-lookup"><span data-stu-id="5f064-117">This service is registered for dependency injection in `ConfigureServices` in *Startup.cs*:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Startup.cs?highlight=6,7&range=15-22)]

<span data-ttu-id="5f064-118">Die `StatisticsService` führt Berechnungen für die `ToDoItem`-Instanzen durch, auf die er über ein Repository zugreift:</span><span class="sxs-lookup"><span data-stu-id="5f064-118">The `StatisticsService` performs some calculations on the set of `ToDoItem` instances, which it accesses via a repository:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/StatisticsService.cs?highlight=15,20,25)]

<span data-ttu-id="5f064-119">Das Beispielrepository verwendet eine speicherinterne Auflistung.</span><span class="sxs-lookup"><span data-stu-id="5f064-119">The sample repository uses an in-memory collection.</span></span> <span data-ttu-id="5f064-120">Die obenstehend dargestellte Implementierung (die auf allen Daten im Arbeitsspeicher ausgeführt wird) wird nicht für große Datasets empfohlen, auf die über eine Remoteverbindung zugegriffen wird.</span><span class="sxs-lookup"><span data-stu-id="5f064-120">The implementation shown above (which operates on all of the data in memory) isn't recommended for large, remotely accessed data sets.</span></span>

<span data-ttu-id="5f064-121">Das Beispiel stellt Daten aus dem Modell, die an die Ansicht gebunden sind, und den in die Ansicht eingefügten Dienst dar:</span><span class="sxs-lookup"><span data-stu-id="5f064-121">The sample displays data from the model bound to the view and the service injected into the view:</span></span>

![Die „Zu erledigen“-Ansicht, die die Gesamtelemente, fertiggestellte Elemente, durchschnittliche Priorität und eine Liste mit Aufgaben enthält, deren Prioritätsstufen und boolesche Werte auf die Fertigstellung hindeuten.](dependency-injection/_static/screenshot.png)

## <a name="populating-lookup-data"></a><span data-ttu-id="5f064-123">Auffüllen von Nachschlagedaten</span><span class="sxs-lookup"><span data-stu-id="5f064-123">Populating Lookup Data</span></span>

<span data-ttu-id="5f064-124">Die Ansichtsinjektion kann nützlich sein, wenn Sie Optionen in Benutzeroberflächenelemente wie Dropdownlisten einfügen möchten.</span><span class="sxs-lookup"><span data-stu-id="5f064-124">View injection can be useful to populate options in UI elements, such as dropdown lists.</span></span> <span data-ttu-id="5f064-125">Verwenden Sie ggf. ein Benutzerprofilformular, das Optionen zum Festlegen des Geschlechts, des Staats und anderer Vorlieben beinhaltet.</span><span class="sxs-lookup"><span data-stu-id="5f064-125">Consider a user profile form that includes options for specifying gender, state, and other preferences.</span></span> <span data-ttu-id="5f064-126">Wenn Sie ein solches Formular über einen Standard-MVC-Ansatz rendern, muss der Controller Datenzugriffsdienste für all diese Optionen anfordern, und dann ein Modell oder `ViewBag` mit den Optionen auffüllen, die gebunden werden sollen.</span><span class="sxs-lookup"><span data-stu-id="5f064-126">Rendering such a form using a standard MVC approach would require the controller to request data access services for each of these sets of options, and then populate a model or `ViewBag` with each set of options to be bound.</span></span>

<span data-ttu-id="5f064-127">Über einen alternativen Ansatz werden Dienste direkt in die Ansicht eingefügt, um die Optionen abzurufen.</span><span class="sxs-lookup"><span data-stu-id="5f064-127">An alternative approach injects services directly into the view to obtain the options.</span></span> <span data-ttu-id="5f064-128">Dadurch ist weniger Code für den Controller erforderlich, denn die Konstruktionsebene dieses Ansichtselements wird in die Ansicht verschoben.</span><span class="sxs-lookup"><span data-stu-id="5f064-128">This minimizes the amount of code required by the controller, moving this view element construction logic into the view itself.</span></span> <span data-ttu-id="5f064-129">Die Controlleraktion, die das Formular zur Profilbearbeitung anzeigen soll, muss das Formular nur an die Profilinstanz übergeben:</span><span class="sxs-lookup"><span data-stu-id="5f064-129">The controller action to display a profile editing form only needs to pass the form the profile instance:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Controllers/ProfileController.cs?highlight=9,19)]

<span data-ttu-id="5f064-130">Das HTML-Formular, das verwendet wird, um diese Vorlieben zu aktualisieren, umfasst Dropdownlisten für drei Eigenschaften:</span><span class="sxs-lookup"><span data-stu-id="5f064-130">The HTML form used to update these preferences includes dropdown lists for three of the properties:</span></span>

![Die „Profil aktualisieren“-Ansicht mit einem Formular, in das der Name, das Geschlecht, der Staat und die Lieblingsfarbe eingetragen werden kann.](dependency-injection/_static/updateprofile.png)

<span data-ttu-id="5f064-132">Die Listen werden über einen Dienst aufgefüllt, der in die Ansicht eingefügt wurde:</span><span class="sxs-lookup"><span data-stu-id="5f064-132">These lists are populated by a service that has been injected into the view:</span></span>

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Profile/Index.cshtml?highlight=4,16,17,21,22,26,27)]

<span data-ttu-id="5f064-133">`ProfileOptionsService` ist ein Dienst auf Benutzeroberflächenebene, der nur die für das Formular benötigten Daten einfügt:</span><span class="sxs-lookup"><span data-stu-id="5f064-133">The `ProfileOptionsService` is a UI-level service designed to provide just the data needed for this form:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/ProfileOptionsService.cs?highlight=7,13,24)]

> [!IMPORTANT]
> <span data-ttu-id="5f064-134">Denken Sie daran, Typen zu registrieren, die Sie über Dependency Injection in `Startup.ConfigureServices` anfordern.</span><span class="sxs-lookup"><span data-stu-id="5f064-134">Don't forget to register types you request through dependency injection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5f064-135">Ein nicht registrierter Typ löst eine Ausnahme zur Laufzeit aus, weil der Dienstanbieter intern über [GetRequiredService](/dotnet/api/microsoft.extensions.dependencyinjection.serviceproviderserviceextensions.getrequiredservice) abgefragt wird.</span><span class="sxs-lookup"><span data-stu-id="5f064-135">An unregistered type throws an exception at runtime because the service provider is internally queried via [GetRequiredService](/dotnet/api/microsoft.extensions.dependencyinjection.serviceproviderserviceextensions.getrequiredservice).</span></span>

## <a name="overriding-services"></a><span data-ttu-id="5f064-136">Überschreiben von Diensten</span><span class="sxs-lookup"><span data-stu-id="5f064-136">Overriding Services</span></span>

<span data-ttu-id="5f064-137">Sie können über diese Technik nicht nur neue Dienste einfügen, sondern auch zuvor auf einer Seite eingefügte Dienste überschreiben.</span><span class="sxs-lookup"><span data-stu-id="5f064-137">In addition to injecting new services, this technique can also be used to override previously injected services on a page.</span></span> <span data-ttu-id="5f064-138">In der nachfolgenden Abbildung werden alle Felder angezeigt, die auf der Seite verfügbar sind, die im ersten Beispiel verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="5f064-138">The figure below shows all of the fields available on the page used in the first example:</span></span>

![Kontextmenü von IntelliSense, in dem das @-Symbol eingegeben wurde und „HTML“, „Komponente“, „StatsService“ und „URL-Felder“ angezeigt werden](dependency-injection/_static/razor-fields.png)

<span data-ttu-id="5f064-140">Die Standardfelder umfassen also `Html`, `Component` und `Url` sowie den `StatsService`-Dienst, den Sie eingefügt haben.</span><span class="sxs-lookup"><span data-stu-id="5f064-140">As you can see, the default fields include `Html`, `Component`, and `Url` (as well as the `StatsService` that we injected).</span></span> <span data-ttu-id="5f064-141">Wenn Sie beispielsweise die Standard-HTML-Hilfsprogramme durch ihre eigenen ersetzen möchten, können Sie dafür ganz einfach `@inject` verwenden:</span><span class="sxs-lookup"><span data-stu-id="5f064-141">If for instance you wanted to replace the default HTML Helpers with your own, you could easily do so using `@inject`:</span></span>

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Helper/Index.cshtml?highlight=3,11)]

<span data-ttu-id="5f064-142">Wenn Sie vorhandene Dienste erweitern möchten, können Sie diese Technik verwenden und aus der vorhanden Implementierung erben bzw. diese mit Ihrer eigenen umschließen.</span><span class="sxs-lookup"><span data-stu-id="5f064-142">If you want to extend existing services, you can simply use this technique while inheriting from or wrapping the existing implementation with your own.</span></span>

## <a name="see-also"></a><span data-ttu-id="5f064-143">Siehe auch</span><span class="sxs-lookup"><span data-stu-id="5f064-143">See Also</span></span>

* <span data-ttu-id="5f064-144">Blog von Simon Timms: [Getting Lookup Data Into Your View (Übertragen von Nachschlagedaten in Ihre Ansicht)](http://blog.simontimms.com/2015/06/09/getting-lookup-data-into-you-view/)</span><span class="sxs-lookup"><span data-stu-id="5f064-144">Simon Timms Blog: [Getting Lookup Data Into Your View](http://blog.simontimms.com/2015/06/09/getting-lookup-data-into-you-view/)</span></span>

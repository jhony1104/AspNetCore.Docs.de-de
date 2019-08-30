---
title: 'Tutorial: Erste Schritte mit EF Core in einer ASP.NET Core MVC-Web-App'
description: Dies ist die erste Tutorial in der Reihe, in dem die Erstellung der Beispielanwendung der Contoso University von Grund auf erläutert wird.
author: tdykstra
ms.author: riande
ms.custom: mvc
ms.date: 02/06/2019
ms.topic: tutorial
uid: data/ef-mvc/intro
ms.openlocfilehash: 3450ac5b46e2a03b5d58c8760b78a52065343992
ms.sourcegitcommit: 6189b0ced9c115248c6ede02efcd0b29d31f2115
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2019
ms.locfileid: "69985370"
---
# <a name="tutorial-get-started-with-ef-core-in-an-aspnet-mvc-web-app"></a><span data-ttu-id="b5f68-103">Tutorial: Erste Schritte mit EF Core in einer ASP.NET Core MVC-Web-App</span><span class="sxs-lookup"><span data-stu-id="b5f68-103">Tutorial: Get started with EF Core in an ASP.NET MVC web app</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b5f68-104">Dieses Tutorial wurde **nicht** auf ASP.NET Core 3.0 aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="b5f68-104">This tutorial has **not** been updated to ASP.NET Core 3.0.</span></span> <span data-ttu-id="b5f68-105">Die [Razor Pages-Version](xref:data/ef-rp/intro) wurde aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="b5f68-105">The [Razor Pages version](xref:data/ef-rp/intro) has been updated.</span></span> <span data-ttu-id="b5f68-106">Informationen zum Zeitpunkt des Updates finden Sie in [diesem GitHub-Issue](https://github.com/aspnet/AspNetCore.Docs/issues/13920).</span><span class="sxs-lookup"><span data-stu-id="b5f68-106">For information on when this might be updated, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/13920).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

<span data-ttu-id="b5f68-107">Die Beispielwebanwendung der Contoso University veranschaulicht, wie mit Entity Framework Core 2.2 (EF Core 2.2) und Visual Studio 2017 oder 2019 ASP.NET Core 2.2 MVC-Webanwendungen erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="b5f68-107">The Contoso University sample web application demonstrates how to create ASP.NET Core 2.2 MVC web applications using Entity Framework (EF) Core 2.2 and Visual Studio 2017 or 2019.</span></span>

<span data-ttu-id="b5f68-108">Bei der Beispiel-App handelt es sich um eine Website für die fiktive Contoso University.</span><span class="sxs-lookup"><span data-stu-id="b5f68-108">The sample application is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="b5f68-109">Sie enthält Funktionen wie die Zulassung von Studenten, die Erstellung von Kursen und Aufgaben von Dozenten.</span><span class="sxs-lookup"><span data-stu-id="b5f68-109">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="b5f68-110">Dies ist die erste Tutorial in der Reihe, in dem die Erstellung der Beispielanwendung der Contoso University von Grund auf erläutert wird.</span><span class="sxs-lookup"><span data-stu-id="b5f68-110">This is the first in a series of tutorials that explain how to build the Contoso University sample application from scratch.</span></span>

<span data-ttu-id="b5f68-111">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="b5f68-111">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="b5f68-112">Erstellen einer ASP.NET Core-MVC-Web-App</span><span class="sxs-lookup"><span data-stu-id="b5f68-112">Create an ASP.NET Core MVC web app</span></span>
> * <span data-ttu-id="b5f68-113">Einrichten des Websitestils</span><span class="sxs-lookup"><span data-stu-id="b5f68-113">Set up the site style</span></span>
> * <span data-ttu-id="b5f68-114">Erfahren Sie mehr über EF Core-NuGet-Pakete</span><span class="sxs-lookup"><span data-stu-id="b5f68-114">Learn about EF Core NuGet packages</span></span>
> * <span data-ttu-id="b5f68-115">Erstellen des Datenmodells</span><span class="sxs-lookup"><span data-stu-id="b5f68-115">Create the data model</span></span>
> * <span data-ttu-id="b5f68-116">Erstellen des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="b5f68-116">Create the database context</span></span>
> * <span data-ttu-id="b5f68-117">Registrieren des Kontexts für die Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="b5f68-117">Register the context for dependency injection</span></span>
> * <span data-ttu-id="b5f68-118">Initialisieren der Datenbank mithilfe von Testdaten</span><span class="sxs-lookup"><span data-stu-id="b5f68-118">Initialize the database with test data</span></span>
> * <span data-ttu-id="b5f68-119">Erstellen von einem Controller und Ansichten</span><span class="sxs-lookup"><span data-stu-id="b5f68-119">Create a controller and views</span></span>
> * <span data-ttu-id="b5f68-120">Zeigen Sie die Datenbank an</span><span class="sxs-lookup"><span data-stu-id="b5f68-120">View the database</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b5f68-121">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="b5f68-121">Prerequisites</span></span>

* [<span data-ttu-id="b5f68-122">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="b5f68-122">.NET Core SDK 2.2</span></span>](https://www.microsoft.com/net/download)
* <span data-ttu-id="b5f68-123">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) mit den folgenden Workloads:</span><span class="sxs-lookup"><span data-stu-id="b5f68-123">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the following workloads:</span></span>
  * <span data-ttu-id="b5f68-124">Workload **ASP.NET und Webentwicklung**</span><span class="sxs-lookup"><span data-stu-id="b5f68-124">**ASP.NET and web development** workload</span></span>
  * <span data-ttu-id="b5f68-125">Workload **Plattformübergreifende .NET Core-Entwicklung**</span><span class="sxs-lookup"><span data-stu-id="b5f68-125">**.NET Core cross-platform development** workload</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="b5f68-126">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="b5f68-126">Troubleshooting</span></span>

<span data-ttu-id="b5f68-127">Wenn Sie auf ein Problem stoßen, das Sie nicht lösen können, sollten Sie versuchen, Ihren Code mit dem [abgeschlossenen Projekt](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final) zu vergleichen.</span><span class="sxs-lookup"><span data-stu-id="b5f68-127">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final).</span></span> <span data-ttu-id="b5f68-128">Eine Liste mit häufig auftretenden Fehlern und den jeweiligen Lösungen finden Sie im Abschnitt [zur Fehlerbehebung auf im letzten Tutorial dieser Tutorialreihe](advanced.md#common-errors).</span><span class="sxs-lookup"><span data-stu-id="b5f68-128">For a list of common errors and how to solve them, see [the Troubleshooting section of the last tutorial in the series](advanced.md#common-errors).</span></span> <span data-ttu-id="b5f68-129">Wenn Sie dort nicht die gewünschten Informationen finden, können Sie unter „StackOverflow.com“ für [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) oder [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core) eine Frage posten.</span><span class="sxs-lookup"><span data-stu-id="b5f68-129">If you don't find what you need there, you can post a question to StackOverflow.com for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

> [!TIP]
> <span data-ttu-id="b5f68-130">Diese Reihe besteht aus 10 Tutorials, die aufeinander aufbauen.</span><span class="sxs-lookup"><span data-stu-id="b5f68-130">This is a series of 10 tutorials, each of which builds on what is done in earlier tutorials.</span></span> <span data-ttu-id="b5f68-131">Sie sollten jedes Mal, wenn Sie erfolgreich ein Tutorial abgeschlossen haben, eine Kopie des Projekts erstellen.</span><span class="sxs-lookup"><span data-stu-id="b5f68-131">Consider saving a copy of the project after each successful tutorial completion.</span></span> <span data-ttu-id="b5f68-132">Wenn Sie dann auf Probleme stoßen, können Sie zurück zum vorherigen Tutorial wechseln und müssen nicht wieder ganz von vorne beginnen.</span><span class="sxs-lookup"><span data-stu-id="b5f68-132">Then if you run into problems, you can start over from the previous tutorial instead of going back to the beginning of the whole series.</span></span>

## <a name="contoso-university-web-app"></a><span data-ttu-id="b5f68-133">Web-App der Contoso University</span><span class="sxs-lookup"><span data-stu-id="b5f68-133">Contoso University web app</span></span>

<span data-ttu-id="b5f68-134">Bei der Anwendung, die Sie mithilfe dieser Tutorials erstellen, handelt es sich um eine einfache Universitätswebsite.</span><span class="sxs-lookup"><span data-stu-id="b5f68-134">The application you'll be building in these tutorials is a simple university web site.</span></span>

<span data-ttu-id="b5f68-135">Benutzer können Informationen zu den Studenten, Kursen und Dozenten abrufen.</span><span class="sxs-lookup"><span data-stu-id="b5f68-135">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="b5f68-136">Nachfolgend werden einige Anzeigen dargestellt, die erstellt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="b5f68-136">Here are a few of the screens you'll create.</span></span>

![Indexseite „Studenten“](intro/_static/students-index.png)

![Bearbeitungsseite für Studenten](intro/_static/student-edit.png)

## <a name="create-web-app"></a><span data-ttu-id="b5f68-139">Erstellen einer Web-App</span><span class="sxs-lookup"><span data-stu-id="b5f68-139">Create web app</span></span>

* <span data-ttu-id="b5f68-140">Öffnen Sie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b5f68-140">Open Visual Studio.</span></span>

* <span data-ttu-id="b5f68-141">Klicken Sie im Menü **Datei** auf **Neu > Projekt**.</span><span class="sxs-lookup"><span data-stu-id="b5f68-141">From the **File** menu, select **New > Project**.</span></span>

* <span data-ttu-id="b5f68-142">Klicken Sie im linken Bereich auf **Installiert > Visual C# > Web**.</span><span class="sxs-lookup"><span data-stu-id="b5f68-142">From the left pane, select **Installed > Visual C# > Web**.</span></span>

* <span data-ttu-id="b5f68-143">Wählen Sie die Projektvorlage **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="b5f68-143">Select the **ASP.NET Core Web Application** project template.</span></span>

* <span data-ttu-id="b5f68-144">Geben Sie **ContosoUniversity** als Name ein, und klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="b5f68-144">Enter **ContosoUniversity** as the name and click **OK**.</span></span>

  ![Dialogfeld "Neues Projekt"](intro/_static/new-project2.png)

* <span data-ttu-id="b5f68-146">Warten Sie, bis das Dialogfeld **Neue ASP.NET Core-Webanwendung** angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="b5f68-146">Wait for the **New ASP.NET Core Web Application** dialog to appear.</span></span>

* <span data-ttu-id="b5f68-147">Wählen Sie **.NET Core**, **ASP.NET Core 2.2** und die Vorlage **Webanwendung (Model-View-Controller)** aus.</span><span class="sxs-lookup"><span data-stu-id="b5f68-147">Select **.NET Core**, **ASP.NET Core 2.2** and the **Web Application (Model-View-Controller)** template.</span></span>

* <span data-ttu-id="b5f68-148">Stellen Sie sicher, dass **Authentifizierung** auf **Keine Authentifizierung** festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="b5f68-148">Make sure **Authentication** is set to **No Authentication**.</span></span>

* <span data-ttu-id="b5f68-149">Wählen Sie **OK** aus.</span><span class="sxs-lookup"><span data-stu-id="b5f68-149">Select **OK**</span></span>

  ![Dialogfeld „Neues ASP.NET Core-Projekt“](intro/_static/new-aspnet2.png)

## <a name="set-up-the-site-style"></a><span data-ttu-id="b5f68-151">Einrichten des Websitestils</span><span class="sxs-lookup"><span data-stu-id="b5f68-151">Set up the site style</span></span>

<span data-ttu-id="b5f68-152">Sie können das Websitemenü, das Layout und die Startseite über einige Änderungen einrichten.</span><span class="sxs-lookup"><span data-stu-id="b5f68-152">A few simple changes will set up the site menu, layout, and home page.</span></span>

<span data-ttu-id="b5f68-153">Öffnen Sie *Views/Shared/_Layout.cshtml*, und nehmen Sie die folgenden Änderungen vor:</span><span class="sxs-lookup"><span data-stu-id="b5f68-153">Open *Views/Shared/_Layout.cshtml* and make the following changes:</span></span>

* <span data-ttu-id="b5f68-154">Ändern Sie jedes „ContosoUniversity“ in „Contoso University“.</span><span class="sxs-lookup"><span data-stu-id="b5f68-154">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="b5f68-155">Diese Begriffskombination kommt dreimal vor.</span><span class="sxs-lookup"><span data-stu-id="b5f68-155">There are three occurrences.</span></span>

* <span data-ttu-id="b5f68-156">Fügen Sie Menüeinträge für **Informationen**, **Studenten**, **Kurse**, **Dozenten** und **Abteilungen** hinzu, und löschen Sie den Menüeintrag **Datenschutz**.</span><span class="sxs-lookup"><span data-stu-id="b5f68-156">Add menu entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Privacy** menu entry.</span></span>

<span data-ttu-id="b5f68-157">Die Änderungen werden hervorgehoben.</span><span class="sxs-lookup"><span data-stu-id="b5f68-157">The changes are highlighted.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Shared/_Layout.cshtml?highlight=6,34-48,63)]

<span data-ttu-id="b5f68-158">Ersetzen Sie in der *Views/Home/Index.cshtml*-Datei die Inhalte der Datei durch den folgenden Code. Dadurch ersetzen Sie den Text zu ASP.NET und MVC durch Text zu dieser Anwendung:</span><span class="sxs-lookup"><span data-stu-id="b5f68-158">In *Views/Home/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this application:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Home/Index.cshtml)]

<span data-ttu-id="b5f68-159">Drücken Sie STRG+F5, um das Projekt auszuführen, oder wählen Sie aus dem Menü **Debuggen > Ohne Debuggen starten** aus.</span><span class="sxs-lookup"><span data-stu-id="b5f68-159">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span> <span data-ttu-id="b5f68-160">Dann wird Ihnen die Startseite mit Registerkarten für die Seiten angezeigt, die Sie mithilfe dieses Tutorials erstellen.</span><span class="sxs-lookup"><span data-stu-id="b5f68-160">You see the home page with tabs for the pages you'll create in these tutorials.</span></span>

![Contoso University-Startseite](intro/_static/home-page.png)

## <a name="about-ef-core-nuget-packages"></a><span data-ttu-id="b5f68-162">Informationen zu EF Core-NuGet-Paketen</span><span class="sxs-lookup"><span data-stu-id="b5f68-162">About EF Core NuGet packages</span></span>

<span data-ttu-id="b5f68-163">Installieren Sie den Datenbankanbieter, der verwendet werden soll, um einem Projekt EF Core-Unterstützung hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="b5f68-163">To add EF Core support to a project, install the database provider that you want to target.</span></span> <span data-ttu-id="b5f68-164">In diesem Tutorial wird SQL Server verwendet, und das Anbieterpaket lautet [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="b5f68-164">This tutorial uses SQL Server, and the provider package is [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span> <span data-ttu-id="b5f68-165">Dieses Paket ist im [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app) enthalten, weshalb Sie nicht auf das Paket verweisen müssen.</span><span class="sxs-lookup"><span data-stu-id="b5f68-165">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to reference the package.</span></span>

<span data-ttu-id="b5f68-166">Das EF SQL Server-Paket und dessen Abhängigkeiten (`Microsoft.EntityFrameworkCore` und `Microsoft.EntityFrameworkCore.Relational`) stellen für EF Runtimeunterstützung bereit.</span><span class="sxs-lookup"><span data-stu-id="b5f68-166">The EF SQL Server package and its dependencies (`Microsoft.EntityFrameworkCore` and `Microsoft.EntityFrameworkCore.Relational`) provide runtime support for EF.</span></span> <span data-ttu-id="b5f68-167">Sie fügen später im Laufe des [Migrations](migrations.md)-Tutorials ein Paket mit Tools hinzu.</span><span class="sxs-lookup"><span data-stu-id="b5f68-167">You'll add a tooling package later, in the [Migrations](migrations.md) tutorial.</span></span>

<span data-ttu-id="b5f68-168">Informationen zu anderen Datenbankanbietern, die für Entity Framework Core verfügbar sind, finden Sie unter [Datenbankanbieter](/ef/core/providers/).</span><span class="sxs-lookup"><span data-stu-id="b5f68-168">For information about other database providers that are available for Entity Framework Core, see [Database providers](/ef/core/providers/).</span></span>

## <a name="create-the-data-model"></a><span data-ttu-id="b5f68-169">Erstellen des Datenmodells</span><span class="sxs-lookup"><span data-stu-id="b5f68-169">Create the data model</span></span>

<span data-ttu-id="b5f68-170">Als nächstes erstellen Sie Entitätsklassen für die Contoso University-Anwendung.</span><span class="sxs-lookup"><span data-stu-id="b5f68-170">Next you'll create entity classes for the Contoso University application.</span></span> <span data-ttu-id="b5f68-171">Beginnen Sie mit dem folgenden drei Entitäten.</span><span class="sxs-lookup"><span data-stu-id="b5f68-171">You'll start with the following three entities.</span></span>

![Datenmodelldiagramm zur Kursanmeldung für Studenten](intro/_static/data-model-diagram.png)

<span data-ttu-id="b5f68-173">Es besteht eine 1:n-Beziehung zwischen den Entitäten `Student` und `Enrollment`. Außerdem besteht eine 1:n-Beziehung zwischen den Entitäten `Course` und `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="b5f68-173">There's a one-to-many relationship between `Student` and `Enrollment` entities, and there's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="b5f68-174">Das bedeutet, dass ein Student für beliebig viele Kurse angemeldet sein kann und sich für jeden Kurs eine beliebige Anzahl von Studenten anmelden kann.</span><span class="sxs-lookup"><span data-stu-id="b5f68-174">In other words, a student can be enrolled in any number of courses, and a course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="b5f68-175">In den folgenden Abschnitten erstellen Sie für jede dieser Entitäten eine Klasse.</span><span class="sxs-lookup"><span data-stu-id="b5f68-175">In the following sections you'll create a class for each one of these entities.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="b5f68-176">Die Entität „Student“</span><span class="sxs-lookup"><span data-stu-id="b5f68-176">The Student entity</span></span>

![Entitätsdiagramm „Student“](intro/_static/student-entity.png)

<span data-ttu-id="b5f68-178">Erstellen Sie im Ordner *Models* (Modelle) die Klassendatei *Student.cs*, und ersetzen Sie den Vorlagencode durch folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="b5f68-178">In the *Models* folder, create a class file named *Student.cs* and replace the template code with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="b5f68-179">Die `ID`-Eigenschaft fungiert als Primärschlüsselspalte der Datenbanktabelle, die dieser Klasse entspricht.</span><span class="sxs-lookup"><span data-stu-id="b5f68-179">The `ID` property will become the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="b5f68-180">Standardmäßig interpretiert Entity Framework Core eine Eigenschaft mit dem Namen `ID` oder `classnameID` als Primärschlüssel.</span><span class="sxs-lookup"><span data-stu-id="b5f68-180">By default, the Entity Framework interprets a property that's named `ID` or `classnameID` as the primary key.</span></span>

<span data-ttu-id="b5f68-181">Die `Enrollments`-Eigenschaft ist eine [Navigationseigenschaft](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="b5f68-181">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="b5f68-182">Navigationseigenschaften enthalten andere Entitäten, die dieser Entität zugehörig sind.</span><span class="sxs-lookup"><span data-stu-id="b5f68-182">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="b5f68-183">In diesem Fall enthält die `Enrollments`-Eigenschaft einer `Student entity` all diese `Enrollment`-Entitäten, die mit der `Student`-Entität in Zusammenhang stehen.</span><span class="sxs-lookup"><span data-stu-id="b5f68-183">In this case, the `Enrollments` property of a `Student entity` will hold all of the `Enrollment` entities that are related to that `Student` entity.</span></span> <span data-ttu-id="b5f68-184">Das heißt: Wenn eine vorhandene „Student“-Zeile in der Datenbank über zwei zugehörige „Enrollment“-Zeilen (Zeilen, in denen der Primärschlüsselwert dieses Studenten in der StudentID-Fremdschlüsselspalte enthalten ist), enthält die `Enrollments`-Navigationseigenschaft dieser `Student`-Entität diese beiden `Enrollment`-Entitäten.</span><span class="sxs-lookup"><span data-stu-id="b5f68-184">In other words, if a given Student row in the database has two related Enrollment rows (rows that contain that student's primary key value in their StudentID foreign key column), that `Student` entity's `Enrollments` navigation property will contain those two `Enrollment` entities.</span></span>

<span data-ttu-id="b5f68-185">Wenn eine Navigationseigenschaft mehrere Entitäten enthalten kann (wie bei m:n- oder 1:n-Beziehungen), muss dessen Typ aus einer Liste bestehen, in der Einträge hinzugefügt, gelöscht und aktualisiert werden können – z.B.: `ICollection<T>`.</span><span class="sxs-lookup"><span data-stu-id="b5f68-185">If a navigation property can hold multiple entities (as in many-to-many or one-to-many relationships), its type must be a list in which entries can be added, deleted, and updated, such as `ICollection<T>`.</span></span> <span data-ttu-id="b5f68-186">Sie können die `ICollection<T>`-Instanz oder einen Typ wie `List<T>` oder `HashSet<T>` angeben.</span><span class="sxs-lookup"><span data-stu-id="b5f68-186">You can specify `ICollection<T>` or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="b5f68-187">Wenn Sie `ICollection<T>` angeben, erstellt EF standardmäßig eine `HashSet<T>`-Auflistung.</span><span class="sxs-lookup"><span data-stu-id="b5f68-187">If you specify `ICollection<T>`, EF creates a `HashSet<T>` collection by default.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="b5f68-188">Die Entität „Enrollment“</span><span class="sxs-lookup"><span data-stu-id="b5f68-188">The Enrollment entity</span></span>

![Entitätsdiagramm „Enrollment“](intro/_static/enrollment-entity.png)

<span data-ttu-id="b5f68-190">Erstellen Sie im Ordner *Models* (Modelle) die Datei *Enrollment.cs*, und ersetzen Sie den vorhandenen Code durch folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="b5f68-190">In the *Models* folder, create *Enrollment.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="b5f68-191">Die `EnrollmentID`-Eigenschaft wird als Primärschlüssel verwendet. Diese Entität verwendet das `classnameID`-Muster anstelle der `ID` alleine, wie in der `Student`-Entität dargestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="b5f68-191">The `EnrollmentID` property will be the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself as you saw in the `Student` entity.</span></span> <span data-ttu-id="b5f68-192">Normalerweise würden Sie nur ein Muster auswählen und dieses für das gesamte Datenmodell verwenden.</span><span class="sxs-lookup"><span data-stu-id="b5f68-192">Ordinarily you would choose one pattern and use it throughout your data model.</span></span> <span data-ttu-id="b5f68-193">Diese Variation soll verdeutlichen, dass Sie ein beliebiges Muster erstellen können.</span><span class="sxs-lookup"><span data-stu-id="b5f68-193">Here, the variation illustrates that you can use either pattern.</span></span> <span data-ttu-id="b5f68-194">In einem der [nächsten Tutorials](inheritance.md) wird erläutert, wie Sie eine ID ohne Klassennamen verwenden, um die Vererbung einfacher in das Datenmodell zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="b5f68-194">In a [later tutorial](inheritance.md), you'll see how using ID without classname makes it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="b5f68-195">Die `Grade`-Eigenschaft ist `enum`.</span><span class="sxs-lookup"><span data-stu-id="b5f68-195">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="b5f68-196">Das Fragezeichen nach der `Grade`-Typdeklaration gibt an, dass die `Grade`-Eigenschaft NULL-Werte zulässt.</span><span class="sxs-lookup"><span data-stu-id="b5f68-196">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="b5f68-197">Eine Grade-Eigenschaft mit dem Wert NULL unterscheidet sich von einer Grade-Eigenschaft mit dem Wert 0 (null). Der Wert NULL bedeutet, dass keine Grade-Eigenschaft bekannt ist oder noch keine zugewiesen wurde.</span><span class="sxs-lookup"><span data-stu-id="b5f68-197">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="b5f68-198">Bei der `StudentID`-Eigenschaft handelt es sich um einen Fremdschlüssel, und `Student` ist die entsprechende Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="b5f68-198">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="b5f68-199">Eine `Enrollment`-Entität wird einer `Student`-Entität zugeordnet, damit die Eigenschaft nur eine `Student`-Entität enthalten kann. Dies steht im Gegensatz zu der bereits erläuterten `Student.Enrollments`-Navigationseigenschaft, die mehrere `Enrollment`-Entitäten enthalten kann.</span><span class="sxs-lookup"><span data-stu-id="b5f68-199">An `Enrollment` entity is associated with one `Student` entity, so the property can only hold a single `Student` entity (unlike the `Student.Enrollments` navigation property you saw earlier, which can hold multiple `Enrollment` entities).</span></span>

<span data-ttu-id="b5f68-200">Bei der `CourseID`-Eigenschaft handelt es sich um einen Fremdschlüssel, und die zugehörige Navigationseigenschaft lautet `Course`.</span><span class="sxs-lookup"><span data-stu-id="b5f68-200">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="b5f68-201">Die `Enrollment`-Entität wird einer `Course`-Entität zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="b5f68-201">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="b5f68-202">Entity Framework interpretiert Eigenschaften als Fremdschlüsseleigenschaften, wenn Sie den Namen `<navigation property name><primary key property name>` haben – z.B. `StudentID` für die `Student`-Navigationseigenschaft, da der Primärschlüssel der `Student`-Entität `ID` lautet.</span><span class="sxs-lookup"><span data-stu-id="b5f68-202">Entity Framework interprets a property as a foreign key property if it's named `<navigation property name><primary key property name>` (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="b5f68-203">Fremdschlüsseleigenschaften können auch einfach den Namen `<primary key property name>` haben – z.B. `CourseID`, da der Primärschlüssel der `Course`-Entität `CourseID` lautet.</span><span class="sxs-lookup"><span data-stu-id="b5f68-203">Foreign key properties can also be named simply `<primary key property name>` (for example, `CourseID` since the `Course` entity's primary key is `CourseID`).</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="b5f68-204">Die Entität „Course“</span><span class="sxs-lookup"><span data-stu-id="b5f68-204">The Course entity</span></span>

![Entitätsdiagramm „Course“](intro/_static/course-entity.png)

<span data-ttu-id="b5f68-206">Erstellen Sie im Ordner *Models* (Modelle) die Datei *Course.cs*, und ersetzen Sie den vorhandenen Code durch folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="b5f68-206">In the *Models* folder, create *Course.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="b5f68-207">Die `Enrollments`-Eigenschaft ist eine Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="b5f68-207">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="b5f68-208">`Course`-Entitäten können sich auf jede beliebige Anzahl von `Enrollment`-Entitäten beziehen.</span><span class="sxs-lookup"><span data-stu-id="b5f68-208">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="b5f68-209">Weitere Informationen zum `DatabaseGenerated`-Attribut erhalten Sie in einem [späteren Tutorial](complex-data-model.md) dieser Reihe.</span><span class="sxs-lookup"><span data-stu-id="b5f68-209">We'll say more about the `DatabaseGenerated` attribute in a [later tutorial](complex-data-model.md) in this series.</span></span> <span data-ttu-id="b5f68-210">Im Grunde können Sie über dieses Attribut den Primärschlüssel für den Kurs angeben, anstatt ihn von der Datenbank generieren zu lassen.</span><span class="sxs-lookup"><span data-stu-id="b5f68-210">Basically, this attribute lets you enter the primary key for the course rather than having the database generate it.</span></span>

## <a name="create-the-database-context"></a><span data-ttu-id="b5f68-211">Erstellen des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="b5f68-211">Create the database context</span></span>

<span data-ttu-id="b5f68-212">Die Datenbankkontextklasse ist die Hauptklasse, die die Entity Framework-Funktionen für ein angegebenes Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="b5f68-212">The main class that coordinates Entity Framework functionality for a given data model is the database context class.</span></span> <span data-ttu-id="b5f68-213">Sie können diese Klasse durch Ableiten von der `Microsoft.EntityFrameworkCore.DbContext`-Klasse erstellen.</span><span class="sxs-lookup"><span data-stu-id="b5f68-213">You create this class by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span> <span data-ttu-id="b5f68-214">Sie geben in Ihrem Code an, welche Entitäten im Datenmodell enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="b5f68-214">In your code you specify which entities are included in the data model.</span></span> <span data-ttu-id="b5f68-215">Außerdem können Sie bestimmte Entity Framework-Verhalten anpassen.</span><span class="sxs-lookup"><span data-stu-id="b5f68-215">You can also customize certain Entity Framework behavior.</span></span> <span data-ttu-id="b5f68-216">In diesem Projekt heißt die Klasse `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="b5f68-216">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="b5f68-217">Erstellen Sie im Projektordner einen Ordner mit dem Namen *Data* (Daten).</span><span class="sxs-lookup"><span data-stu-id="b5f68-217">In the project folder, create a folder named *Data*.</span></span>

<span data-ttu-id="b5f68-218">Erstellen Sie im Ordner *Data* (Daten) die Klassendatei *SchoolContext.cs*, und ersetzen Sie den Vorlagencode durch folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="b5f68-218">In the *Data* folder create a new class file named *SchoolContext.cs*, and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

<span data-ttu-id="b5f68-219">Dieser Code erstellt eine `DbSet`-Eigenschaft für jede Entitätenmenge.</span><span class="sxs-lookup"><span data-stu-id="b5f68-219">This code creates a `DbSet` property for each entity set.</span></span> <span data-ttu-id="b5f68-220">In der Terminologie von Entity Framework entspricht eine Entitätenmenge in der Regel einer Datenbanktabelle, und eine Entität entspricht einer Zeile in einer Tabelle.</span><span class="sxs-lookup"><span data-stu-id="b5f68-220">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<span data-ttu-id="b5f68-221">Auch wenn Sie die Anweisungen `DbSet<Enrollment>` und `DbSet<Course>` auslassen, ändert dies nichts an der Funktionsweise.</span><span class="sxs-lookup"><span data-stu-id="b5f68-221">You could've omitted the `DbSet<Enrollment>` and `DbSet<Course>` statements and it would work the same.</span></span> <span data-ttu-id="b5f68-222">Diese sind implizit in Entity Framework enthalten, da die `Student`-Entität auf die `Enrollment`-Entität und die `Enrollment`-Entität auf die `Course`-Entität verweist.</span><span class="sxs-lookup"><span data-stu-id="b5f68-222">The Entity Framework would include them implicitly because the `Student` entity references the `Enrollment` entity and the `Enrollment` entity references the `Course` entity.</span></span>

<span data-ttu-id="b5f68-223">Wenn die Datenbank erstellt wird, erstellt EF Core Tabellen mit Namen, die den `DbSet`-Eigenschaftennamen entsprechen.</span><span class="sxs-lookup"><span data-stu-id="b5f68-223">When the database is created, EF creates tables that have names the same as the `DbSet` property names.</span></span> <span data-ttu-id="b5f68-224">Eigenschaftennamen für Auflistungen stehen in der Regel im Plural (Students anstelle von Student). Allerdings sind sich Entwickler uneinig darüber, ob auch Tabellennamen im Plural stehen sollten.</span><span class="sxs-lookup"><span data-stu-id="b5f68-224">Property names for collections are typically plural (Students rather than Student), but developers disagree about whether table names should be pluralized or not.</span></span> <span data-ttu-id="b5f68-225">In diesen Tutorials wird das Standardverhalten außer Kraft gesetzt, indem im DbContext Tabellennamen im Singular angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="b5f68-225">For these tutorials you'll override the default behavior by specifying singular table names in the DbContext.</span></span> <span data-ttu-id="b5f68-226">Fügen Sie dafür den hervorgehobenen Code nach der DbSet-Eigenschaft ein.</span><span class="sxs-lookup"><span data-stu-id="b5f68-226">To do that, add the following highlighted code after the last DbSet property.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-schoolcontext"></a><span data-ttu-id="b5f68-227">Registrieren Sie den SchoolContext</span><span class="sxs-lookup"><span data-stu-id="b5f68-227">Register the SchoolContext</span></span>

<span data-ttu-id="b5f68-228">ASP.NET Core implementiert standardmäßig [Dependency Injection](../../fundamentals/dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="b5f68-228">ASP.NET Core implements [dependency injection](../../fundamentals/dependency-injection.md) by default.</span></span> <span data-ttu-id="b5f68-229">Dienste wie der EF-Datenbankkontext werden per Dependency Injection beim Anwendungsstart registriert.</span><span class="sxs-lookup"><span data-stu-id="b5f68-229">Services (such as the EF database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="b5f68-230">Komponenten, die diese Dienste erfordern (z.B. Ihre MVC-Controller), werden über Konstruktorparameter bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="b5f68-230">Components that require these services (such as MVC controllers) are provided these services via constructor parameters.</span></span> <span data-ttu-id="b5f68-231">Nachfolgend in diesem Tutorial wird der Konstruktorcode des Controllers angezeigt, der eine Kontextinstanz abruft.</span><span class="sxs-lookup"><span data-stu-id="b5f68-231">You'll see the controller constructor code that gets a context instance later in this tutorial.</span></span>

<span data-ttu-id="b5f68-232">Öffnen Sie die *Startup.cs*-Datei, und fügen Sie der `ConfigureServices`-Methode die hervorgehobenen Zeilen hinzu, um `SchoolContext` als Dienst zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="b5f68-232">To register `SchoolContext` as a service, open *Startup.cs*, and add the highlighted lines to the `ConfigureServices` method.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=9-10)]

<span data-ttu-id="b5f68-233">Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem `DbContextOptionsBuilder`-Objekt aufrufen.</span><span class="sxs-lookup"><span data-stu-id="b5f68-233">The name of the connection string is passed in to the context by calling a method on a `DbContextOptionsBuilder` object.</span></span> <span data-ttu-id="b5f68-234">Für die lokale Entwicklung liest das [ASP.NET Core-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der *appsettings.json*-Datei.</span><span class="sxs-lookup"><span data-stu-id="b5f68-234">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<span data-ttu-id="b5f68-235">Fügen Sie `using`-Anweisungen für die Namespaces `ContosoUniversity.Data` und `Microsoft.EntityFrameworkCore` hinzu, und erstellen Sie dann das Projekt.</span><span class="sxs-lookup"><span data-stu-id="b5f68-235">Add `using` statements for `ContosoUniversity.Data` and `Microsoft.EntityFrameworkCore` namespaces, and then build the project.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Usings)]

<span data-ttu-id="b5f68-236">Öffnen Sie die *appsettings.json*-Datei, und fügen Sie wie im folgenden Beispiel dargestellt eine Verbindungszeichenfolge hinzu.</span><span class="sxs-lookup"><span data-stu-id="b5f68-236">Open the *appsettings.json* file and add a connection string as shown in the following example.</span></span>

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

### <a name="sql-server-express-localdb"></a><span data-ttu-id="b5f68-237">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="b5f68-237">SQL Server Express LocalDB</span></span>

<span data-ttu-id="b5f68-238">Die Verbindungszeichenfolge gibt eine SQL Server-LocalDB-Datenbank an.</span><span class="sxs-lookup"><span data-stu-id="b5f68-238">The connection string specifies a SQL Server LocalDB database.</span></span> <span data-ttu-id="b5f68-239">LocalDB ist eine Basisversion der SQL Server Express-Datenbank-Engine, die zwar zur Anwendungsentwicklung, aber nicht für den Produktionseinsatz bestimmt ist.</span><span class="sxs-lookup"><span data-stu-id="b5f68-239">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for application development, not production use.</span></span> <span data-ttu-id="b5f68-240">LocalDB wird bedarfsgesteuert gestartet und im Benutzermodus ausgeführt, sodass keine komplexe Konfiguration anfällt.</span><span class="sxs-lookup"><span data-stu-id="b5f68-240">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="b5f68-241">Standardmäßig erstellt LocalDB *.mdf*-Datenbankdateien im `C:/Users/<user>`-Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="b5f68-241">By default, LocalDB creates *.mdf* database files in the `C:/Users/<user>` directory.</span></span>

## <a name="initialize-db-with-test-data"></a><span data-ttu-id="b5f68-242">Initialisieren Sie die Datenbank mit Testdaten</span><span class="sxs-lookup"><span data-stu-id="b5f68-242">Initialize DB with test data</span></span>

<span data-ttu-id="b5f68-243">Entity Framework erstellt eine leere Datenbank für Sie.</span><span class="sxs-lookup"><span data-stu-id="b5f68-243">The Entity Framework will create an empty database for you.</span></span> <span data-ttu-id="b5f68-244">In diesem Abschnitt schreiben Sie eine Methode, die aufgerufen wird, nachdem die Datenbank erstellt wurde, um diese mit Testdaten aufzufüllen.</span><span class="sxs-lookup"><span data-stu-id="b5f68-244">In this section, you write a method that's called after the database is created in order to populate it with test data.</span></span>

<span data-ttu-id="b5f68-245">Verwenden Sie an dieser Stelle die `EnsureCreated`-Methode, um die Datenbank automatisch zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="b5f68-245">Here you'll use the `EnsureCreated` method to automatically create the database.</span></span> <span data-ttu-id="b5f68-246">In einem [späteren Tutorial](migrations.md) wird dargestellt, wie Sie mit Änderungen an dem Modell umgehen können, indem Sie Code First-Migrationen verwenden, um das Datenbankschema zu verwenden, anstatt die Datenbank zu verwerfen und neu zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="b5f68-246">In a [later tutorial](migrations.md) you'll see how to handle model changes by using Code First Migrations to change the database schema instead of dropping and re-creating the database.</span></span>

<span data-ttu-id="b5f68-247">Erstellen Sie im Ordner *Data* (Daten) eine neue Klassendatei mit dem Namen *DbInitializer.cs*, und ersetzen Sie den Vorlagencode durch den folgenden Code, wodurch, falls nötig, eine Datenbank erstellt wird und Testdaten in eine neue Datenbank geladen werden.</span><span class="sxs-lookup"><span data-stu-id="b5f68-247">In the *Data* folder, create a new class file named *DbInitializer.cs* and replace the template code with the following code, which causes a database to be created when needed and loads test data into the new database.</span></span>

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="b5f68-248">Der Code überprüft, ob Studenten in der Datenbank enthalten sind. Wenn dies nicht der Fall ist, nimmt diese an, dass die Datenbank neu ist und mit Testdaten aufgefüllt werden muss.</span><span class="sxs-lookup"><span data-stu-id="b5f68-248">The code checks if there are any students in the database, and if not, it assumes the database is new and needs to be seeded with test data.</span></span> <span data-ttu-id="b5f68-249">Testdaten werden in Arrays anstelle von `List<T>`-Auflistungen geladen, um die Leistung zu optimieren.</span><span class="sxs-lookup"><span data-stu-id="b5f68-249">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="b5f68-250">Ändern Sie in der *Program.cs*-Datei die `Main`-Methode, um beim Anwendungsstart die folgenden Vorgänge auszuführen:</span><span class="sxs-lookup"><span data-stu-id="b5f68-250">In *Program.cs*, modify the `Main` method to do the following on application startup:</span></span>

* <span data-ttu-id="b5f68-251">Rufen Sie eine Datenbankkontextinstanz aus dem Dependency Injection-Container ab.</span><span class="sxs-lookup"><span data-stu-id="b5f68-251">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="b5f68-252">Rufen Sie die Seedmethode auf, indem Sie den Kontext an diese übergeben.</span><span class="sxs-lookup"><span data-stu-id="b5f68-252">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="b5f68-253">Löschen Sie den Kontext, nachdem die Seedmethode abgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="b5f68-253">Dispose the context when the seed method is done.</span></span>

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Seed&highlight=3-20)]

<span data-ttu-id="b5f68-254">Fügen Sie `using`-Anweisungen hinzu:</span><span class="sxs-lookup"><span data-stu-id="b5f68-254">Add `using` statements:</span></span>

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Usings)]

<span data-ttu-id="b5f68-255">Es kann sein, dass in älteren Tutorials ähnlicher Code in der `Configure`-Methode in der *Startup.cs*-Datei angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="b5f68-255">In older tutorials, you may see similar code in the `Configure` method in *Startup.cs*.</span></span> <span data-ttu-id="b5f68-256">Es wird empfohlen, dass Sie die `Configure`-Methode nur verwenden, um die Anforderungspipeline einzurichten.</span><span class="sxs-lookup"><span data-stu-id="b5f68-256">We recommend that you use the `Configure` method only to set up the request pipeline.</span></span> <span data-ttu-id="b5f68-257">Der Code für den Anwendungsstart gehört zur `Main`-Methode.</span><span class="sxs-lookup"><span data-stu-id="b5f68-257">Application startup code belongs in the `Main` method.</span></span>

<span data-ttu-id="b5f68-258">Wenn dann die Anwendung das erste Mal ausgeführt wird, wird die Datenbank erstellt und mit Testdaten aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="b5f68-258">Now the first time you run the application, the database will be created and seeded with test data.</span></span> <span data-ttu-id="b5f68-259">Wenn Sie Ihr Datenmodell ändern, können Sie die Datenbank löschen, die Seedmethode aktualisieren, und mit einer neuen Datenbank auf die gleiche Weise beginnen.</span><span class="sxs-lookup"><span data-stu-id="b5f68-259">Whenever you change your data model, you can delete the database, update your seed method, and start afresh with a new database the same way.</span></span> <span data-ttu-id="b5f68-260">In späteren Tutorials wird ein Update für die Datenbank ausgeführt, wenn das Datenmodell geändert wird, ohne die Datenbank zu löschen und neu zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="b5f68-260">In later tutorials, you'll see how to modify the database when the data model changes, without deleting and re-creating it.</span></span>

## <a name="create-controller-and-views"></a><span data-ttu-id="b5f68-261">Erstellen Sie Controller und Ansichten</span><span class="sxs-lookup"><span data-stu-id="b5f68-261">Create controller and views</span></span>

<span data-ttu-id="b5f68-262">Verwenden Sie als nächstes die Gerüstbauengine in Visual Studio, um einen MVC-Controller und Ansichten hinzuzufügen, die EF verwenden, um Daten abzufragen und zu speichern.</span><span class="sxs-lookup"><span data-stu-id="b5f68-262">Next, you'll use the scaffolding engine in Visual Studio to add an MVC controller and views that will use EF to query and save data.</span></span>

<span data-ttu-id="b5f68-263">Die automatische Erstellung von CRUD-Aktionsmethoden und Ansichten wird als Gerüstbau bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="b5f68-263">The automatic creation of CRUD action methods and views is known as scaffolding.</span></span> <span data-ttu-id="b5f68-264">Gerüstbau und Codegeneration unterscheiden sich insofern als der Gerüstbaucode ein Startpunkt ist, den Sie Ihren eigenen Anforderungen entsprechend verändern können. Generierter Code wird in der Regel nicht verändert.</span><span class="sxs-lookup"><span data-stu-id="b5f68-264">Scaffolding differs from code generation in that the scaffolded code is a starting point that you can modify to suit your own requirements, whereas you typically don't modify generated code.</span></span> <span data-ttu-id="b5f68-265">Wenn Sie generierten Code anpassen müssen, verwenden Sie partielle Klassen, oder generieren Sie den Code erneut, wenn Änderungen vorgenommen werden.</span><span class="sxs-lookup"><span data-stu-id="b5f68-265">When you need to customize generated code, you use partial classes or you regenerate the code when things change.</span></span>

* <span data-ttu-id="b5f68-266">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner **Controller**, und klicken Sie auf **Hinzufügen > Neues Gerüstelement**.</span><span class="sxs-lookup"><span data-stu-id="b5f68-266">Right-click the **Controllers** folder in **Solution Explorer** and select **Add > New Scaffolded Item**.</span></span>

* <span data-ttu-id="b5f68-267">Im Dialogfeld **Gerüst hinzufügen**:</span><span class="sxs-lookup"><span data-stu-id="b5f68-267">In the **Add Scaffold** dialog box:</span></span>

  * <span data-ttu-id="b5f68-268">Wählen Sie **MVC-Controller mit Ansichten unter Verwendung von Entity Framework** aus.</span><span class="sxs-lookup"><span data-stu-id="b5f68-268">Select **MVC controller with views, using Entity Framework**.</span></span>

  * <span data-ttu-id="b5f68-269">Klicken Sie auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="b5f68-269">Click **Add**.</span></span> <span data-ttu-id="b5f68-270">Das Dialogfeld **MVC-Controller mit Ansichten unter Verwendung von Entity Framework hinzufügen** wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b5f68-270">The **Add MVC Controller with views, using Entity Framework** dialog box appears.</span></span>

    ![Gerüst „Student“](intro/_static/scaffold-student2.png)

  * <span data-ttu-id="b5f68-272">Wählen Sie unter **Modellklasse** **Student** aus.</span><span class="sxs-lookup"><span data-stu-id="b5f68-272">In **Model class** select **Student**.</span></span>

  * <span data-ttu-id="b5f68-273">Wählen Sie in der **Datenkontextklasse** **SchoolContext** aus.</span><span class="sxs-lookup"><span data-stu-id="b5f68-273">In **Data context class** select **SchoolContext**.</span></span>

  * <span data-ttu-id="b5f68-274">Akzeptieren Sie den Standardnamen **StudentsController**.</span><span class="sxs-lookup"><span data-stu-id="b5f68-274">Accept the default **StudentsController** as the name.</span></span>

  * <span data-ttu-id="b5f68-275">Klicken Sie auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="b5f68-275">Click **Add**.</span></span>

  <span data-ttu-id="b5f68-276">Wenn Sie auf **Hinzufügen** klicken, erstellt die Visual Studio-Gerüstbauengine eine *StudentsController.cs*-Datei und mehrere Ansichten ( *.cshtml*-Dateien), die mit dem Controller zusammenarbeiten.</span><span class="sxs-lookup"><span data-stu-id="b5f68-276">When you click **Add**, the Visual Studio scaffolding engine creates a *StudentsController.cs* file and a set of views (*.cshtml* files) that work with the controller.</span></span>

<span data-ttu-id="b5f68-277">(Die Gerüstbauengine kann ebenfalls für Sie den Datenbankkontext erstellen, wenn Sie diesen nicht zuvor manuell erstellt haben, wie obenstehend in diesem Tutorial beschrieben.</span><span class="sxs-lookup"><span data-stu-id="b5f68-277">(The scaffolding engine can also create the database context for you if you don't create it manually first as you did earlier for this tutorial.</span></span> <span data-ttu-id="b5f68-278">Sie können im Feld **Controller hinzufügen** eine neue Kontextklasse hinzufügen, indem Sie rechts neben der **Datenkontextklasse** auf das Pluszeichen klicken.</span><span class="sxs-lookup"><span data-stu-id="b5f68-278">You can specify a new context class in the **Add Controller** box by clicking the plus sign to the right of **Data context class**.</span></span>  <span data-ttu-id="b5f68-279">Visual Studio erstellt dann neben dem Controller und den Ansichten Ihre `DbContext`-Klasse.)</span><span class="sxs-lookup"><span data-stu-id="b5f68-279">Visual Studio will then create your `DbContext` class as well as the controller and views.)</span></span>

<span data-ttu-id="b5f68-280">Der Controller verwendet einen `SchoolContext`-Kontext als Konstruktorparameter.</span><span class="sxs-lookup"><span data-stu-id="b5f68-280">You'll notice that the controller takes a `SchoolContext` as a constructor parameter.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

<span data-ttu-id="b5f68-281">Über die Abhängigkeitsinjektion in ASP.NET Core wird eine Instanz von `SchoolContext` an den Controller übergeben.</span><span class="sxs-lookup"><span data-stu-id="b5f68-281">ASP.NET Core dependency injection takes care of passing an instance of `SchoolContext` into the controller.</span></span> <span data-ttu-id="b5f68-282">Diese Konfiguration haben Sie bereits in der Datei *Startup.cs*vorgenommen.</span><span class="sxs-lookup"><span data-stu-id="b5f68-282">You configured that in the *Startup.cs* file earlier.</span></span>

<span data-ttu-id="b5f68-283">Der Controller enthält eine `Index`-Aktionsmethode, über die alle Studenten in der Datenbank angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="b5f68-283">The controller contains an `Index` action method, which displays all students in the database.</span></span> <span data-ttu-id="b5f68-284">Die Methode ruft eine Listen von Studenten aus der Entitätenmenge „Student“ ab, indem sie die `Students`-Eigenschaft aus der Datenbankkontextinstanz liest:</span><span class="sxs-lookup"><span data-stu-id="b5f68-284">The method gets a list of students from the Students entity set by reading the `Students` property of the database context instance:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

<span data-ttu-id="b5f68-285">Später in diesem Tutorial erfahren Sie mehr zu den Elementen für die asynchrone Programmierung in diesem Code.</span><span class="sxs-lookup"><span data-stu-id="b5f68-285">You'll learn about the asynchronous programming elements in this code later in the tutorial.</span></span>

<span data-ttu-id="b5f68-286">In der *Views/Students/Index.cshtml*-Ansicht wird diese Liste in einer Tabelle dargestellt:</span><span class="sxs-lookup"><span data-stu-id="b5f68-286">The *Views/Students/Index.cshtml* view displays this list in a table:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

<span data-ttu-id="b5f68-287">Drücken Sie STRG+F5, um das Projekt auszuführen, oder wählen Sie aus dem Menü **Debuggen > Ohne Debuggen starten** aus.</span><span class="sxs-lookup"><span data-stu-id="b5f68-287">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span>

<span data-ttu-id="b5f68-288">Klicken Sie auf die Registerkarte „Students“, um die Testdaten abzurufen, die über die `DbInitializer.Initialize`-Methode eingefügt wurden.</span><span class="sxs-lookup"><span data-stu-id="b5f68-288">Click the Students tab to see the test data that the `DbInitializer.Initialize` method inserted.</span></span> <span data-ttu-id="b5f68-289">Je nachdem, wie klein Ihr Browserfenster dargestellt wird, sehen Sie einen `Students`-Registerkartenlink unten auf der Seite, oder Sie müssen auf das Navigationssymbol in der oberen rechten Ecke klicken, damit der Link angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="b5f68-289">Depending on how narrow your browser window is, you'll see the `Students` tab link at the top of the page or you'll have to click the navigation icon in the upper right corner to see the link.</span></span>

![Contoso University-Startseite verkleinert](intro/_static/home-page-narrow.png)

![Indexseite „Studenten“](intro/_static/students-index.png)

## <a name="view-the-database"></a><span data-ttu-id="b5f68-292">Zeigen Sie die Datenbank an</span><span class="sxs-lookup"><span data-stu-id="b5f68-292">View the database</span></span>

<span data-ttu-id="b5f68-293">Nachdem Sie die Anwendung gestartet haben, ruft die `DbInitializer.Initialize`-Methode `EnsureCreated` auf.</span><span class="sxs-lookup"><span data-stu-id="b5f68-293">When you started the application, the `DbInitializer.Initialize` method calls `EnsureCreated`.</span></span> <span data-ttu-id="b5f68-294">EF hat festgestellt, dass noch keine Datenbank vorhanden war und hat deshalb eine erstellt. Anschließend hat der restliche `Initialize`-Methodencode die Datenbank mit Daten aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="b5f68-294">EF saw that there was no database and so it created one, then the remainder of the `Initialize` method code populated the database with data.</span></span> <span data-ttu-id="b5f68-295">Sie können den **SQL Server-Objekt-Explorer** (SSOX) verwenden, um die Datenbank in Visual Studio abzurufen.</span><span class="sxs-lookup"><span data-stu-id="b5f68-295">You can use **SQL Server Object Explorer** (SSOX) to view the database in Visual Studio.</span></span>

<span data-ttu-id="b5f68-296">Schließen Sie den Browser.</span><span class="sxs-lookup"><span data-stu-id="b5f68-296">Close the browser.</span></span>

<span data-ttu-id="b5f68-297">Wenn das SSOX-Fenster noch nicht geöffnet ist, wählen Sie es aus dem Menü **Ansicht** in Visual Studio aus.</span><span class="sxs-lookup"><span data-stu-id="b5f68-297">If the SSOX window isn't already open, select it from the **View** menu in Visual Studio.</span></span>

<span data-ttu-id="b5f68-298">Klicken Sie im SSOX auf **(localdb)\MSSQLLocalDB > Datenbanken**, und klicken Sie anschließend auf den Eintrag zu Ihrem Datenbanknamen, der sich in der Verbindungszeichenfolge in Ihrer *appsettings.json*-Datei befindet.</span><span class="sxs-lookup"><span data-stu-id="b5f68-298">In SSOX, click **(localdb)\MSSQLLocalDB > Databases**, and then click the entry for the database name that's in the connection string in your *appsettings.json* file.</span></span>

<span data-ttu-id="b5f68-299">Erweitern Sie den Knoten **Tabellen**, um die Tabellen in Ihrer Datenbank anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="b5f68-299">Expand the **Tables** node to see the tables in your database.</span></span>

![Tabellen im SSOX](intro/_static/ssox-tables.png)

<span data-ttu-id="b5f68-301">Klicken Sie mit der rechten Maustaste auf die Tabelle **Students**, und klicken Sie auf **Daten anzeigen**, um die erstellten Spalten und die in die Tabelle eingefügten Zeilen aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="b5f68-301">Right-click the **Student** table and click **View Data** to see the columns that were created and the rows that were inserted into the table.</span></span>

![Tabelle „Student“ im SSOX](intro/_static/ssox-student-table.png)

<span data-ttu-id="b5f68-303">Die Datenbankdateien im Format *MDF* und *LDF* befinden sich im Ordner *C:\Benutzer\\\<Ihr Benutzername>* .</span><span class="sxs-lookup"><span data-stu-id="b5f68-303">The *.mdf* and *.ldf* database files are in the *C:\Users\\\<yourusername>* folder.</span></span>

<span data-ttu-id="b5f68-304">Da Sie `EnsureCreated` in der Initialisierermethode aufrufen, die beim App-Start ausgeführt wird, können Sie Änderungen an der `Student`-Klasse vornehmen, die Datenbank löschen oder die Anwendung erneut ausführen. Dann wird Ihre Datenbank automatisch Ihren Änderungen entsprechend neu erstellt.</span><span class="sxs-lookup"><span data-stu-id="b5f68-304">Because you're calling `EnsureCreated` in the initializer method that runs on app start, you could now make a change to the `Student` class, delete the database, run the application again, and the database would automatically be re-created to match your change.</span></span> <span data-ttu-id="b5f68-305">Wenn Sie z.B. eine `EmailAddress`-Eigenschaft zu der `Student`-Klasse hinzufügen, wird eine neue `EmailAddress`-Spalte in der neu erstellten Tabelle angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b5f68-305">For example, if you add an `EmailAddress` property to the `Student` class, you'll see a new `EmailAddress` column in the re-created table.</span></span>

## <a name="conventions"></a><span data-ttu-id="b5f68-306">Konventionen</span><span class="sxs-lookup"><span data-stu-id="b5f68-306">Conventions</span></span>

<span data-ttu-id="b5f68-307">Sie mussten nur wenig Code schreiben, damit Entity Framework eine vollständige Datenbank erstellen kann, da Konventionen oder Annahmen von Entity Framework verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="b5f68-307">The amount of code you had to write in order for the Entity Framework to be able to create a complete database for you is minimal because of the use of conventions, or assumptions that the Entity Framework makes.</span></span>

* <span data-ttu-id="b5f68-308">Die Namen der `DbSet`-Eigenschaften werden als Tabellennamen verwendet.</span><span class="sxs-lookup"><span data-stu-id="b5f68-308">The names of `DbSet` properties are used as table names.</span></span> <span data-ttu-id="b5f68-309">Für Entitäten, auf die nicht über eine `DbSet`-Eigenschaft verwiesen wird, werden Entitätsklassennamen als Tabellennamen verwendet.</span><span class="sxs-lookup"><span data-stu-id="b5f68-309">For entities not referenced by a `DbSet` property, entity class names are used as table names.</span></span>

* <span data-ttu-id="b5f68-310">Eigenschaftennamen von Entitäten werden als Spaltennamen verwendet.</span><span class="sxs-lookup"><span data-stu-id="b5f68-310">Entity property names are used for column names.</span></span>

* <span data-ttu-id="b5f68-311">Entitätseigenschaften mit dem Namen „ID“ oder „classnameID“ werden als Primärschlüsseleigenschaften erkannt.</span><span class="sxs-lookup"><span data-stu-id="b5f68-311">Entity properties that are named ID or classnameID are recognized as primary key properties.</span></span>

* <span data-ttu-id="b5f68-312">Eigenschaften werden als Fremdschlüsseleigenschaften interpretiert, wenn Sie den Namen *\<Name der Navigationseigenschaft>\<Eigenschaftenname des Primärschlüssels* haben – z. B. `StudentID` für die `Student`-Navigationseigenschaft, da der Primärschlüssel der `Student`-Entität `ID` lautet.</span><span class="sxs-lookup"><span data-stu-id="b5f68-312">A property is interpreted as a foreign key property if it's named *\<navigation property name>\<primary key property name>* (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="b5f68-313">Fremdschlüsseleigenschaften können auch einfach den Namen *\<Eigenschaftenname des Primärschlüssels* haben – z. B. `EnrollmentID`, da der Primärschlüssel der `Enrollment`-Entität `EnrollmentID` lautet.</span><span class="sxs-lookup"><span data-stu-id="b5f68-313">Foreign key properties can also be named simply *\<primary key property name>* (for example, `EnrollmentID` since the `Enrollment` entity's primary key is `EnrollmentID`).</span></span>

<span data-ttu-id="b5f68-314">Konventionelles Verhalten kann überschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="b5f68-314">Conventional behavior can be overridden.</span></span> <span data-ttu-id="b5f68-315">Beispielsweise können Sie, wie bereits in diesem Tutorial erläutert, Tabellennamen explizit angeben.</span><span class="sxs-lookup"><span data-stu-id="b5f68-315">For example, you can explicitly specify table names, as you saw earlier in this tutorial.</span></span> <span data-ttu-id="b5f68-316">Außerdem können Sie Spaltennamen und jede beliebige Eigenschaft als Primär- oder Fremdschlüssel festlegen. Dies wird in einem [späteren Tutorial](complex-data-model.md) in dieser Reihe erläutert.</span><span class="sxs-lookup"><span data-stu-id="b5f68-316">And you can set column names and set any property as primary key or foreign key, as you'll see in a [later tutorial](complex-data-model.md) in this series.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="b5f68-317">Asynchroner Code</span><span class="sxs-lookup"><span data-stu-id="b5f68-317">Asynchronous code</span></span>

<span data-ttu-id="b5f68-318">Die asynchrone Programmierung ist der Standardmodus für ASP.NET Core und EF Core.</span><span class="sxs-lookup"><span data-stu-id="b5f68-318">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="b5f68-319">Der Webserver verfügt nur über eine begrenzte Anzahl von Threads. Daher werden bei hoher Auslastung möglicherweise alle verfügbaren Threads gleichzeitig verwendet.</span><span class="sxs-lookup"><span data-stu-id="b5f68-319">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="b5f68-320">Wenn dies der Fall ist, kann der Server keine neuen Anforderungen verarbeiten, bis die Threads wieder freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="b5f68-320">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="b5f68-321">Wenn synchroner Code verwendet wird, kann es sein, dass zwar viele Threads belegt sind, diese aber keine Vorgänge ausführen, da sie auf den Abschluss der E/A-Vorgänge warten.</span><span class="sxs-lookup"><span data-stu-id="b5f68-321">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="b5f68-322">Wenn asynchroner Code verwendet wird, werden Threads für den Server freigegeben, wenn diese nur auf den Abschluss der E/A-Vorgänge warten, damit andere Anforderungen verarbeitet werden können.</span><span class="sxs-lookup"><span data-stu-id="b5f68-322">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="b5f68-323">Das bedeutet, dass es durch asynchronen Code ermöglicht wird, Serverressourcen effizienter zu nutzen, und der Server kann ohne Verzögerungen eine größere Menge von Datenverkehr verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="b5f68-323">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="b5f68-324">Durch die Verwendung von asynchronem Code entsteht ein geringes Maß an Mehraufwand zur Laufzeit. In Situationen, in denen nur wenig Datenverkehr verarbeitet werden muss, haben diese Leistungseinbußen keine negativen Folgen. Wenn es jedoch eine große Menge an Datenverkehr gibt, ist eine potentielle Verbesserung der Leistung von Bedeutung.</span><span class="sxs-lookup"><span data-stu-id="b5f68-324">Asynchronous code does introduce a small amount of overhead at run time, but for low traffic situations the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="b5f68-325">Im folgenden Code haben das `async`-Schlüsselwort, der `Task<T>`-Rückgabewert, das `await`-Schlüsselwort und die `ToListAsync`-Methode zur Folge, dass der Code auf asynchrone Weise ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="b5f68-325">In the following code, the `async` keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="b5f68-326">Das `async`-Schlüsselwort teilt dem Compiler mit, dass er für einzelne Teile des Methodentexts Rückrufe generieren und das zurückgegebene `Task<IActionResult>`-Objekt automatisch erstellen soll.</span><span class="sxs-lookup"><span data-stu-id="b5f68-326">The `async` keyword tells the compiler to generate callbacks for parts of the method body and to automatically create the `Task<IActionResult>` object that's returned.</span></span>

* <span data-ttu-id="b5f68-327">Der Rückgabetyp `Task<IActionResult>` stellt derzeit ausgeführte Arbeiten mit dem Ergebnis von Typ `IActionResult` dar.</span><span class="sxs-lookup"><span data-stu-id="b5f68-327">The return type `Task<IActionResult>` represents ongoing work with a result of type `IActionResult`.</span></span>

* <span data-ttu-id="b5f68-328">Das `await`-Schlüsselwort hat zur Folge, dass der Compiler die Methode in zwei Teile unterteilt.</span><span class="sxs-lookup"><span data-stu-id="b5f68-328">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="b5f68-329">Der erste Teil endet mit dem Vorgang, der auf asynchrone Weise gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="b5f68-329">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="b5f68-330">Der zweite Teil wird in eine Rückrufmethode übertragen, die aufgerufen wird, wenn der Vorgang abgeschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="b5f68-330">The second part is put into a callback method that's called when the operation completes.</span></span>

* <span data-ttu-id="b5f68-331">Bei `ToListAsync` handelt es sich um die asynchrone Version der `ToList`-Erweiterungsmethode.</span><span class="sxs-lookup"><span data-stu-id="b5f68-331">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="b5f68-332">Behalten Sie Folgendes im Hinterkopf, wenn Sie asynchronen Code schreiben, der Entity Framework Core verwendet:</span><span class="sxs-lookup"><span data-stu-id="b5f68-332">Some things to be aware of when you are writing asynchronous code that uses the Entity Framework:</span></span>

* <span data-ttu-id="b5f68-333">Es werden nur Anweisungen auf asynchrone Weise ausgeführt, die Abfragen oder Befehle auslösen, die an die Datenbank gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="b5f68-333">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="b5f68-334">Das schließt beispielsweise `ToListAsync`, `SingleOrDefaultAsync` und `SaveChangesAsync` ein.</span><span class="sxs-lookup"><span data-stu-id="b5f68-334">That includes, for example, `ToListAsync`, `SingleOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="b5f68-335">Anweisungen wie `var students = context.Students.Where(s => s.LastName == "Davolio")`, die nur eine `IQueryable`-Instanz ändern, sind beispielsweise davon ausgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="b5f68-335">It doesn't include, for example, statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>

* <span data-ttu-id="b5f68-336">Entity Framework Core-Kontexte sind nicht threadsicher. Versuchen Sie daher nicht, mehrere Vorgänge gleichzeitig auszuführen.</span><span class="sxs-lookup"><span data-stu-id="b5f68-336">An EF context isn't thread safe: don't try to do multiple operations in parallel.</span></span> <span data-ttu-id="b5f68-337">Wenn Sie eine beliebige EF-Methode aufrufen, sollten Sie immer das `await`-Schlüsselwort verwenden.</span><span class="sxs-lookup"><span data-stu-id="b5f68-337">When you call any async EF method, always use the `await` keyword.</span></span>

* <span data-ttu-id="b5f68-338">Wenn Sie von den Leistungsvorteilen des asynchronen Codes profitieren möchten, vergewissern Sie sich, dass auch alle Bibliothekspakete, die Sie verwenden (z.B. zum Paging) asynchronen Code verwenden, wenn sie Entity Framework Core-Methoden aufrufen, die Abfragen an die Datenbank senden.</span><span class="sxs-lookup"><span data-stu-id="b5f68-338">If you want to take advantage of the performance benefits of async code, make sure that any library packages that you're using (such as for paging), also use async if they call any Entity Framework methods that cause queries to be sent to the database.</span></span>

<span data-ttu-id="b5f68-339">Weitere Informationen zur asynchronen Programmierung in .NET finden Sie unter [Async (Übersicht)](/dotnet/articles/standard/async).</span><span class="sxs-lookup"><span data-stu-id="b5f68-339">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/articles/standard/async).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="b5f68-340">Abrufen des Codes</span><span class="sxs-lookup"><span data-stu-id="b5f68-340">Get the code</span></span>

[<span data-ttu-id="b5f68-341">Download or view the completed app (Herunterladen oder anzeigen der vollständigen App).</span><span class="sxs-lookup"><span data-stu-id="b5f68-341">Download or view the completed application.</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="b5f68-342">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="b5f68-342">Next steps</span></span>

<span data-ttu-id="b5f68-343">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="b5f68-343">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="b5f68-344">Haben Sie eine ASP.NET Core-MVC-Web-App erstellt</span><span class="sxs-lookup"><span data-stu-id="b5f68-344">Created ASP.NET Core MVC web app</span></span>
> * <span data-ttu-id="b5f68-345">Einrichten des Websitestils</span><span class="sxs-lookup"><span data-stu-id="b5f68-345">Set up the site style</span></span>
> * <span data-ttu-id="b5f68-346">Haben Sie mehr über EF Core-NuGet-Pakete erfahren</span><span class="sxs-lookup"><span data-stu-id="b5f68-346">Learned about EF Core NuGet packages</span></span>
> * <span data-ttu-id="b5f68-347">Haben Sie das Datenmodell erstellt</span><span class="sxs-lookup"><span data-stu-id="b5f68-347">Created the data model</span></span>
> * <span data-ttu-id="b5f68-348">Haben Sie den Datenbankkontext erstellt</span><span class="sxs-lookup"><span data-stu-id="b5f68-348">Created the database context</span></span>
> * <span data-ttu-id="b5f68-349">Haben Sie den SchoolContext registriert</span><span class="sxs-lookup"><span data-stu-id="b5f68-349">Registered the SchoolContext</span></span>
> * <span data-ttu-id="b5f68-350">Haben Sie die Datenbank mit Testdaten initialisiert</span><span class="sxs-lookup"><span data-stu-id="b5f68-350">Initialized DB with test data</span></span>
> * <span data-ttu-id="b5f68-351">Haben Sie Controller und Ansichten erstellt</span><span class="sxs-lookup"><span data-stu-id="b5f68-351">Created controller and views</span></span>
> * <span data-ttu-id="b5f68-352">Haben Sie die Datenbank angezeigt</span><span class="sxs-lookup"><span data-stu-id="b5f68-352">Viewed the database</span></span>

<span data-ttu-id="b5f68-353">Im nächsten Tutorial erfahren Sie, wie Sie grundlegende CRUD-Vorgänge (Create, Read, Update, Delete = Erstellen, Lesen, Aktualisieren, Löschen) ausführen.</span><span class="sxs-lookup"><span data-stu-id="b5f68-353">In the following tutorial, you'll learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>

<span data-ttu-id="b5f68-354">Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie grundlegende CRUD-Vorgänge (Create, Read, Update, Delete = Erstellen, Lesen, Aktualisieren, Löschen) ausführen.</span><span class="sxs-lookup"><span data-stu-id="b5f68-354">Advance to the next tutorial to learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="b5f68-355">Implementieren von grundlegenden CRUD-Funktionen</span><span class="sxs-lookup"><span data-stu-id="b5f68-355">Implement basic CRUD functionality</span></span>](crud.md)

::: moniker-end

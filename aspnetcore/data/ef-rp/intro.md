---
title: 'Razor-Seiten mit Entity Framework Core in ASP.NET Core: Tutorial 1 von 8'
author: rick-anderson
description: Informationen zum Erstellen einer Razor Pages-App mit Entity Framework Core
ms.author: riande
ms.custom: seodec18
ms.date: 11/22/2018
uid: data/ef-rp/intro
ms.openlocfilehash: fa18307485596ef9b3e9d5116df062d190cb6c72
ms.sourcegitcommit: 34bf9fc6ea814c039401fca174642f0acb14be3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/14/2019
ms.locfileid: "57841487"
---
# <a name="razor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="e7fb3-103">Razor-Seiten mit Entity Framework Core in ASP.NET Core: Tutorial 1 von 8</span><span class="sxs-lookup"><span data-stu-id="e7fb3-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

[!INCLUDE[2.0 version](~/includes/RP-EF/20-pdf.md)]

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="e7fb3-104">Von [Tom Dykstra](https://github.com/tdykstra) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e7fb3-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e7fb3-105">Die Beispiel-Web-App der Contoso University veranschaulicht, wie mit Entity Framework Core (EF Core) eine ASP.NET Core-App mit Razor Pages erstellt werden kann.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-105">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="e7fb3-106">Bei der Beispiel-App handelt es sich um eine Website für die fiktive Contoso University.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-106">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="e7fb3-107">Sie enthält Funktionen wie die Zulassung von Studenten, die Erstellung von Kursen und Aufgaben von Dozenten.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-107">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="e7fb3-108">Dies ist die erste Seite eines mehrseitigen Tutorials, in dem die Erstellung der Beispiel-App der Contoso University erläutert wird.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-108">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="e7fb3-109">Download or view the completed app (Herunterladen oder anzeigen der vollständigen App).</span><span class="sxs-lookup"><span data-stu-id="e7fb3-109">Download or view the completed app.</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="e7fb3-110">[Anweisungen zum Download.](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="e7fb3-110">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e7fb3-111">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="e7fb3-111">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e7fb3-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e7fb3-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="e7fb3-113">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="e7fb3-113">.NET Core CLI</span></span>](#tab/netcore-cli)

[!INCLUDE [](~/includes/2.1-SDK.md)]

------

<span data-ttu-id="e7fb3-114">Kenntnisse über [Razor Pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="e7fb3-114">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="e7fb3-115">Anfänger sollten den Artikel [Erste Schritte mit Razor Pages in ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start) lesen, bevor sie mit diesem Tutorial beginnen.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-115">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="e7fb3-116">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="e7fb3-116">Troubleshooting</span></span>

<span data-ttu-id="e7fb3-117">Wenn Sie auf ein Problem stoßen, das Sie nicht lösen können, sollten Sie versuchen, Ihren Code mit dem [abgeschlossenen Projekt](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) zu vergleichen.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-117">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="e7fb3-118">Sie können auch Hilfe erhalten, indem Sie eine Frage zu [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) oder [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core) auf [stackoverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) stellen.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-118">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="e7fb3-119">Die Web-App der Contoso University</span><span class="sxs-lookup"><span data-stu-id="e7fb3-119">The Contoso University web app</span></span>

<span data-ttu-id="e7fb3-120">Bei der App, die mithilfe dieser Tutorials erstellt werden soll, handelt es sich um eine einfache Website einer Universität.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-120">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="e7fb3-121">Benutzer können Informationen zu den Studenten, Kursen und Dozenten abrufen.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-121">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="e7fb3-122">Im Folgenden sind einige Anzeigen dargestellt, die mithilfe dieses Tutorials erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-122">Here are a few of the screens created in the tutorial.</span></span>

![Indexseite „Studenten“](intro/_static/students-index.png)

![Bearbeitungsseite für Studenten](intro/_static/student-edit.png)

<span data-ttu-id="e7fb3-125">Der Benutzeroberflächenstil dieser Website ähnelt den durch die integrierten Vorlagen generierten Seiten.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-125">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="e7fb3-126">In diesem Tutorial wird Entity Framework Core mit Razor Pages thematisiert. Die Benutzeroberfläche wird nicht erläutert.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-126">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-razor-pages-web-app"></a><span data-ttu-id="e7fb3-127">Erstellen der Razor Pages-Web-App „ContosoUniversity“</span><span class="sxs-lookup"><span data-stu-id="e7fb3-127">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e7fb3-128">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e7fb3-128">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e7fb3-129">Wählen Sie in Visual Studio im Menü **Datei** die Option **Neu** > **Projekt** aus.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-129">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="e7fb3-130">Erstellen Sie eine neue ASP.NET Core-Webanwendung.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-130">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="e7fb3-131">Geben Sie dem Projekt den Namen **ContosoUniversity**.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-131">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="e7fb3-132">Es ist wichtig, dass Sie dem Projekt *ContosoUniversity* zu nennen, sodass die Namespaces übereinstimmen, wenn der Code kopiert und eingefügt wird.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-132">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="e7fb3-133">Wählen Sie in der Dropdownliste **ASP.NET Core 2.1** und anschließend **Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-133">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="e7fb3-134">Bilder zu den vorherigen Schritten finden Sie unter [Erstellen einer Razor-Web-App](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span><span class="sxs-lookup"><span data-stu-id="e7fb3-134">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="e7fb3-135">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-135">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="e7fb3-136">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="e7fb3-136">.NET Core CLI</span></span>](#tab/netcore-cli)

```CLI
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

------

## <a name="set-up-the-site-style"></a><span data-ttu-id="e7fb3-137">Einrichten des Websitestils</span><span class="sxs-lookup"><span data-stu-id="e7fb3-137">Set up the site style</span></span>

<span data-ttu-id="e7fb3-138">Sie können das Websitemenü, das Layout und die Startseite über einige Änderungen einrichten.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-138">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="e7fb3-139">Aktualisieren Sie *Pages/Shared/_Layout.cshtml* mit folgenden Änderungen:</span><span class="sxs-lookup"><span data-stu-id="e7fb3-139">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="e7fb3-140">Ändern Sie jedes „ContosoUniversity“ in „Contoso University“.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-140">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="e7fb3-141">Diese Begriffskombination kommt dreimal vor.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-141">There are three occurrences.</span></span>

* <span data-ttu-id="e7fb3-142">Fügen Sie Menüeinträge für **Studenten**, **Kurse**, **Dozenten** und **Abteilungen** hinzu, und löschen Sie den Menüeintrag **Kontakt**.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-142">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="e7fb3-143">Die Änderungen werden hervorgehoben.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-143">The changes are highlighted.</span></span> <span data-ttu-id="e7fb3-144">(Es wird *nicht* das gesamte Markup angezeigt.)</span><span class="sxs-lookup"><span data-stu-id="e7fb3-144">(All the markup is *not* displayed.)</span></span>

[!code-html[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="e7fb3-145">Ersetzen Sie in *Pages/Index.cshtml* die Inhalte der Datei durch den folgenden Code. Dadurch ersetzen Sie den Text zu ASP.NET und MVC durch Text zu dieser App:</span><span class="sxs-lookup"><span data-stu-id="e7fb3-145">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-html[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="e7fb3-146">Erstellen des Datenmodells</span><span class="sxs-lookup"><span data-stu-id="e7fb3-146">Create the data model</span></span>

<span data-ttu-id="e7fb3-147">Erstellen Sie Entitätsklassen für die Contoso University-App.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-147">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="e7fb3-148">Beginnen Sie mit dem folgenden drei Entitäten:</span><span class="sxs-lookup"><span data-stu-id="e7fb3-148">Start with the following three entities:</span></span>

![Datenmodelldiagramm zur Kursanmeldung für Studenten](intro/_static/data-model-diagram.png)

<span data-ttu-id="e7fb3-150">Es besteht eine 1:n-Beziehung zwischen der `Student`- und der `Enrollment`-Entität.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-150">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="e7fb3-151">Es besteht eine 1:n-Beziehung zwischen der `Course`- und der `Enrollment`-Entität.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-151">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="e7fb3-152">Jeder Student kann sich für eine beliebige Anzahl von Kursen anmelden.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-152">A student can enroll in any number of courses.</span></span> <span data-ttu-id="e7fb3-153">Für jeden Kurs kann sich eine beliebige Anzahl von Studenten anmelden.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-153">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="e7fb3-154">In den folgenden Abschnitten wird für jede dieser Entitäten eine Klasse erstellt.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-154">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="e7fb3-155">Die Entität „Student“</span><span class="sxs-lookup"><span data-stu-id="e7fb3-155">The Student entity</span></span>

![Entitätsdiagramm „Student“](intro/_static/student-entity.png)

<span data-ttu-id="e7fb3-157">Erstellen Sie einen Ordner *Models* (Modelle).</span><span class="sxs-lookup"><span data-stu-id="e7fb3-157">Create a *Models* folder.</span></span> <span data-ttu-id="e7fb3-158">Erstellen Sie mit dem folgenden Code im Ordner *Models* (Modelle) eine Klassendatei mit dem Namen *Student.cs*:</span><span class="sxs-lookup"><span data-stu-id="e7fb3-158">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="e7fb3-159">Die `ID`-Eigenschaft fungiert als Primärschlüsselspalte der Datenbanktabelle, die dieser Klasse entspricht.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-159">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="e7fb3-160">Standardmäßig interpretiert Entity Framework Core eine Eigenschaft mit dem Namen `ID` oder `classnameID` als Primärschlüssel.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-160">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="e7fb3-161">In `classnameID` ist `classname` der Name der Klasse.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-161">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="e7fb3-162">Der Primärschlüssel, der alternativ automatisch erkannt wurde, ist im vorherigen Beispiel `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-162">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="e7fb3-163">Die `Enrollments`-Eigenschaft ist eine [Navigationseigenschaft](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="e7fb3-163">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="e7fb3-164">Navigationseigenschaften werden mit anderen Entitäten verknüpft, die dieser Entität zugehörig sind.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-164">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="e7fb3-165">In diesem Fall enthält die `Enrollments`-Eigenschaft einer `Student entity` all diese `Enrollment`-Entitäten, die mit diesem `Student` in Zusammenhang stehen.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-165">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="e7fb3-166">Wenn es z.B. für eine „Student“-Zeile in der Datenbank zwei zugehörige „Enrollment“-Zeilen gibt, enthält die Navigationseigenschaft `Enrollments` zwei `Enrollment`-Entitäten.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-166">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="e7fb3-167">Bei einer zugehörigen `Enrollment`-Zeile handelt es sich um eine Zeile, die den Wert des Primärschlüssels des Studenten in der `StudentID`-Spalte enthält.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-167">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="e7fb3-168">Nehmen Sie z.B. an, dass es für den Studenten mit ID=1 zwei Zeilen in der `Enrollment`-Tabelle gibt.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-168">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="e7fb3-169">Die `Enrollment`-Tabelle verfügt über zwei Zeilen mit `StudentID`=1.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-169">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="e7fb3-170">Bei `StudentID` handelt es sich um einen Fremdschlüssel in der `Enrollment`-Tabelle, der den Studenten in der `Student`-Tabelle angibt.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-170">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="e7fb3-171">Wenn in einer Navigationseigenschaft mehrere Entitäten gespeichert werden können, muss es sich um einen Listentyp wie `ICollection<T>` handeln.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-171">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="e7fb3-172">`ICollection<T>` oder ein Typ wie `List<T>` oder `HashSet<T>` können angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-172">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="e7fb3-173">Wenn `ICollection<T>` verwendet wird, erstellt Entity Framework Core standardmäßig eine `HashSet<T>`-Auflistung.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-173">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="e7fb3-174">Navigationseigenschaften, die mehrere Entitäten enthalten, entstehen auf der Grundlage von m:n- oder 1:n-Beziehungen.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-174">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="e7fb3-175">Die Entität „Enrollment“</span><span class="sxs-lookup"><span data-stu-id="e7fb3-175">The Enrollment entity</span></span>

![Entitätsdiagramm „Enrollment“](intro/_static/enrollment-entity.png)

<span data-ttu-id="e7fb3-177">Erstellen Sie mit dem folgenden Code im Ordner *Models* (Modelle) eine *Enrollment.cs*-Datei:</span><span class="sxs-lookup"><span data-stu-id="e7fb3-177">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="e7fb3-178">Bei der `EnrollmentID`-Eigenschaft handelt es sich um den Primärschlüssel.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-178">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="e7fb3-179">Diese Entität verwendet genau wie die `Student`-Entität das `classnameID`-Muster anstelle von `ID`.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-179">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="e7fb3-180">In der Regel wählen Entwickler ein Muster aus und verwenden dieses im gesamten Datenmodell.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-180">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="e7fb3-181">In einem der nächsten Tutorials wird erläutert, wie Sie eine ID ohne Klassennamen verwenden, um die Vererbung einfacher in das Datenmodell zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-181">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="e7fb3-182">Bei der `Grade`-Eigenschaft handelt es sich um eine `enum`.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-182">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="e7fb3-183">Das Fragezeichen nach der `Grade`-Typdeklaration gibt an, dass die `Grade`-Eigenschaft NULL-Werte zulässt.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-183">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="e7fb3-184">Eine Grade-Eigenschaft mit dem Wert NULL unterscheidet sich von einer Grade-Eigenschaft mit dem Wert 0 (null). Der Wert NULL bedeutet, dass keine Grade-Eigenschaft bekannt ist oder noch keine zugewiesen wurde.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-184">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="e7fb3-185">Bei der `StudentID`-Eigenschaft handelt es sich um einen Fremdschlüssel und `Student` ist die entsprechende Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-185">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="e7fb3-186">Die `Enrollment`-Entität wird einer `Student`-Entität zugewiesen. Das bedeutet, dass die Eigenschaft genau eine `Student`-Entität enthält.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-186">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="e7fb3-187">Die `Student`-Entität unterscheidet sich von der `Student.Enrollments`-Navigationseigenschaft, die mehrere `Enrollment`-Entitäten enthält.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-187">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="e7fb3-188">Bei der `CourseID`-Eigenschaft handelt es sich um einen Fremdschlüssel, und `Course` ist die entsprechende Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-188">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="e7fb3-189">Die `Enrollment`-Entität wird einer `Course`-Entität zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-189">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="e7fb3-190">Entity Framework Core interpretiert Eigenschaften als Fremdschlüssel, wenn diese den Namen `<navigation property name><primary key property name>` haben.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-190">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="e7fb3-191">Beispielsweise `StudentID` für die `Student`-Navigationseigenschaft, da `ID` der Primärschlüssel der `Student`-Entität ist.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-191">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="e7fb3-192">Fremdschlüsseleigenschaften können ebenfalls den Namen `<primary key property name>` haben.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-192">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="e7fb3-193">Beispielsweise `CourseID`, da `CourseID` der Primärschlüssel der `Course`-Entität ist.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-193">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="e7fb3-194">Die Entität „Course“</span><span class="sxs-lookup"><span data-stu-id="e7fb3-194">The Course entity</span></span>

![Entitätsdiagramm „Course“](intro/_static/course-entity.png)

<span data-ttu-id="e7fb3-196">Erstellen Sie mit dem folgenden Code im Ordner *Models* (Modelle) eine *Course.cs*-Datei:</span><span class="sxs-lookup"><span data-stu-id="e7fb3-196">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="e7fb3-197">Die `Enrollments`-Eigenschaft ist eine Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-197">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="e7fb3-198">`Course`-Entitäten können sich auf jede beliebige Anzahl von `Enrollment`-Entitäten beziehen.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-198">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="e7fb3-199">Das `DatabaseGenerated`-Attribut lässt es zu, dass die App den Primärschlüssel angibt, sodass die Datenbank diesen nicht generieren muss.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-199">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="e7fb3-200">Erstellen des Gerüsts für das Studentenmodell</span><span class="sxs-lookup"><span data-stu-id="e7fb3-200">Scaffold the student model</span></span>

<span data-ttu-id="e7fb3-201">In diesem Abschnitt wird das Gerüst für das Studentenmodell erstellt.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-201">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="e7fb3-202">Mit dem Tool für den Gerüstbau werden Seiten für die Vorgänge „Create“ (Erstellen), „Read“ (Lesen), „Update“ (Aktualisieren) und „Delete“ (Löschen), kurz CRUD-Vorgänge, für das Studentenmodell erstellt.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-202">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="e7fb3-203">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-203">Build the project.</span></span>
* <span data-ttu-id="e7fb3-204">Erstellen Sie den Ordner *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-204">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e7fb3-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e7fb3-205">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e7fb3-206">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner *Pages/Students*, und wählen Sie **Hinzufügen** > **Neues Gerüstelement** aus.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-206">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="e7fb3-207">Wählen Sie im Dialogfeld **Gerüst hinzufügen** den Eintrag **Razor Pages mit Entity Framework (CRUD)** > **HINZUFÜGEN** aus.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-207">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="e7fb3-208">Vervollständigen Sie das Dialogfeld **Razor Pages mit Entity Framework (CRUD) hinzufügen**:</span><span class="sxs-lookup"><span data-stu-id="e7fb3-208">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="e7fb3-209">Wählen Sie im Dropdownmenü **Modellklasse** **Student (ContosoUniversity.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-209">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="e7fb3-210">Wählen Sie in der Zeile **Datenkontextklasse** das Pluszeichen (**+**) aus, und ändern Sie den generierten Namen in **ContosoUniversity.Models.SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-210">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="e7fb3-211">Wählen Sie im Dropdownmenü **Datenkontextklasse** **ContosoUniversity.Models.SchoolContext** aus.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-211">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="e7fb3-212">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-212">Select **Add**.</span></span>

![CRUD-Dialogfeld](intro/_static/s1.png)

<span data-ttu-id="e7fb3-214">Wenn Sie Probleme mit dem vorherigen Schritt haben, finden Sie weitere Informationen unter [Erstellen des Gerüsts für das Filmmodell](xref:tutorials/razor-pages/model#scaffold-the-movie-model).</span><span class="sxs-lookup"><span data-stu-id="e7fb3-214">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="e7fb3-215">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="e7fb3-215">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="e7fb3-216">Führen Sie folgende Befehle aus, um das Gerüst für das Studentenmodell zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-216">Run the following commands to scaffold the student model.</span></span>

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```
------

<span data-ttu-id="e7fb3-217">Der Gerüstprozess hat folgende Dateien erstellt und geändert:</span><span class="sxs-lookup"><span data-stu-id="e7fb3-217">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="e7fb3-218">Erstellte Dateien</span><span class="sxs-lookup"><span data-stu-id="e7fb3-218">Files created</span></span>

* <span data-ttu-id="e7fb3-219">*Pages/Students/Create.cshtml.cs* ( bzw. /Delete, /Details, /Edit, /Index).</span><span class="sxs-lookup"><span data-stu-id="e7fb3-219">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="e7fb3-220">*Data/SchoolContext.cs*</span><span class="sxs-lookup"><span data-stu-id="e7fb3-220">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="e7fb3-221">Dateiupdates</span><span class="sxs-lookup"><span data-stu-id="e7fb3-221">File updates</span></span>

* <span data-ttu-id="e7fb3-222">*Startup.cs*: Die Änderungen an dieser Datei werden im nächsten Abschnitt ausführlich erläutert.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-222">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="e7fb3-223">*appsettings.json*: Die Verbindungszeichenfolge, die zum Herstellen einer Verbindung mit einer lokalen Datenbank verwendet wird, wurde hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-223">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="e7fb3-224">Überprüfen des mit Dependency Injection registrierten Kontexts</span><span class="sxs-lookup"><span data-stu-id="e7fb3-224">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="e7fb3-225">ASP.NET Core wird mit [Dependency Injection](xref:fundamentals/dependency-injection) erstellt.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-225">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="e7fb3-226">Dienste (z.B. der Datenbankkontext Entity Framework Core) werden über Dependency Injection beim Anwendungsstart registriert.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-226">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="e7fb3-227">Komponenten, die diese Dienste erfordern (z.B. Razor Pages), werden von diesen Diensten über Konstruktorparameter bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-227">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="e7fb3-228">Der Konstruktorcode, der eine Datenbankkontextinstanz abruft, wird später in diesem Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-228">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="e7fb3-229">Das Gerüstbautool hat automatisch einen Datenbankkontext erstellt und diesen mit dem Dependency Injection-Container registriert.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-229">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="e7fb3-230">Untersuchen Sie die Methode `ConfigureServices` in *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-230">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="e7fb3-231">Die hervorgehobene Zeile wurde vom Gerüst hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="e7fb3-231">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="e7fb3-232">Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions)-Objekt aufrufen.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-232">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="e7fb3-233">Für die lokale Entwicklung liest das [ASP.NET Core-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der *appsettings.json*-Datei.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-233">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="e7fb3-234">Aktualisieren der Main-Methode</span><span class="sxs-lookup"><span data-stu-id="e7fb3-234">Update main</span></span>

<span data-ttu-id="e7fb3-235">Ändern Sie in der *Program.cs*-Datei die `Main`-Methode, um die folgenden Vorgänge auszuführen:</span><span class="sxs-lookup"><span data-stu-id="e7fb3-235">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="e7fb3-236">Rufen Sie eine Datenbankkontextinstanz aus dem Dependency Injection-Container ab.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-236">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="e7fb3-237">Rufen Sie [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) auf.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-237">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="e7fb3-238">Löschen Sie den Kontext, wenn die `EnsureCreated`-Methode abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-238">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="e7fb3-239">Der folgende Code zeigt die aktualisierte *Program.cs*-Datei.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-239">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="e7fb3-240">`EnsureCreated` stellt sicher, dass die Datenbank für den Kontext vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-240">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="e7fb3-241">Wenn sie vorhanden ist, werden keine Aktionen durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-241">If it exists, no action is taken.</span></span> <span data-ttu-id="e7fb3-242">Wenn sie nicht vorhanden ist, werden die Datenbank und alle Schemas erstellt.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-242">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="e7fb3-243">`EnsureCreated` verwendet keine Migrationen, um die Datenbank zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-243">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="e7fb3-244">Eine Datenbank, die mit `EnsureCreated` erstellt wird, kann später nicht mithilfe von Migrationen aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-244">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="e7fb3-245">`EnsureCreated` wird beim Starten der App aufgerufen, wodurch der folgende Workflow ermöglicht wird:</span><span class="sxs-lookup"><span data-stu-id="e7fb3-245">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="e7fb3-246">Löschen Sie die Datenbank.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-246">Delete the DB.</span></span>
* <span data-ttu-id="e7fb3-247">Ändern Sie das Datenbankschema, also fügen Sie z.B. ein `EmailAddress`-Feld hinzu.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-247">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="e7fb3-248">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-248">Run the app.</span></span>
* <span data-ttu-id="e7fb3-249">Über `EnsureCreated` wird eine Datenbank mit der `EmailAddress`-Spalte erstellt.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-249">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="e7fb3-250">`EnsureCreated` eignet sich am Anfang der Entwicklung, wenn das Schema schnell weiterentwickelt wird.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-250">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="e7fb3-251">Im Verlauf des Tutorials wird die Datenbank gelöscht, und Migrationen werden verwendet.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-251">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="e7fb3-252">Testen der App</span><span class="sxs-lookup"><span data-stu-id="e7fb3-252">Test the app</span></span>

<span data-ttu-id="e7fb3-253">Führen Sie die App aus, und akzeptieren Sie die Cookierichtlinie.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-253">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="e7fb3-254">Diese App bewahrt keine personenbezogenen Informationen auf.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-254">This app doesn't keep personal information.</span></span> <span data-ttu-id="e7fb3-255">Weitere Informationen zur Cookierichtlinie finden Sie auf der Seite [EU General Data Protection Regulation (GDPR) support (Unterstützung für die Datenschutz-Grundverordnung (DSGVO) der EU)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="e7fb3-255">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="e7fb3-256">Klicken Sie auf den Link **Students** (Studenten) und anschließend auf **Neu erstellen**.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-256">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="e7fb3-257">Testen Sie die Links „Edit“ (Bearbeiten), „Details“ und „Delete“ (Löschen).</span><span class="sxs-lookup"><span data-stu-id="e7fb3-257">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="e7fb3-258">Untersuchen des Datenbankkontexts „SchoolContext“</span><span class="sxs-lookup"><span data-stu-id="e7fb3-258">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="e7fb3-259">Bei der Datenbankkontextklasse handelt es sich um die Hauptklasse, die die Entity Framework Core-Funktionen für ein angegebenes Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-259">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="e7fb3-260">Der Datenkontext wird von [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-260">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="e7fb3-261">Der Datenkontext gibt an, welche Entitäten im Datenmodell enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-261">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="e7fb3-262">In diesem Projekt heißt die Klasse `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-262">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="e7fb3-263">Aktualisieren Sie *SchoolContext.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="e7fb3-263">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="e7fb3-264">Der hervorgehobene Code erstellt eine [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1)-Eigenschaft für jede Entitätenmenge.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-264">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="e7fb3-265">Das heißt für Entity Framework Core:</span><span class="sxs-lookup"><span data-stu-id="e7fb3-265">In EF Core terminology:</span></span>

* <span data-ttu-id="e7fb3-266">Entitätenmengen entsprechen in der Regel einer Datenbanktabelle.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-266">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="e7fb3-267">Entitäten entsprechen Zeilen in Tabellen.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-267">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="e7fb3-268">`DbSet<Enrollment>` und `DbSet<Course>` können ausgelassen werden.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-268">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="e7fb3-269">Diese sind implizit in Entity Framework Core enthalten, da die `Student`-Entität auf die `Enrollment`-Entität verweist, und die `Enrollment`-Entität auf die `Course`-Entität verweist.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-269">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="e7fb3-270">Behalten Sie für dieses Tutorial `DbSet<Enrollment>` und `DbSet<Course>` im `SchoolContext`-Kontext.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-270">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="e7fb3-271">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="e7fb3-271">SQL Server Express LocalDB</span></span>

<span data-ttu-id="e7fb3-272">Die Verbindungszeichenfolge gibt [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) an.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-272">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="e7fb3-273">LocalDB ist eine Basisversion der SQL Server Express-Datenbank-Engine, die zwar für die Anwendungsentwicklung, aber nicht für den Produktionseinsatz bestimmt ist.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-273">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="e7fb3-274">LocalDB wird bedarfsgesteuert gestartet und im Benutzermodus ausgeführt, sodass keine komplexe Konfiguration anfällt.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-274">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="e7fb3-275">Standardmäßig erstellt LocalDB *.mdf*-Datenbankdateien im `C:/Users/<user>`-Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-275">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="e7fb3-276">Hinzufügen von Code zum Initialisieren der Datenbank mithilfe von Testdaten</span><span class="sxs-lookup"><span data-stu-id="e7fb3-276">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="e7fb3-277">Entity Framework Core erstellt eine leere Datenbank.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-277">EF Core creates an empty DB.</span></span> <span data-ttu-id="e7fb3-278">In diesem Abschnitt wird eine `Initialize`-Methode geschrieben, um diese mit Testdaten aufzufüllen.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-278">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="e7fb3-279">Erstellen Sie im Ordner *Data* eine neuen Klassendatei mit dem Namen *DbInitializer.cs*, und fügen Sie den folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="e7fb3-279">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="e7fb3-280">Hinweis: Der vorherige Code verwendet `Models` für den Namespace (`namespace ContosoUniversity.Models`) statt `Data`.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-280">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="e7fb3-281">`Models` entspricht dem vom Gerüst generierten Code.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-281">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="e7fb3-282">Weitere Informationen finden Sie in diesem [GitHub-Gerüstbau-Problem](https://github.com/aspnet/Scaffolding/issues/822).</span><span class="sxs-lookup"><span data-stu-id="e7fb3-282">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="e7fb3-283">Der Code überprüft, ob Studenten in der Datenbank enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-283">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="e7fb3-284">Wenn keine Studenten in der Datenbank enthalten sind, wird diese mit Testdaten initialisiert.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-284">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="e7fb3-285">Testdaten werden in Arrays anstelle von `List<T>`-Auflistungen geladen, um die Leistung zu optimieren.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-285">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="e7fb3-286">Die `EnsureCreated`-Methode erstellt automatisch die Datenbank für den Datenbankkontext.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-286">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="e7fb3-287">Wenn die Datenbank vorhanden ist, wird `EnsureCreated` zurückgegeben, ohne Änderungen an der Datenbank vorzunehmen.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-287">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="e7fb3-288">Ändern Sie in *Program.cs* die `Main`-Methode, um `Initialize` aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="e7fb3-288">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

<span data-ttu-id="e7fb3-289">Löschen Sie alle Datensätze für Studenten, und starten Sie die App neu.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-289">Delete any student records and restart the app.</span></span> <span data-ttu-id="e7fb3-290">Wenn die Datenbank nicht initialisiert wird, legen Sie einen Breakpoint in `Initialize` fest, um das Problem zu diagnostizieren.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-290">If the DB is not initialized, set a break point in `Initialize` to diagnose the problem.</span></span>

## <a name="view-the-db"></a><span data-ttu-id="e7fb3-291">Abrufen der Datenbank</span><span class="sxs-lookup"><span data-stu-id="e7fb3-291">View the DB</span></span>

<span data-ttu-id="e7fb3-292">Öffnen Sie über das Menü **Ansicht** im Visual Studio **SQL Server-Objekt-Explorer** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="e7fb3-292">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="e7fb3-293">Klicken Sie im SSOX auf **(localdb)\MSSQLLocalDB > Databases > ContosoUniversity1**.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-293">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > ContosoUniversity1**.</span></span>

<span data-ttu-id="e7fb3-294">Erweitern Sie den Knoten **Tabellen**.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-294">Expand the **Tables** node.</span></span>

<span data-ttu-id="e7fb3-295">Klicken Sie mit der rechten Maustaste auf die Tabelle **Student**, und klicken Sie auf **Daten anzeigen**, um die erstellten Spalten und die in die Tabelle eingefügten Zeilen aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-295">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="e7fb3-296">Asynchroner Code</span><span class="sxs-lookup"><span data-stu-id="e7fb3-296">Asynchronous code</span></span>

<span data-ttu-id="e7fb3-297">Die asynchrone Programmierung ist der Standardmodus für ASP.NET Core und EF Core.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-297">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="e7fb3-298">Der Webserver verfügt nur über eine begrenzte Anzahl von Threads. Daher werden bei hoher Auslastung möglicherweise alle verfügbaren Threads gleichzeitig verwendet.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-298">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="e7fb3-299">Wenn dies der Fall ist, kann der Server keine neuen Anforderungen verarbeiten, bis die Threads wieder freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-299">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="e7fb3-300">Wenn synchroner Code verwendet wird, kann es sein, dass zwar viele Threads belegt sind, diese aber keine Vorgänge ausführen, da sie auf den Abschluss der E/A-Vorgänge warten.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-300">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="e7fb3-301">Wenn asynchroner Code verwendet wird, werden Threads für den Server freigegeben, wenn diese nur auf den Abschluss der E/A-Vorgänge warten, damit andere Anforderungen verarbeitet werden können.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-301">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="e7fb3-302">Das bedeutet, dass es durch asynchronen Code ermöglicht wird, Serverressourcen effizienter zu nutzen, und der Server kann ohne Verzögerungen eine größere Menge von Datenverkehr verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-302">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="e7fb3-303">Zur Laufzeit bedeutet die Verwendung von asynchronem Code allerdings, dass ein wenig mehr Aufwand entsteht.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-303">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="e7fb3-304">Für Situationen mit wenig Datenverkehr haben diese Leistungseinbußen keine negativen Folgen. Wenn es jedoch eine große Menge an Datenverkehr gibt, ist eine potentielle Verbesserung der Leistung von Bedeutung.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-304">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="e7fb3-305">Im folgenden Code führen das [async](/dotnet/csharp/language-reference/keywords/async)-Schlüsselwort, der `Task<T>`-Rückgabewert, das `await`-Schlüsselwort und die `ToListAsync`-Methode dazu, dass der Code asynchron ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-305">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="e7fb3-306">Das `async`-Schlüsselwort gibt dem Compiler folgende Anweisungen:</span><span class="sxs-lookup"><span data-stu-id="e7fb3-306">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="e7fb3-307">Er soll Rückrufe für Teile des Methodentexts generieren.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-307">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="e7fb3-308">Er soll automatisch das [Task](/dotnet/api/system.threading.tasks.task)-Objekt erstellen, das zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-308">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="e7fb3-309">Weitere Informationen finden Sie unter [Aufgabenrückgabetyp](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="e7fb3-309">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="e7fb3-310">Der implizite Rückgabetyp `Task` steht für die derzeit ausgeführte Arbeit.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-310">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="e7fb3-311">Das `await`-Schlüsselwort hat zur Folge, dass der Compiler die Methode in zwei Teile unterteilt.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-311">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="e7fb3-312">Der erste Teil endet mit dem Vorgang, der auf asynchrone Weise gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-312">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="e7fb3-313">Der zweite Teil wird in eine Rückrufmethode übertragen, die aufgerufen wird, wenn der Vorgang abgeschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-313">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="e7fb3-314">Bei `ToListAsync` handelt es sich um die asynchrone Version der `ToList`-Erweiterungsmethode.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-314">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="e7fb3-315">Behalten Sie Folgendes im Hinterkopf, wenn Sie asynchronen Code schreiben, der Entity Framework Core verwendet:</span><span class="sxs-lookup"><span data-stu-id="e7fb3-315">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="e7fb3-316">Es werden nur Anweisungen auf asynchrone Weise ausgeführt, die Abfragen oder Befehle auslösen, die an die Datenbank gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-316">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="e7fb3-317">Dazu gehören `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` und `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-317">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="e7fb3-318">Anweisungen wie `var students = context.Students.Where(s => s.LastName == "Davolio")`, die nur eine `IQueryable`-Instanz ändern, sind davon ausgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-318">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="e7fb3-319">Entity Framework Core-Kontexte sind nicht threadsicher. Versuchen Sie daher nicht, mehrere Vorgänge gleichzeitig auszuführen.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-319">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="e7fb3-320">Wenn Sie von den Leistungsvorteilen durch asynchronen Code profitieren möchten, überprüfen Sie, ob Bibliothekspakete (z.B. zum Paging) asynchronen Code verwenden, wenn sie Entity Framework Core-Methoden aufrufen, die Abfragen an die Datenbank senden.</span><span class="sxs-lookup"><span data-stu-id="e7fb3-320">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="e7fb3-321">Weitere Informationen zur asynchronen Programmierung in .NET finden Sie unter [Async (Übersicht)](/dotnet/standard/async) und [Asynchrone Programmierung mit Async und Await (C#)](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="e7fb3-321">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="e7fb3-322">Im nächsten Tutorial erfahren Sie mehr über die CRUD-Vorgänge (Create, Read, Update, Delete = Erstellen, Lesen, Aktualisieren, Löschen).</span><span class="sxs-lookup"><span data-stu-id="e7fb3-322">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="e7fb3-323">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="e7fb3-323">Additional resources</span></span>

* [<span data-ttu-id="e7fb3-324">Dieses Tutorial auf YouTube</span><span class="sxs-lookup"><span data-stu-id="e7fb3-324">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="e7fb3-325">Nächste</span><span class="sxs-lookup"><span data-stu-id="e7fb3-325">Next</span></span>](xref:data/ef-rp/crud)

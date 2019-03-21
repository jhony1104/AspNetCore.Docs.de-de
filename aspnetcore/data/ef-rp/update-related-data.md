---
title: 'Razor-Seiten mit EF Core in ASP.NET Core: Aktualisieren verwandter Daten (7 von 8)'
author: rick-anderson
description: Mithilfe dieses Tutorials führen Sie Updates für verwandte Daten aus, indem Sie Felder mit Fremdschlüsseln sowie Navigationseigenschaften aktualisieren.
ms.author: riande
ms.date: 11/15/2017
uid: data/ef-rp/update-related-data
ms.openlocfilehash: e1407048157fccdbfdd854071a6fbf9785356026
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58208608"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---update-related-data---7-of-8"></a><span data-ttu-id="523b1-103">Razor-Seiten mit EF Core in ASP.NET Core: Aktualisieren verwandter Daten (7 von 8)</span><span class="sxs-lookup"><span data-stu-id="523b1-103">Razor Pages with EF Core in ASP.NET Core - Update Related Data - 7 of 8</span></span>

<span data-ttu-id="523b1-104">Von [Tom Dykstra](https://github.com/tdykstra) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="523b1-104">By [Tom Dykstra](https://github.com/tdykstra), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

<span data-ttu-id="523b1-105">In diesem Tutorial wird veranschaulicht, wie verwandte Daten aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="523b1-105">This tutorial demonstrates updating related data.</span></span> <span data-ttu-id="523b1-106">Wenn nicht zu lösende Probleme auftreten, laden Sie die [fertige App](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) herunter, oder zeigen Sie diese an.</span><span class="sxs-lookup"><span data-stu-id="523b1-106">If you run into problems you can't solve, [download or view the completed app.](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="523b1-107">[Anweisungen zum Download.](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="523b1-107">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="523b1-108">In den folgenden Abbildungen werden einige der abgeschlossenen Seiten dargestellt.</span><span class="sxs-lookup"><span data-stu-id="523b1-108">The following illustrations shows some of the completed pages.</span></span>

<span data-ttu-id="523b1-109">![Bearbeitungsseite „Course“ (Kurs)](update-related-data/_static/course-edit.png)
![Bearbeitungsseite „Instructor“ (Dozent)](update-related-data/_static/instructor-edit-courses.png)</span><span class="sxs-lookup"><span data-stu-id="523b1-109">![Course Edit page](update-related-data/_static/course-edit.png)
![Instructor Edit page](update-related-data/_static/instructor-edit-courses.png)</span></span>

<span data-ttu-id="523b1-110">Überprüfen und testen Sie die Seiten „Create Course“ (Kurs erstellen) und „Edit Course“ (Kurs bearbeiten).</span><span class="sxs-lookup"><span data-stu-id="523b1-110">Examine and test the Create and Edit course pages.</span></span> <span data-ttu-id="523b1-111">Erstellen Sie einen neuen Kurs.</span><span class="sxs-lookup"><span data-stu-id="523b1-111">Create a new course.</span></span> <span data-ttu-id="523b1-112">Die Abteilung wird nach dem zugehörigen Primärschlüssel (eine ganze Zahl) ausgewählt, nicht nach dem Namen.</span><span class="sxs-lookup"><span data-stu-id="523b1-112">The department is selected by its primary key (an integer), not its name.</span></span> <span data-ttu-id="523b1-113">Bearbeiten Sie den neuen Kurs.</span><span class="sxs-lookup"><span data-stu-id="523b1-113">Edit the new course.</span></span> <span data-ttu-id="523b1-114">Wenn Sie die Tests ausgeführt haben, können Sie den neuen Kurs löschen.</span><span class="sxs-lookup"><span data-stu-id="523b1-114">When you have finished testing, delete the new course.</span></span>

## <a name="create-a-base-class-to-share-common-code"></a><span data-ttu-id="523b1-115">Erstellen einer Basisklasse für die gemeinsame Nutzung von allgemeinem Code</span><span class="sxs-lookup"><span data-stu-id="523b1-115">Create a base class to share common code</span></span>

<span data-ttu-id="523b1-116">Auf den Seiten „Courses/Create“ (Kurse/Erstellen) und „Courses/Edit“ (Kurse/Bearbeiten) ist jeweils eine Liste mit Abteilungsnamen erforderlich.</span><span class="sxs-lookup"><span data-stu-id="523b1-116">The Courses/Create and Courses/Edit pages each need a list of department names.</span></span> <span data-ttu-id="523b1-117">Erstellen Sie die Basisklasse *Pages/Courses/DepartmentNamePageModel.cshtml.cs* für die Seiten „Create“ (Erstellen) und „Edit“ (Bearbeiten):</span><span class="sxs-lookup"><span data-stu-id="523b1-117">Create the *Pages/Courses/DepartmentNamePageModel.cshtml.cs* base class for the Create and Edit pages:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/DepartmentNamePageModel.cshtml.cs?highlight=9,11,20-21)]

<span data-ttu-id="523b1-118">Der vorangehende Code erstellt eine [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0), in der die Liste der Abteilungsnamen enthalten sein soll.</span><span class="sxs-lookup"><span data-stu-id="523b1-118">The preceding code creates a [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) to contain the list of department names.</span></span> <span data-ttu-id="523b1-119">Wenn `selectedDepartment` angegeben ist, wird diese Abteilung in `SelectList` ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="523b1-119">If `selectedDepartment` is specified, that department is selected in the `SelectList`.</span></span>

<span data-ttu-id="523b1-120">Die Modellklassen der Seiten „Create“ (Erstellen) und „Edit“ (Bearbeiten) werden von `DepartmentNamePageModel` abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="523b1-120">The Create and Edit page model classes will derive from `DepartmentNamePageModel`.</span></span>

## <a name="customize-the-courses-pages"></a><span data-ttu-id="523b1-121">Anpassen der Kursseiten</span><span class="sxs-lookup"><span data-stu-id="523b1-121">Customize the Courses Pages</span></span>

<span data-ttu-id="523b1-122">Wenn eine neue Kursentität erstellt wird, muss diese in Beziehung zu einer vorhandenen Abteilung stehen.</span><span class="sxs-lookup"><span data-stu-id="523b1-122">When a new course entity is created, it must have a relationship to an existing department.</span></span> <span data-ttu-id="523b1-123">Wenn Sie bei der Erstellung eines Kurses eine Abteilung hinzufügen möchten, können Sie aus einer Dropdownliste in der Basisklasse für „Erstellen“ und „Bearbeiten“ die Abteilung auswählen.</span><span class="sxs-lookup"><span data-stu-id="523b1-123">To add a department while creating a course, the base class for Create and Edit contains a drop-down list for selecting the department.</span></span> <span data-ttu-id="523b1-124">In der Dropdownliste wird die Fremdschlüsseleigenschaft `Course.DepartmentID` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="523b1-124">The drop-down list sets the `Course.DepartmentID` foreign key (FK) property.</span></span> <span data-ttu-id="523b1-125">EF Core verwendet den Fremdschlüssel `Course.DepartmentID` zum Laden der Navigationseigenschaft `Department`.</span><span class="sxs-lookup"><span data-stu-id="523b1-125">EF Core uses the `Course.DepartmentID` FK to load the `Department` navigation property.</span></span>

![Erstellen eines Kurses](update-related-data/_static/ddl.png)

<span data-ttu-id="523b1-127">Aktualisieren Sie das Seitenmodell „Create“ (Erstellen) mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="523b1-127">Update the Create page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Create.cshtml.cs?highlight=7,18,32-999)]

<span data-ttu-id="523b1-128">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="523b1-128">The preceding code:</span></span>

* <span data-ttu-id="523b1-129">Wird von `DepartmentNamePageModel`abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="523b1-129">Derives from `DepartmentNamePageModel`.</span></span>
* <span data-ttu-id="523b1-130">Verwendet `TryUpdateModelAsync`, um ein [Overposting](xref:data/ef-rp/crud#overposting) zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="523b1-130">Uses `TryUpdateModelAsync` to prevent [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="523b1-131">Ersetzt `ViewData["DepartmentID"]` durch `DepartmentNameSL` (aus der Basisklasse).</span><span class="sxs-lookup"><span data-stu-id="523b1-131">Replaces `ViewData["DepartmentID"]` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="523b1-132">`ViewData["DepartmentID"]` wird durch das stark typisierte Modell `DepartmentNameSL` ersetzt.</span><span class="sxs-lookup"><span data-stu-id="523b1-132">`ViewData["DepartmentID"]` is replaced with the strongly typed `DepartmentNameSL`.</span></span> <span data-ttu-id="523b1-133">Stark typisierte Modelle werden gegenüber schwach typisierten Modellen bevorzugt.</span><span class="sxs-lookup"><span data-stu-id="523b1-133">Strongly typed models are preferred over weakly typed.</span></span> <span data-ttu-id="523b1-134">Weitere Informationen hierzu finden Sie unter [Weakly typed data (ViewData and ViewBag) (Schwach typisierte Daten (ViewData und ViewBag))](xref:mvc/views/overview#VD_VB).</span><span class="sxs-lookup"><span data-stu-id="523b1-134">For more information, see [Weakly typed data (ViewData and ViewBag)](xref:mvc/views/overview#VD_VB).</span></span>

### <a name="update-the-courses-create-page"></a><span data-ttu-id="523b1-135">Aktualisieren der Seite „Courses Create“ (Kurse erstellen)</span><span class="sxs-lookup"><span data-stu-id="523b1-135">Update the Courses Create page</span></span>

<span data-ttu-id="523b1-136">Aktualisieren Sie *Pages/Courses/Create.cshtml* mit folgendem Markup:</span><span class="sxs-lookup"><span data-stu-id="523b1-136">Update *Pages/Courses/Create.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?highlight=29-34)]

<span data-ttu-id="523b1-137">Das oben stehende Markup führt die folgenden Änderungen durch:</span><span class="sxs-lookup"><span data-stu-id="523b1-137">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="523b1-138">Ändert die Beschriftung von **DepartmentID** in **Department**.</span><span class="sxs-lookup"><span data-stu-id="523b1-138">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="523b1-139">Ersetzt `"ViewBag.DepartmentID"` durch `DepartmentNameSL` (aus der Basisklasse).</span><span class="sxs-lookup"><span data-stu-id="523b1-139">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>
* <span data-ttu-id="523b1-140">Fügt die Option „Abteilung auswählen“ hinzu.</span><span class="sxs-lookup"><span data-stu-id="523b1-140">Adds the "Select Department" option.</span></span> <span data-ttu-id="523b1-141">Durch diese Änderung wird anstelle der ersten Abteilung die Option „Abteilung auswählen“ gerendert.</span><span class="sxs-lookup"><span data-stu-id="523b1-141">This change renders "Select Department" rather than the first department.</span></span>
* <span data-ttu-id="523b1-142">Fügt eine Validierungsmeldung hinzu, wenn die Abteilung nicht ausgewählt wird.</span><span class="sxs-lookup"><span data-stu-id="523b1-142">Adds a validation message when the department isn't selected.</span></span>

<span data-ttu-id="523b1-143">Die Razor Page verwendet die Option [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper) (Taghilfsprogramm auswählen):</span><span class="sxs-lookup"><span data-stu-id="523b1-143">The Razor Page uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper):</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

<span data-ttu-id="523b1-144">Testen Sie die Seite „Create“ (Erstellen).</span><span class="sxs-lookup"><span data-stu-id="523b1-144">Test the Create page.</span></span> <span data-ttu-id="523b1-145">Auf der Seite „Create“ (Erstellen) wird anstelle der Abteilungs-ID der Abteilungsname angezeigt.</span><span class="sxs-lookup"><span data-stu-id="523b1-145">The Create page displays the department name rather than the department ID.</span></span>

### <a name="update-the-courses-edit-page"></a><span data-ttu-id="523b1-146">Aktualisieren Sie die Seite „Courses Edit“ (Kurse bearbeiten).</span><span class="sxs-lookup"><span data-stu-id="523b1-146">Update the Courses Edit page.</span></span>

<span data-ttu-id="523b1-147">Aktualisieren Sie das Seitenmodell „Edit“ (Bearbeiten) mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="523b1-147">Update the edit page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40,47-999)]

<span data-ttu-id="523b1-148">Die Änderungen ähneln den im Seitenmodell „Create“ (Erstellen) vorgenommenen Änderungen.</span><span class="sxs-lookup"><span data-stu-id="523b1-148">The changes are similar to those made in the Create page model.</span></span> <span data-ttu-id="523b1-149">Im vorangehenden Code wird `PopulateDepartmentsDropDownList` an die Abteilungs-ID übergeben, wodurch die in der Dropdownliste angegebene Abteilung ausgewählt wird.</span><span class="sxs-lookup"><span data-stu-id="523b1-149">In the preceding code, `PopulateDepartmentsDropDownList` passes in the department ID, which select the department specified in the drop-down list.</span></span>

<span data-ttu-id="523b1-150">Aktualisieren Sie *Pages/Courses/Edit.cshtml* mit folgendem Markup:</span><span class="sxs-lookup"><span data-stu-id="523b1-150">Update *Pages/Courses/Edit.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

<span data-ttu-id="523b1-151">Das oben stehende Markup führt die folgenden Änderungen durch:</span><span class="sxs-lookup"><span data-stu-id="523b1-151">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="523b1-152">Zeigt die Kurs-ID an.</span><span class="sxs-lookup"><span data-stu-id="523b1-152">Displays the course ID.</span></span> <span data-ttu-id="523b1-153">In der Regel wird der Primärschlüssel (PS) einer Entität nicht angezeigt.</span><span class="sxs-lookup"><span data-stu-id="523b1-153">Generally the Primary Key (PK) of an entity isn't displayed.</span></span> <span data-ttu-id="523b1-154">PS sind für Benutzer normalerweise nicht von Bedeutung.</span><span class="sxs-lookup"><span data-stu-id="523b1-154">PKs are usually meaningless to users.</span></span> <span data-ttu-id="523b1-155">In diesem Fall handelt es sich bei dem PS um die Kursnummer.</span><span class="sxs-lookup"><span data-stu-id="523b1-155">In this case, the PK is the course number.</span></span>
* <span data-ttu-id="523b1-156">Ändert die Beschriftung von **DepartmentID** in **Department**.</span><span class="sxs-lookup"><span data-stu-id="523b1-156">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="523b1-157">Ersetzt `"ViewBag.DepartmentID"` durch `DepartmentNameSL` (aus der Basisklasse).</span><span class="sxs-lookup"><span data-stu-id="523b1-157">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="523b1-158">Die Seite enthält ein ausgeblendetes Feld (`<input type="hidden">`) für die Kursnummer.</span><span class="sxs-lookup"><span data-stu-id="523b1-158">The page contains a hidden field (`<input type="hidden">`) for the course number.</span></span> <span data-ttu-id="523b1-159">Das Hinzufügen eines `<label>`-Taghilfsprogramms mit `asp-for="Course.CourseID"` bewirkt nicht, dass das ausgeblendete Feld nicht mehr benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="523b1-159">Adding a `<label>` tag helper with `asp-for="Course.CourseID"` doesn't eliminate the need for the hidden field.</span></span> <span data-ttu-id="523b1-160">`<input type="hidden">` ist erforderlich, damit die Kursnummer in die bereitgestellten Daten eingeschlossen wird, wenn der Benutzer auf **Speichern** klickt.</span><span class="sxs-lookup"><span data-stu-id="523b1-160">`<input type="hidden">` is required for the course number to be included in the posted data when the user clicks **Save**.</span></span>

<span data-ttu-id="523b1-161">Testen Sie den aktualisierten Code.</span><span class="sxs-lookup"><span data-stu-id="523b1-161">Test the updated code.</span></span> <span data-ttu-id="523b1-162">Erstellen, bearbeiten und löschen Sie einen Kurs.</span><span class="sxs-lookup"><span data-stu-id="523b1-162">Create, edit, and delete a course.</span></span>

## <a name="add-asnotracking-to-the-details-and-delete-page-models"></a><span data-ttu-id="523b1-163">Hinzufügen von „AsNoTracking“ zu den Seitenmodellen „Details“ und „Delete“ (Löschen)</span><span class="sxs-lookup"><span data-stu-id="523b1-163">Add AsNoTracking to the Details and Delete page models</span></span>

<span data-ttu-id="523b1-164">Durch [AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) kann die Leistung verbessert werden, wenn keine Nachverfolgung erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="523b1-164">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) can improve performance when tracking isn't required.</span></span> <span data-ttu-id="523b1-165">Fügen Sie `AsNoTracking` zu den Seitenmodellen „Delete“ (Löschen) und „Details“ (Details) hinzu.</span><span class="sxs-lookup"><span data-stu-id="523b1-165">Add `AsNoTracking` to the Delete and Details page model.</span></span> <span data-ttu-id="523b1-166">Im folgenden Code wird das aktualisierte Seitenmodell „Delete“ (Löschen) dargestellt:</span><span class="sxs-lookup"><span data-stu-id="523b1-166">The following code shows the updated Delete page model:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Delete.cshtml.cs?name=snippet&highlight=21,23,40,41)]

<span data-ttu-id="523b1-167">Aktualisieren Sie die Methode `OnGetAsync` in der Datei *Pages/Courses/Details.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="523b1-167">Update the `OnGetAsync` method in the *Pages/Courses/Details.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Details.cshtml.cs?name=snippet)]

### <a name="modify-the-delete-and-details-pages"></a><span data-ttu-id="523b1-168">Ändern der Seiten „Delete“ (Löschen) und „Details“</span><span class="sxs-lookup"><span data-stu-id="523b1-168">Modify the Delete and Details pages</span></span>

<span data-ttu-id="523b1-169">Aktualisieren Sie die Razor Page „Delete“ (Löschen) mit folgendem Markup:</span><span class="sxs-lookup"><span data-stu-id="523b1-169">Update the Delete Razor page with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Delete.cshtml?highlight=15-20)]

<span data-ttu-id="523b1-170">Nehmen Sie auf der Seite „Details“ die gleichen Änderungen vor.</span><span class="sxs-lookup"><span data-stu-id="523b1-170">Make the same changes to the Details page.</span></span>

### <a name="test-the-course-pages"></a><span data-ttu-id="523b1-171">Testen der Kursseiten</span><span class="sxs-lookup"><span data-stu-id="523b1-171">Test the Course pages</span></span>

<span data-ttu-id="523b1-172">Testen Sie das Erstellen, Bearbeiten, Löschen und Details.</span><span class="sxs-lookup"><span data-stu-id="523b1-172">Test create, edit, details, and delete.</span></span>

## <a name="update-the-instructor-pages"></a><span data-ttu-id="523b1-173">Aktualisieren der Seiten für Dozenten</span><span class="sxs-lookup"><span data-stu-id="523b1-173">Update the instructor pages</span></span>

<span data-ttu-id="523b1-174">In den folgenden Abschnitten werden die Seiten für Dozenten aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="523b1-174">The following sections update the instructor pages.</span></span>

### <a name="add-office-location"></a><span data-ttu-id="523b1-175">Hinzufügen eines Bürostandorts</span><span class="sxs-lookup"><span data-stu-id="523b1-175">Add office location</span></span>

<span data-ttu-id="523b1-176">Bei der Bearbeitung eines Datensatzes für Dozenten sollten Sie auch die Bürozuweisung des Dozenten aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="523b1-176">When editing an instructor record, you may want to update the instructor's office assignment.</span></span> <span data-ttu-id="523b1-177">Die Entität `Instructor` verfügt über eine 1:0..1-Beziehung zur Entität `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="523b1-177">The `Instructor` entity has a one-to-zero-or-one relationship with the `OfficeAssignment` entity.</span></span> <span data-ttu-id="523b1-178">Der Code für „Instructor“ muss Folgendes verarbeiten:</span><span class="sxs-lookup"><span data-stu-id="523b1-178">The instructor code must handle:</span></span>

* <span data-ttu-id="523b1-179">Löschen Sie die Entität `OfficeAssignment`, wenn der Benutzer die Bürozuweisung aufhebt.</span><span class="sxs-lookup"><span data-stu-id="523b1-179">If the user clears the office assignment, delete the `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="523b1-180">Das Erstellen einer neuen Entität `OfficeAssignment`, wenn der Benutzer eine Bürozuweisung eingibt und diese leer war.</span><span class="sxs-lookup"><span data-stu-id="523b1-180">If the user enters an office assignment and it was empty, create a new `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="523b1-181">Aktualisieren Sie die Entität `OfficeAssignment`, wenn der Benutzer die Bürozuweisung ändert.</span><span class="sxs-lookup"><span data-stu-id="523b1-181">If the user changes the office assignment, update the `OfficeAssignment` entity.</span></span>

<span data-ttu-id="523b1-182">Aktualisieren Sie das Seitenmodell „Edit“ (Bearbeiten) für Dozenten mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="523b1-182">Update the instructors Edit page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit1.cshtml.cs?name=snippet&highlight=20-23,32,39-999)]

<span data-ttu-id="523b1-183">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="523b1-183">The preceding code:</span></span>

* <span data-ttu-id="523b1-184">Ruft die aktuelle Entität `Instructor` von der Datenbank über Eager Loading für die Navigationseigenschaft `OfficeAssignment` ab.</span><span class="sxs-lookup"><span data-stu-id="523b1-184">Gets the current `Instructor` entity from the database using eager loading for the `OfficeAssignment` navigation property.</span></span>
* <span data-ttu-id="523b1-185">Aktualisiert die abgerufene Entität `Instructor` mit Werten aus der Modellbindung.</span><span class="sxs-lookup"><span data-stu-id="523b1-185">Updates the retrieved `Instructor` entity with values from the model binder.</span></span> <span data-ttu-id="523b1-186">`TryUpdateModel` verhindert ein [Overposting](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="523b1-186">`TryUpdateModel` prevents [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="523b1-187">Wenn kein Bürostandort angegeben ist, wird `Instructor.OfficeAssignment` auf NULL festgelegt.</span><span class="sxs-lookup"><span data-stu-id="523b1-187">If the office location is blank, sets `Instructor.OfficeAssignment` to null.</span></span> <span data-ttu-id="523b1-188">Wenn `Instructor.OfficeAssignment` auf NULL festgelegt ist, wird die zugehörige Zeile in der `OfficeAssignment`-Tabelle gelöscht.</span><span class="sxs-lookup"><span data-stu-id="523b1-188">When `Instructor.OfficeAssignment` is null, the related row in the `OfficeAssignment` table is deleted.</span></span>

### <a name="update-the-instructor-edit-page"></a><span data-ttu-id="523b1-189">Aktualisieren der Dozentenseite „Edit“ (Bearbeiten)</span><span class="sxs-lookup"><span data-stu-id="523b1-189">Update the instructor Edit page</span></span>

<span data-ttu-id="523b1-190">Aktualisieren Sie *Pages/Instructors/Edit.cshtml* mit dem Bürostandort:</span><span class="sxs-lookup"><span data-stu-id="523b1-190">Update *Pages/Instructors/Edit.cshtml* with the office location:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit1.cshtml?highlight=29-33)]

<span data-ttu-id="523b1-191">Stellen Sie sicher, dass Sie den Bürostandort eines Dozenten ändern können.</span><span class="sxs-lookup"><span data-stu-id="523b1-191">Verify you can change an instructors office location.</span></span>

## <a name="add-course-assignments-to-the-instructor-edit-page"></a><span data-ttu-id="523b1-192">Hinzufügen von Kurszuweisungen zur Dozentenseite „Edit“ (Bearbeiten)</span><span class="sxs-lookup"><span data-stu-id="523b1-192">Add Course assignments to the instructor Edit page</span></span>

<span data-ttu-id="523b1-193">Dozenten können eine beliebige Anzahl von Kursen unterrichten.</span><span class="sxs-lookup"><span data-stu-id="523b1-193">Instructors may teach any number of courses.</span></span> <span data-ttu-id="523b1-194">In diesem Abschnitt fügen Sie die Möglichkeit zum Ändern von Kurszuweisungen hinzu.</span><span class="sxs-lookup"><span data-stu-id="523b1-194">In this section, you add the ability to change course assignments.</span></span> <span data-ttu-id="523b1-195">Die folgende Abbildung zeigt die aktualisierte Dozentenseite „Edit“ (Bearbeiten):</span><span class="sxs-lookup"><span data-stu-id="523b1-195">The following image shows the updated instructor Edit page:</span></span>

![Dozentenseite „Edit“ (Bearbeiten) mit Kursen](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="523b1-197">`Course` und `Instructor` weisen eine m:n-Beziehung auf.</span><span class="sxs-lookup"><span data-stu-id="523b1-197">`Course` and `Instructor` has a many-to-many relationship.</span></span> <span data-ttu-id="523b1-198">Wenn Sie Beziehungen hinzufügen und entfernen möchten, können Sie Entitäten aus der verknüpften `CourseAssignments`-Entitätenmenge hinzufügen und entfernen.</span><span class="sxs-lookup"><span data-stu-id="523b1-198">To add and remove relationships, you add and remove entities from the `CourseAssignments` join entity set.</span></span>

<span data-ttu-id="523b1-199">Über Kontrollkästchen können an Kursen vorgenommene Änderungen aktiviert werden, denen ein Dozent zugewiesen ist.</span><span class="sxs-lookup"><span data-stu-id="523b1-199">Check boxes enable changes to courses an instructor is assigned to.</span></span> <span data-ttu-id="523b1-200">Für jeden Kurs in der Datenbank wird ein Kontrollkästchen angezeigt.</span><span class="sxs-lookup"><span data-stu-id="523b1-200">A check box is displayed for every course in the database.</span></span> <span data-ttu-id="523b1-201">Kurse, denen der Dozent zugewiesen ist, sind aktiviert.</span><span class="sxs-lookup"><span data-stu-id="523b1-201">Courses that the instructor is assigned to are checked.</span></span> <span data-ttu-id="523b1-202">Der Benutzer kann Kontrollkästchen aktivieren oder deaktivieren, um Kurszuweisungen zu ändern.</span><span class="sxs-lookup"><span data-stu-id="523b1-202">The user can select or clear check boxes to change course assignments.</span></span> <span data-ttu-id="523b1-203">Wenn die Anzahl der Kurse viel größer wäre:</span><span class="sxs-lookup"><span data-stu-id="523b1-203">If the number of courses were much greater:</span></span>

* <span data-ttu-id="523b1-204">Würden Sie wahrscheinlich eine andere Benutzeroberfläche zum Anzeigen der Kurse verwenden.</span><span class="sxs-lookup"><span data-stu-id="523b1-204">You'd probably use a different user interface to display the courses.</span></span>
* <span data-ttu-id="523b1-205">Würde sich die Methode zum Bearbeiten einer verknüpften Entität zum Erstellen oder Löschen von Beziehungen nicht ändern.</span><span class="sxs-lookup"><span data-stu-id="523b1-205">The method of manipulating a join entity to create or delete relationships wouldn't change.</span></span>

### <a name="add-classes-to-support-create-and-edit-instructor-pages"></a><span data-ttu-id="523b1-206">Hinzufügen von Klassen zur Unterstützung der Dozentenseite „Create“ (Erstellen) und „Edit“ (Bearbeiten)</span><span class="sxs-lookup"><span data-stu-id="523b1-206">Add classes to support Create and Edit instructor pages</span></span>

<span data-ttu-id="523b1-207">Erstellen Sie *SchoolViewModels/AssignedCourseData.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="523b1-207">Create *SchoolViewModels/AssignedCourseData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="523b1-208">Die `AssignedCourseData`-Klasse enthält Daten zur Erstellung der Kontrollkästchen für durch einen Dozenten zugewiesene Kurse.</span><span class="sxs-lookup"><span data-stu-id="523b1-208">The `AssignedCourseData` class contains data to create the check boxes for assigned courses by an instructor.</span></span>

<span data-ttu-id="523b1-209">Erstellen Sie die Basisklasse *Pages/Instructors/InstructorCoursesPageModel.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="523b1-209">Create the *Pages/Instructors/InstructorCoursesPageModel.cshtml.cs* base class:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/InstructorCoursesPageModel.cshtml.cs)]

<span data-ttu-id="523b1-210">Bei `InstructorCoursesPageModel` handelt es sich um die Basisklasse, die Sie für die Seitenmodelle „Edit“ (Bearbeiten) und „Create“ (Erstellen) verwenden.</span><span class="sxs-lookup"><span data-stu-id="523b1-210">The `InstructorCoursesPageModel` is the base class you will use for the Edit and Create page models.</span></span> <span data-ttu-id="523b1-211">`PopulateAssignedCourseData` liest alle `Course`-Entitäten, mit denen `AssignedCourseDataList` aufgefüllt werden soll.</span><span class="sxs-lookup"><span data-stu-id="523b1-211">`PopulateAssignedCourseData` reads all `Course` entities to populate `AssignedCourseDataList`.</span></span> <span data-ttu-id="523b1-212">Für jeden Kurs legt der Code die `CourseID` und den Titel fest. Zudem legt er fest, ob der Dozent einem Kurs zugewiesen ist.</span><span class="sxs-lookup"><span data-stu-id="523b1-212">For each course, the code sets the `CourseID`, title, and whether or not the instructor is assigned to the course.</span></span> <span data-ttu-id="523b1-213">Mit einem [HashSet](/dotnet/api/system.collections.generic.hashset-1) werden effiziente Suchvorgänge erstellt.</span><span class="sxs-lookup"><span data-stu-id="523b1-213">A [HashSet](/dotnet/api/system.collections.generic.hashset-1) is used to create efficient lookups.</span></span>

### <a name="instructors-edit-page-model"></a><span data-ttu-id="523b1-214">Seitenmodell „Edit“ (Bearbeiten) für Dozenten</span><span class="sxs-lookup"><span data-stu-id="523b1-214">Instructors Edit page model</span></span>

<span data-ttu-id="523b1-215">Aktualisieren Sie das Seitenmodell „Edit“ (Bearbeiten) für Dozenten mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="523b1-215">Update the instructor Edit page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit.cshtml.cs?name=snippet&highlight=1,20-24,30,34,41-999)]

<span data-ttu-id="523b1-216">Der vorangehende Code behandelt an Bürozuweisungen vorgenommene Änderungen.</span><span class="sxs-lookup"><span data-stu-id="523b1-216">The preceding code handles office assignment changes.</span></span>

<span data-ttu-id="523b1-217">Aktualisieren Sie die Razor-Ansicht des Dozenten:</span><span class="sxs-lookup"><span data-stu-id="523b1-217">Update the instructor Razor View:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit.cshtml?highlight=34-59)]

<a id="notepad"></a>
> [!NOTE]
> <span data-ttu-id="523b1-218">Wenn Sie den Code in Visual Studio einfügen, werden Zeilenumbrüche so geändert, dass der Code unterbrochen wird.</span><span class="sxs-lookup"><span data-stu-id="523b1-218">When you paste the code in Visual Studio, line breaks are changed in a way that breaks the code.</span></span> <span data-ttu-id="523b1-219">Drücken Sie einmal Strg+Z, um die automatische Formatierung rückgängig zu machen.</span><span class="sxs-lookup"><span data-stu-id="523b1-219">Press Ctrl+Z one time to undo the automatic formatting.</span></span> <span data-ttu-id="523b1-220">Mit Strg+Z werden die Zeilenumbrüche korrigiert, damit sie dem entsprechen, was Sie hier sehen.</span><span class="sxs-lookup"><span data-stu-id="523b1-220">Ctrl+Z fixes the line breaks so that they look like what you see here.</span></span> <span data-ttu-id="523b1-221">Der Einzug muss nicht perfekt sein, die Zeilen `@</tr><tr>`, `@:<td>`, `@:</td>` und `@:</tr>` müssen jedoch, wie dargestellt, jeweils in einer einzelnen Zeile stehen.</span><span class="sxs-lookup"><span data-stu-id="523b1-221">The indentation doesn't have to be perfect, but the `@</tr><tr>`, `@:<td>`, `@:</td>`, and `@:</tr>` lines must each be on a single line as shown.</span></span> <span data-ttu-id="523b1-222">Drücken Sie, nachdem Sie den Block mit dem neuen Code ausgewählt haben, dreimal auf die TAB-Taste, um den neuen Code am vorhandenen Code auszurichten.</span><span class="sxs-lookup"><span data-stu-id="523b1-222">With the block of new code selected, press Tab three times to line up the new code with the existing code.</span></span> <span data-ttu-id="523b1-223">Stimmen Sie über [folgenden Link](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html) über den Status dieses Fehlers ab, oder überprüfen Sie diesen.</span><span class="sxs-lookup"><span data-stu-id="523b1-223">Vote on or review the status of this bug [with this link](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).</span></span>

<span data-ttu-id="523b1-224">Der vorangehende Code erstellt eine HTML-Tabelle mit drei Spalten.</span><span class="sxs-lookup"><span data-stu-id="523b1-224">The preceding code creates an HTML table that has three columns.</span></span> <span data-ttu-id="523b1-225">Jede Spalte verfügt über ein Kontrollkästchen und eine Beschriftung mit der Nummer und dem Titel des Kurses.</span><span class="sxs-lookup"><span data-stu-id="523b1-225">Each column has a check box and a caption containing the course number and title.</span></span> <span data-ttu-id="523b1-226">Die Kontrollkästchen haben alle den gleichen Namen („selectedCourses“).</span><span class="sxs-lookup"><span data-stu-id="523b1-226">The check boxes all have the same name ("selectedCourses").</span></span> <span data-ttu-id="523b1-227">Durch die Verwendung des gleichen Namens weiß die Modellbindung, dass diese Kontrollkästchen wie eine Gruppe behandelt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="523b1-227">Using the same name informs the model binder to treat them as a group.</span></span> <span data-ttu-id="523b1-228">Das Wertattribut der einzelnen Kontrollkästchen ist auf `CourseID` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="523b1-228">The value attribute of each check box is set to `CourseID`.</span></span> <span data-ttu-id="523b1-229">Wenn die Seite zurückgesendet wird, übergibt die Modellbindung ein Array, das lediglich die `CourseID`-Werte der aktivierten Kontrollkästchen enthält.</span><span class="sxs-lookup"><span data-stu-id="523b1-229">When the page is posted, the model binder passes an array that consists of the `CourseID` values for only the check boxes that are selected.</span></span>

<span data-ttu-id="523b1-230">Wenn die Kontrollkästchen ursprünglich gerendert wurden, weisen dem Dozenten zugewiesene Kurse überprüfte Attribute auf.</span><span class="sxs-lookup"><span data-stu-id="523b1-230">When the check boxes are initially rendered, courses assigned to the instructor have checked attributes.</span></span>

<span data-ttu-id="523b1-231">Führen Sie die App aus, und testen Sie die aktualisierte Dozentenseite „Edit“ (Bearbeiten).</span><span class="sxs-lookup"><span data-stu-id="523b1-231">Run the app and test the updated instructors Edit page.</span></span> <span data-ttu-id="523b1-232">Ändern Sie einige Kurszuweisungen.</span><span class="sxs-lookup"><span data-stu-id="523b1-232">Change some course assignments.</span></span> <span data-ttu-id="523b1-233">Die Änderungen werden auf der Seite „Index“ widergespiegelt.</span><span class="sxs-lookup"><span data-stu-id="523b1-233">The changes are reflected on the Index page.</span></span>

<span data-ttu-id="523b1-234">Hinweis: Der hier gewählte Ansatz für die Bearbeitung der Kursdaten von Dozenten wird empfohlen, wenn eine begrenzte Anzahl von Kursen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="523b1-234">Note: The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="523b1-235">Bei umfangreicheren Sammlungen wären eine andere Benutzeroberfläche und eine andere Aktualisierungsmethode nützlicher und effizienter.</span><span class="sxs-lookup"><span data-stu-id="523b1-235">For collections that are much larger, a different UI and a different updating method would be more useable and efficient.</span></span>

### <a name="update-the-instructors-create-page"></a><span data-ttu-id="523b1-236">Aktualisieren der Dozentenseite „Create“ (Erstellen)</span><span class="sxs-lookup"><span data-stu-id="523b1-236">Update the instructors Create page</span></span>

<span data-ttu-id="523b1-237">Aktualisieren Sie das Seitenmodell „Create“ (Erstellen) für Dozenten mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="523b1-237">Update the instructor Create page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Create.cshtml.cs)]

<span data-ttu-id="523b1-238">Der vorangehende Code ähnelt dem Code *Pages/Instructors/Edit.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="523b1-238">The preceding code is similar to the *Pages/Instructors/Edit.cshtml.cs* code.</span></span>

<span data-ttu-id="523b1-239">Aktualisieren Sie die Razor Page „Create“ (Erstellen) für Dozenten mit folgendem Markup:</span><span class="sxs-lookup"><span data-stu-id="523b1-239">Update the instructor Create Razor page with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Create.cshtml?highlight=32-62)]

<span data-ttu-id="523b1-240">Testen Sie die Dozentenseite „Create“ (Erstellen).</span><span class="sxs-lookup"><span data-stu-id="523b1-240">Test the instructor Create page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="523b1-241">Aktualisieren der Seite „Delete“ (Löschen)</span><span class="sxs-lookup"><span data-stu-id="523b1-241">Update the Delete page</span></span>

<span data-ttu-id="523b1-242">Aktualisieren Sie das Seitenmodell „Delete“ (Löschen) mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="523b1-242">Update the Delete page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Delete.cshtml.cs?highlight=5,40-999)]

<span data-ttu-id="523b1-243">Durch den vorangehenden Code werden folgende Änderungen vorgenommen:</span><span class="sxs-lookup"><span data-stu-id="523b1-243">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="523b1-244">Verwendet Eager Loading für die Navigationseigenschaft `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="523b1-244">Uses eager loading for the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="523b1-245">`CourseAssignments` müssen eingeschlossen werden. Andernfalls werden diese nicht gelöscht, wenn der Dozent gelöscht wird.</span><span class="sxs-lookup"><span data-stu-id="523b1-245">`CourseAssignments` must be included or they aren't deleted when the instructor is deleted.</span></span> <span data-ttu-id="523b1-246">Wenn Sie vermeiden möchten, diese lesen zu müssen, konfigurieren Sie in der Datenbank eine Löschweitergabe.</span><span class="sxs-lookup"><span data-stu-id="523b1-246">To avoid needing to read them, configure cascade delete in the database.</span></span>

* <span data-ttu-id="523b1-247">Wenn der zu löschende Dozent als Administrator einer beliebigen Abteilung zugewiesen ist, wird die Dozentenzuweisung aus diesen Abteilungen entfernt.</span><span class="sxs-lookup"><span data-stu-id="523b1-247">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="523b1-248">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="523b1-248">Additional resources</span></span>

* [<span data-ttu-id="523b1-249">Dieses Tutorial auf YouTube (Teil 1)</span><span class="sxs-lookup"><span data-stu-id="523b1-249">YouTube version of this tutorial (Part 1)</span></span>](https://www.youtube.com/watch?v=Csh6gkmwc9E)
* [<span data-ttu-id="523b1-250">Dieses Tutorial auf YouTube (Teil 2)</span><span class="sxs-lookup"><span data-stu-id="523b1-250">YouTube version of this tutorial (Part 2)</span></span>](https://www.youtube.com/watch?v=mOAankB_Zgc)

> [!div class="step-by-step"]
> <span data-ttu-id="523b1-251">[Zurück](xref:data/ef-rp/read-related-data)
> [Weiter](xref:data/ef-rp/concurrency)</span><span class="sxs-lookup"><span data-stu-id="523b1-251">[Previous](xref:data/ef-rp/read-related-data)
[Next](xref:data/ef-rp/concurrency)</span></span>

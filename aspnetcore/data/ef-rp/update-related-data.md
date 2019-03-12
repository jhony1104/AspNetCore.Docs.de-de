---
title: 'Razor-Seiten mit EF Core in ASP.NET Core: Aktualisieren verwandter Daten (7 von 8)'
author: rick-anderson
description: Mithilfe dieses Tutorials führen Sie Updates für verwandte Daten aus, indem Sie Felder mit Fremdschlüsseln sowie Navigationseigenschaften aktualisieren.
ms.author: riande
ms.date: 11/15/2017
uid: data/ef-rp/update-related-data
ms.openlocfilehash: c3dbfe57a832ba63c5da2f89c0d29e443a1277a7
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57345761"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---update-related-data---7-of-8"></a>Razor-Seiten mit EF Core in ASP.NET Core: Aktualisieren verwandter Daten (7 von 8)

Von [Tom Dykstra](https://github.com/tdykstra) und [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

In diesem Tutorial wird veranschaulicht, wie verwandte Daten aktualisiert werden. Wenn nicht zu lösende Probleme auftreten, laden Sie die [fertige App](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) herunter, oder zeigen Sie diese an. [Anweisungen zum Download.](xref:index#how-to-download-a-sample)

In den folgenden Abbildungen werden einige der abgeschlossenen Seiten dargestellt.

![Bearbeitungsseite „Course“ (Kurs)](update-related-data/_static/course-edit.png)
![Bearbeitungsseite „Instructor“ (Dozent)](update-related-data/_static/instructor-edit-courses.png)

Überprüfen und testen Sie die Seiten „Create Course“ (Kurs erstellen) und „Edit Course“ (Kurs bearbeiten). Erstellen Sie einen neuen Kurs. Die Abteilung wird nach dem zugehörigen Primärschlüssel (eine ganze Zahl) ausgewählt, nicht nach dem Namen. Bearbeiten Sie den neuen Kurs. Wenn Sie die Tests ausgeführt haben, können Sie den neuen Kurs löschen.

## <a name="create-a-base-class-to-share-common-code"></a>Erstellen einer Basisklasse für die gemeinsame Nutzung von allgemeinem Code

Auf den Seiten „Courses/Create“ (Kurse/Erstellen) und „Courses/Edit“ (Kurse/Bearbeiten) ist jeweils eine Liste mit Abteilungsnamen erforderlich. Erstellen Sie die Basisklasse *Pages/Courses/DepartmentNamePageModel.cshtml.cs* für die Seiten „Create“ (Erstellen) und „Edit“ (Bearbeiten):

[!code-csharp[](intro/samples/cu/Pages/Courses/DepartmentNamePageModel.cshtml.cs?highlight=9,11,20-21)]

Der vorangehende Code erstellt eine [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0), in der die Liste der Abteilungsnamen enthalten sein soll. Wenn `selectedDepartment` angegeben ist, wird diese Abteilung in `SelectList` ausgewählt.

Die Modellklassen der Seiten „Create“ (Erstellen) und „Edit“ (Bearbeiten) werden von `DepartmentNamePageModel` abgeleitet.

## <a name="customize-the-courses-pages"></a>Anpassen der Kursseiten

Wenn eine neue Kursentität erstellt wird, muss diese in Beziehung zu einer vorhandenen Abteilung stehen. Wenn Sie bei der Erstellung eines Kurses eine Abteilung hinzufügen möchten, können Sie aus einer Dropdownliste in der Basisklasse für „Erstellen“ und „Bearbeiten“ die Abteilung auswählen. In der Dropdownliste wird die Fremdschlüsseleigenschaft `Course.DepartmentID` festgelegt. EF Core verwendet den Fremdschlüssel `Course.DepartmentID` zum Laden der Navigationseigenschaft `Department`.

![Erstellen eines Kurses](update-related-data/_static/ddl.png)

Aktualisieren Sie das Seitenmodell „Create“ (Erstellen) mit dem folgenden Code:

[!code-csharp[](intro/samples/cu/Pages/Courses/Create.cshtml.cs?highlight=7,18,32-999)]

Der vorangehende Code:

* Wird von `DepartmentNamePageModel`abgeleitet.
* Verwendet `TryUpdateModelAsync`, um ein [Overposting](xref:data/ef-rp/crud#overposting) zu verhindern.
* Ersetzt `ViewData["DepartmentID"]` durch `DepartmentNameSL` (aus der Basisklasse).

`ViewData["DepartmentID"]` wird durch das stark typisierte Modell `DepartmentNameSL` ersetzt. Stark typisierte Modelle werden gegenüber schwach typisierten Modellen bevorzugt. Weitere Informationen hierzu finden Sie unter [Weakly typed data (ViewData and ViewBag) (Schwach typisierte Daten (ViewData und ViewBag))](xref:mvc/views/overview#VD_VB).

### <a name="update-the-courses-create-page"></a>Aktualisieren der Seite „Courses Create“ (Kurse erstellen)

Aktualisieren Sie *Pages/Courses/Create.cshtml* mit folgendem Markup:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?highlight=29-34)]

Das oben stehende Markup führt die folgenden Änderungen durch:

* Ändert die Beschriftung von **DepartmentID** in **Department**.
* Ersetzt `"ViewBag.DepartmentID"` durch `DepartmentNameSL` (aus der Basisklasse).
* Fügt die Option „Abteilung auswählen“ hinzu. Durch diese Änderung wird anstelle der ersten Abteilung die Option „Abteilung auswählen“ gerendert.
* Fügt eine Validierungsmeldung hinzu, wenn die Abteilung nicht ausgewählt wird.

Die Razor Page verwendet die Option [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper) (Taghilfsprogramm auswählen):

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

Testen Sie die Seite „Create“ (Erstellen). Auf der Seite „Create“ (Erstellen) wird anstelle der Abteilungs-ID der Abteilungsname angezeigt.

### <a name="update-the-courses-edit-page"></a>Aktualisieren Sie die Seite „Courses Edit“ (Kurse bearbeiten).

Aktualisieren Sie das Seitenmodell „Edit“ (Bearbeiten) mit dem folgenden Code:

[!code-csharp[](intro/samples/cu/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40,47-999)]

Die Änderungen ähneln den im Seitenmodell „Create“ (Erstellen) vorgenommenen Änderungen. Im vorangehenden Code wird `PopulateDepartmentsDropDownList` an die Abteilungs-ID übergeben, wodurch die in der Dropdownliste angegebene Abteilung ausgewählt wird.

Aktualisieren Sie *Pages/Courses/Edit.cshtml* mit folgendem Markup:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

Das oben stehende Markup führt die folgenden Änderungen durch:

* Zeigt die Kurs-ID an. In der Regel wird der Primärschlüssel (PS) einer Entität nicht angezeigt. PS sind für Benutzer normalerweise nicht von Bedeutung. In diesem Fall handelt es sich bei dem PS um die Kursnummer.
* Ändert die Beschriftung von **DepartmentID** in **Department**.
* Ersetzt `"ViewBag.DepartmentID"` durch `DepartmentNameSL` (aus der Basisklasse).

Die Seite enthält ein ausgeblendetes Feld (`<input type="hidden">`) für die Kursnummer. Das Hinzufügen eines `<label>`-Taghilfsprogramms mit `asp-for="Course.CourseID"` bewirkt nicht, dass das ausgeblendete Feld nicht mehr benötigt wird. `<input type="hidden">` ist erforderlich, damit die Kursnummer in die bereitgestellten Daten eingeschlossen wird, wenn der Benutzer auf **Speichern** klickt.

Testen Sie den aktualisierten Code. Erstellen, bearbeiten und löschen Sie einen Kurs.

## <a name="add-asnotracking-to-the-details-and-delete-page-models"></a>Hinzufügen von „AsNoTracking“ zu den Seitenmodellen „Details“ und „Delete“ (Löschen)

Durch [AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) kann die Leistung verbessert werden, wenn keine Nachverfolgung erforderlich ist. Fügen Sie `AsNoTracking` zu den Seitenmodellen „Delete“ (Löschen) und „Details“ (Details) hinzu. Im folgenden Code wird das aktualisierte Seitenmodell „Delete“ (Löschen) dargestellt:

[!code-csharp[](intro/samples/cu/Pages/Courses/Delete.cshtml.cs?name=snippet&highlight=21,23,40,41)]

Aktualisieren Sie die Methode `OnGetAsync` in der Datei *Pages/Courses/Details.cshtml.cs*:

[!code-csharp[](intro/samples/cu/Pages/Courses/Details.cshtml.cs?name=snippet)]

### <a name="modify-the-delete-and-details-pages"></a>Ändern der Seiten „Delete“ (Löschen) und „Details“

Aktualisieren Sie die Razor Page „Delete“ (Löschen) mit folgendem Markup:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Delete.cshtml?highlight=15-20)]

Nehmen Sie auf der Seite „Details“ die gleichen Änderungen vor.

### <a name="test-the-course-pages"></a>Testen der Kursseiten

Testen Sie das Erstellen, Bearbeiten, Löschen und Details.

## <a name="update-the-instructor-pages"></a>Aktualisieren der Seiten für Dozenten

In den folgenden Abschnitten werden die Seiten für Dozenten aktualisiert.

### <a name="add-office-location"></a>Hinzufügen eines Bürostandorts

Bei der Bearbeitung eines Datensatzes für Dozenten sollten Sie auch die Bürozuweisung des Dozenten aktualisieren. Die Entität `Instructor` verfügt über eine 1:0..1-Beziehung zur Entität `OfficeAssignment`. Der Code für „Instructor“ muss Folgendes verarbeiten:

* Löschen Sie die Entität `OfficeAssignment`, wenn der Benutzer die Bürozuweisung aufhebt.
* Das Erstellen einer neuen Entität `OfficeAssignment`, wenn der Benutzer eine Bürozuweisung eingibt und diese leer war.
* Aktualisieren Sie die Entität `OfficeAssignment`, wenn der Benutzer die Bürozuweisung ändert.

Aktualisieren Sie das Seitenmodell „Edit“ (Bearbeiten) für Dozenten mit dem folgenden Code:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit1.cshtml.cs?name=snippet&highlight=20-23,32,39-999)]

Der vorangehende Code:

- Ruft die aktuelle Entität `Instructor` von der Datenbank über Eager Loading für die Navigationseigenschaft `OfficeAssignment` ab.
- Aktualisiert die abgerufene Entität `Instructor` mit Werten aus der Modellbindung. `TryUpdateModel` verhindert ein [Overposting](xref:data/ef-rp/crud#overposting).
- Wenn kein Bürostandort angegeben ist, wird `Instructor.OfficeAssignment` auf NULL festgelegt. Wenn `Instructor.OfficeAssignment` auf NULL festgelegt ist, wird die zugehörige Zeile in der `OfficeAssignment`-Tabelle gelöscht.

### <a name="update-the-instructor-edit-page"></a>Aktualisieren der Dozentenseite „Edit“ (Bearbeiten)

Aktualisieren Sie *Pages/Instructors/Edit.cshtml* mit dem Bürostandort:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit1.cshtml?highlight=29-33)]

Stellen Sie sicher, dass Sie den Bürostandort eines Dozenten ändern können.

## <a name="add-course-assignments-to-the-instructor-edit-page"></a>Hinzufügen von Kurszuweisungen zur Dozentenseite „Edit“ (Bearbeiten)

Dozenten können eine beliebige Anzahl von Kursen unterrichten. In diesem Abschnitt fügen Sie die Möglichkeit zum Ändern von Kurszuweisungen hinzu. Die folgende Abbildung zeigt die aktualisierte Dozentenseite „Edit“ (Bearbeiten):

![Dozentenseite „Edit“ (Bearbeiten) mit Kursen](update-related-data/_static/instructor-edit-courses.png)

`Course` und `Instructor` weisen eine m:n-Beziehung auf. Wenn Sie Beziehungen hinzufügen und entfernen möchten, können Sie Entitäten aus der verknüpften `CourseAssignments`-Entitätenmenge hinzufügen und entfernen.

Über Kontrollkästchen können an Kursen vorgenommene Änderungen aktiviert werden, denen ein Dozent zugewiesen ist. Für jeden Kurs in der Datenbank wird ein Kontrollkästchen angezeigt. Kurse, denen der Dozent zugewiesen ist, sind aktiviert. Der Benutzer kann Kontrollkästchen aktivieren oder deaktivieren, um Kurszuweisungen zu ändern. Wenn die Anzahl der Kurse viel größer wäre:

* Würden Sie wahrscheinlich eine andere Benutzeroberfläche zum Anzeigen der Kurse verwenden.
* Würde sich die Methode zum Bearbeiten einer verknüpften Entität zum Erstellen oder Löschen von Beziehungen nicht ändern.

### <a name="add-classes-to-support-create-and-edit-instructor-pages"></a>Hinzufügen von Klassen zur Unterstützung der Dozentenseite „Create“ (Erstellen) und „Edit“ (Bearbeiten)

Erstellen Sie *SchoolViewModels/AssignedCourseData.cs* mit folgendem Code:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

Die `AssignedCourseData`-Klasse enthält Daten zur Erstellung der Kontrollkästchen für durch einen Dozenten zugewiesene Kurse.

Erstellen Sie die Basisklasse *Pages/Instructors/InstructorCoursesPageModel.cshtml.cs*:

[!code-csharp[](intro/samples/cu/Pages/Instructors/InstructorCoursesPageModel.cshtml.cs)]

Bei `InstructorCoursesPageModel` handelt es sich um die Basisklasse, die Sie für die Seitenmodelle „Edit“ (Bearbeiten) und „Create“ (Erstellen) verwenden. `PopulateAssignedCourseData` liest alle `Course`-Entitäten, mit denen `AssignedCourseDataList` aufgefüllt werden soll. Für jeden Kurs legt der Code die `CourseID` und den Titel fest. Zudem legt er fest, ob der Dozent einem Kurs zugewiesen ist. Mit einem [HashSet](/dotnet/api/system.collections.generic.hashset-1) werden effiziente Suchvorgänge erstellt.

### <a name="instructors-edit-page-model"></a>Seitenmodell „Edit“ (Bearbeiten) für Dozenten

Aktualisieren Sie das Seitenmodell „Edit“ (Bearbeiten) für Dozenten mit dem folgenden Code:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit.cshtml.cs?name=snippet&highlight=1,20-24,30,34,41-999)]

Der vorangehende Code behandelt an Bürozuweisungen vorgenommene Änderungen.

Aktualisieren Sie die Razor-Ansicht des Dozenten:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit.cshtml?highlight=34-59)]

<a id="notepad"></a>
> [!NOTE]
> Wenn Sie den Code in Visual Studio einfügen, werden Zeilenumbrüche so geändert, dass der Code unterbrochen wird. Drücken Sie einmal Strg+Z, um die automatische Formatierung rückgängig zu machen. Mit Strg+Z werden die Zeilenumbrüche korrigiert, damit sie dem entsprechen, was Sie hier sehen. Der Einzug muss nicht perfekt sein, die Zeilen `@</tr><tr>`, `@:<td>`, `@:</td>` und `@:</tr>` müssen jedoch, wie dargestellt, jeweils in einer einzelnen Zeile stehen. Drücken Sie, nachdem Sie den Block mit dem neuen Code ausgewählt haben, dreimal auf die TAB-Taste, um den neuen Code am vorhandenen Code auszurichten. Stimmen Sie über [folgenden Link](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html) über den Status dieses Fehlers ab, oder überprüfen Sie diesen.

Der vorangehende Code erstellt eine HTML-Tabelle mit drei Spalten. Jede Spalte verfügt über ein Kontrollkästchen und eine Beschriftung mit der Nummer und dem Titel des Kurses. Die Kontrollkästchen haben alle den gleichen Namen („selectedCourses“). Durch die Verwendung des gleichen Namens weiß die Modellbindung, dass diese Kontrollkästchen wie eine Gruppe behandelt werden sollen. Das Wertattribut der einzelnen Kontrollkästchen ist auf `CourseID` festgelegt. Wenn die Seite zurückgesendet wird, übergibt die Modellbindung ein Array, das lediglich die `CourseID`-Werte der aktivierten Kontrollkästchen enthält.

Wenn die Kontrollkästchen ursprünglich gerendert wurden, weisen dem Dozenten zugewiesene Kurse überprüfte Attribute auf.

Führen Sie die App aus, und testen Sie die aktualisierte Dozentenseite „Edit“ (Bearbeiten). Ändern Sie einige Kurszuweisungen. Die Änderungen werden auf der Seite „Index“ widergespiegelt.

Hinweis: Der hier gewählte Ansatz für die Bearbeitung der Kursdaten von Dozenten wird empfohlen, wenn eine begrenzte Anzahl von Kursen verwendet wird. Bei umfangreicheren Sammlungen wären eine andere Benutzeroberfläche und eine andere Aktualisierungsmethode nützlicher und effizienter.

### <a name="update-the-instructors-create-page"></a>Aktualisieren der Dozentenseite „Create“ (Erstellen)

Aktualisieren Sie das Seitenmodell „Create“ (Erstellen) für Dozenten mit dem folgenden Code:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Create.cshtml.cs)]

Der vorangehende Code ähnelt dem Code *Pages/Instructors/Edit.cshtml.cs*.

Aktualisieren Sie die Razor Page „Create“ (Erstellen) für Dozenten mit folgendem Markup:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Create.cshtml?highlight=32-62)]

Testen Sie die Dozentenseite „Create“ (Erstellen).

## <a name="update-the-delete-page"></a>Aktualisieren der Seite „Delete“ (Löschen)

Aktualisieren Sie das Seitenmodell „Delete“ (Löschen) mit dem folgenden Code:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Delete.cshtml.cs?highlight=5,40-999)]

Durch den vorangehenden Code werden folgende Änderungen vorgenommen:

* Verwendet Eager Loading für die Navigationseigenschaft `CourseAssignments`. `CourseAssignments` müssen eingeschlossen werden. Andernfalls werden diese nicht gelöscht, wenn der Dozent gelöscht wird. Wenn Sie vermeiden möchten, diese lesen zu müssen, konfigurieren Sie in der Datenbank eine Löschweitergabe.

* Wenn der zu löschende Dozent als Administrator einer beliebigen Abteilung zugewiesen ist, wird die Dozentenzuweisung aus diesen Abteilungen entfernt.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Dieses Tutorial auf YouTube (Teil 1)](https://www.youtube.com/watch?v=Csh6gkmwc9E)
* [Dieses Tutorial auf YouTube (Teil 2)](https://www.youtube.com/watch?v=mOAankB_Zgc)

> [!div class="step-by-step"]
> [Zurück](xref:data/ef-rp/read-related-data)
> [Weiter](xref:data/ef-rp/concurrency)

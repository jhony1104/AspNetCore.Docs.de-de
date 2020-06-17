---
title: 'Teil 6: Razor Pages mit EF Core in ASP.NET Core – Lesen relevanter Daten'
author: rick-anderson
description: Dies ist Teil 6 der Tutorialreihe zu Razor Pages und dem Entity Framework.
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-rp/read-related-data
ms.openlocfilehash: e67738015f64ca7077c2f87a8f7eabe722aac9d8
ms.sourcegitcommit: fa67462abdf0cc4051977d40605183c629db7c64
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/10/2020
ms.locfileid: "84652612"
---
# <a name="part-6-razor-pages-with-ef-core-in-aspnet-core---read-related-data"></a><span data-ttu-id="77077-103">Teil 6: Razor Pages mit EF Core in ASP.NET Core – Lesen relevanter Daten</span><span class="sxs-lookup"><span data-stu-id="77077-103">Part 6, Razor Pages with EF Core in ASP.NET Core - Read Related Data</span></span>

<span data-ttu-id="77077-104">Von [Tom Dykstra](https://github.com/tdykstra), [Jon P. Smith](https://twitter.com/thereformedprog) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="77077-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="77077-105">Dieses Tutorial zeigt, wie verwandte Daten gelesen und angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="77077-105">This tutorial shows how to read and display related data.</span></span> <span data-ttu-id="77077-106">Verwandte Daten sind Daten, die von EF Core in die Navigationseigenschaften geladen werden.</span><span class="sxs-lookup"><span data-stu-id="77077-106">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="77077-107">Die folgenden Abbildungen zeigen die abgeschlossenen Seiten für dieses Tutorial:</span><span class="sxs-lookup"><span data-stu-id="77077-107">The following illustrations show the completed pages for this tutorial:</span></span>

![Indexseite „Kurse“](read-related-data/_static/courses-index30.png)

![Indexseite „Dozenten“](read-related-data/_static/instructors-index30.png)

## <a name="eager-explicit-and-lazy-loading"></a><span data-ttu-id="77077-110">Explizites Laden, Eager Loading und Lazy Loading</span><span class="sxs-lookup"><span data-stu-id="77077-110">Eager, explicit, and lazy loading</span></span>

<span data-ttu-id="77077-111">Es gibt mehrere Möglichkeiten, mit denen EF Core verwandte Daten in die Navigationseigenschaften einer Entität laden kann:</span><span class="sxs-lookup"><span data-stu-id="77077-111">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="77077-112">[Eager Loading (vorzeitiges Laden)](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="77077-112">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="77077-113">Beim Eager Loading lädt eine Abfrage für einen Entitätstyp auch verwandte Entitäten.</span><span class="sxs-lookup"><span data-stu-id="77077-113">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="77077-114">Wenn eine Entität gelesen wird, werden ihre zugehörigen Daten abgerufen.</span><span class="sxs-lookup"><span data-stu-id="77077-114">When an entity is read, its related data is retrieved.</span></span> <span data-ttu-id="77077-115">Dies führt normalerweise zu einer einzelnen Joinabfrage, die alle Daten abruft, die erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="77077-115">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="77077-116">EF Core wird mehrere Abfragen für einige Typen von Eager Loading ausgeben.</span><span class="sxs-lookup"><span data-stu-id="77077-116">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="77077-117">Das Ausgeben mehrerer Abfragen kann effizienter sein als eine einzelne große Abfrage.</span><span class="sxs-lookup"><span data-stu-id="77077-117">Issuing multiple queries can be more efficient than a giant single query.</span></span> <span data-ttu-id="77077-118">Eager Loading wird mit den `Include`- und `ThenInclude`-Methoden angegeben.</span><span class="sxs-lookup"><span data-stu-id="77077-118">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Beispiel für Eager Loading](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="77077-120">Eager Loading sendet mehrere Abfragen, wenn eine Sammlungsnavigation enthalten ist:</span><span class="sxs-lookup"><span data-stu-id="77077-120">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="77077-121">Eine Abfrage für die Hauptabfrage</span><span class="sxs-lookup"><span data-stu-id="77077-121">One query for the main query</span></span> 
  * <span data-ttu-id="77077-122">Eine Abfrage für jeden „Sammlungsrand“ in der Ladestruktur.</span><span class="sxs-lookup"><span data-stu-id="77077-122">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="77077-123">Beispiel für separate Abfragen mit `Load`: Die Daten können in separaten Abfragen abgerufen werden. EF Core korrigiert die Navigationseigenschaften.</span><span class="sxs-lookup"><span data-stu-id="77077-123">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="77077-124">„Korrigieren“ bedeutet, dass EF Core die Navigationseigenschaften automatisch mit Daten auffüllt.</span><span class="sxs-lookup"><span data-stu-id="77077-124">"Fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="77077-125">Separate Abfragen mit `Load` ähneln mehr dem expliziten Laden als dem Eager Loading.</span><span class="sxs-lookup"><span data-stu-id="77077-125">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![Beispiel für separate Abfragen](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="77077-127">Hinweis: EF Core korrigiert automatisch Navigationseigenschaften für alle anderen Entitäten, die zuvor in die Kontextinstanz geladen wurden.</span><span class="sxs-lookup"><span data-stu-id="77077-127">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="77077-128">Auch wenn die Daten für eine Navigationseigenschaft *nicht* explizit eingeschlossen sind, kann die Eigenschaft immer noch aufgefüllt werden, wenn einige oder alle verwandten Entitäten zuvor geladen wurden.</span><span class="sxs-lookup"><span data-stu-id="77077-128">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="77077-129">[Explizites Laden](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="77077-129">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="77077-130">Wenn die Entität zuerst gelesen wird, werden verwandte Daten nicht abgerufen.</span><span class="sxs-lookup"><span data-stu-id="77077-130">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="77077-131">Es muss Code geschrieben werden, um die verwandten Daten bei Bedarf abzurufen.</span><span class="sxs-lookup"><span data-stu-id="77077-131">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="77077-132">Explizites Laden mit separaten Abfragen führt zu mehreren Abfragen, die an die Datenbank gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="77077-132">Explicit loading with separate queries results in multiple queries sent to the database.</span></span> <span data-ttu-id="77077-133">Mit explizitem Laden gibt der Code die zu ladenden Navigationseigenschaften an.</span><span class="sxs-lookup"><span data-stu-id="77077-133">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="77077-134">Verwenden Sie für explizites Laden die `Load`-Methode.</span><span class="sxs-lookup"><span data-stu-id="77077-134">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="77077-135">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="77077-135">For example:</span></span>

  ![Beispiel für explizites Laden](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="77077-137">[Lazy Loading (verzögertes Laden)](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="77077-137">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="77077-138">[Lazy Loading wurde in Version 2.1 zu EF Core hinzugefügt](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="77077-138">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="77077-139">Wenn die Entität zuerst gelesen wird, werden verwandte Daten nicht abgerufen.</span><span class="sxs-lookup"><span data-stu-id="77077-139">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="77077-140">Wenn zum ersten Mal auf eine Navigationseigenschaft zugegriffen wird, werden die für diese Navigationseigenschaft erforderlichen Daten automatisch abgerufen.</span><span class="sxs-lookup"><span data-stu-id="77077-140">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="77077-141">Wenn zum ersten Mal auf eine Navigationseigenschaft zugegriffen wird, wird jedes Mal eine Abfrage an die Datenbank gesendet.</span><span class="sxs-lookup"><span data-stu-id="77077-141">A query is sent to the database each time a navigation property is accessed for the first time.</span></span>

## <a name="create-course-pages"></a><span data-ttu-id="77077-142">Erstellen von Course-Seiten</span><span class="sxs-lookup"><span data-stu-id="77077-142">Create Course pages</span></span>

<span data-ttu-id="77077-143">Die `Course`-Entität enthält eine Navigationseigenschaft, die die zugehörige `Department`-Entität enthält.</span><span class="sxs-lookup"><span data-stu-id="77077-143">The `Course` entity includes a navigation property that contains the related `Department` entity.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<span data-ttu-id="77077-145">So zeigen Sie den Namen des zugewiesenen Fachbereichs für einen Kurs an:</span><span class="sxs-lookup"><span data-stu-id="77077-145">To display the name of the assigned department for a course:</span></span>

* <span data-ttu-id="77077-146">Laden Sie die zugehörige `Department`-Entität in die `Course.Department`-Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="77077-146">Load the related `Department` entity into the `Course.Department` navigation property.</span></span>
* <span data-ttu-id="77077-147">Rufen Sie den Namen aus der `Name`-Eigenschaft der `Department`-Entität ab.</span><span class="sxs-lookup"><span data-stu-id="77077-147">Get the name from the `Department` entity's `Name` property.</span></span>

<a name="scaffold"></a>

### <a name="scaffold-course-pages"></a><span data-ttu-id="77077-148">Gerüstbau von Course-Seiten</span><span class="sxs-lookup"><span data-stu-id="77077-148">Scaffold Course pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="77077-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="77077-149">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="77077-150">Befolgen Sie die Anweisungen unter [Gerüstbau der Student-Seiten](xref:data/ef-rp/intro#scaffold-student-pages) mit den folgenden Ausnahmen:</span><span class="sxs-lookup"><span data-stu-id="77077-150">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="77077-151">Erstellen Sie einen Ordner *Pages/Courses*.</span><span class="sxs-lookup"><span data-stu-id="77077-151">Create a *Pages/Courses* folder.</span></span>
  * <span data-ttu-id="77077-152">Verwenden Sie `Course` als Modellklasse.</span><span class="sxs-lookup"><span data-stu-id="77077-152">Use `Course` for the model class.</span></span>
  * <span data-ttu-id="77077-153">Verwenden Sie die vorhandene Kontextklasse, anstatt eine neue Klasse zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="77077-153">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="77077-154">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="77077-154">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="77077-155">Erstellen Sie einen Ordner *Pages/Courses*.</span><span class="sxs-lookup"><span data-stu-id="77077-155">Create a *Pages/Courses* folder.</span></span>

* <span data-ttu-id="77077-156">Führen Sie den folgenden Befehl aus, um das Gerüst für Course-Seiten zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="77077-156">Run the following command to scaffold the Course pages.</span></span>

  <span data-ttu-id="77077-157">**Unter Windows:**</span><span class="sxs-lookup"><span data-stu-id="77077-157">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

  <span data-ttu-id="77077-158">**Unter Linux oder macOS:**</span><span class="sxs-lookup"><span data-stu-id="77077-158">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages/Courses --referenceScriptLibraries
  ```

---

* <span data-ttu-id="77077-159">Öffnen Sie *Pages/Courses/Index.cshtml.cs*. Untersuchen Sie die `OnGetAsync`-Methode.</span><span class="sxs-lookup"><span data-stu-id="77077-159">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="77077-160">Die Gerüstbauengine gibt Eager Loading für die `Department`-Navigationseigenschaft an.</span><span class="sxs-lookup"><span data-stu-id="77077-160">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="77077-161">Die `Include`-Methode gibt Eager Loading an.</span><span class="sxs-lookup"><span data-stu-id="77077-161">The `Include` method specifies eager loading.</span></span>

* <span data-ttu-id="77077-162">Führen Sie die Anwendung aus, und klicken Sie auf den Link **Kurse**.</span><span class="sxs-lookup"><span data-stu-id="77077-162">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="77077-163">Die Abteilungsspalte zeigt die `DepartmentID` an, die nicht hilfreich ist.</span><span class="sxs-lookup"><span data-stu-id="77077-163">The department column displays the `DepartmentID`, which isn't useful.</span></span>

### <a name="display-the-department-name"></a><span data-ttu-id="77077-164">Anzeigen des Fachbereichnamens</span><span class="sxs-lookup"><span data-stu-id="77077-164">Display the department name</span></span>

<span data-ttu-id="77077-165">Aktualisieren Sie „Pages/Courses/Index.cshtml.cs“ mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="77077-165">Update Pages/Courses/Index.cshtml.cs with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Index.cshtml.cs?highlight=18,22,24)]

<span data-ttu-id="77077-166">Der vorstehende Code ändert die `Course`-Eigenschaft in `Courses` und fügt `AsNoTracking` hinzu.</span><span class="sxs-lookup"><span data-stu-id="77077-166">The preceding code changes the `Course` property to `Courses` and adds `AsNoTracking`.</span></span> <span data-ttu-id="77077-167">`AsNoTracking` verbessert die Leistung, da die zurückgegebenen Entitäten nicht nachverfolgt werden.</span><span class="sxs-lookup"><span data-stu-id="77077-167">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="77077-168">Die Entitäten müssen nicht nachverfolgt werden, da sie nicht im aktuellen Kontext aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="77077-168">The entities don't need to be tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="77077-169">Aktualisieren Sie *Pages/Courses/Index.cshtml* mit dem folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="77077-169">Update *Pages/Courses/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Index.cshtml?highlight=5,8,16-18,20,23,26,32,35-37,45)]

<span data-ttu-id="77077-170">Die folgenden Änderungen wurden am Codegerüst vorgenommen:</span><span class="sxs-lookup"><span data-stu-id="77077-170">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="77077-171">Der `Course`-Eigenschaftenname wurde in `Courses` geändert.</span><span class="sxs-lookup"><span data-stu-id="77077-171">Changed the `Course` property name to `Courses`.</span></span>
* <span data-ttu-id="77077-172">Die Spalte **Anzahl** wurde hinzugefügt. Sie zeigt den `CourseID`-Eigenschaftswert an.</span><span class="sxs-lookup"><span data-stu-id="77077-172">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="77077-173">Primärschlüssel werden nicht standardmäßig eingerüstet, da sie normalerweise ohne Bedeutung für Endbenutzer sind.</span><span class="sxs-lookup"><span data-stu-id="77077-173">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="77077-174">Allerdings hat der Primärschlüssel in diesem Fall jedoch Bedeutung.</span><span class="sxs-lookup"><span data-stu-id="77077-174">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="77077-175">Die Spalte **Abteilung** wurde geändert, sodass sie jetzt den Namen der Abteilung anzeigt.</span><span class="sxs-lookup"><span data-stu-id="77077-175">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="77077-176">Der Code zeigt die `Name`-Eigenschaft der `Department`-Entität an, die in die `Department`-Navigationseigenschaft geladen wird:</span><span class="sxs-lookup"><span data-stu-id="77077-176">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="77077-177">Führen Sie die Anwendung aus. Wählen Sie die Registerkarte **Kurse** aus, um die Liste mit den Abteilungsnamen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="77077-177">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Indexseite „Kurse“](read-related-data/_static/courses-index30.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="77077-179">Laden verwandter Daten mit „Select“</span><span class="sxs-lookup"><span data-stu-id="77077-179">Loading related data with Select</span></span>

<span data-ttu-id="77077-180">Die `OnGetAsync`-Methode lädt zugehörige Daten mit der `Include`-Methode.</span><span class="sxs-lookup"><span data-stu-id="77077-180">The `OnGetAsync` method loads related data with the `Include` method.</span></span> <span data-ttu-id="77077-181">Die `Select`-Methode ist eine Alternative, die nur die zugehörigen benötigten Daten lädt.</span><span class="sxs-lookup"><span data-stu-id="77077-181">The `Select` method is an alternative that loads only the related data needed.</span></span> <span data-ttu-id="77077-182">Für die einzelnen Elemente wie `Department.Name` wird ein INNER JOIN von SQL verwendet.</span><span class="sxs-lookup"><span data-stu-id="77077-182">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="77077-183">Für Sammlungen wird ein anderer Zugriff auf die Datenbank verwendet, aber Gleiches gilt für den `Include`-Operator für Sammlungen.</span><span class="sxs-lookup"><span data-stu-id="77077-183">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="77077-184">Der folgende Code lädt verwandte Daten mit der `Select`-Methode:</span><span class="sxs-lookup"><span data-stu-id="77077-184">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=6)]

<span data-ttu-id="77077-185">Der voranstehende Code gibt keine Entitätstypen zurück, weshalb keine Nachverfolgung ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="77077-185">The preceding code doesn't return any entity types, therefore no tracking is done.</span></span> <span data-ttu-id="77077-186">Weitere Informationen zur EF-Nachverfolgung finden Sie unter [Nachverfolgungsabfragen im Vergleich zu Abfragen ohne Nachverfolgung](/ef/core/querying/tracking).</span><span class="sxs-lookup"><span data-stu-id="77077-186">For more information about the EF tracking, see [Tracking vs. No-Tracking Queries](/ef/core/querying/tracking).</span></span>

<span data-ttu-id="77077-187">Die `CourseViewModel`:</span><span class="sxs-lookup"><span data-stu-id="77077-187">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="77077-188">Ein vollständiges Beispiel finden Sie unter [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) und [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs).</span><span class="sxs-lookup"><span data-stu-id="77077-188">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-instructor-pages"></a><span data-ttu-id="77077-189">Erstellen von Instructor-Seiten</span><span class="sxs-lookup"><span data-stu-id="77077-189">Create Instructor pages</span></span>

<span data-ttu-id="77077-190">In diesem Abschnitt erfolgt der Gerüstbau von Instructor-Seiten, und der Index-Seite des Dozenten werden zugehörige Courses und Enrollments hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="77077-190">This section scaffolds Instructor pages and adds related Courses and Enrollments to the Instructors Index page.</span></span>

<a name="IP"></a>
<span data-ttu-id="77077-191">![Indexseite „Dozenten“](read-related-data/_static/instructors-index30.png)</span><span class="sxs-lookup"><span data-stu-id="77077-191">![Instructors Index page](read-related-data/_static/instructors-index30.png)</span></span>

<span data-ttu-id="77077-192">Auf dieser Seite werden verwandte Daten auf folgende Weise gelesen und angezeigt:</span><span class="sxs-lookup"><span data-stu-id="77077-192">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="77077-193">Die Liste der Dozenten zeigt verwandte Daten aus der `OfficeAssignment`-Entität (Office in der vorherigen Abbildung).</span><span class="sxs-lookup"><span data-stu-id="77077-193">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="77077-194">Die `Instructor`- und `OfficeAssignment`-Entitäten stehen in einer 1:0..1-Beziehung zueinander.</span><span class="sxs-lookup"><span data-stu-id="77077-194">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="77077-195">Eager Loading wird für die `OfficeAssignment`-Entitäten verwendet.</span><span class="sxs-lookup"><span data-stu-id="77077-195">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="77077-196">Eager Loading ist in der Regel effizienter, wenn die verwandten Daten angezeigt werden müssen.</span><span class="sxs-lookup"><span data-stu-id="77077-196">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="77077-197">In diesem Fall werden die Office-Zuweisungen für die Dozenten angezeigt.</span><span class="sxs-lookup"><span data-stu-id="77077-197">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="77077-198">Wenn der Benutzer einen Dozenten auswählt, werden zugehörige `Course`-Entitäten angezeigt.</span><span class="sxs-lookup"><span data-stu-id="77077-198">When the user selects an instructor, related `Course` entities are displayed.</span></span> <span data-ttu-id="77077-199">Die `Instructor`- und `Course`-Entitäten stehen in einer m:n-Beziehung zueinander.</span><span class="sxs-lookup"><span data-stu-id="77077-199">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="77077-200">Für die `Course`-Entitäten und ihre verwandten `Department`-Entitäten wird das Eager Loading verwendet.</span><span class="sxs-lookup"><span data-stu-id="77077-200">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="77077-201">In diesem Fall können separate Abfragen effizienter sein, da nur Kurse für den ausgewählten Dozenten benötigt werden.</span><span class="sxs-lookup"><span data-stu-id="77077-201">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="77077-202">Dieses Beispiel zeigt, wie Eager Loading für Navigationseigenschaften in Entitäten in den Navigationseigenschaften verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="77077-202">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="77077-203">Wenn der Benutzer einen Kurs auswählt, werden zugehörige Daten aus der `Enrollments`-Entität angezeigt.</span><span class="sxs-lookup"><span data-stu-id="77077-203">When the user selects a course, related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="77077-204">In der vorherigen Abbildung sind der Name des Studenten und die Note angezeigt.</span><span class="sxs-lookup"><span data-stu-id="77077-204">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="77077-205">Die `Course`- und `Enrollment`-Entitäten stehen in einer 1:n-Beziehung zueinander.</span><span class="sxs-lookup"><span data-stu-id="77077-205">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model"></a><span data-ttu-id="77077-206">Erstellen von Anzeigemodellen</span><span class="sxs-lookup"><span data-stu-id="77077-206">Create a view model</span></span>

<span data-ttu-id="77077-207">Die Dozentenseite zeigt Daten aus drei verschiedenen Tabellen.</span><span class="sxs-lookup"><span data-stu-id="77077-207">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="77077-208">Ein Ansichtsmodell ist erforderlich, das drei Eigenschaften enthält, die die drei Tabellen darstellen.</span><span class="sxs-lookup"><span data-stu-id="77077-208">A view model is needed that includes three properties representing the three tables.</span></span>

<span data-ttu-id="77077-209">Erstellen *SchoolViewModels/InstructorIndexData.cs* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="77077-209">Create *SchoolViewModels/InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-instructor-pages"></a><span data-ttu-id="77077-210">Gerüstbau von Instructor-Seiten</span><span class="sxs-lookup"><span data-stu-id="77077-210">Scaffold Instructor pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="77077-211">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="77077-211">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="77077-212">Befolgen Sie die Anweisungen unter [Gerüstbau der Student-Seiten](xref:data/ef-rp/intro#scaffold-student-pages) mit den folgenden Ausnahmen:</span><span class="sxs-lookup"><span data-stu-id="77077-212">Follow the instructions in [Scaffold the student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="77077-213">Erstellen Sie einen Ordner *Pages/Instructors*.</span><span class="sxs-lookup"><span data-stu-id="77077-213">Create a *Pages/Instructors* folder.</span></span>
  * <span data-ttu-id="77077-214">Verwenden Sie `Instructor` als Modellklasse.</span><span class="sxs-lookup"><span data-stu-id="77077-214">Use `Instructor` for the model class.</span></span>
  * <span data-ttu-id="77077-215">Verwenden Sie die vorhandene Kontextklasse, anstatt eine neue Klasse zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="77077-215">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="77077-216">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="77077-216">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="77077-217">Erstellen Sie einen Ordner *Pages/Instructors*.</span><span class="sxs-lookup"><span data-stu-id="77077-217">Create a *Pages/Instructors* folder.</span></span>

* <span data-ttu-id="77077-218">Führen Sie den folgenden Befehl aus, um das Gerüst für Instructor-Seiten zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="77077-218">Run the following command to scaffold the Instructor pages.</span></span>

  <span data-ttu-id="77077-219">**Unter Windows:**</span><span class="sxs-lookup"><span data-stu-id="77077-219">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

  <span data-ttu-id="77077-220">**Unter Linux oder macOS:**</span><span class="sxs-lookup"><span data-stu-id="77077-220">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages/Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="77077-221">Führen Sie die App aus, und navigieren Sie zur Seite Instructors, um zu sehen, wie die Gerüstbauseite aussieht, bevor Sie sie aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="77077-221">To see what the scaffolded page looks like before you update it, run the app and navigate to the Instructors page.</span></span>

<span data-ttu-id="77077-222">Aktualisieren Sie *Pages/Instructors/Index.cshtml.cs* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="77077-222">Update *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,19-53)]

<span data-ttu-id="77077-223">Die `OnGetAsync`-Methode akzeptiert optional Routendaten für die ID des ausgewählten Dozenten.</span><span class="sxs-lookup"><span data-stu-id="77077-223">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="77077-224">Untersuchen Sie die Abfrage in der Datei *Pages/Instructors/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="77077-224">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading)]

<span data-ttu-id="77077-225">Der Code gibt Eager Loading für die folgenden Navigationseigenschaften an:</span><span class="sxs-lookup"><span data-stu-id="77077-225">The code specifies eager loading for the following navigation properties:</span></span>

* `Instructor.OfficeAssignment`
* `Instructor.CourseAssignments`
  * `CourseAssignments.Course`
    * `Course.Department`
    * `Course.Enrollments`
      * `Enrollment.Student`

<span data-ttu-id="77077-226">Beachten Sie die Wiederholung der `Include`- und `ThenInclude`-Methoden für `CourseAssignments` und `Course`.</span><span class="sxs-lookup"><span data-stu-id="77077-226">Notice the repetition of `Include` and `ThenInclude` methods for `CourseAssignments` and `Course`.</span></span> <span data-ttu-id="77077-227">Diese Wiederholung ist erforderlich, um Eager Loading für zwei Navigationseigenschaften der `Course`-Entität anzugeben.</span><span class="sxs-lookup"><span data-stu-id="77077-227">This repetition is necessary to specify eager loading for two navigation properties of the `Course` entity.</span></span>

<span data-ttu-id="77077-228">Der folgende Code wird ausgeführt, wenn ein Dozent ausgewählt wird (`id != null`).</span><span class="sxs-lookup"><span data-stu-id="77077-228">The following code executes when an instructor is selected (`id != null`).</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectInstructor)]

<span data-ttu-id="77077-229">Der ausgewählte Dozent wird aus der Liste der Dozenten im Ansichtsmodell abgerufen.</span><span class="sxs-lookup"><span data-stu-id="77077-229">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="77077-230">Die `Courses`-Eigenschaft des Ansichtsmodells wird mit den `Course`-Entitäten aus der `CourseAssignments`-Navigationseigenschaft dieses Dozenten geladen.</span><span class="sxs-lookup"><span data-stu-id="77077-230">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

<span data-ttu-id="77077-231">Die `Where`-Methode gibt eine Sammlung zurück.</span><span class="sxs-lookup"><span data-stu-id="77077-231">The `Where` method returns a collection.</span></span> <span data-ttu-id="77077-232">In diesem Fall wählt der Filter jedoch eine einzelne Entität aus.</span><span class="sxs-lookup"><span data-stu-id="77077-232">But in this case, the filter will select a single entity.</span></span> <span data-ttu-id="77077-233">Daher wird die `Single`-Methode aufgerufen, um die Sammlung in eine einzelne `Instructor`-Entität zu konvertieren.</span><span class="sxs-lookup"><span data-stu-id="77077-233">so the `Single` method is called to convert the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="77077-234">Die `Instructor`-Entität ermöglicht Zugriff auf die `CourseAssignments`-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="77077-234">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="77077-235">`CourseAssignments` ermöglicht Zugriff auf die verwandten `Course`-Entitäten.</span><span class="sxs-lookup"><span data-stu-id="77077-235">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![Dozent:Kurse m:N](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="77077-237">Die `Single`-Methode wird für eine Sammlung verwendet, wenn die Sammlung nur ein Element aufweist.</span><span class="sxs-lookup"><span data-stu-id="77077-237">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="77077-238">Die `Single`-Methode löst eine Ausnahme aus, wenn die Sammlung leer ist oder mehr als ein Element vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="77077-238">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="77077-239">Eine Alternative ist `SingleOrDefault`, womit ein Standardwert (in diesem Fall 0 (null)) zurückgegeben wird, wenn die Sammlung leer ist.</span><span class="sxs-lookup"><span data-stu-id="77077-239">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span>

<span data-ttu-id="77077-240">Wenn ein Kurs ausgewählt ist, füllt der folgende Code die `Enrollments`-Eigenschaft des Ansichtsmodells:</span><span class="sxs-lookup"><span data-stu-id="77077-240">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectCourse)]

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="77077-241">Aktualisieren der Indexseite „Dozenten“</span><span class="sxs-lookup"><span data-stu-id="77077-241">Update the instructors Index page</span></span>

<span data-ttu-id="77077-242">Aktualisieren Sie *Pages/Instructors/Index.cshtml* mit dem folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="77077-242">Update *Pages/Instructors/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Index.cshtml?highlight=1,5,8,16-21,25-32,43-57,67-102,104-126)]

<span data-ttu-id="77077-243">Durch den vorangehenden Code werden folgende Änderungen vorgenommen:</span><span class="sxs-lookup"><span data-stu-id="77077-243">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="77077-244">Aktualisiert die `page`-Anweisung von `@page` auf `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="77077-244">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="77077-245">`"{id:int?}"` ist eine Routenvorlage.</span><span class="sxs-lookup"><span data-stu-id="77077-245">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="77077-246">Die Routenvorlage ändert ganzzahlige Abfragezeichenfolgen in der URL in Routendaten.</span><span class="sxs-lookup"><span data-stu-id="77077-246">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="77077-247">Klicken Sie beispielsweise auf den Link **Auswählen** für einen Dozenten, wenn nur die `@page`-Anweisung eine URL wie die folgende erzeugt:</span><span class="sxs-lookup"><span data-stu-id="77077-247">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `https://localhost:5001/Instructors?id=2`

  <span data-ttu-id="77077-248">Wenn die Seitenanweisung `@page "{id:int?}"` ist, lautet die URL folgendermaßen:</span><span class="sxs-lookup"><span data-stu-id="77077-248">When the page directive is `@page "{id:int?}"`, the URL is:</span></span>

  `https://localhost:5001/Instructors/2`

* <span data-ttu-id="77077-249">Fügt eine **Office**-Spalte hinzu, die `item.OfficeAssignment.Location` nur anzeigt, wenn `item.OfficeAssignment` nicht NULL ist.</span><span class="sxs-lookup"><span data-stu-id="77077-249">Adds an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="77077-250">Da dies eine 1:0..1-Beziehung ist, gibt es möglicherweise keine verwandte OfficeAssignment-Entität.</span><span class="sxs-lookup"><span data-stu-id="77077-250">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="77077-251">Fügt eine **Courses**-Spalte hinzu, die die Kurse eines jeden Dozenten anzeigt.</span><span class="sxs-lookup"><span data-stu-id="77077-251">Adds a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="77077-252">Weitere Informationen zu dieser Razor-Syntax finden Sie unter [Expliziter Zeilenübergang](xref:mvc/views/razor#explicit-line-transition).</span><span class="sxs-lookup"><span data-stu-id="77077-252">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="77077-253">Fügt Code hinzu, der `class="success"` dynamisch zum `tr`-Element des ausgewählten Dozenten und Kurses hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="77077-253">Adds code that dynamically adds `class="success"` to the `tr` element of the selected instructor and course.</span></span> <span data-ttu-id="77077-254">Hiermit wird mit einer Bootstrapklasse eine Hintergrundfarbe für die ausgewählte Zeile hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="77077-254">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="77077-255">Fügt einen neuen Link mit der Bezeichnung **Select** hinzu.</span><span class="sxs-lookup"><span data-stu-id="77077-255">Adds a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="77077-256">Dieser Link sendet die ID des ausgewählten Dozenten an die `Index`-Methode und legt die Hintergrundfarbe fest.</span><span class="sxs-lookup"><span data-stu-id="77077-256">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

* <span data-ttu-id="77077-257">Fügt eine Tabelle mit Kursen für den ausgewählten Dozenten hinzu.</span><span class="sxs-lookup"><span data-stu-id="77077-257">Adds a table of courses for the selected Instructor.</span></span>

* <span data-ttu-id="77077-258">Fügt eine Tabelle mit Studentenregistrierungen für den ausgewählten Kurs hinzu.</span><span class="sxs-lookup"><span data-stu-id="77077-258">Adds a table of student enrollments for the selected course.</span></span>

<span data-ttu-id="77077-259">Führen Sie die Anwendung aus. Klicken Sie auf die Registerkarte **Dozenten**. Die Seite zeigt den `Location` (Office) aus der verwandten `OfficeAssignment`-Entität an.</span><span class="sxs-lookup"><span data-stu-id="77077-259">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="77077-260">Wenn `OfficeAssignment` NULL ist, wird eine leere Tabellenzelle angezeigt.</span><span class="sxs-lookup"><span data-stu-id="77077-260">If `OfficeAssignment` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="77077-261">Klicken Sie auf den Link **Select** für einen Dozenten.</span><span class="sxs-lookup"><span data-stu-id="77077-261">Click on the **Select** link for an instructor.</span></span> <span data-ttu-id="77077-262">Der Zeilenstil ändert sich, und Kurse, die diesem Dozenten zugewiesen sind, werden angezeigt.</span><span class="sxs-lookup"><span data-stu-id="77077-262">The row style changes and courses assigned to that instructor are displayed.</span></span>

<span data-ttu-id="77077-263">Wählen Sie einen Kurs aus, um die Liste der registrierten Studenten und deren Noten einzusehen.</span><span class="sxs-lookup"><span data-stu-id="77077-263">Select a course to see the list of enrolled students and their grades.</span></span>

![Indexseite „Dozenten“, Dozent und Kurs ausgewählt](read-related-data/_static/instructors-index30.png)

## <a name="using-single"></a><span data-ttu-id="77077-265">Verwenden von „Single“</span><span class="sxs-lookup"><span data-stu-id="77077-265">Using Single</span></span>

<span data-ttu-id="77077-266">Die `Single`-Methode kann die `Where`-Bedingung übergehen, anstatt die `Where`-Methode separat aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="77077-266">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="77077-267">Die Verwendung von `Single` mit einer Where-Bedingung ist eine Frage der persönlichen Präferenz.</span><span class="sxs-lookup"><span data-stu-id="77077-267">Use of `Single` with a Where condition is a matter of personal preference.</span></span> <span data-ttu-id="77077-268">Sie bietet keine Vorteile gegenüber der Verwendung der `Where`-Methode.</span><span class="sxs-lookup"><span data-stu-id="77077-268">It provides no benefits over using the `Where` method.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="77077-269">Explizites Laden</span><span class="sxs-lookup"><span data-stu-id="77077-269">Explicit loading</span></span>

<span data-ttu-id="77077-270">Der aktuelle Code gibt Eager Loading für `Enrollments` und `Students` an:</span><span class="sxs-lookup"><span data-stu-id="77077-270">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading&highlight=6-9)]

<span data-ttu-id="77077-271">Angenommen, Benutzer möchten Registrierungen für einen Kurs nur selten anzeigen lassen.</span><span class="sxs-lookup"><span data-stu-id="77077-271">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="77077-272">In diesem Fall wäre es eine Optimierung, die Registrierungsdaten nur dann zu laden, wenn diese angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="77077-272">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="77077-273">In diesem Abschnitt wird die `OnGetAsync` aktualisiert, um das explizite Laden von `Enrollments` und `Students` zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="77077-273">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="77077-274">Aktualisieren Sie *Pages/Instructors/Index.cshtml.cs* mit dem folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="77077-274">Update *Pages/Instructors/Index.cshtml.cs* with the following code.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Index.cshtml.cs?highlight=31-35,52-56)]

<span data-ttu-id="77077-275">Der vorangehende Code löscht die *ThenInclude*-Methodenaufrufe für Registrierung und Studentendaten.</span><span class="sxs-lookup"><span data-stu-id="77077-275">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="77077-276">Wenn ein Kurs ausgewählt ist, ruft der explizit geladene Code Folgendes ab:</span><span class="sxs-lookup"><span data-stu-id="77077-276">If a course is selected, the explicit loading code retrieves:</span></span>

* <span data-ttu-id="77077-277">Die `Enrollment`-Entitäten für den ausgewählten Kurs.</span><span class="sxs-lookup"><span data-stu-id="77077-277">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="77077-278">Die `Student`-Entitäten für jede `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="77077-278">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="77077-279">Beachten Sie, dass der vorherige Code `.AsNoTracking()` auskommentiert.</span><span class="sxs-lookup"><span data-stu-id="77077-279">Notice that the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="77077-280">Navigationseigenschaften können nur für nachverfolgte Entitäten explizit geladen werden.</span><span class="sxs-lookup"><span data-stu-id="77077-280">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="77077-281">Testen Sie die App.</span><span class="sxs-lookup"><span data-stu-id="77077-281">Test the app.</span></span> <span data-ttu-id="77077-282">Aus Benutzersicht verhält sich die Anwendung identisch mit der vorherigen Version.</span><span class="sxs-lookup"><span data-stu-id="77077-282">From a user's perspective, the app behaves identically to the previous version.</span></span>

## <a name="next-steps"></a><span data-ttu-id="77077-283">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="77077-283">Next steps</span></span>

<span data-ttu-id="77077-284">Das nächste Tutorial zeigt, wie verwandte Daten aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="77077-284">The next tutorial shows how to update related data.</span></span>

>[!div class="step-by-step"]
><span data-ttu-id="77077-285">[Vorheriges Tutorial](xref:data/ef-rp/complex-data-model)
>[Nächstes Tutorial](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="77077-285">[Previous tutorial](xref:data/ef-rp/complex-data-model)
[Next tutorial](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="77077-286">In diesem Tutorial werden verwandte Daten gelesen und angezeigt.</span><span class="sxs-lookup"><span data-stu-id="77077-286">In this tutorial, related data is read and displayed.</span></span> <span data-ttu-id="77077-287">Verwandte Daten sind Daten, die von EF Core in die Navigationseigenschaften geladen werden.</span><span class="sxs-lookup"><span data-stu-id="77077-287">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="77077-288">Wenn nicht zu lösende Probleme auftreten, laden Sie die [fertige App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) herunter, oder zeigen Sie diese an.</span><span class="sxs-lookup"><span data-stu-id="77077-288">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="77077-289">[Anweisungen zum Download.](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="77077-289">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="77077-290">Die folgenden Abbildungen zeigen die abgeschlossenen Seiten für dieses Tutorial:</span><span class="sxs-lookup"><span data-stu-id="77077-290">The following illustrations show the completed pages for this tutorial:</span></span>

![Indexseite „Kurse“](read-related-data/_static/courses-index.png)

![Indexseite „Dozenten“](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a><span data-ttu-id="77077-293">Explizites, Eager und Lazy Loading verwandter Daten</span><span class="sxs-lookup"><span data-stu-id="77077-293">Eager, explicit, and lazy Loading of related data</span></span>

<span data-ttu-id="77077-294">Es gibt mehrere Möglichkeiten, mit denen EF Core verwandte Daten in die Navigationseigenschaften einer Entität laden kann:</span><span class="sxs-lookup"><span data-stu-id="77077-294">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="77077-295">[Eager Loading (vorzeitiges Laden)](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="77077-295">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="77077-296">Beim Eager Loading lädt eine Abfrage für einen Entitätstyp auch verwandte Entitäten.</span><span class="sxs-lookup"><span data-stu-id="77077-296">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="77077-297">Wenn die Entität gelesen wird, werden ihre verwandten Daten abgerufen.</span><span class="sxs-lookup"><span data-stu-id="77077-297">When the entity is read, its related data is retrieved.</span></span> <span data-ttu-id="77077-298">Dies führt normalerweise zu einer einzelnen Joinabfrage, die alle Daten abruft, die erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="77077-298">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="77077-299">EF Core wird mehrere Abfragen für einige Typen von Eager Loading ausgeben.</span><span class="sxs-lookup"><span data-stu-id="77077-299">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="77077-300">Die Ausgabe mehrerer Abfragen kann effizienter sein, als dies bei einigen Abfragen in EF6 der Fall war. Dort war nur eine einzelne Abfrage vorhanden.</span><span class="sxs-lookup"><span data-stu-id="77077-300">Issuing multiple queries can be more efficient than was the case for some queries in EF6 where there was a single query.</span></span> <span data-ttu-id="77077-301">Eager Loading wird mit den `Include`- und `ThenInclude`-Methoden angegeben.</span><span class="sxs-lookup"><span data-stu-id="77077-301">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Beispiel für Eager Loading](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="77077-303">Eager Loading sendet mehrere Abfragen, wenn eine Sammlungsnavigation enthalten ist:</span><span class="sxs-lookup"><span data-stu-id="77077-303">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="77077-304">Eine Abfrage für die Hauptabfrage</span><span class="sxs-lookup"><span data-stu-id="77077-304">One query for the main query</span></span> 
  * <span data-ttu-id="77077-305">Eine Abfrage für jeden „Sammlungsrand“ in der Ladestruktur.</span><span class="sxs-lookup"><span data-stu-id="77077-305">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="77077-306">Beispiel für separate Abfragen mit `Load`: Die Daten können in separaten Abfragen abgerufen werden. EF Core korrigiert die Navigationseigenschaften.</span><span class="sxs-lookup"><span data-stu-id="77077-306">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="77077-307">„Korrigieren“ bedeutet, dass EF Core die Navigationseigenschaften automatisch füllt.</span><span class="sxs-lookup"><span data-stu-id="77077-307">"fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="77077-308">Separate Abfragen mit `Load` ähneln mehr dem expliziten Laden als dem Eager Loading.</span><span class="sxs-lookup"><span data-stu-id="77077-308">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![Beispiel für separate Abfragen](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="77077-310">Hinweis: EF Core korrigiert automatisch Navigationseigenschaften für alle anderen Entitäten, die zuvor in die Kontextinstanz geladen wurden.</span><span class="sxs-lookup"><span data-stu-id="77077-310">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="77077-311">Auch wenn die Daten für eine Navigationseigenschaft *nicht* explizit eingeschlossen sind, kann die Eigenschaft immer noch aufgefüllt werden, wenn einige oder alle verwandten Entitäten zuvor geladen wurden.</span><span class="sxs-lookup"><span data-stu-id="77077-311">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="77077-312">[Explizites Laden](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="77077-312">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="77077-313">Wenn die Entität zuerst gelesen wird, werden verwandte Daten nicht abgerufen.</span><span class="sxs-lookup"><span data-stu-id="77077-313">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="77077-314">Es muss Code geschrieben werden, um die verwandten Daten bei Bedarf abzurufen.</span><span class="sxs-lookup"><span data-stu-id="77077-314">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="77077-315">Explizites Laden mit separaten Abfragen führt zu mehreren Abfragen, die an die Datenbank gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="77077-315">Explicit loading with separate queries results in multiple queries sent to the DB.</span></span> <span data-ttu-id="77077-316">Mit explizitem Laden gibt der Code die zu ladenden Navigationseigenschaften an.</span><span class="sxs-lookup"><span data-stu-id="77077-316">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="77077-317">Verwenden Sie für explizites Laden die `Load`-Methode.</span><span class="sxs-lookup"><span data-stu-id="77077-317">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="77077-318">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="77077-318">For example:</span></span>

  ![Beispiel für explizites Laden](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="77077-320">[Lazy Loading (verzögertes Laden)](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="77077-320">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="77077-321">[Lazy Loading wurde in Version 2.1 zu EF Core hinzugefügt](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="77077-321">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="77077-322">Wenn die Entität zuerst gelesen wird, werden verwandte Daten nicht abgerufen.</span><span class="sxs-lookup"><span data-stu-id="77077-322">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="77077-323">Wenn zum ersten Mal auf eine Navigationseigenschaft zugegriffen wird, werden die für diese Navigationseigenschaft erforderlichen Daten automatisch abgerufen.</span><span class="sxs-lookup"><span data-stu-id="77077-323">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="77077-324">Wenn zum ersten Mal auf eine Navigationseigenschaft zugegriffen wird, wird jedes Mal eine Abfrage an die Datenbank geschickt.</span><span class="sxs-lookup"><span data-stu-id="77077-324">A query is sent to the DB each time a navigation property is accessed for the first time.</span></span>

* <span data-ttu-id="77077-325">Der `Select`-Operator lädt nur die erforderlichen verwandten Daten.</span><span class="sxs-lookup"><span data-stu-id="77077-325">The `Select` operator loads only the related data needed.</span></span>

## <a name="create-a-course-page-that-displays-department-name"></a><span data-ttu-id="77077-326">Erstellen einer Kursseite, die den Abteilungsnamen anzeigt</span><span class="sxs-lookup"><span data-stu-id="77077-326">Create a Course page that displays department name</span></span>

<span data-ttu-id="77077-327">Die Course-Entität enthält eine Navigationseigenschaft, welche die `Department`-Entität enthält.</span><span class="sxs-lookup"><span data-stu-id="77077-327">The Course entity includes a navigation property that contains the `Department` entity.</span></span> <span data-ttu-id="77077-328">Die `Department`-Entität enthält die Abteilung, der der Kurs zugewiesen ist.</span><span class="sxs-lookup"><span data-stu-id="77077-328">The `Department` entity contains the department that the course is assigned to.</span></span>

<span data-ttu-id="77077-329">So zeigen Sie den Namen der zugewiesenen Abteilung in einer Kursliste an:</span><span class="sxs-lookup"><span data-stu-id="77077-329">To display the name of the assigned department in a list of courses:</span></span>

* <span data-ttu-id="77077-330">Rufen Sie `Name`-Eigenschaft aus der `Department`-Entität ab.</span><span class="sxs-lookup"><span data-stu-id="77077-330">Get the `Name` property from the `Department` entity.</span></span>
* <span data-ttu-id="77077-331">Die `Department`-Entität stammt aus der `Course.Department`-Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="77077-331">The `Department` entity comes from the `Course.Department` navigation property.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a><span data-ttu-id="77077-333">Erstellen des Gerüsts für das Kursmodell</span><span class="sxs-lookup"><span data-stu-id="77077-333">Scaffold the Course model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="77077-334">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="77077-334">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="77077-335">Führen Sie die Schritte unter [Erstellen des Gerüsts für das Studentenmodell](xref:data/ef-rp/intro#scaffold-the-student-model) aus, und verwenden Sie `Course` für die Modellklasse.</span><span class="sxs-lookup"><span data-stu-id="77077-335">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Course` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="77077-336">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="77077-336">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="77077-337">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="77077-337">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

<span data-ttu-id="77077-338">Der vorherige Befehl erstellt ein Gerüst für das `Course`-Modell.</span><span class="sxs-lookup"><span data-stu-id="77077-338">The preceding command scaffolds the `Course` model.</span></span> <span data-ttu-id="77077-339">Öffnen Sie das Projekt in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="77077-339">Open the project in Visual Studio.</span></span>

<span data-ttu-id="77077-340">Öffnen Sie *Pages/Courses/Index.cshtml.cs*. Untersuchen Sie die `OnGetAsync`-Methode.</span><span class="sxs-lookup"><span data-stu-id="77077-340">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="77077-341">Die Gerüstbauengine gibt Eager Loading für die `Department`-Navigationseigenschaft an.</span><span class="sxs-lookup"><span data-stu-id="77077-341">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="77077-342">Die `Include`-Methode gibt Eager Loading an.</span><span class="sxs-lookup"><span data-stu-id="77077-342">The `Include` method specifies eager loading.</span></span>

<span data-ttu-id="77077-343">Führen Sie die Anwendung aus, und klicken Sie auf den Link **Kurse**.</span><span class="sxs-lookup"><span data-stu-id="77077-343">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="77077-344">Die Abteilungsspalte zeigt die `DepartmentID` an, die nicht hilfreich ist.</span><span class="sxs-lookup"><span data-stu-id="77077-344">The department column displays the `DepartmentID`, which isn't useful.</span></span>

<span data-ttu-id="77077-345">Aktualisieren Sie die `OnGetAsync`-Methode mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="77077-345">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="77077-346">Der vorangehende Code fügt `AsNoTracking` hinzu.</span><span class="sxs-lookup"><span data-stu-id="77077-346">The preceding code adds `AsNoTracking`.</span></span> <span data-ttu-id="77077-347">`AsNoTracking` verbessert die Leistung, da die zurückgegebenen Entitäten nicht nachverfolgt werden.</span><span class="sxs-lookup"><span data-stu-id="77077-347">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="77077-348">Die Entitäten werden nicht nachverfolgt, da sie nicht im aktuellen Kontext aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="77077-348">The entities are not tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="77077-349">Aktualisieren Sie *Pages/Courses/Index.cshtml* mit dem folgenden hervorgehobenen Markup:</span><span class="sxs-lookup"><span data-stu-id="77077-349">Update *Pages/Courses/Index.cshtml* with the following highlighted markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="77077-350">Die folgenden Änderungen wurden am Codegerüst vorgenommen:</span><span class="sxs-lookup"><span data-stu-id="77077-350">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="77077-351">Die Überschrift wurde von „Index“ in „Kurse“ geändert.</span><span class="sxs-lookup"><span data-stu-id="77077-351">Changed the heading from Index to Courses.</span></span>
* <span data-ttu-id="77077-352">Die Spalte **Anzahl** wurde hinzugefügt. Sie zeigt den `CourseID`-Eigenschaftswert an.</span><span class="sxs-lookup"><span data-stu-id="77077-352">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="77077-353">Primärschlüssel werden nicht standardmäßig eingerüstet, da sie normalerweise ohne Bedeutung für Endbenutzer sind.</span><span class="sxs-lookup"><span data-stu-id="77077-353">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="77077-354">Allerdings hat der Primärschlüssel in diesem Fall jedoch Bedeutung.</span><span class="sxs-lookup"><span data-stu-id="77077-354">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="77077-355">Die Spalte **Abteilung** wurde geändert, sodass sie jetzt den Namen der Abteilung anzeigt.</span><span class="sxs-lookup"><span data-stu-id="77077-355">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="77077-356">Der Code zeigt die `Name`-Eigenschaft der `Department`-Entität an, die in die `Department`-Navigationseigenschaft geladen wird:</span><span class="sxs-lookup"><span data-stu-id="77077-356">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="77077-357">Führen Sie die Anwendung aus. Wählen Sie die Registerkarte **Kurse** aus, um die Liste mit den Abteilungsnamen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="77077-357">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Indexseite „Kurse“](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="77077-359">Laden verwandter Daten mit „Select“</span><span class="sxs-lookup"><span data-stu-id="77077-359">Loading related data with Select</span></span>

<span data-ttu-id="77077-360">Die `OnGetAsync`-Methode lädt verwandte Daten mit der `Include`-Methode:</span><span class="sxs-lookup"><span data-stu-id="77077-360">The `OnGetAsync` method loads related data with the `Include` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="77077-361">Der `Select`-Operator lädt nur die erforderlichen verwandten Daten.</span><span class="sxs-lookup"><span data-stu-id="77077-361">The `Select` operator loads only the related data needed.</span></span> <span data-ttu-id="77077-362">Für die einzelnen Elemente wie `Department.Name` wird ein INNER JOIN von SQL verwendet.</span><span class="sxs-lookup"><span data-stu-id="77077-362">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="77077-363">Für Sammlungen wird ein anderer Zugriff auf die Datenbank verwendet, aber Gleiches gilt für den `Include`-Operator für Sammlungen.</span><span class="sxs-lookup"><span data-stu-id="77077-363">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="77077-364">Der folgende Code lädt verwandte Daten mit der `Select`-Methode:</span><span class="sxs-lookup"><span data-stu-id="77077-364">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="77077-365">Die `CourseViewModel`:</span><span class="sxs-lookup"><span data-stu-id="77077-365">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="77077-366">Ein vollständiges Beispiel finden Sie unter [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) und [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs).</span><span class="sxs-lookup"><span data-stu-id="77077-366">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a><span data-ttu-id="77077-367">Erstellen einer Dozentenseite, die Kurse und Registrierungen anzeigt</span><span class="sxs-lookup"><span data-stu-id="77077-367">Create an Instructors page that shows Courses and Enrollments</span></span>

<span data-ttu-id="77077-368">In diesem Abschnitt wird die Dozentenseite erstellt.</span><span class="sxs-lookup"><span data-stu-id="77077-368">In this section, the Instructors page is created.</span></span>

<a name="IP"></a>
<span data-ttu-id="77077-369">![Indexseite „Dozenten“](read-related-data/_static/instructors-index.png)</span><span class="sxs-lookup"><span data-stu-id="77077-369">![Instructors Index page](read-related-data/_static/instructors-index.png)</span></span>

<span data-ttu-id="77077-370">Auf dieser Seite werden verwandte Daten auf folgende Weise gelesen und angezeigt:</span><span class="sxs-lookup"><span data-stu-id="77077-370">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="77077-371">Die Liste der Dozenten zeigt verwandte Daten aus der `OfficeAssignment`-Entität (Office in der vorherigen Abbildung).</span><span class="sxs-lookup"><span data-stu-id="77077-371">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="77077-372">Die `Instructor`- und `OfficeAssignment`-Entitäten stehen in einer 1:0..1-Beziehung zueinander.</span><span class="sxs-lookup"><span data-stu-id="77077-372">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="77077-373">Eager Loading wird für die `OfficeAssignment`-Entitäten verwendet.</span><span class="sxs-lookup"><span data-stu-id="77077-373">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="77077-374">Eager Loading ist in der Regel effizienter, wenn die verwandten Daten angezeigt werden müssen.</span><span class="sxs-lookup"><span data-stu-id="77077-374">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="77077-375">In diesem Fall werden die Office-Zuweisungen für die Dozenten angezeigt.</span><span class="sxs-lookup"><span data-stu-id="77077-375">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="77077-376">Wenn der Benutzer einen Dozenten auswählt (Harui in der vorherigen Abbildung), werden verwandte `Course`-Entitäten angezeigt.</span><span class="sxs-lookup"><span data-stu-id="77077-376">When the user selects an instructor (Harui in the preceding image), related `Course` entities are displayed.</span></span> <span data-ttu-id="77077-377">Die `Instructor`- und `Course`-Entitäten stehen in einer m:n-Beziehung zueinander.</span><span class="sxs-lookup"><span data-stu-id="77077-377">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="77077-378">Für die `Course`-Entitäten und ihre verwandten `Department`-Entitäten wird das Eager Loading verwendet.</span><span class="sxs-lookup"><span data-stu-id="77077-378">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="77077-379">In diesem Fall können separate Abfragen effizienter sein, da nur Kurse für den ausgewählten Dozenten benötigt werden.</span><span class="sxs-lookup"><span data-stu-id="77077-379">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="77077-380">Dieses Beispiel zeigt, wie Eager Loading für Navigationseigenschaften in Entitäten in den Navigationseigenschaften verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="77077-380">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="77077-381">Wenn der Benutzer einen Kurs auswählt (Chemie in der vorherigen Abbildung), werden verwandte Daten aus der `Enrollments`-Entität angezeigt.</span><span class="sxs-lookup"><span data-stu-id="77077-381">When the user selects a course (Chemistry in the preceding image), related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="77077-382">In der vorherigen Abbildung sind der Name des Studenten und die Note angezeigt.</span><span class="sxs-lookup"><span data-stu-id="77077-382">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="77077-383">Die `Course`- und `Enrollment`-Entitäten stehen in einer 1:n-Beziehung zueinander.</span><span class="sxs-lookup"><span data-stu-id="77077-383">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="77077-384">Erstellen eines Ansichtsmodells für die Indexansicht „Dozenten“</span><span class="sxs-lookup"><span data-stu-id="77077-384">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="77077-385">Die Dozentenseite zeigt Daten aus drei verschiedenen Tabellen.</span><span class="sxs-lookup"><span data-stu-id="77077-385">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="77077-386">Es wird ein Ansichtsmodell erstellt, das die drei Entitäten enthält, die die drei Tabellen darstellen.</span><span class="sxs-lookup"><span data-stu-id="77077-386">A view model is created that includes the three entities representing the three tables.</span></span>

<span data-ttu-id="77077-387">Erstellen Sie im Ordner *SchoolViewModels* die Datei *InstructorIndexData.cs* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="77077-387">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a><span data-ttu-id="77077-388">Gerüstbau für das Dozentenmodell</span><span class="sxs-lookup"><span data-stu-id="77077-388">Scaffold the Instructor model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="77077-389">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="77077-389">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="77077-390">Führen Sie die Schritte unter [Erstellen des Gerüsts für das Studentenmodell](xref:data/ef-rp/intro#scaffold-the-student-model) aus, und verwenden Sie `Instructor` für die Modellklasse.</span><span class="sxs-lookup"><span data-stu-id="77077-390">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Instructor` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="77077-391">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="77077-391">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="77077-392">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="77077-392">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="77077-393">Der vorherige Befehl erstellt ein Gerüst für das `Instructor`-Modell.</span><span class="sxs-lookup"><span data-stu-id="77077-393">The preceding command scaffolds the `Instructor` model.</span></span> 
<span data-ttu-id="77077-394">Führen Sie die Anwendung aus, und navigieren Sie zur Dozentenseite.</span><span class="sxs-lookup"><span data-stu-id="77077-394">Run the app and navigate to the instructors page.</span></span>

<span data-ttu-id="77077-395">Ersetzen Sie *Pages/Instructors/Index.cshtml.cs* durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="77077-395">Replace *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

<span data-ttu-id="77077-396">Die `OnGetAsync`-Methode akzeptiert optional Routendaten für die ID des ausgewählten Dozenten.</span><span class="sxs-lookup"><span data-stu-id="77077-396">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="77077-397">Untersuchen Sie die Abfrage in der Datei *Pages/Instructors/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="77077-397">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="77077-398">Die Abfrage enthält zwei Dinge:</span><span class="sxs-lookup"><span data-stu-id="77077-398">The query has two includes:</span></span>

* <span data-ttu-id="77077-399">`OfficeAssignment`: In der [Dozentenansicht](#IP) angezeigt.</span><span class="sxs-lookup"><span data-stu-id="77077-399">`OfficeAssignment`: Displayed in the [instructors view](#IP).</span></span>
* <span data-ttu-id="77077-400">`CourseAssignments`: Welche Kurse gegeben werden.</span><span class="sxs-lookup"><span data-stu-id="77077-400">`CourseAssignments`: Which brings in the courses taught.</span></span>

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="77077-401">Aktualisieren der Indexseite „Dozenten“</span><span class="sxs-lookup"><span data-stu-id="77077-401">Update the instructors Index page</span></span>

<span data-ttu-id="77077-402">Aktualisieren Sie *Pages/Instructors/Index.cshtml* mit folgendem Markup:</span><span class="sxs-lookup"><span data-stu-id="77077-402">Update *Pages/Instructors/Index.cshtml* with the following markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

<span data-ttu-id="77077-403">Das oben stehende Markup führt die folgenden Änderungen durch:</span><span class="sxs-lookup"><span data-stu-id="77077-403">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="77077-404">Aktualisiert die `page`-Anweisung von `@page` auf `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="77077-404">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="77077-405">`"{id:int?}"` ist eine Routenvorlage.</span><span class="sxs-lookup"><span data-stu-id="77077-405">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="77077-406">Die Routenvorlage ändert ganzzahlige Abfragezeichenfolgen in der URL in Routendaten.</span><span class="sxs-lookup"><span data-stu-id="77077-406">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="77077-407">Klicken Sie beispielsweise auf den Link **Auswählen** für einen Dozenten, wenn nur die `@page`-Anweisung eine URL wie die folgende erzeugt:</span><span class="sxs-lookup"><span data-stu-id="77077-407">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `http://localhost:1234/Instructors?id=2`

  <span data-ttu-id="77077-408">Wenn die Seitenanweisung `@page "{id:int?}"` ist, sieht die vorherige URL wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="77077-408">When the page directive is `@page "{id:int?}"`, the previous URL is:</span></span>

  `http://localhost:1234/Instructors/2`

* <span data-ttu-id="77077-409">Der Seitentitel lautet **Dozenten**.</span><span class="sxs-lookup"><span data-stu-id="77077-409">Page title is **Instructors**.</span></span>
* <span data-ttu-id="77077-410">Es wurde eine **Office**-Spalte hinzugefügt, die `item.OfficeAssignment.Location` nur anzeigt, wenn `item.OfficeAssignment` nicht gleich 0 (null) ist.</span><span class="sxs-lookup"><span data-stu-id="77077-410">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="77077-411">Da dies eine 1:0..1-Beziehung ist, gibt es möglicherweise keine verwandte OfficeAssignment-Entität.</span><span class="sxs-lookup"><span data-stu-id="77077-411">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="77077-412">Es wurde eine **Kurse**-Spalte hinzugefügt, die die Kurse eines jeden Dozenten anzeigt.</span><span class="sxs-lookup"><span data-stu-id="77077-412">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="77077-413">Weitere Informationen zu dieser Razor-Syntax finden Sie unter [Expliziter Zeilenübergang](xref:mvc/views/razor#explicit-line-transition).</span><span class="sxs-lookup"><span data-stu-id="77077-413">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="77077-414">Es wurde Code hinzugefügt, der `class="success"` dynamisch zum `tr`-Element des ausgewählten Dozenten hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="77077-414">Added code that dynamically adds `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="77077-415">Hiermit wird eine Hintergrundfarbe mit einer Bootstrapklasse für die ausgewählte Zeile hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="77077-415">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="77077-416">Es wurde ein neuer Link mit der Bezeichnung **Auswählen** hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="77077-416">Added a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="77077-417">Dieser Link sendet die ID des ausgewählten Dozenten an die `Index`-Methode und legt die Hintergrundfarbe fest.</span><span class="sxs-lookup"><span data-stu-id="77077-417">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="77077-418">Führen Sie die Anwendung aus. Klicken Sie auf die Registerkarte **Dozenten**. Die Seite zeigt den `Location` (Office) aus der verwandten `OfficeAssignment`-Entität an.</span><span class="sxs-lookup"><span data-stu-id="77077-418">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="77077-419">Wenn OfficeAssignment gleich ist 0 (null), wird eine leere Tabellenzelle angezeigt.</span><span class="sxs-lookup"><span data-stu-id="77077-419">If OfficeAssignment\` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="77077-420">Klicken Sie auf den Link **Auswählen**.</span><span class="sxs-lookup"><span data-stu-id="77077-420">Click on the **Select** link.</span></span> <span data-ttu-id="77077-421">Der Zeilenstil verändert sich.</span><span class="sxs-lookup"><span data-stu-id="77077-421">The row style changes.</span></span>

### <a name="add-courses-taught-by-selected-instructor"></a><span data-ttu-id="77077-422">Hinzufügen von Kursen eines ausgewählten Dozenten</span><span class="sxs-lookup"><span data-stu-id="77077-422">Add courses taught by selected instructor</span></span>

<span data-ttu-id="77077-423">Aktualisieren Sie die `OnGetAsync`-Methode in *Pages/Instructors/Index.cshtml.cs* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="77077-423">Update the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

<span data-ttu-id="77077-424">Fügen Sie `public int CourseID { get; set; }` hinzu.</span><span class="sxs-lookup"><span data-stu-id="77077-424">Add `public int CourseID { get; set; }`</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

<span data-ttu-id="77077-425">Überprüfen Sie die aktualisierte Abfrage:</span><span class="sxs-lookup"><span data-stu-id="77077-425">Examine the updated query:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="77077-426">Die vorhergehende Abfrage fügt die `Department`-Entitäten hinzu.</span><span class="sxs-lookup"><span data-stu-id="77077-426">The preceding query adds the `Department` entities.</span></span>

<span data-ttu-id="77077-427">Der folgende Code wird ausgeführt, wenn ein Dozent ausgewählt wird (`id != null`).</span><span class="sxs-lookup"><span data-stu-id="77077-427">The following code executes when an instructor is selected (`id != null`).</span></span> <span data-ttu-id="77077-428">Der ausgewählte Dozent wird aus der Liste der Dozenten im Ansichtsmodell abgerufen.</span><span class="sxs-lookup"><span data-stu-id="77077-428">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="77077-429">Die `Courses`-Eigenschaft des Ansichtsmodells wird mit den `Course`-Entitäten aus der `CourseAssignments`-Navigationseigenschaft dieses Dozenten geladen.</span><span class="sxs-lookup"><span data-stu-id="77077-429">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

<span data-ttu-id="77077-430">Die `Where`-Methode gibt eine Sammlung zurück.</span><span class="sxs-lookup"><span data-stu-id="77077-430">The `Where` method returns a collection.</span></span> <span data-ttu-id="77077-431">In der vorherigen `Where`-Methode wird nur eine einzige `Instructor`-Entität zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="77077-431">In the preceding `Where` method, only a single `Instructor` entity is returned.</span></span> <span data-ttu-id="77077-432">Die `Single`-Methode konvertiert die Sammlung in eine einzelne `Instructor`-Entität.</span><span class="sxs-lookup"><span data-stu-id="77077-432">The `Single` method converts the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="77077-433">Die `Instructor`-Entität ermöglicht Zugriff auf die `CourseAssignments`-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="77077-433">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="77077-434">`CourseAssignments` ermöglicht Zugriff auf die verwandten `Course`-Entitäten.</span><span class="sxs-lookup"><span data-stu-id="77077-434">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![Dozent:Kurse m:N](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="77077-436">Die `Single`-Methode wird für eine Sammlung verwendet, wenn die Sammlung nur ein Element aufweist.</span><span class="sxs-lookup"><span data-stu-id="77077-436">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="77077-437">Die `Single`-Methode löst eine Ausnahme aus, wenn die Sammlung leer ist oder mehr als ein Element vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="77077-437">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="77077-438">Eine Alternative ist `SingleOrDefault`, womit ein Standardwert (in diesem Fall 0 (null)) zurückgegeben wird, wenn die Sammlung leer ist.</span><span class="sxs-lookup"><span data-stu-id="77077-438">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="77077-439">Für eine leere Sammlung wird `SingleOrDefault` verwendet:</span><span class="sxs-lookup"><span data-stu-id="77077-439">Using `SingleOrDefault` on an empty collection:</span></span>

* <span data-ttu-id="77077-440">Löst eine Ausnahme aus (auf der Suche nach einer `Courses`-Eigenschaft eines Nullverweises).</span><span class="sxs-lookup"><span data-stu-id="77077-440">Results in an exception (from trying to find a `Courses` property on a null reference).</span></span>
* <span data-ttu-id="77077-441">Die Ausnahmemeldung würde die Ursache des Problems weniger deutlich angeben.</span><span class="sxs-lookup"><span data-stu-id="77077-441">The exception message would less clearly indicate the cause of the problem.</span></span>

<span data-ttu-id="77077-442">Wenn ein Kurs ausgewählt ist, füllt der folgende Code die `Enrollments`-Eigenschaft des Ansichtsmodells:</span><span class="sxs-lookup"><span data-stu-id="77077-442">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

<span data-ttu-id="77077-443">Fügen Sie das folgende Markup zum Ende der Razor-Seite *Pages/Instructors/Index.cshtml* hinzu:</span><span class="sxs-lookup"><span data-stu-id="77077-443">Add the following markup to the end of the *Pages/Instructors/Index.cshtml* Razor Page:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

<span data-ttu-id="77077-444">Das vorhergehende Markup zeigt eine Liste der Kurse eines bestimmten Dozenten an, wenn einer ausgewählt wird.</span><span class="sxs-lookup"><span data-stu-id="77077-444">The preceding markup displays a list of courses related to an instructor when an instructor is selected.</span></span>

<span data-ttu-id="77077-445">Testen Sie die App.</span><span class="sxs-lookup"><span data-stu-id="77077-445">Test the app.</span></span> <span data-ttu-id="77077-446">Klicken Sie auf den Link **Auswählen** auf der Dozentenseite.</span><span class="sxs-lookup"><span data-stu-id="77077-446">Click on a **Select** link on the instructors page.</span></span>

### <a name="show-student-data"></a><span data-ttu-id="77077-447">Anzeigen der Studentendaten</span><span class="sxs-lookup"><span data-stu-id="77077-447">Show student data</span></span>

<span data-ttu-id="77077-448">In diesem Abschnitt wird die Anwendung aktualisiert, um die Studentendaten für einen ausgewählten Kurs anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="77077-448">In this section, the app is updated to show the student data for a selected course.</span></span>

<span data-ttu-id="77077-449">Aktualisieren Sie die Abfrage in der `OnGetAsync`-Methode in *Pages/Instructors/Index.cshtml.cs* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="77077-449">Update the query in the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="77077-450">Aktualisieren Sie *Pages/Instructors/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="77077-450">Update *Pages/Instructors/Index.cshtml*.</span></span> <span data-ttu-id="77077-451">Fügen Sie dem Dateiende das folgende Markup hinzu:</span><span class="sxs-lookup"><span data-stu-id="77077-451">Add the following markup to the end of the file:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

<span data-ttu-id="77077-452">Das vorhergehende Markup zeigt eine Liste der Studenten, die im ausgewählten Kurs registriert sind.</span><span class="sxs-lookup"><span data-stu-id="77077-452">The preceding markup displays a list of the students who are enrolled in the selected course.</span></span>

<span data-ttu-id="77077-453">Aktualisieren Sie die Seite. Wählen Sie einen Dozenten aus.</span><span class="sxs-lookup"><span data-stu-id="77077-453">Refresh the page and select an instructor.</span></span> <span data-ttu-id="77077-454">Wählen Sie einen Kurs aus, um die Liste der registrierten Studenten und deren Noten einzusehen.</span><span class="sxs-lookup"><span data-stu-id="77077-454">Select a course to see the list of enrolled students and their grades.</span></span>

![Indexseite „Dozenten“, Dozent und Kurs ausgewählt](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a><span data-ttu-id="77077-456">Verwenden von „Single“</span><span class="sxs-lookup"><span data-stu-id="77077-456">Using Single</span></span>

<span data-ttu-id="77077-457">Die `Single`-Methode kann die `Where`-Bedingung übergehen, anstatt die `Where`-Methode separat aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="77077-457">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="77077-458">Der vorangehende `Single`-Ansatz bietet keine Vorteile gegenüber `Where`.</span><span class="sxs-lookup"><span data-stu-id="77077-458">The preceding `Single` approach provides no benefits over using `Where`.</span></span> <span data-ttu-id="77077-459">Einige Entwickler bevorzugen einfach den `Single`-Ansatz.</span><span class="sxs-lookup"><span data-stu-id="77077-459">Some developers prefer the `Single` approach style.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="77077-460">Explizites Laden</span><span class="sxs-lookup"><span data-stu-id="77077-460">Explicit loading</span></span>

<span data-ttu-id="77077-461">Der aktuelle Code gibt Eager Loading für `Enrollments` und `Students` an:</span><span class="sxs-lookup"><span data-stu-id="77077-461">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="77077-462">Angenommen, Benutzer möchten Registrierungen für einen Kurs nur selten anzeigen lassen.</span><span class="sxs-lookup"><span data-stu-id="77077-462">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="77077-463">In diesem Fall wäre es eine Optimierung, die Registrierungsdaten nur dann zu laden, wenn diese angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="77077-463">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="77077-464">In diesem Abschnitt wird die `OnGetAsync` aktualisiert, um das explizite Laden von `Enrollments` und `Students` zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="77077-464">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="77077-465">Aktualisieren Sie `OnGetAsync` mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="77077-465">Update the `OnGetAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

<span data-ttu-id="77077-466">Der vorangehende Code löscht die *ThenInclude*-Methodenaufrufe für Registrierung und Studentendaten.</span><span class="sxs-lookup"><span data-stu-id="77077-466">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="77077-467">Wenn ein Kurs ausgewählt ist, ruft der hervorgehobene Code Folgendes ab:</span><span class="sxs-lookup"><span data-stu-id="77077-467">If a course is selected, the highlighted code retrieves:</span></span>

* <span data-ttu-id="77077-468">Die `Enrollment`-Entitäten für den ausgewählten Kurs.</span><span class="sxs-lookup"><span data-stu-id="77077-468">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="77077-469">Die `Student`-Entitäten für jede `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="77077-469">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="77077-470">Beachten Sie, dass der vorherige Code `.AsNoTracking()` auskommentiert.</span><span class="sxs-lookup"><span data-stu-id="77077-470">Notice the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="77077-471">Navigationseigenschaften können nur für nachverfolgte Entitäten explizit geladen werden.</span><span class="sxs-lookup"><span data-stu-id="77077-471">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="77077-472">Testen Sie die App.</span><span class="sxs-lookup"><span data-stu-id="77077-472">Test the app.</span></span> <span data-ttu-id="77077-473">Aus Benutzersicht verhält sich die Anwendung identisch mit der vorherigen Version.</span><span class="sxs-lookup"><span data-stu-id="77077-473">From a users perspective, the app behaves identically to the previous version.</span></span>

<span data-ttu-id="77077-474">Das nächste Tutorial zeigt, wie verwandte Daten aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="77077-474">The next tutorial shows how to update related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="77077-475">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="77077-475">Additional resources</span></span>

* [<span data-ttu-id="77077-476">Dieses Tutorial auf YouTube (Teil 1)</span><span class="sxs-lookup"><span data-stu-id="77077-476">YouTube version of this tutorial (part1)</span></span>](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [<span data-ttu-id="77077-477">Dieses Tutorial auf YouTube (Teil 2)</span><span class="sxs-lookup"><span data-stu-id="77077-477">YouTube version of this tutorial (part2)</span></span>](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
><span data-ttu-id="77077-478">[Zurück](xref:data/ef-rp/complex-data-model)
>[Weiter](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="77077-478">[Previous](xref:data/ef-rp/complex-data-model)
[Next](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end

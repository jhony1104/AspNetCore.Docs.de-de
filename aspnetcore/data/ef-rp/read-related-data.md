---
title: 'Razor-Seiten mit EF Core in ASP.NET Core: Lesen verwandter Daten (6 von 8)'
author: rick-anderson
description: In diesem Tutorial werden verwandte Daten gelesen und angezeigt. Das gilt für Daten, die Entity Framework in Navigationseigenschaften lädt.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
uid: data/ef-rp/read-related-data
ms.openlocfilehash: a4864a749ed9c839c2a22f179759a3f28db525ee
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68914218"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---read-related-data---6-of-8"></a><span data-ttu-id="b013e-103">Razor-Seiten mit EF Core in ASP.NET Core: Lesen verwandter Daten (6 von 8)</span><span class="sxs-lookup"><span data-stu-id="b013e-103">Razor Pages with EF Core in ASP.NET Core - Read Related Data - 6 of 8</span></span>

<span data-ttu-id="b013e-104">Von [Tom Dykstra](https://github.com/tdykstra), [Jon P. Smith](https://twitter.com/thereformedprog) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b013e-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b013e-105">Dieses Tutorial zeigt, wie verwandte Daten gelesen und angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="b013e-105">This tutorial shows how to read and display related data.</span></span> <span data-ttu-id="b013e-106">Verwandte Daten sind Daten, die von EF Core in die Navigationseigenschaften geladen werden.</span><span class="sxs-lookup"><span data-stu-id="b013e-106">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="b013e-107">Die folgenden Abbildungen zeigen die abgeschlossenen Seiten für dieses Tutorial:</span><span class="sxs-lookup"><span data-stu-id="b013e-107">The following illustrations show the completed pages for this tutorial:</span></span>

![Indexseite „Kurse“](read-related-data/_static/courses-index30.png)

![Indexseite „Dozenten“](read-related-data/_static/instructors-index30.png)

## <a name="eager-explicit-and-lazy-loading"></a><span data-ttu-id="b013e-110">Explizites Laden, Eager Loading und Lazy Loading</span><span class="sxs-lookup"><span data-stu-id="b013e-110">Eager, explicit, and lazy loading</span></span>

<span data-ttu-id="b013e-111">Es gibt mehrere Möglichkeiten, mit denen EF Core verwandte Daten in die Navigationseigenschaften einer Entität laden kann:</span><span class="sxs-lookup"><span data-stu-id="b013e-111">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="b013e-112">[Eager Loading (vorzeitiges Laden)](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="b013e-112">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="b013e-113">Beim Eager Loading lädt eine Abfrage für einen Entitätstyp auch verwandte Entitäten.</span><span class="sxs-lookup"><span data-stu-id="b013e-113">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="b013e-114">Wenn eine Entität gelesen wird, werden ihre zugehörigen Daten abgerufen.</span><span class="sxs-lookup"><span data-stu-id="b013e-114">When an entity is read, its related data is retrieved.</span></span> <span data-ttu-id="b013e-115">Dies führt normalerweise zu einer einzelnen Joinabfrage, die alle Daten abruft, die erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="b013e-115">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="b013e-116">EF Core wird mehrere Abfragen für einige Typen von Eager Loading ausgeben.</span><span class="sxs-lookup"><span data-stu-id="b013e-116">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="b013e-117">Das Ausgeben mehrerer Abfragen kann effizienter sein als eine einzelne große Abfrage.</span><span class="sxs-lookup"><span data-stu-id="b013e-117">Issuing multiple queries can be more efficient than a giant single query.</span></span> <span data-ttu-id="b013e-118">Eager Loading wird mit den `Include`- und `ThenInclude`-Methoden angegeben.</span><span class="sxs-lookup"><span data-stu-id="b013e-118">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Beispiel für Eager Loading](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="b013e-120">Eager Loading sendet mehrere Abfragen, wenn eine Sammlungsnavigation enthalten ist:</span><span class="sxs-lookup"><span data-stu-id="b013e-120">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="b013e-121">Eine Abfrage für die Hauptabfrage</span><span class="sxs-lookup"><span data-stu-id="b013e-121">One query for the main query</span></span> 
  * <span data-ttu-id="b013e-122">Eine Abfrage für jeden „Sammlungsrand“ in der Ladestruktur.</span><span class="sxs-lookup"><span data-stu-id="b013e-122">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="b013e-123">Beispiel für separate Abfragen mit `Load`: Die Daten können in separaten Abfragen abgerufen werden. EF Core korrigiert die Navigationseigenschaften.</span><span class="sxs-lookup"><span data-stu-id="b013e-123">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="b013e-124">„Korrigieren“ bedeutet, dass EF Core die Navigationseigenschaften automatisch mit Daten auffüllt.</span><span class="sxs-lookup"><span data-stu-id="b013e-124">"Fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="b013e-125">Separate Abfragen mit `Load` ähneln mehr dem expliziten Laden als dem Eager Loading.</span><span class="sxs-lookup"><span data-stu-id="b013e-125">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![Beispiel für separate Abfragen](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="b013e-127">Hinweis: EF Core korrigiert automatisch Navigationseigenschaften für alle anderen Entitäten, die zuvor in die Kontextinstanz geladen wurden.</span><span class="sxs-lookup"><span data-stu-id="b013e-127">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="b013e-128">Auch wenn die Daten für eine Navigationseigenschaft *nicht* explizit eingeschlossen sind, kann die Eigenschaft immer noch aufgefüllt werden, wenn einige oder alle verwandten Entitäten zuvor geladen wurden.</span><span class="sxs-lookup"><span data-stu-id="b013e-128">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="b013e-129">[Explizites Laden](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="b013e-129">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="b013e-130">Wenn die Entität zuerst gelesen wird, werden verwandte Daten nicht abgerufen.</span><span class="sxs-lookup"><span data-stu-id="b013e-130">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="b013e-131">Es muss Code geschrieben werden, um die verwandten Daten bei Bedarf abzurufen.</span><span class="sxs-lookup"><span data-stu-id="b013e-131">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="b013e-132">Explizites Laden mit separaten Abfragen führt zu mehreren Abfragen, die an die Datenbank gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="b013e-132">Explicit loading with separate queries results in multiple queries sent to the database.</span></span> <span data-ttu-id="b013e-133">Mit explizitem Laden gibt der Code die zu ladenden Navigationseigenschaften an.</span><span class="sxs-lookup"><span data-stu-id="b013e-133">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="b013e-134">Verwenden Sie für explizites Laden die `Load`-Methode.</span><span class="sxs-lookup"><span data-stu-id="b013e-134">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="b013e-135">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="b013e-135">For example:</span></span>

  ![Beispiel für explizites Laden](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="b013e-137">[Lazy Loading (verzögertes Laden)](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="b013e-137">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="b013e-138">[Lazy Loading wurde in Version 2.1 zu EF Core hinzugefügt](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="b013e-138">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="b013e-139">Wenn die Entität zuerst gelesen wird, werden verwandte Daten nicht abgerufen.</span><span class="sxs-lookup"><span data-stu-id="b013e-139">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="b013e-140">Wenn zum ersten Mal auf eine Navigationseigenschaft zugegriffen wird, werden die für diese Navigationseigenschaft erforderlichen Daten automatisch abgerufen.</span><span class="sxs-lookup"><span data-stu-id="b013e-140">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="b013e-141">Wenn zum ersten Mal auf eine Navigationseigenschaft zugegriffen wird, wird jedes Mal eine Abfrage an die Datenbank gesendet.</span><span class="sxs-lookup"><span data-stu-id="b013e-141">A query is sent to the database each time a navigation property is accessed for the first time.</span></span>

## <a name="create-course-pages"></a><span data-ttu-id="b013e-142">Erstellen von Course-Seiten</span><span class="sxs-lookup"><span data-stu-id="b013e-142">Create Course pages</span></span>

<span data-ttu-id="b013e-143">Die `Course`-Entität enthält eine Navigationseigenschaft, die die zugehörige `Department`-Entität enthält.</span><span class="sxs-lookup"><span data-stu-id="b013e-143">The `Course` entity includes a navigation property that contains the related `Department` entity.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<span data-ttu-id="b013e-145">So zeigen Sie den Namen des zugewiesenen Fachbereichs für einen Kurs an:</span><span class="sxs-lookup"><span data-stu-id="b013e-145">To display the name of the assigned department for a course:</span></span>

* <span data-ttu-id="b013e-146">Laden Sie die zugehörige `Department`-Entität in die `Course.Department`-Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="b013e-146">Load the related `Department` entity into the `Course.Department` navigation property.</span></span>
* <span data-ttu-id="b013e-147">Rufen Sie den Namen aus der `Name`-Eigenschaft der `Department`-Entität ab.</span><span class="sxs-lookup"><span data-stu-id="b013e-147">Get the name from the `Department` entity's `Name` property.</span></span>

<a name="scaffold"></a>

### <a name="scaffold-course-pages"></a><span data-ttu-id="b013e-148">Gerüstbau von Course-Seiten</span><span class="sxs-lookup"><span data-stu-id="b013e-148">Scaffold Course pages</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b013e-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b013e-149">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b013e-150">Befolgen Sie die Anweisungen unter [Gerüstbau der Student-Seiten](xref:data/ef-rp/intro#scaffold-student-pages) mit den folgenden Ausnahmen:</span><span class="sxs-lookup"><span data-stu-id="b013e-150">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="b013e-151">Erstellen Sie einen Ordner *Pages/Courses*.</span><span class="sxs-lookup"><span data-stu-id="b013e-151">Create a *Pages/Courses* folder.</span></span>
  * <span data-ttu-id="b013e-152">Verwenden Sie `Course` als Modellklasse.</span><span class="sxs-lookup"><span data-stu-id="b013e-152">Use `Course` for the model class.</span></span>
  * <span data-ttu-id="b013e-153">Verwenden Sie die vorhandene Kontextklasse, anstatt eine neue Klasse zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="b013e-153">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b013e-154">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b013e-154">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b013e-155">Erstellen Sie einen Ordner *Pages/Courses*.</span><span class="sxs-lookup"><span data-stu-id="b013e-155">Create a *Pages/Courses* folder.</span></span>

* <span data-ttu-id="b013e-156">Führen Sie den folgenden Befehl aus, um das Gerüst für Course-Seiten zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="b013e-156">Run the following command to scaffold the Course pages.</span></span>

  <span data-ttu-id="b013e-157">**Unter Windows:**</span><span class="sxs-lookup"><span data-stu-id="b013e-157">**On Windows:**</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

  <span data-ttu-id="b013e-158">**Unter Linux oder macOS:**</span><span class="sxs-lookup"><span data-stu-id="b013e-158">**On Linux or macOS:**</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages/Courses --referenceScriptLibraries
  ```

---

* <span data-ttu-id="b013e-159">Öffnen Sie *Pages/Courses/Index.cshtml.cs*. Untersuchen Sie die `OnGetAsync`-Methode.</span><span class="sxs-lookup"><span data-stu-id="b013e-159">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="b013e-160">Die Gerüstbauengine gibt Eager Loading für die `Department`-Navigationseigenschaft an.</span><span class="sxs-lookup"><span data-stu-id="b013e-160">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="b013e-161">Die `Include`-Methode gibt Eager Loading an.</span><span class="sxs-lookup"><span data-stu-id="b013e-161">The `Include` method specifies eager loading.</span></span>

* <span data-ttu-id="b013e-162">Führen Sie die Anwendung aus, und klicken Sie auf den Link **Kurse**.</span><span class="sxs-lookup"><span data-stu-id="b013e-162">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="b013e-163">Die Abteilungsspalte zeigt die `DepartmentID` an, die nicht hilfreich ist.</span><span class="sxs-lookup"><span data-stu-id="b013e-163">The department column displays the `DepartmentID`, which isn't useful.</span></span>

### <a name="display-the-department-name"></a><span data-ttu-id="b013e-164">Anzeigen des Fachbereichnamens</span><span class="sxs-lookup"><span data-stu-id="b013e-164">Display the department name</span></span>

<span data-ttu-id="b013e-165">Aktualisieren Sie „Pages/Courses/Index.cshtml.cs“ mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="b013e-165">Update Pages/Courses/Index.cshtml.cs with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Index.cshtml.cs?highlight=18,22,24)]

<span data-ttu-id="b013e-166">Der vorstehende Code ändert die `Course`-Eigenschaft in `Courses` und fügt `AsNoTracking` hinzu.</span><span class="sxs-lookup"><span data-stu-id="b013e-166">The preceding code changes the `Course` property to `Courses` and adds `AsNoTracking`.</span></span> <span data-ttu-id="b013e-167">`AsNoTracking` verbessert die Leistung, da die zurückgegebenen Entitäten nicht nachverfolgt werden.</span><span class="sxs-lookup"><span data-stu-id="b013e-167">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="b013e-168">Die Entitäten müssen nicht nachverfolgt werden, da sie nicht im aktuellen Kontext aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="b013e-168">The entities don't need to be tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="b013e-169">Aktualisieren Sie *Pages/Courses/Index.cshtml* mit dem folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="b013e-169">Update *Pages/Courses/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Index.cshtml?highlight=5,8,16-18,20,23,26,32,35-37,45)]

<span data-ttu-id="b013e-170">Die folgenden Änderungen wurden am Codegerüst vorgenommen:</span><span class="sxs-lookup"><span data-stu-id="b013e-170">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="b013e-171">Der `Course`-Eigenschaftenname wurde in `Courses` geändert.</span><span class="sxs-lookup"><span data-stu-id="b013e-171">Changed the `Course` property name to `Courses`.</span></span>
* <span data-ttu-id="b013e-172">Die Spalte **Anzahl** wurde hinzugefügt. Sie zeigt den `CourseID`-Eigenschaftswert an.</span><span class="sxs-lookup"><span data-stu-id="b013e-172">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="b013e-173">Primärschlüssel werden nicht standardmäßig eingerüstet, da sie normalerweise ohne Bedeutung für Endbenutzer sind.</span><span class="sxs-lookup"><span data-stu-id="b013e-173">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="b013e-174">Allerdings hat der Primärschlüssel in diesem Fall jedoch Bedeutung.</span><span class="sxs-lookup"><span data-stu-id="b013e-174">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="b013e-175">Die Spalte **Abteilung** wurde geändert, sodass sie jetzt den Namen der Abteilung anzeigt.</span><span class="sxs-lookup"><span data-stu-id="b013e-175">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="b013e-176">Der Code zeigt die `Name`-Eigenschaft der `Department`-Entität an, die in die `Department`-Navigationseigenschaft geladen wird:</span><span class="sxs-lookup"><span data-stu-id="b013e-176">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="b013e-177">Führen Sie die Anwendung aus. Wählen Sie die Registerkarte **Kurse** aus, um die Liste mit den Abteilungsnamen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="b013e-177">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Indexseite „Kurse“](read-related-data/_static/courses-index30.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="b013e-179">Laden verwandter Daten mit „Select“</span><span class="sxs-lookup"><span data-stu-id="b013e-179">Loading related data with Select</span></span>

<span data-ttu-id="b013e-180">Die `OnGetAsync`-Methode lädt zugehörige Daten mit der `Include`-Methode.</span><span class="sxs-lookup"><span data-stu-id="b013e-180">The `OnGetAsync` method loads related data with the `Include` method.</span></span> <span data-ttu-id="b013e-181">Die `Select`-Methode ist eine Alternative, die nur die zugehörigen benötigten Daten lädt.</span><span class="sxs-lookup"><span data-stu-id="b013e-181">The `Select` method is an alternative that loads only the related data needed.</span></span> <span data-ttu-id="b013e-182">Für die einzelnen Elemente wie `Department.Name` wird ein INNER JOIN von SQL verwendet.</span><span class="sxs-lookup"><span data-stu-id="b013e-182">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="b013e-183">Für Sammlungen wird ein anderer Zugriff auf die Datenbank verwendet, aber Gleiches gilt für den `Include`-Operator für Sammlungen.</span><span class="sxs-lookup"><span data-stu-id="b013e-183">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="b013e-184">Der folgende Code lädt verwandte Daten mit der `Select`-Methode:</span><span class="sxs-lookup"><span data-stu-id="b013e-184">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=6)]

<span data-ttu-id="b013e-185">Die `CourseViewModel`:</span><span class="sxs-lookup"><span data-stu-id="b013e-185">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="b013e-186">Ein vollständiges Beispiel finden Sie unter [IndexSelect.cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) und [IndexSelect.cshtml.cs](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs).</span><span class="sxs-lookup"><span data-stu-id="b013e-186">See [IndexSelect.cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-instructor-pages"></a><span data-ttu-id="b013e-187">Erstellen von Instructor-Seiten</span><span class="sxs-lookup"><span data-stu-id="b013e-187">Create Instructor pages</span></span>

<span data-ttu-id="b013e-188">In diesem Abschnitt erfolgt der Gerüstbau von Instructor-Seiten, und der Index-Seite des Dozenten werden zugehörige Courses und Enrollments hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="b013e-188">This section scaffolds Instructor pages and adds related Courses and Enrollments to the Instructors Index page.</span></span>

<a name="IP"></a>
<span data-ttu-id="b013e-189">![Indexseite „Dozenten“](read-related-data/_static/instructors-index30.png)</span><span class="sxs-lookup"><span data-stu-id="b013e-189">![Instructors Index page](read-related-data/_static/instructors-index30.png)</span></span>

<span data-ttu-id="b013e-190">Auf dieser Seite werden verwandte Daten auf folgende Weise gelesen und angezeigt:</span><span class="sxs-lookup"><span data-stu-id="b013e-190">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="b013e-191">Die Liste der Dozenten zeigt verwandte Daten aus der `OfficeAssignment`-Entität (Office in der vorherigen Abbildung).</span><span class="sxs-lookup"><span data-stu-id="b013e-191">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="b013e-192">Die `Instructor`- und `OfficeAssignment`-Entitäten stehen in einer 1:0..1-Beziehung zueinander.</span><span class="sxs-lookup"><span data-stu-id="b013e-192">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="b013e-193">Eager Loading wird für die `OfficeAssignment`-Entitäten verwendet.</span><span class="sxs-lookup"><span data-stu-id="b013e-193">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="b013e-194">Eager Loading ist in der Regel effizienter, wenn die verwandten Daten angezeigt werden müssen.</span><span class="sxs-lookup"><span data-stu-id="b013e-194">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="b013e-195">In diesem Fall werden die Office-Zuweisungen für die Dozenten angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b013e-195">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="b013e-196">Wenn der Benutzer einen Dozenten auswählt, werden zugehörige `Course`-Entitäten angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b013e-196">When the user selects an instructor, related `Course` entities are displayed.</span></span> <span data-ttu-id="b013e-197">Die `Instructor`- und `Course`-Entitäten stehen in einer m:n-Beziehung zueinander.</span><span class="sxs-lookup"><span data-stu-id="b013e-197">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="b013e-198">Für die `Course`-Entitäten und ihre verwandten `Department`-Entitäten wird das Eager Loading verwendet.</span><span class="sxs-lookup"><span data-stu-id="b013e-198">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="b013e-199">In diesem Fall können separate Abfragen effizienter sein, da nur Kurse für den ausgewählten Dozenten benötigt werden.</span><span class="sxs-lookup"><span data-stu-id="b013e-199">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="b013e-200">Dieses Beispiel zeigt, wie Eager Loading für Navigationseigenschaften in Entitäten in den Navigationseigenschaften verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="b013e-200">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="b013e-201">Wenn der Benutzer einen Kurs auswählt, werden zugehörige Daten aus der `Enrollments`-Entität angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b013e-201">When the user selects a course, related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="b013e-202">In der vorherigen Abbildung sind der Name des Studenten und die Note angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b013e-202">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="b013e-203">Die `Course`- und `Enrollment`-Entitäten stehen in einer 1:n-Beziehung zueinander.</span><span class="sxs-lookup"><span data-stu-id="b013e-203">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model"></a><span data-ttu-id="b013e-204">Erstellen von Anzeigemodellen</span><span class="sxs-lookup"><span data-stu-id="b013e-204">Create a view model</span></span>

<span data-ttu-id="b013e-205">Die Dozentenseite zeigt Daten aus drei verschiedenen Tabellen.</span><span class="sxs-lookup"><span data-stu-id="b013e-205">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="b013e-206">Ein Ansichtsmodell ist erforderlich, das drei Eigenschaften enthält, die die drei Tabellen darstellen.</span><span class="sxs-lookup"><span data-stu-id="b013e-206">A view model is needed that includes three properties representing the three tables.</span></span>

<span data-ttu-id="b013e-207">Erstellen *SchoolViewModels/InstructorIndexData.cs* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="b013e-207">Create *SchoolViewModels/InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-instructor-pages"></a><span data-ttu-id="b013e-208">Gerüstbau von Instructor-Seiten</span><span class="sxs-lookup"><span data-stu-id="b013e-208">Scaffold Instructor pages</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b013e-209">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b013e-209">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b013e-210">Befolgen Sie die Anweisungen unter [Gerüstbau der Student-Seiten](xref:data/ef-rp/intro#scaffold-student-pages) mit den folgenden Ausnahmen:</span><span class="sxs-lookup"><span data-stu-id="b013e-210">Follow the instructions in [Scaffold the student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="b013e-211">Erstellen Sie einen Ordner *Pages/Instructors*.</span><span class="sxs-lookup"><span data-stu-id="b013e-211">Create a *Pages/Instructors* folder.</span></span>
  * <span data-ttu-id="b013e-212">Verwenden Sie `Instructor` als Modellklasse.</span><span class="sxs-lookup"><span data-stu-id="b013e-212">Use `Instructor` for the model class.</span></span>
  * <span data-ttu-id="b013e-213">Verwenden Sie die vorhandene Kontextklasse, anstatt eine neue Klasse zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="b013e-213">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b013e-214">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b013e-214">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b013e-215">Erstellen Sie einen Ordner *Pages/Instructors*.</span><span class="sxs-lookup"><span data-stu-id="b013e-215">Create a *Pages/Instructors* folder.</span></span>

* <span data-ttu-id="b013e-216">Führen Sie den folgenden Befehl aus, um das Gerüst für Instructor-Seiten zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="b013e-216">Run the following command to scaffold the Instructor pages.</span></span>

  <span data-ttu-id="b013e-217">**Unter Windows:**</span><span class="sxs-lookup"><span data-stu-id="b013e-217">**On Windows:**</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

  <span data-ttu-id="b013e-218">**Unter Linux oder macOS:**</span><span class="sxs-lookup"><span data-stu-id="b013e-218">**On Linux or macOS:**</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages/Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="b013e-219">Führen Sie die App aus, und navigieren Sie zur Seite Instructors, um zu sehen, wie die Gerüstbauseite aussieht, bevor Sie sie aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="b013e-219">To see what the scaffolded page looks like before you update it, run the app and navigate to the Instructors page.</span></span>

<span data-ttu-id="b013e-220">Aktualisieren Sie *Pages/Instructors/Index.cshtml.cs* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="b013e-220">Update *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,19-53)]

<span data-ttu-id="b013e-221">Die `OnGetAsync`-Methode akzeptiert optional Routendaten für die ID des ausgewählten Dozenten.</span><span class="sxs-lookup"><span data-stu-id="b013e-221">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="b013e-222">Untersuchen Sie die Abfrage in der Datei *Pages/Instructors/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="b013e-222">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading)]

<span data-ttu-id="b013e-223">Der Code gibt Eager Loading für die folgenden Navigationseigenschaften an:</span><span class="sxs-lookup"><span data-stu-id="b013e-223">The code specifies eager loading for the following navigation properties:</span></span>

* `Instructor.OfficeAssignment`
* `Instructor.CourseAssignments`
  * `CourseAssignments.Course`
    * `Course.Department`
    * `Course.Enrollments`
      * `Enrollment.Student`

<span data-ttu-id="b013e-224">Beachten Sie die Wiederholung der `Include`- und `ThenInclude`-Methoden für `CourseAssignments` und `Course`.</span><span class="sxs-lookup"><span data-stu-id="b013e-224">Notice the repetition of `Include` and `ThenInclude` methods for `CourseAssignments` and `Course`.</span></span> <span data-ttu-id="b013e-225">Diese Wiederholung ist erforderlich, um Eager Loading für zwei Navigationseigenschaften der `Course`-Entität anzugeben.</span><span class="sxs-lookup"><span data-stu-id="b013e-225">This repetition is necessary to specify eager loading for two navigation properties of the `Course` entity.</span></span>

<span data-ttu-id="b013e-226">Der folgende Code wird ausgeführt, wenn ein Dozent ausgewählt wird (`id != null`).</span><span class="sxs-lookup"><span data-stu-id="b013e-226">The following code executes when an instructor is selected (`id != null`).</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectInstructor)]

<span data-ttu-id="b013e-227">Der ausgewählte Dozent wird aus der Liste der Dozenten im Ansichtsmodell abgerufen.</span><span class="sxs-lookup"><span data-stu-id="b013e-227">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="b013e-228">Die `Courses`-Eigenschaft des Ansichtsmodells wird mit den `Course`-Entitäten aus der `CourseAssignments`-Navigationseigenschaft dieses Dozenten geladen.</span><span class="sxs-lookup"><span data-stu-id="b013e-228">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

<span data-ttu-id="b013e-229">Die `Where`-Methode gibt eine Sammlung zurück.</span><span class="sxs-lookup"><span data-stu-id="b013e-229">The `Where` method returns a collection.</span></span> <span data-ttu-id="b013e-230">In diesem Fall wählt der Filter jedoch eine einzelne Entität aus.</span><span class="sxs-lookup"><span data-stu-id="b013e-230">But in this case, the filter will select a single entity.</span></span> <span data-ttu-id="b013e-231">Daher wird die `Single`-Methode aufgerufen, um die Sammlung in eine einzelne `Instructor`-Entität zu konvertieren.</span><span class="sxs-lookup"><span data-stu-id="b013e-231">so the `Single` method is called to convert the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="b013e-232">Die `Instructor`-Entität ermöglicht Zugriff auf die `CourseAssignments`-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="b013e-232">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="b013e-233">`CourseAssignments` ermöglicht Zugriff auf die verwandten `Course`-Entitäten.</span><span class="sxs-lookup"><span data-stu-id="b013e-233">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![Dozent:Kurse m:N](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="b013e-235">Die `Single`-Methode wird für eine Sammlung verwendet, wenn die Sammlung nur ein Element aufweist.</span><span class="sxs-lookup"><span data-stu-id="b013e-235">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="b013e-236">Die `Single`-Methode löst eine Ausnahme aus, wenn die Sammlung leer ist oder mehr als ein Element vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="b013e-236">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="b013e-237">Eine Alternative ist `SingleOrDefault`, womit ein Standardwert (in diesem Fall 0 (null)) zurückgegeben wird, wenn die Sammlung leer ist.</span><span class="sxs-lookup"><span data-stu-id="b013e-237">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span>

<span data-ttu-id="b013e-238">Wenn ein Kurs ausgewählt ist, füllt der folgende Code die `Enrollments`-Eigenschaft des Ansichtsmodells:</span><span class="sxs-lookup"><span data-stu-id="b013e-238">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectCourse)]

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="b013e-239">Aktualisieren der Indexseite „Dozenten“</span><span class="sxs-lookup"><span data-stu-id="b013e-239">Update the instructors Index page</span></span>

<span data-ttu-id="b013e-240">Aktualisieren Sie *Pages/Instructors/Index.cshtml* mit dem folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="b013e-240">Update *Pages/Instructors/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Index.cshtml?highlight=1,5,8,16-21,25-32,43-57,67-102,104-126)]

<span data-ttu-id="b013e-241">Durch den vorangehenden Code werden folgende Änderungen vorgenommen:</span><span class="sxs-lookup"><span data-stu-id="b013e-241">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="b013e-242">Aktualisiert die `page`-Anweisung von `@page` auf `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="b013e-242">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="b013e-243">`"{id:int?}"` ist eine Routenvorlage.</span><span class="sxs-lookup"><span data-stu-id="b013e-243">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="b013e-244">Die Routenvorlage ändert ganzzahlige Abfragezeichenfolgen in der URL in Routendaten.</span><span class="sxs-lookup"><span data-stu-id="b013e-244">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="b013e-245">Klicken Sie beispielsweise auf den Link **Auswählen** für einen Dozenten, wenn nur die `@page`-Anweisung eine URL wie die folgende erzeugt:</span><span class="sxs-lookup"><span data-stu-id="b013e-245">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `https://localhost:5001/Instructors?id=2`

  <span data-ttu-id="b013e-246">Wenn die Seitenanweisung `@page "{id:int?}"` ist, lautet die URL folgendermaßen:</span><span class="sxs-lookup"><span data-stu-id="b013e-246">When the page directive is `@page "{id:int?}"`, the URL is:</span></span>

  `https://localhost:5001/Instructors/2`

* <span data-ttu-id="b013e-247">Fügt eine **Office**-Spalte hinzu, die `item.OfficeAssignment.Location` nur anzeigt, wenn `item.OfficeAssignment` nicht NULL ist.</span><span class="sxs-lookup"><span data-stu-id="b013e-247">Adds an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="b013e-248">Da dies eine 1:0..1-Beziehung ist, gibt es möglicherweise keine verwandte OfficeAssignment-Entität.</span><span class="sxs-lookup"><span data-stu-id="b013e-248">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="b013e-249">Fügt eine **Courses**-Spalte hinzu, die die Kurse eines jeden Dozenten anzeigt.</span><span class="sxs-lookup"><span data-stu-id="b013e-249">Adds a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="b013e-250">Weitere Informationen über diese Razor-Syntax finden Sie unter [Explizite Zeilenübergänge mit `@:`](xref:mvc/views/razor#explicit-line-transition-with-).</span><span class="sxs-lookup"><span data-stu-id="b013e-250">See [Explicit Line Transition with `@:`](xref:mvc/views/razor#explicit-line-transition-with-) for more about this razor syntax.</span></span>

* <span data-ttu-id="b013e-251">Fügt Code hinzu, der `class="success"` dynamisch zum `tr`-Element des ausgewählten Dozenten und Kurses hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="b013e-251">Adds code that dynamically adds `class="success"` to the `tr` element of the selected instructor and course.</span></span> <span data-ttu-id="b013e-252">Hiermit wird mit einer Bootstrapklasse eine Hintergrundfarbe für die ausgewählte Zeile hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="b013e-252">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="b013e-253">Fügt einen neuen Link mit der Bezeichnung **Select** hinzu.</span><span class="sxs-lookup"><span data-stu-id="b013e-253">Adds a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="b013e-254">Dieser Link sendet die ID des ausgewählten Dozenten an die `Index`-Methode und legt die Hintergrundfarbe fest.</span><span class="sxs-lookup"><span data-stu-id="b013e-254">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

* <span data-ttu-id="b013e-255">Fügt eine Tabelle mit Kursen für den ausgewählten Dozenten hinzu.</span><span class="sxs-lookup"><span data-stu-id="b013e-255">Adds a table of courses for the selected Instructor.</span></span>

* <span data-ttu-id="b013e-256">Fügt eine Tabelle mit Studentenregistrierungen für den ausgewählten Kurs hinzu.</span><span class="sxs-lookup"><span data-stu-id="b013e-256">Adds a table of student enrollments for the selected course.</span></span>

<span data-ttu-id="b013e-257">Führen Sie die Anwendung aus. Klicken Sie auf die Registerkarte **Dozenten**. Die Seite zeigt den `Location` (Office) aus der verwandten `OfficeAssignment`-Entität an.</span><span class="sxs-lookup"><span data-stu-id="b013e-257">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="b013e-258">Wenn `OfficeAssignment` NULL ist, wird eine leere Tabellenzelle angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b013e-258">If `OfficeAssignment` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="b013e-259">Klicken Sie auf den Link **Select** für einen Dozenten.</span><span class="sxs-lookup"><span data-stu-id="b013e-259">Click on the **Select** link for an instructor.</span></span> <span data-ttu-id="b013e-260">Der Zeilenstil ändert sich, und Kurse, die diesem Dozenten zugewiesen sind, werden angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b013e-260">The row style changes and courses assigned to that instructor are displayed.</span></span>

<span data-ttu-id="b013e-261">Wählen Sie einen Kurs aus, um die Liste der registrierten Studenten und deren Noten einzusehen.</span><span class="sxs-lookup"><span data-stu-id="b013e-261">Select a course to see the list of enrolled students and their grades.</span></span>

![Indexseite „Dozenten“, Dozent und Kurs ausgewählt](read-related-data/_static/instructors-index30.png)

## <a name="using-single"></a><span data-ttu-id="b013e-263">Verwenden von „Single“</span><span class="sxs-lookup"><span data-stu-id="b013e-263">Using Single</span></span>

<span data-ttu-id="b013e-264">Die `Single`-Methode kann die `Where`-Bedingung übergehen, anstatt die `Where`-Methode separat aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="b013e-264">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="b013e-265">Die Verwendung von `Single` mit einer Where-Bedingung ist eine Frage der persönlichen Präferenz.</span><span class="sxs-lookup"><span data-stu-id="b013e-265">Use of `Single` with a Where condition is a matter of personal preference.</span></span> <span data-ttu-id="b013e-266">Sie bietet keine Vorteile gegenüber der Verwendung der `Where`-Methode.</span><span class="sxs-lookup"><span data-stu-id="b013e-266">It provides no benefits over using the `Where` method.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="b013e-267">Explizites Laden</span><span class="sxs-lookup"><span data-stu-id="b013e-267">Explicit loading</span></span>

<span data-ttu-id="b013e-268">Der aktuelle Code gibt Eager Loading für `Enrollments` und `Students` an:</span><span class="sxs-lookup"><span data-stu-id="b013e-268">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading&highlight=6-9)]

<span data-ttu-id="b013e-269">Angenommen, Benutzer möchten Registrierungen für einen Kurs nur selten anzeigen lassen.</span><span class="sxs-lookup"><span data-stu-id="b013e-269">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="b013e-270">In diesem Fall wäre es eine Optimierung, die Registrierungsdaten nur dann zu laden, wenn diese angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="b013e-270">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="b013e-271">In diesem Abschnitt wird die `OnGetAsync` aktualisiert, um das explizite Laden von `Enrollments` und `Students` zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="b013e-271">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="b013e-272">Aktualisieren Sie *Pages/Instructors/Index.cshtml.cs* mit dem folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="b013e-272">Update *Pages/Instructors/Index.cshtml.cs* with the following code.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Index.cshtml.cs?highlight=31-35,52-56)]

<span data-ttu-id="b013e-273">Der vorangehende Code löscht die *ThenInclude*-Methodenaufrufe für Registrierung und Studentendaten.</span><span class="sxs-lookup"><span data-stu-id="b013e-273">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="b013e-274">Wenn ein Kurs ausgewählt ist, ruft der explizit geladene Code Folgendes ab:</span><span class="sxs-lookup"><span data-stu-id="b013e-274">If a course is selected, the explicit loading code retrieves:</span></span>

* <span data-ttu-id="b013e-275">Die `Enrollment`-Entitäten für den ausgewählten Kurs.</span><span class="sxs-lookup"><span data-stu-id="b013e-275">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="b013e-276">Die `Student`-Entitäten für jede `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="b013e-276">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="b013e-277">Beachten Sie, dass der vorherige Code `.AsNoTracking()` auskommentiert.</span><span class="sxs-lookup"><span data-stu-id="b013e-277">Notice that the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="b013e-278">Navigationseigenschaften können nur für nachverfolgte Entitäten explizit geladen werden.</span><span class="sxs-lookup"><span data-stu-id="b013e-278">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="b013e-279">Testen Sie die App.</span><span class="sxs-lookup"><span data-stu-id="b013e-279">Test the app.</span></span> <span data-ttu-id="b013e-280">Aus Benutzersicht verhält sich die Anwendung identisch mit der vorherigen Version.</span><span class="sxs-lookup"><span data-stu-id="b013e-280">From a user's perspective, the app behaves identically to the previous version.</span></span>

## <a name="next-steps"></a><span data-ttu-id="b013e-281">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="b013e-281">Next steps</span></span>

<span data-ttu-id="b013e-282">Das nächste Tutorial zeigt, wie verwandte Daten aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="b013e-282">The next tutorial shows how to update related data.</span></span>

>[!div class="step-by-step"]
><span data-ttu-id="b013e-283">[Vorheriges Tutorial](xref:data/ef-rp/complex-data-model)
>[Nächstes Tutorial](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="b013e-283">[Previous tutorial](xref:data/ef-rp/complex-data-model)
[Next tutorial](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b013e-284">In diesem Tutorial werden verwandte Daten gelesen und angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b013e-284">In this tutorial, related data is read and displayed.</span></span> <span data-ttu-id="b013e-285">Verwandte Daten sind Daten, die von EF Core in die Navigationseigenschaften geladen werden.</span><span class="sxs-lookup"><span data-stu-id="b013e-285">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="b013e-286">Wenn nicht zu lösende Probleme auftreten, laden Sie die [fertige App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) herunter, oder zeigen Sie diese an.</span><span class="sxs-lookup"><span data-stu-id="b013e-286">If you run into problems you can't solve, [download or view the completed app.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="b013e-287">[Anweisungen zum Download.](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="b013e-287">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="b013e-288">Die folgenden Abbildungen zeigen die abgeschlossenen Seiten für dieses Tutorial:</span><span class="sxs-lookup"><span data-stu-id="b013e-288">The following illustrations show the completed pages for this tutorial:</span></span>

![Indexseite „Kurse“](read-related-data/_static/courses-index.png)

![Indexseite „Dozenten“](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a><span data-ttu-id="b013e-291">Explizites, Eager und Lazy Loading verwandter Daten</span><span class="sxs-lookup"><span data-stu-id="b013e-291">Eager, explicit, and lazy Loading of related data</span></span>

<span data-ttu-id="b013e-292">Es gibt mehrere Möglichkeiten, mit denen EF Core verwandte Daten in die Navigationseigenschaften einer Entität laden kann:</span><span class="sxs-lookup"><span data-stu-id="b013e-292">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="b013e-293">[Eager Loading (vorzeitiges Laden)](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="b013e-293">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="b013e-294">Beim Eager Loading lädt eine Abfrage für einen Entitätstyp auch verwandte Entitäten.</span><span class="sxs-lookup"><span data-stu-id="b013e-294">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="b013e-295">Wenn die Entität gelesen wird, werden ihre verwandten Daten abgerufen.</span><span class="sxs-lookup"><span data-stu-id="b013e-295">When the entity is read, its related data is retrieved.</span></span> <span data-ttu-id="b013e-296">Dies führt normalerweise zu einer einzelnen Joinabfrage, die alle Daten abruft, die erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="b013e-296">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="b013e-297">EF Core wird mehrere Abfragen für einige Typen von Eager Loading ausgeben.</span><span class="sxs-lookup"><span data-stu-id="b013e-297">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="b013e-298">Die Ausgabe mehrerer Abfragen kann effizienter sein, als dies bei einigen Abfragen in EF6 der Fall war. Dort war nur eine einzelne Abfrage vorhanden.</span><span class="sxs-lookup"><span data-stu-id="b013e-298">Issuing multiple queries can be more efficient than was the case for some queries in EF6 where there was a single query.</span></span> <span data-ttu-id="b013e-299">Eager Loading wird mit den `Include`- und `ThenInclude`-Methoden angegeben.</span><span class="sxs-lookup"><span data-stu-id="b013e-299">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Beispiel für Eager Loading](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="b013e-301">Eager Loading sendet mehrere Abfragen, wenn eine Sammlungsnavigation enthalten ist:</span><span class="sxs-lookup"><span data-stu-id="b013e-301">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="b013e-302">Eine Abfrage für die Hauptabfrage</span><span class="sxs-lookup"><span data-stu-id="b013e-302">One query for the main query</span></span> 
  * <span data-ttu-id="b013e-303">Eine Abfrage für jeden „Sammlungsrand“ in der Ladestruktur.</span><span class="sxs-lookup"><span data-stu-id="b013e-303">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="b013e-304">Beispiel für separate Abfragen mit `Load`: Die Daten können in separaten Abfragen abgerufen werden. EF Core korrigiert die Navigationseigenschaften.</span><span class="sxs-lookup"><span data-stu-id="b013e-304">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="b013e-305">„Korrigieren“ bedeutet, dass EF Core die Navigationseigenschaften automatisch füllt.</span><span class="sxs-lookup"><span data-stu-id="b013e-305">"fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="b013e-306">Separate Abfragen mit `Load` ähneln mehr dem expliziten Laden als dem Eager Loading.</span><span class="sxs-lookup"><span data-stu-id="b013e-306">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![Beispiel für separate Abfragen](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="b013e-308">Hinweis: EF Core korrigiert automatisch Navigationseigenschaften für alle anderen Entitäten, die zuvor in die Kontextinstanz geladen wurden.</span><span class="sxs-lookup"><span data-stu-id="b013e-308">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="b013e-309">Auch wenn die Daten für eine Navigationseigenschaft *nicht* explizit eingeschlossen sind, kann die Eigenschaft immer noch aufgefüllt werden, wenn einige oder alle verwandten Entitäten zuvor geladen wurden.</span><span class="sxs-lookup"><span data-stu-id="b013e-309">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="b013e-310">[Explizites Laden](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="b013e-310">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="b013e-311">Wenn die Entität zuerst gelesen wird, werden verwandte Daten nicht abgerufen.</span><span class="sxs-lookup"><span data-stu-id="b013e-311">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="b013e-312">Es muss Code geschrieben werden, um die verwandten Daten bei Bedarf abzurufen.</span><span class="sxs-lookup"><span data-stu-id="b013e-312">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="b013e-313">Explizites Laden mit separaten Abfragen führt zu mehreren Abfragen, die an die Datenbank gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="b013e-313">Explicit loading with separate queries results in multiple queries sent to the DB.</span></span> <span data-ttu-id="b013e-314">Mit explizitem Laden gibt der Code die zu ladenden Navigationseigenschaften an.</span><span class="sxs-lookup"><span data-stu-id="b013e-314">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="b013e-315">Verwenden Sie für explizites Laden die `Load`-Methode.</span><span class="sxs-lookup"><span data-stu-id="b013e-315">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="b013e-316">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="b013e-316">For example:</span></span>

  ![Beispiel für explizites Laden](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="b013e-318">[Lazy Loading (verzögertes Laden)](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="b013e-318">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="b013e-319">[Lazy Loading wurde in Version 2.1 zu EF Core hinzugefügt](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="b013e-319">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="b013e-320">Wenn die Entität zuerst gelesen wird, werden verwandte Daten nicht abgerufen.</span><span class="sxs-lookup"><span data-stu-id="b013e-320">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="b013e-321">Wenn zum ersten Mal auf eine Navigationseigenschaft zugegriffen wird, werden die für diese Navigationseigenschaft erforderlichen Daten automatisch abgerufen.</span><span class="sxs-lookup"><span data-stu-id="b013e-321">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="b013e-322">Wenn zum ersten Mal auf eine Navigationseigenschaft zugegriffen wird, wird jedes Mal eine Abfrage an die Datenbank geschickt.</span><span class="sxs-lookup"><span data-stu-id="b013e-322">A query is sent to the DB each time a navigation property is accessed for the first time.</span></span>

* <span data-ttu-id="b013e-323">Der `Select`-Operator lädt nur die erforderlichen verwandten Daten.</span><span class="sxs-lookup"><span data-stu-id="b013e-323">The `Select` operator loads only the related data needed.</span></span>

## <a name="create-a-course-page-that-displays-department-name"></a><span data-ttu-id="b013e-324">Erstellen einer Kursseite, die den Abteilungsnamen anzeigt</span><span class="sxs-lookup"><span data-stu-id="b013e-324">Create a Course page that displays department name</span></span>

<span data-ttu-id="b013e-325">Die Course-Entität enthält eine Navigationseigenschaft, welche die `Department`-Entität enthält.</span><span class="sxs-lookup"><span data-stu-id="b013e-325">The Course entity includes a navigation property that contains the `Department` entity.</span></span> <span data-ttu-id="b013e-326">Die `Department`-Entität enthält die Abteilung, der der Kurs zugewiesen ist.</span><span class="sxs-lookup"><span data-stu-id="b013e-326">The `Department` entity contains the department that the course is assigned to.</span></span>

<span data-ttu-id="b013e-327">So zeigen Sie den Namen der zugewiesenen Abteilung in einer Kursliste an:</span><span class="sxs-lookup"><span data-stu-id="b013e-327">To display the name of the assigned department in a list of courses:</span></span>

* <span data-ttu-id="b013e-328">Rufen Sie `Name`-Eigenschaft aus der `Department`-Entität ab.</span><span class="sxs-lookup"><span data-stu-id="b013e-328">Get the `Name` property from the `Department` entity.</span></span>
* <span data-ttu-id="b013e-329">Die `Department`-Entität stammt aus der `Course.Department`-Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="b013e-329">The `Department` entity comes from the `Course.Department` navigation property.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a><span data-ttu-id="b013e-331">Erstellen des Gerüsts für das Kursmodell</span><span class="sxs-lookup"><span data-stu-id="b013e-331">Scaffold the Course model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b013e-332">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b013e-332">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="b013e-333">Führen Sie die Schritte unter [Erstellen des Gerüsts für das Studentenmodell](xref:data/ef-rp/intro#scaffold-student-pages) aus, und verwenden Sie `Course` für die Modellklasse.</span><span class="sxs-lookup"><span data-stu-id="b013e-333">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-student-pages) and use `Course` for the model class.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b013e-334">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b013e-334">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="b013e-335">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="b013e-335">Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

<span data-ttu-id="b013e-336">Der vorherige Befehl erstellt ein Gerüst für das `Course`-Modell.</span><span class="sxs-lookup"><span data-stu-id="b013e-336">The preceding command scaffolds the `Course` model.</span></span> <span data-ttu-id="b013e-337">Öffnen Sie das Projekt in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b013e-337">Open the project in Visual Studio.</span></span>

<span data-ttu-id="b013e-338">Öffnen Sie *Pages/Courses/Index.cshtml.cs*. Untersuchen Sie die `OnGetAsync`-Methode.</span><span class="sxs-lookup"><span data-stu-id="b013e-338">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="b013e-339">Die Gerüstbauengine gibt Eager Loading für die `Department`-Navigationseigenschaft an.</span><span class="sxs-lookup"><span data-stu-id="b013e-339">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="b013e-340">Die `Include`-Methode gibt Eager Loading an.</span><span class="sxs-lookup"><span data-stu-id="b013e-340">The `Include` method specifies eager loading.</span></span>

<span data-ttu-id="b013e-341">Führen Sie die Anwendung aus, und klicken Sie auf den Link **Kurse**.</span><span class="sxs-lookup"><span data-stu-id="b013e-341">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="b013e-342">Die Abteilungsspalte zeigt die `DepartmentID` an, die nicht hilfreich ist.</span><span class="sxs-lookup"><span data-stu-id="b013e-342">The department column displays the `DepartmentID`, which isn't useful.</span></span>

<span data-ttu-id="b013e-343">Aktualisieren Sie die `OnGetAsync`-Methode mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="b013e-343">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="b013e-344">Der vorangehende Code fügt `AsNoTracking` hinzu.</span><span class="sxs-lookup"><span data-stu-id="b013e-344">The preceding code adds `AsNoTracking`.</span></span> <span data-ttu-id="b013e-345">`AsNoTracking` verbessert die Leistung, da die zurückgegebenen Entitäten nicht nachverfolgt werden.</span><span class="sxs-lookup"><span data-stu-id="b013e-345">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="b013e-346">Die Entitäten werden nicht nachverfolgt, da sie nicht im aktuellen Kontext aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="b013e-346">The entities are not tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="b013e-347">Aktualisieren Sie *Pages/Courses/Index.cshtml* mit dem folgenden hervorgehobenen Markup:</span><span class="sxs-lookup"><span data-stu-id="b013e-347">Update *Pages/Courses/Index.cshtml* with the following highlighted markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="b013e-348">Die folgenden Änderungen wurden am Codegerüst vorgenommen:</span><span class="sxs-lookup"><span data-stu-id="b013e-348">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="b013e-349">Die Überschrift wurde von „Index“ in „Kurse“ geändert.</span><span class="sxs-lookup"><span data-stu-id="b013e-349">Changed the heading from Index to Courses.</span></span>
* <span data-ttu-id="b013e-350">Die Spalte **Anzahl** wurde hinzugefügt. Sie zeigt den `CourseID`-Eigenschaftswert an.</span><span class="sxs-lookup"><span data-stu-id="b013e-350">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="b013e-351">Primärschlüssel werden nicht standardmäßig eingerüstet, da sie normalerweise ohne Bedeutung für Endbenutzer sind.</span><span class="sxs-lookup"><span data-stu-id="b013e-351">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="b013e-352">Allerdings hat der Primärschlüssel in diesem Fall jedoch Bedeutung.</span><span class="sxs-lookup"><span data-stu-id="b013e-352">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="b013e-353">Die Spalte **Abteilung** wurde geändert, sodass sie jetzt den Namen der Abteilung anzeigt.</span><span class="sxs-lookup"><span data-stu-id="b013e-353">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="b013e-354">Der Code zeigt die `Name`-Eigenschaft der `Department`-Entität an, die in die `Department`-Navigationseigenschaft geladen wird:</span><span class="sxs-lookup"><span data-stu-id="b013e-354">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="b013e-355">Führen Sie die Anwendung aus. Wählen Sie die Registerkarte **Kurse** aus, um die Liste mit den Abteilungsnamen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="b013e-355">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Indexseite „Kurse“](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="b013e-357">Laden verwandter Daten mit „Select“</span><span class="sxs-lookup"><span data-stu-id="b013e-357">Loading related data with Select</span></span>

<span data-ttu-id="b013e-358">Die `OnGetAsync`-Methode lädt verwandte Daten mit der `Include`-Methode:</span><span class="sxs-lookup"><span data-stu-id="b013e-358">The `OnGetAsync` method loads related data with the `Include` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="b013e-359">Der `Select`-Operator lädt nur die erforderlichen verwandten Daten.</span><span class="sxs-lookup"><span data-stu-id="b013e-359">The `Select` operator loads only the related data needed.</span></span> <span data-ttu-id="b013e-360">Für die einzelnen Elemente wie `Department.Name` wird ein INNER JOIN von SQL verwendet.</span><span class="sxs-lookup"><span data-stu-id="b013e-360">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="b013e-361">Für Sammlungen wird ein anderer Zugriff auf die Datenbank verwendet, aber Gleiches gilt für den `Include`-Operator für Sammlungen.</span><span class="sxs-lookup"><span data-stu-id="b013e-361">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="b013e-362">Der folgende Code lädt verwandte Daten mit der `Select`-Methode:</span><span class="sxs-lookup"><span data-stu-id="b013e-362">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="b013e-363">Die `CourseViewModel`:</span><span class="sxs-lookup"><span data-stu-id="b013e-363">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="b013e-364">Ein vollständiges Beispiel finden Sie unter [IndexSelect.cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) und [IndexSelect.cshtml.cs](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs).</span><span class="sxs-lookup"><span data-stu-id="b013e-364">See [IndexSelect.cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a><span data-ttu-id="b013e-365">Erstellen einer Dozentenseite, die Kurse und Registrierungen anzeigt</span><span class="sxs-lookup"><span data-stu-id="b013e-365">Create an Instructors page that shows Courses and Enrollments</span></span>

<span data-ttu-id="b013e-366">In diesem Abschnitt wird die Dozentenseite erstellt.</span><span class="sxs-lookup"><span data-stu-id="b013e-366">In this section, the Instructors page is created.</span></span>

<a name="IP"></a>
<span data-ttu-id="b013e-367">![Indexseite „Dozenten“](read-related-data/_static/instructors-index.png)</span><span class="sxs-lookup"><span data-stu-id="b013e-367">![Instructors Index page](read-related-data/_static/instructors-index.png)</span></span>

<span data-ttu-id="b013e-368">Auf dieser Seite werden verwandte Daten auf folgende Weise gelesen und angezeigt:</span><span class="sxs-lookup"><span data-stu-id="b013e-368">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="b013e-369">Die Liste der Dozenten zeigt verwandte Daten aus der `OfficeAssignment`-Entität (Office in der vorherigen Abbildung).</span><span class="sxs-lookup"><span data-stu-id="b013e-369">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="b013e-370">Die `Instructor`- und `OfficeAssignment`-Entitäten stehen in einer 1:0..1-Beziehung zueinander.</span><span class="sxs-lookup"><span data-stu-id="b013e-370">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="b013e-371">Eager Loading wird für die `OfficeAssignment`-Entitäten verwendet.</span><span class="sxs-lookup"><span data-stu-id="b013e-371">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="b013e-372">Eager Loading ist in der Regel effizienter, wenn die verwandten Daten angezeigt werden müssen.</span><span class="sxs-lookup"><span data-stu-id="b013e-372">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="b013e-373">In diesem Fall werden die Office-Zuweisungen für die Dozenten angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b013e-373">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="b013e-374">Wenn der Benutzer einen Dozenten auswählt (Harui in der vorherigen Abbildung), werden verwandte `Course`-Entitäten angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b013e-374">When the user selects an instructor (Harui in the preceding image), related `Course` entities are displayed.</span></span> <span data-ttu-id="b013e-375">Die `Instructor`- und `Course`-Entitäten stehen in einer m:n-Beziehung zueinander.</span><span class="sxs-lookup"><span data-stu-id="b013e-375">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="b013e-376">Für die `Course`-Entitäten und ihre verwandten `Department`-Entitäten wird das Eager Loading verwendet.</span><span class="sxs-lookup"><span data-stu-id="b013e-376">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="b013e-377">In diesem Fall können separate Abfragen effizienter sein, da nur Kurse für den ausgewählten Dozenten benötigt werden.</span><span class="sxs-lookup"><span data-stu-id="b013e-377">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="b013e-378">Dieses Beispiel zeigt, wie Eager Loading für Navigationseigenschaften in Entitäten in den Navigationseigenschaften verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="b013e-378">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="b013e-379">Wenn der Benutzer einen Kurs auswählt (Chemie in der vorherigen Abbildung), werden verwandte Daten aus der `Enrollments`-Entität angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b013e-379">When the user selects a course (Chemistry in the preceding image), related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="b013e-380">In der vorherigen Abbildung sind der Name des Studenten und die Note angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b013e-380">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="b013e-381">Die `Course`- und `Enrollment`-Entitäten stehen in einer 1:n-Beziehung zueinander.</span><span class="sxs-lookup"><span data-stu-id="b013e-381">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="b013e-382">Erstellen eines Ansichtsmodells für die Indexansicht „Dozenten“</span><span class="sxs-lookup"><span data-stu-id="b013e-382">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="b013e-383">Die Dozentenseite zeigt Daten aus drei verschiedenen Tabellen.</span><span class="sxs-lookup"><span data-stu-id="b013e-383">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="b013e-384">Es wird ein Ansichtsmodell erstellt, das die drei Entitäten enthält, die die drei Tabellen darstellen.</span><span class="sxs-lookup"><span data-stu-id="b013e-384">A view model is created that includes the three entities representing the three tables.</span></span>

<span data-ttu-id="b013e-385">Erstellen Sie im Ordner *SchoolViewModels* die Datei *InstructorIndexData.cs* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="b013e-385">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a><span data-ttu-id="b013e-386">Gerüstbau für das Dozentenmodell</span><span class="sxs-lookup"><span data-stu-id="b013e-386">Scaffold the Instructor model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b013e-387">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b013e-387">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="b013e-388">Führen Sie die Schritte unter [Erstellen des Gerüsts für das Studentenmodell](xref:data/ef-rp/intro#scaffold-student-pages) aus, und verwenden Sie `Instructor` für die Modellklasse.</span><span class="sxs-lookup"><span data-stu-id="b013e-388">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-student-pages) and use `Instructor` for the model class.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b013e-389">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b013e-389">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="b013e-390">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="b013e-390">Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="b013e-391">Der vorherige Befehl erstellt ein Gerüst für das `Instructor`-Modell.</span><span class="sxs-lookup"><span data-stu-id="b013e-391">The preceding command scaffolds the `Instructor` model.</span></span> 
<span data-ttu-id="b013e-392">Führen Sie die Anwendung aus, und navigieren Sie zur Dozentenseite.</span><span class="sxs-lookup"><span data-stu-id="b013e-392">Run the app and navigate to the instructors page.</span></span>

<span data-ttu-id="b013e-393">Ersetzen Sie *Pages/Instructors/Index.cshtml.cs* durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="b013e-393">Replace *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

<span data-ttu-id="b013e-394">Die `OnGetAsync`-Methode akzeptiert optional Routendaten für die ID des ausgewählten Dozenten.</span><span class="sxs-lookup"><span data-stu-id="b013e-394">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="b013e-395">Untersuchen Sie die Abfrage in der Datei *Pages/Instructors/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="b013e-395">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="b013e-396">Die Abfrage enthält zwei Dinge:</span><span class="sxs-lookup"><span data-stu-id="b013e-396">The query has two includes:</span></span>

* <span data-ttu-id="b013e-397">`OfficeAssignment`: In der [Dozentenansicht](#IP) angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b013e-397">`OfficeAssignment`: Displayed in the [instructors view](#IP).</span></span>
* <span data-ttu-id="b013e-398">`CourseAssignments`: Welche Kurse gegeben werden.</span><span class="sxs-lookup"><span data-stu-id="b013e-398">`CourseAssignments`: Which brings in the courses taught.</span></span>

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="b013e-399">Aktualisieren der Indexseite „Dozenten“</span><span class="sxs-lookup"><span data-stu-id="b013e-399">Update the instructors Index page</span></span>

<span data-ttu-id="b013e-400">Aktualisieren Sie *Pages/Instructors/Index.cshtml* mit folgendem Markup:</span><span class="sxs-lookup"><span data-stu-id="b013e-400">Update *Pages/Instructors/Index.cshtml* with the following markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

<span data-ttu-id="b013e-401">Das oben stehende Markup führt die folgenden Änderungen durch:</span><span class="sxs-lookup"><span data-stu-id="b013e-401">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="b013e-402">Aktualisiert die `page`-Anweisung von `@page` auf `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="b013e-402">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="b013e-403">`"{id:int?}"` ist eine Routenvorlage.</span><span class="sxs-lookup"><span data-stu-id="b013e-403">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="b013e-404">Die Routenvorlage ändert ganzzahlige Abfragezeichenfolgen in der URL in Routendaten.</span><span class="sxs-lookup"><span data-stu-id="b013e-404">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="b013e-405">Klicken Sie beispielsweise auf den Link **Auswählen** für einen Dozenten, wenn nur die `@page`-Anweisung eine URL wie die folgende erzeugt:</span><span class="sxs-lookup"><span data-stu-id="b013e-405">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `http://localhost:1234/Instructors?id=2`

  <span data-ttu-id="b013e-406">Wenn die Seitenanweisung `@page "{id:int?}"` ist, sieht die vorherige URL wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="b013e-406">When the page directive is `@page "{id:int?}"`, the previous URL is:</span></span>

  `http://localhost:1234/Instructors/2`

* <span data-ttu-id="b013e-407">Der Seitentitel lautet **Dozenten**.</span><span class="sxs-lookup"><span data-stu-id="b013e-407">Page title is **Instructors**.</span></span>
* <span data-ttu-id="b013e-408">Es wurde eine **Office**-Spalte hinzugefügt, die `item.OfficeAssignment.Location` nur anzeigt, wenn `item.OfficeAssignment` nicht gleich 0 (null) ist.</span><span class="sxs-lookup"><span data-stu-id="b013e-408">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="b013e-409">Da dies eine 1:0..1-Beziehung ist, gibt es möglicherweise keine verwandte OfficeAssignment-Entität.</span><span class="sxs-lookup"><span data-stu-id="b013e-409">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="b013e-410">Es wurde eine **Kurse**-Spalte hinzugefügt, die die Kurse eines jeden Dozenten anzeigt.</span><span class="sxs-lookup"><span data-stu-id="b013e-410">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="b013e-411">Weitere Informationen zu dieser Razor-Syntax finden Sie unter [Expliziter Zeilenübergang mit `@:`](xref:mvc/views/razor#explicit-line-transition-with-).</span><span class="sxs-lookup"><span data-stu-id="b013e-411">See [Explicit line transition with `@:`](xref:mvc/views/razor#explicit-line-transition-with-) for more about this razor syntax.</span></span>

* <span data-ttu-id="b013e-412">Es wurde Code hinzugefügt, der `class="success"` dynamisch zum `tr`-Element des ausgewählten Dozenten hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="b013e-412">Added code that dynamically adds `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="b013e-413">Hiermit wird eine Hintergrundfarbe mit einer Bootstrapklasse für die ausgewählte Zeile hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="b013e-413">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="b013e-414">Es wurde ein neuer Link mit der Bezeichnung **Auswählen** hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="b013e-414">Added a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="b013e-415">Dieser Link sendet die ID des ausgewählten Dozenten an die `Index`-Methode und legt die Hintergrundfarbe fest.</span><span class="sxs-lookup"><span data-stu-id="b013e-415">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="b013e-416">Führen Sie die Anwendung aus. Klicken Sie auf die Registerkarte **Dozenten**. Die Seite zeigt den `Location` (Office) aus der verwandten `OfficeAssignment`-Entität an.</span><span class="sxs-lookup"><span data-stu-id="b013e-416">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="b013e-417">Wenn OfficeAssignment gleich ist 0 (null), wird eine leere Tabellenzelle angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b013e-417">If OfficeAssignment\` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="b013e-418">Klicken Sie auf den Link **Auswählen**.</span><span class="sxs-lookup"><span data-stu-id="b013e-418">Click on the **Select** link.</span></span> <span data-ttu-id="b013e-419">Der Zeilenstil verändert sich.</span><span class="sxs-lookup"><span data-stu-id="b013e-419">The row style changes.</span></span>

### <a name="add-courses-taught-by-selected-instructor"></a><span data-ttu-id="b013e-420">Hinzufügen von Kursen eines ausgewählten Dozenten</span><span class="sxs-lookup"><span data-stu-id="b013e-420">Add courses taught by selected instructor</span></span>

<span data-ttu-id="b013e-421">Aktualisieren Sie die `OnGetAsync`-Methode in *Pages/Instructors/Index.cshtml.cs* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="b013e-421">Update the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

<span data-ttu-id="b013e-422">Fügen Sie `public int CourseID { get; set; }` hinzu.</span><span class="sxs-lookup"><span data-stu-id="b013e-422">Add `public int CourseID { get; set; }`</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

<span data-ttu-id="b013e-423">Überprüfen Sie die aktualisierte Abfrage:</span><span class="sxs-lookup"><span data-stu-id="b013e-423">Examine the updated query:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="b013e-424">Die vorhergehende Abfrage fügt die `Department`-Entitäten hinzu.</span><span class="sxs-lookup"><span data-stu-id="b013e-424">The preceding query adds the `Department` entities.</span></span>

<span data-ttu-id="b013e-425">Der folgende Code wird ausgeführt, wenn ein Dozent ausgewählt wird (`id != null`).</span><span class="sxs-lookup"><span data-stu-id="b013e-425">The following code executes when an instructor is selected (`id != null`).</span></span> <span data-ttu-id="b013e-426">Der ausgewählte Dozent wird aus der Liste der Dozenten im Ansichtsmodell abgerufen.</span><span class="sxs-lookup"><span data-stu-id="b013e-426">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="b013e-427">Die `Courses`-Eigenschaft des Ansichtsmodells wird mit den `Course`-Entitäten aus der `CourseAssignments`-Navigationseigenschaft dieses Dozenten geladen.</span><span class="sxs-lookup"><span data-stu-id="b013e-427">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

<span data-ttu-id="b013e-428">Die `Where`-Methode gibt eine Sammlung zurück.</span><span class="sxs-lookup"><span data-stu-id="b013e-428">The `Where` method returns a collection.</span></span> <span data-ttu-id="b013e-429">In der vorherigen `Where`-Methode wird nur eine einzige `Instructor`-Entität zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="b013e-429">In the preceding `Where` method, only a single `Instructor` entity is returned.</span></span> <span data-ttu-id="b013e-430">Die `Single`-Methode konvertiert die Sammlung in eine einzelne `Instructor`-Entität.</span><span class="sxs-lookup"><span data-stu-id="b013e-430">The `Single` method converts the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="b013e-431">Die `Instructor`-Entität ermöglicht Zugriff auf die `CourseAssignments`-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="b013e-431">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="b013e-432">`CourseAssignments` ermöglicht Zugriff auf die verwandten `Course`-Entitäten.</span><span class="sxs-lookup"><span data-stu-id="b013e-432">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![Dozent:Kurse m:N](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="b013e-434">Die `Single`-Methode wird für eine Sammlung verwendet, wenn die Sammlung nur ein Element aufweist.</span><span class="sxs-lookup"><span data-stu-id="b013e-434">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="b013e-435">Die `Single`-Methode löst eine Ausnahme aus, wenn die Sammlung leer ist oder mehr als ein Element vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="b013e-435">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="b013e-436">Eine Alternative ist `SingleOrDefault`, womit ein Standardwert (in diesem Fall 0 (null)) zurückgegeben wird, wenn die Sammlung leer ist.</span><span class="sxs-lookup"><span data-stu-id="b013e-436">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="b013e-437">Für eine leere Sammlung wird `SingleOrDefault` verwendet:</span><span class="sxs-lookup"><span data-stu-id="b013e-437">Using `SingleOrDefault` on an empty collection:</span></span>

* <span data-ttu-id="b013e-438">Löst eine Ausnahme aus (auf der Suche nach einer `Courses`-Eigenschaft eines Nullverweises).</span><span class="sxs-lookup"><span data-stu-id="b013e-438">Results in an exception (from trying to find a `Courses` property on a null reference).</span></span>
* <span data-ttu-id="b013e-439">Die Ausnahmemeldung würde die Ursache des Problems weniger deutlich angeben.</span><span class="sxs-lookup"><span data-stu-id="b013e-439">The exception message would less clearly indicate the cause of the problem.</span></span>

<span data-ttu-id="b013e-440">Wenn ein Kurs ausgewählt ist, füllt der folgende Code die `Enrollments`-Eigenschaft des Ansichtsmodells:</span><span class="sxs-lookup"><span data-stu-id="b013e-440">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

<span data-ttu-id="b013e-441">Fügen Sie das folgende Markup zum Ende der Razor-Seite *Pages/Instructors/Index.cshtml* hinzu:</span><span class="sxs-lookup"><span data-stu-id="b013e-441">Add the following markup to the end of the *Pages/Instructors/Index.cshtml* Razor Page:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

<span data-ttu-id="b013e-442">Das vorhergehende Markup zeigt eine Liste der Kurse eines bestimmten Dozenten an, wenn einer ausgewählt wird.</span><span class="sxs-lookup"><span data-stu-id="b013e-442">The preceding markup displays a list of courses related to an instructor when an instructor is selected.</span></span>

<span data-ttu-id="b013e-443">Testen Sie die App.</span><span class="sxs-lookup"><span data-stu-id="b013e-443">Test the app.</span></span> <span data-ttu-id="b013e-444">Klicken Sie auf den Link **Auswählen** auf der Dozentenseite.</span><span class="sxs-lookup"><span data-stu-id="b013e-444">Click on a **Select** link on the instructors page.</span></span>

### <a name="show-student-data"></a><span data-ttu-id="b013e-445">Anzeigen der Studentendaten</span><span class="sxs-lookup"><span data-stu-id="b013e-445">Show student data</span></span>

<span data-ttu-id="b013e-446">In diesem Abschnitt wird die Anwendung aktualisiert, um die Studentendaten für einen ausgewählten Kurs anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="b013e-446">In this section, the app is updated to show the student data for a selected course.</span></span>

<span data-ttu-id="b013e-447">Aktualisieren Sie die Abfrage in der `OnGetAsync`-Methode in *Pages/Instructors/Index.cshtml.cs* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="b013e-447">Update the query in the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="b013e-448">Aktualisieren Sie *Pages/Instructors/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="b013e-448">Update *Pages/Instructors/Index.cshtml*.</span></span> <span data-ttu-id="b013e-449">Fügen Sie dem Dateiende das folgende Markup hinzu:</span><span class="sxs-lookup"><span data-stu-id="b013e-449">Add the following markup to the end of the file:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

<span data-ttu-id="b013e-450">Das vorhergehende Markup zeigt eine Liste der Studenten, die im ausgewählten Kurs registriert sind.</span><span class="sxs-lookup"><span data-stu-id="b013e-450">The preceding markup displays a list of the students who are enrolled in the selected course.</span></span>

<span data-ttu-id="b013e-451">Aktualisieren Sie die Seite. Wählen Sie einen Dozenten aus.</span><span class="sxs-lookup"><span data-stu-id="b013e-451">Refresh the page and select an instructor.</span></span> <span data-ttu-id="b013e-452">Wählen Sie einen Kurs aus, um die Liste der registrierten Studenten und deren Noten einzusehen.</span><span class="sxs-lookup"><span data-stu-id="b013e-452">Select a course to see the list of enrolled students and their grades.</span></span>

![Indexseite „Dozenten“, Dozent und Kurs ausgewählt](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a><span data-ttu-id="b013e-454">Verwenden von „Single“</span><span class="sxs-lookup"><span data-stu-id="b013e-454">Using Single</span></span>

<span data-ttu-id="b013e-455">Die `Single`-Methode kann die `Where`-Bedingung übergehen, anstatt die `Where`-Methode separat aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="b013e-455">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="b013e-456">Der vorangehende `Single`-Ansatz bietet keine Vorteile gegenüber `Where`.</span><span class="sxs-lookup"><span data-stu-id="b013e-456">The preceding `Single` approach provides no benefits over using `Where`.</span></span> <span data-ttu-id="b013e-457">Einige Entwickler bevorzugen einfach den `Single`-Ansatz.</span><span class="sxs-lookup"><span data-stu-id="b013e-457">Some developers prefer the `Single` approach style.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="b013e-458">Explizites Laden</span><span class="sxs-lookup"><span data-stu-id="b013e-458">Explicit loading</span></span>

<span data-ttu-id="b013e-459">Der aktuelle Code gibt Eager Loading für `Enrollments` und `Students` an:</span><span class="sxs-lookup"><span data-stu-id="b013e-459">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="b013e-460">Angenommen, Benutzer möchten Registrierungen für einen Kurs nur selten anzeigen lassen.</span><span class="sxs-lookup"><span data-stu-id="b013e-460">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="b013e-461">In diesem Fall wäre es eine Optimierung, die Registrierungsdaten nur dann zu laden, wenn diese angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="b013e-461">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="b013e-462">In diesem Abschnitt wird die `OnGetAsync` aktualisiert, um das explizite Laden von `Enrollments` und `Students` zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="b013e-462">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="b013e-463">Aktualisieren Sie `OnGetAsync` mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="b013e-463">Update the `OnGetAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

<span data-ttu-id="b013e-464">Der vorangehende Code löscht die *ThenInclude*-Methodenaufrufe für Registrierung und Studentendaten.</span><span class="sxs-lookup"><span data-stu-id="b013e-464">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="b013e-465">Wenn ein Kurs ausgewählt ist, ruft der hervorgehobene Code Folgendes ab:</span><span class="sxs-lookup"><span data-stu-id="b013e-465">If a course is selected, the highlighted code retrieves:</span></span>

* <span data-ttu-id="b013e-466">Die `Enrollment`-Entitäten für den ausgewählten Kurs.</span><span class="sxs-lookup"><span data-stu-id="b013e-466">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="b013e-467">Die `Student`-Entitäten für jede `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="b013e-467">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="b013e-468">Beachten Sie, dass der vorherige Code `.AsNoTracking()` auskommentiert.</span><span class="sxs-lookup"><span data-stu-id="b013e-468">Notice the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="b013e-469">Navigationseigenschaften können nur für nachverfolgte Entitäten explizit geladen werden.</span><span class="sxs-lookup"><span data-stu-id="b013e-469">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="b013e-470">Testen Sie die App.</span><span class="sxs-lookup"><span data-stu-id="b013e-470">Test the app.</span></span> <span data-ttu-id="b013e-471">Aus Benutzersicht verhält sich die Anwendung identisch mit der vorherigen Version.</span><span class="sxs-lookup"><span data-stu-id="b013e-471">From a users perspective, the app behaves identically to the previous version.</span></span>

<span data-ttu-id="b013e-472">Das nächste Tutorial zeigt, wie verwandte Daten aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="b013e-472">The next tutorial shows how to update related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b013e-473">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="b013e-473">Additional resources</span></span>

* [<span data-ttu-id="b013e-474">Dieses Tutorial auf YouTube (Teil 1)</span><span class="sxs-lookup"><span data-stu-id="b013e-474">YouTube version of this tutorial (part1)</span></span>](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [<span data-ttu-id="b013e-475">Dieses Tutorial auf YouTube (Teil 2)</span><span class="sxs-lookup"><span data-stu-id="b013e-475">YouTube version of this tutorial (part2)</span></span>](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
><span data-ttu-id="b013e-476">[Zurück](xref:data/ef-rp/complex-data-model)
>[Weiter](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="b013e-476">[Previous](xref:data/ef-rp/complex-data-model)
[Next](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end
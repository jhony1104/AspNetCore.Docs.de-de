---
title: 'Teil 8: Hinzufügen eines neuen Felds zu einer ASP.NET Core MVC-App'
author: rick-anderson
description: Dies ist Teil 8 der Tutorialreihe zu ASP.NET Core MVC.
ms.author: riande
ms.custom: mvc
ms.date: 12/13/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/new-field
ms.openlocfilehash: d56be93c4dd46b4712fe793ccd453807517a2022
ms.sourcegitcommit: d243fadeda20ad4f142ea60301ae5f5e0d41ed60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/12/2020
ms.locfileid: "84724333"
---
# <a name="part-8-add-a-new-field-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="624bb-103">Teil 8: Hinzufügen eines neuen Felds zu einer ASP.NET Core MVC-App</span><span class="sxs-lookup"><span data-stu-id="624bb-103">Part 8, add a new field to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="624bb-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="624bb-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="624bb-105">In diesem Abschnitt werden [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First-Migrationen für folgende Zwecke verwendet:</span><span class="sxs-lookup"><span data-stu-id="624bb-105">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="624bb-106">Dem Modell wird ein neues Feld hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="624bb-106">Add a new field to the model.</span></span>
* <span data-ttu-id="624bb-107">Das neue Feld wird in die Datenbank migriert.</span><span class="sxs-lookup"><span data-stu-id="624bb-107">Migrate the new field to the database.</span></span>

<span data-ttu-id="624bb-108">Wird EF Code First verwendet, um eine Datenbank automatisch zu erstellen, geht Code First wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="624bb-108">When EF Code First is used to automatically create a database, Code First:</span></span>

* <span data-ttu-id="624bb-109">Es fügt der Datenbank eine Tabelle hinzu, um das Schema der Datenbank nachzuverfolgen.</span><span class="sxs-lookup"><span data-stu-id="624bb-109">Adds a table to the database to  track the schema of the database.</span></span>
* <span data-ttu-id="624bb-110">Es überprüft, ob die Datenbank mit den Modellklassen synchron ist, aus denen sie generiert wurde.</span><span class="sxs-lookup"><span data-stu-id="624bb-110">Verifies the database is in sync with the model classes it was generated from.</span></span> <span data-ttu-id="624bb-111">Wenn sie nicht synchron sind, löst EF eine Ausnahme aus.</span><span class="sxs-lookup"><span data-stu-id="624bb-111">If they aren't in sync, EF throws an exception.</span></span> <span data-ttu-id="624bb-112">Dies erleichtert die Suche nach Problemen mit inkonsistenten Datenbanken bzw. inkonsistentem Code.</span><span class="sxs-lookup"><span data-stu-id="624bb-112">This makes it easier to find inconsistent database/code issues.</span></span>

## <a name="add-a-rating-property-to-the-movie-model"></a><span data-ttu-id="624bb-113">Hinzufügen einer Rating-Eigenschaft zum Movie-Modell</span><span class="sxs-lookup"><span data-stu-id="624bb-113">Add a Rating Property to the Movie Model</span></span>

<span data-ttu-id="624bb-114">Fügen Sie eine `Rating`-Eigenschaft zu *Models/Movie.cs* hinzu:</span><span class="sxs-lookup"><span data-stu-id="624bb-114">Add a `Rating` property to *Models/Movie.cs*:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="624bb-115">Erstellen der App</span><span class="sxs-lookup"><span data-stu-id="624bb-115">Build the app</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="624bb-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="624bb-116">Visual Studio</span></span>](#tab/visual-studio)

 <span data-ttu-id="624bb-117">Strg+Umschalt+B</span><span class="sxs-lookup"><span data-stu-id="624bb-117">Ctrl+Shift+B</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="624bb-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="624bb-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet build
```

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="624bb-119">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="624bb-119">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="624bb-120">Befehl ⌘ + B</span><span class="sxs-lookup"><span data-stu-id="624bb-120">Command ⌘ + B</span></span>

------

<span data-ttu-id="624bb-121">Da Sie der `Movie`-Klasse ein neues Feld hinzugefügt haben, müssen Sie die Positivliste für die Bindung aktualisieren, damit diese neue Eigenschaft eingeschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="624bb-121">Because you've added a new field to the `Movie` class, you need to update the binding white list so this new property will be included.</span></span> <span data-ttu-id="624bb-122">Aktualisieren Sie in *MoviesController.cs* das `[Bind]`-Attribut für die Aktionsmethoden `Create` und `Edit` so, dass die `Rating`-Eigenschaft eingeschlossen wird:</span><span class="sxs-lookup"><span data-stu-id="624bb-122">In *MoviesController.cs*, update the `[Bind]` attribute for both the `Create` and `Edit` action methods to include the `Rating` property:</span></span>

```csharp
[Bind("Id,Title,ReleaseDate,Genre,Price,Rating")]
   ```

<span data-ttu-id="624bb-123">Aktualisieren Sie die Ansichtsvorlagen, um die neue `Rating`-Eigenschaft in der Browseransicht anzuzeigen, zu erstellen und zu bearbeiten.</span><span class="sxs-lookup"><span data-stu-id="624bb-123">Update the view templates in order to display, create, and edit the new `Rating` property in the browser view.</span></span>

<span data-ttu-id="624bb-124">Bearbeiten Sie die Datei */Views/Movies/Index.cshtml*, und fügen Sie das Feld `Rating` hinzu:</span><span class="sxs-lookup"><span data-stu-id="624bb-124">Edit the */Views/Movies/Index.cshtml* file and add a `Rating` field:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGenreRating.cshtml?highlight=16,38&range=24-64)]

<span data-ttu-id="624bb-125">Aktualisieren Sie die Datei */Views/Movies/Create.cshtml* mit dem Feld `Rating`.</span><span class="sxs-lookup"><span data-stu-id="624bb-125">Update the */Views/Movies/Create.cshtml* with a `Rating` field.</span></span>

# <a name="visual-studio--visual-studio-for-mac"></a>[<span data-ttu-id="624bb-126">Visual Studio / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="624bb-126">Visual Studio / Visual Studio for Mac</span></span>](#tab/visual-studio+visual-studio-mac)

<span data-ttu-id="624bb-127">Sie können die vorherige „Formulargruppe“ kopieren und einfügen und IntelliSense die Felder aktualisieren lassen.</span><span class="sxs-lookup"><span data-stu-id="624bb-127">You can copy/paste the previous "form group" and let intelliSense help you update the fields.</span></span> <span data-ttu-id="624bb-128">IntelliSense nutzt [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="624bb-128">IntelliSense works with [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

![Der Entwickler hat den Buchstaben R als Attributwert von „asp-for“ im zweiten „label“-Element der Ansicht eingegeben.](new-field/_static/cr.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="624bb-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="624bb-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!-- This tab intentionally left blank. -->

---

<span data-ttu-id="624bb-133">Aktualisieren Sie die übrigen Vorlagen.</span><span class="sxs-lookup"><span data-stu-id="624bb-133">Update the remaining templates.</span></span>

<span data-ttu-id="624bb-134">Aktualisieren Sie die `SeedData`-Klasse so, dass sie einen Wert für die neue Spalte bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="624bb-134">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="624bb-135">Eine Beispieländerung wird nachstehend gezeigt, aber Sie sollten diese Änderung für jedes `new Movie`-Element vornehmen.</span><span class="sxs-lookup"><span data-stu-id="624bb-135">A sample change is shown below, but you'll want to make this change for each `new Movie`.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

<span data-ttu-id="624bb-136">Die App funktioniert erst, nachdem die Datenbank mit dem neuen Feld aktualisiert wurde.</span><span class="sxs-lookup"><span data-stu-id="624bb-136">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="624bb-137">Wird sie jetzt ausgeführt, wird die folgende `SqlException` ausgelöst:</span><span class="sxs-lookup"><span data-stu-id="624bb-137">If it's run now, the following `SqlException` is thrown:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="624bb-138">Dieser Fehler tritt auf, weil die aktualisierte Modellklasse „Movie“ sich vom Schema der Tabelle „Movie“ der vorhandenen Datenbank unterscheidet.</span><span class="sxs-lookup"><span data-stu-id="624bb-138">This error occurs because the updated Movie model class is different than the schema of the Movie table of the existing database.</span></span> <span data-ttu-id="624bb-139">(Die Datenbanktabelle enthält nicht die Spalte `Rating`.)</span><span class="sxs-lookup"><span data-stu-id="624bb-139">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="624bb-140">Es gibt mehrere Vorgehensweisen zum Beheben des Fehlers:</span><span class="sxs-lookup"><span data-stu-id="624bb-140">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="624bb-141">Lassen Sie die Datenbank von Entity Framework automatisch löschen und basierend auf dem neuen Modellklassenschema neu erstellen.</span><span class="sxs-lookup"><span data-stu-id="624bb-141">Have the Entity Framework automatically drop and re-create the database based on the new model class schema.</span></span> <span data-ttu-id="624bb-142">Dieser Ansatz ist früh im Entwicklungszyklus sehr praktisch, wenn die aktive Entwicklung in einer Testdatenbank erfolgt. Er ermöglicht Ihnen, das Modell und das Datenbankschema schnell weiterzuentwickeln.</span><span class="sxs-lookup"><span data-stu-id="624bb-142">This approach is very convenient early in the development cycle when you're doing active development on a test database; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="624bb-143">Der Nachteil ist jedoch, dass in der Datenbank vorhandene Daten verloren gehen. Deshalb eignet sich dieser Ansatz nicht für eine Produktionsdatenbank!</span><span class="sxs-lookup"><span data-stu-id="624bb-143">The downside, though, is that you lose existing data in the database — so you don't want to use this approach on a production database!</span></span> <span data-ttu-id="624bb-144">Das Verwenden eines Initialisierers zum automatischen Ausführen eines Seedings für eine Datenbank mit Testdaten ist häufig eine produktive Möglichkeit zum Entwickeln einer Anwendung.</span><span class="sxs-lookup"><span data-stu-id="624bb-144">Using an initializer to automatically seed a database with test data is often a productive way to develop an application.</span></span> <span data-ttu-id="624bb-145">Dies ist ein guter Ansatz für die frühe Entwicklung und wenn SQLite verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="624bb-145">This is a good approach for early development and when using SQLite.</span></span>

2. <span data-ttu-id="624bb-146">Ändern Sie das Schema der vorhandenen Datenbank explizit so, dass es mit den Modellklassen übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="624bb-146">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="624bb-147">Der Vorteil dieses Ansatzes ist, dass Sie Ihre Daten behalten.</span><span class="sxs-lookup"><span data-stu-id="624bb-147">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="624bb-148">Sie können diese Änderung entweder manuell oder durch Erstellen eines Änderungsskripts für die Datenbank vornehmen.</span><span class="sxs-lookup"><span data-stu-id="624bb-148">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="624bb-149">Verwenden Sie Code First-Migrationen, um das Datenbankschema zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="624bb-149">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="624bb-150">Für dieses Tutorial wird Code First-Migrationen verwendet.</span><span class="sxs-lookup"><span data-stu-id="624bb-150">For this tutorial, Code First Migrations is used.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="624bb-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="624bb-151">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="624bb-152">Öffnen Sie das Menü **Extras**. Wählen Sie dort **NuGet-Paket-Manager > Paket-Manager-Konsole** aus.</span><span class="sxs-lookup"><span data-stu-id="624bb-152">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>

  ![PMC-Menü](adding-model/_static/pmc.png)

<span data-ttu-id="624bb-154">Geben Sie in der PMC die folgenden Befehle ein:</span><span class="sxs-lookup"><span data-stu-id="624bb-154">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="624bb-155">Der Befehl `Add-Migration` weist das Migrationsframework an, das aktuelle `Movie`-Modell mit dem aktuellen `Movie`-Datenbankschema zu untersuchen und den notwendigen Code zu erstellen, um die Datenbank in das neue Modell zu migrieren.</span><span class="sxs-lookup"><span data-stu-id="624bb-155">The `Add-Migration` command tells the migration framework to examine the current `Movie` model with the current `Movie` DB schema and create the necessary code to migrate the DB to the new model.</span></span>

<span data-ttu-id="624bb-156">Der Name „Rating“ ist beliebig und wird verwendet, um die Migrationsdatei zu benennen.</span><span class="sxs-lookup"><span data-stu-id="624bb-156">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="624bb-157">Es ist hilfreich, einen aussagekräftigen Namen für die Migrationsdatei zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="624bb-157">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="624bb-158">Werden alle Datensätze aus der Datenbank gelöscht, führt die initialize-Methode ein Seeding für die Datenbank aus und fügt das Feld `Rating` hinzu.</span><span class="sxs-lookup"><span data-stu-id="624bb-158">If all the records in the DB are deleted, the initialize method will seed the DB and include the `Rating` field.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="624bb-159">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="624bb-159">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="624bb-160">Löschen Sie die Datenbank, und verwenden Sie Migrationen, um die Datenbank erneut zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="624bb-160">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="624bb-161">Um die Datenbank zu löschen, löschen Sie die Datenbankdatei (*MvcMovie.db*).</span><span class="sxs-lookup"><span data-stu-id="624bb-161">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="624bb-162">Führen Sie dann den `ef database update`-Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="624bb-162">Then run the `ef database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

---
<!-- End of VS tabs -->

<span data-ttu-id="624bb-163">Führen Sie die App aus, und überprüfen Sie, ob Sie Filme mit dem Feld `Rating` erstellen, bearbeiten und anzeigen können.</span><span class="sxs-lookup"><span data-stu-id="624bb-163">Run the app and verify you can create, edit, and display movies with a `Rating` field.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="624bb-164">[Zurück](search.md)
> [Weiter](validation.md)</span><span class="sxs-lookup"><span data-stu-id="624bb-164">[Previous](search.md)
[Next](validation.md)</span></span>

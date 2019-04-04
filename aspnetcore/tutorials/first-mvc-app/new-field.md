---
title: Hinzufügen eines neuen Felds zu einer ASP.NET Core MVC-App
author: rick-anderson
description: Erfahren Sie, wie Sie Entity Framework Code First-Migrationen verwenden, um ein neues Feld zu einem Modell hinzuzufügen und diese Änderung an eine Datenbank zu migrieren.
ms.author: riande
ms.custom: mvc
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/new-field
ms.openlocfilehash: cf8bb67703b564a711105123117498c94ab44e68
ms.sourcegitcommit: 3e9e1f6d572947e15347e818f769e27dea56b648
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/30/2019
ms.locfileid: "58750519"
---
# <a name="add-a-new-field-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="9da1c-103">Hinzufügen eines neuen Felds zu einer ASP.NET Core MVC-App</span><span class="sxs-lookup"><span data-stu-id="9da1c-103">Add a new field to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="9da1c-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9da1c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="9da1c-105">In diesem Abschnitt werden [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First-Migrationen für folgende Zwecke verwendet:</span><span class="sxs-lookup"><span data-stu-id="9da1c-105">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="9da1c-106">Dem Modell wird ein neues Feld hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="9da1c-106">Add a new field to the model.</span></span>
* <span data-ttu-id="9da1c-107">Das neue Feld wird in die Datenbank migriert.</span><span class="sxs-lookup"><span data-stu-id="9da1c-107">Migrate the new field to the database.</span></span>

<span data-ttu-id="9da1c-108">Wird EF Code First verwendet, um eine Datenbank automatisch zu erstellen, geht Code First wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="9da1c-108">When EF Code First is used to automatically create a database, Code First:</span></span>

* <span data-ttu-id="9da1c-109">Es fügt der Datenbank eine Tabelle hinzu, um das Schema der Datenbank nachzuverfolgen.</span><span class="sxs-lookup"><span data-stu-id="9da1c-109">Adds a table to the database to  track the schema of the database.</span></span>
* <span data-ttu-id="9da1c-110">Es überprüft, ob die Datenbank mit den Modellklassen synchron ist, aus denen sie generiert wurde.</span><span class="sxs-lookup"><span data-stu-id="9da1c-110">Verify the database is in sync with the model classes it was generated from.</span></span> <span data-ttu-id="9da1c-111">Wenn sie nicht synchron sind, löst EF eine Ausnahme aus.</span><span class="sxs-lookup"><span data-stu-id="9da1c-111">If they aren't in sync, EF throws an exception.</span></span> <span data-ttu-id="9da1c-112">Dies erleichtert die Suche nach Problemen mit inkonsistenten Datenbanken bzw. inkonsistentem Code.</span><span class="sxs-lookup"><span data-stu-id="9da1c-112">This makes it easier to find inconsistent database/code issues.</span></span>

## <a name="add-a-rating-property-to-the-movie-model"></a><span data-ttu-id="9da1c-113">Hinzufügen einer Rating-Eigenschaft zum Movie-Modell</span><span class="sxs-lookup"><span data-stu-id="9da1c-113">Add a Rating Property to the Movie Model</span></span>

<span data-ttu-id="9da1c-114">Fügen Sie eine `Rating`-Eigenschaft zu *Models/Movie.cs* hinzu:</span><span class="sxs-lookup"><span data-stu-id="9da1c-114">Add a `Rating` property to *Models/Movie.cs*:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="9da1c-115">Erstellen Sie die App (STRG+UMSCHALT+B).</span><span class="sxs-lookup"><span data-stu-id="9da1c-115">Build the app (Ctrl+Shift+B).</span></span>

<span data-ttu-id="9da1c-116">Da Sie der `Movie`-Klasse ein neues Feld hinzugefügt haben, müssen Sie die Positivliste für die Bindung aktualisieren, damit diese neue Eigenschaft eingeschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="9da1c-116">Because you've added a new field to the `Movie` class, you need to update the binding white list so this new property will be included.</span></span> <span data-ttu-id="9da1c-117">Aktualisieren Sie in *MoviesController.cs* das `[Bind]`-Attribut für die Aktionsmethoden `Create` und `Edit` so, dass die `Rating`-Eigenschaft eingeschlossen wird:</span><span class="sxs-lookup"><span data-stu-id="9da1c-117">In *MoviesController.cs*, update the `[Bind]` attribute for both the `Create` and `Edit` action methods to include the `Rating` property:</span></span>

```csharp
[Bind("Id,Title,ReleaseDate,Genre,Price,Rating")]
   ```

<span data-ttu-id="9da1c-118">Aktualisieren Sie die Ansichtsvorlagen, um die neue `Rating`-Eigenschaft in der Browseransicht anzuzeigen, zu erstellen und zu bearbeiten.</span><span class="sxs-lookup"><span data-stu-id="9da1c-118">Update the view templates in order to display, create, and edit the new `Rating` property in the browser view.</span></span>

<span data-ttu-id="9da1c-119">Bearbeiten Sie die Datei */Views/Movies/Index.cshtml*, und fügen Sie das Feld `Rating` hinzu:</span><span class="sxs-lookup"><span data-stu-id="9da1c-119">Edit the */Views/Movies/Index.cshtml* file and add a `Rating` field:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGenreRating.cshtml?highlight=16,38&range=24-64)]

<span data-ttu-id="9da1c-120">Aktualisieren Sie die Datei */Views/Movies/Create.cshtml* mit dem Feld `Rating`.</span><span class="sxs-lookup"><span data-stu-id="9da1c-120">Update the */Views/Movies/Create.cshtml* with a `Rating` field.</span></span>

# <a name="visual-studio--visual-studio-for-mactabvisual-studiovisual-studio-mac"></a>[<span data-ttu-id="9da1c-121">Visual Studio / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="9da1c-121">Visual Studio / Visual Studio for Mac</span></span>](#tab/visual-studio+visual-studio-mac)

<span data-ttu-id="9da1c-122">Sie können die vorherige „Formulargruppe“ kopieren und einfügen und IntelliSense die Felder aktualisieren lassen.</span><span class="sxs-lookup"><span data-stu-id="9da1c-122">You can copy/paste the previous "form group" and let intelliSense help you update the fields.</span></span> <span data-ttu-id="9da1c-123">IntelliSense nutzt [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="9da1c-123">IntelliSense works with [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

![Der Entwickler hat den Buchstaben R als Attributwert von „asp-for“ im zweiten „label“-Element der Ansicht eingegeben.](new-field/_static/cr.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="9da1c-127">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9da1c-127">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!-- This tab intentionally left blank. -->

---

<span data-ttu-id="9da1c-128">Aktualisieren Sie die `SeedData`-Klasse so, dass sie einen Wert für die neue Spalte bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="9da1c-128">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="9da1c-129">Eine Beispieländerung wird nachstehend gezeigt, aber Sie sollten diese Änderung für jedes `new Movie`-Element vornehmen.</span><span class="sxs-lookup"><span data-stu-id="9da1c-129">A sample change is shown below, but you'll want to make this change for each `new Movie`.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

<span data-ttu-id="9da1c-130">Die App funktioniert erst, nachdem die Datenbank mit dem neuen Feld aktualisiert wurde.</span><span class="sxs-lookup"><span data-stu-id="9da1c-130">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="9da1c-131">Wird sie jetzt ausgeführt, wird die folgende `SqlException` ausgelöst:</span><span class="sxs-lookup"><span data-stu-id="9da1c-131">If it's run now, the following `SqlException` is thrown:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="9da1c-132">Dieser Fehler tritt auf, weil die aktualisierte Modellklasse „Movie“ sich vom Schema der Tabelle „Movie“ der vorhandenen Datenbank unterscheidet.</span><span class="sxs-lookup"><span data-stu-id="9da1c-132">This error occurs because the updated Movie model class is different than the schema of the Movie table of the existing database.</span></span> <span data-ttu-id="9da1c-133">(Die Datenbanktabelle enthält nicht die Spalte `Rating`.)</span><span class="sxs-lookup"><span data-stu-id="9da1c-133">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="9da1c-134">Es gibt mehrere Vorgehensweisen zum Beheben des Fehlers:</span><span class="sxs-lookup"><span data-stu-id="9da1c-134">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="9da1c-135">Lassen Sie die Datenbank von Entity Framework automatisch löschen und basierend auf dem neuen Modellklassenschema neu erstellen.</span><span class="sxs-lookup"><span data-stu-id="9da1c-135">Have the Entity Framework automatically drop and re-create the database based on the new model class schema.</span></span> <span data-ttu-id="9da1c-136">Dieser Ansatz ist früh im Entwicklungszyklus sehr praktisch, wenn die aktive Entwicklung in einer Testdatenbank erfolgt. Er ermöglicht Ihnen, das Modell und das Datenbankschema schnell weiterzuentwickeln.</span><span class="sxs-lookup"><span data-stu-id="9da1c-136">This approach is very convenient early in the development cycle when you're doing active development on a test database; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="9da1c-137">Der Nachteil ist jedoch, dass in der Datenbank vorhandene Daten verloren gehen. Deshalb eignet sich dieser Ansatz nicht für eine Produktionsdatenbank!</span><span class="sxs-lookup"><span data-stu-id="9da1c-137">The downside, though, is that you lose existing data in the database — so you don't want to use this approach on a production database!</span></span> <span data-ttu-id="9da1c-138">Das Verwenden eines Initialisierers zum automatischen Ausführen eines Seedings für eine Datenbank mit Testdaten ist häufig eine produktive Möglichkeit zum Entwickeln einer Anwendung.</span><span class="sxs-lookup"><span data-stu-id="9da1c-138">Using an initializer to automatically seed a database with test data is often a productive way to develop an application.</span></span> <span data-ttu-id="9da1c-139">Dies ist ein guter Ansatz für die frühe Entwicklung und wenn SQLite verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="9da1c-139">This is a good approach for early development and when using SQLite.</span></span>

2. <span data-ttu-id="9da1c-140">Ändern Sie das Schema der vorhandenen Datenbank explizit so, dass es mit den Modellklassen übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="9da1c-140">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="9da1c-141">Der Vorteil dieses Ansatzes ist, dass Sie Ihre Daten behalten.</span><span class="sxs-lookup"><span data-stu-id="9da1c-141">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="9da1c-142">Sie können diese Änderung entweder manuell oder durch Erstellen eines Änderungsskripts für die Datenbank vornehmen.</span><span class="sxs-lookup"><span data-stu-id="9da1c-142">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="9da1c-143">Verwenden Sie Code First-Migrationen, um das Datenbankschema zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="9da1c-143">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="9da1c-144">Für dieses Tutorial wird Code First-Migrationen verwendet.</span><span class="sxs-lookup"><span data-stu-id="9da1c-144">For this tutorial, Code First Migrations is used.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9da1c-145">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9da1c-145">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9da1c-146">Öffnen Sie das Menü **Extras**. Wählen Sie dort **NuGet-Paket-Manager > Paket-Manager-Konsole** aus.</span><span class="sxs-lookup"><span data-stu-id="9da1c-146">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>

  ![PMC-Menü](adding-model/_static/pmc.png)

<span data-ttu-id="9da1c-148">Geben Sie in der PMC die folgenden Befehle ein:</span><span class="sxs-lookup"><span data-stu-id="9da1c-148">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="9da1c-149">Der Befehl `Add-Migration` weist das Migrationsframework an, das aktuelle `Movie`-Modell mit dem aktuellen `Movie`-Datenbankschema zu untersuchen und den notwendigen Code zu erstellen, um die Datenbank in das neue Modell zu migrieren.</span><span class="sxs-lookup"><span data-stu-id="9da1c-149">The `Add-Migration` command tells the migration framework to examine the current `Movie` model with the current `Movie` DB schema and create the necessary code to migrate the DB to the new model.</span></span>

<span data-ttu-id="9da1c-150">Der Name „Rating“ ist beliebig und wird verwendet, um die Migrationsdatei zu benennen.</span><span class="sxs-lookup"><span data-stu-id="9da1c-150">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="9da1c-151">Es ist hilfreich, einen aussagekräftigen Namen für die Migrationsdatei zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="9da1c-151">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="9da1c-152">Werden alle Datensätze aus der Datenbank gelöscht, führt die initialize-Methode ein Seeding für die Datenbank aus und fügt das Feld `Rating` hinzu.</span><span class="sxs-lookup"><span data-stu-id="9da1c-152">If all the records in the DB are deleted, the initialize method will seed the DB and include the `Rating` field.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="9da1c-153">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="9da1c-153">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="9da1c-154">Löschen Sie die Datenbank, und verwenden Sie Migrationen, um die Datenbank erneut zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="9da1c-154">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="9da1c-155">Um die Datenbank zu löschen, löschen Sie die Datenbankdatei (*MvcMovie.db*).</span><span class="sxs-lookup"><span data-stu-id="9da1c-155">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="9da1c-156">Führen Sie dann den `ef database update`-Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="9da1c-156">Then run the `ef database update` command:</span></span>

```console
dotnet ef database update
```

---
<!-- End of VS tabs -->

<span data-ttu-id="9da1c-157">Führen Sie die App aus, und überprüfen Sie, ob Sie Filme mit dem Feld `Rating` erstellen/bearbeiten/anzeigen können.</span><span class="sxs-lookup"><span data-stu-id="9da1c-157">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="9da1c-158">Sie müssen das Feld `Rating` den Ansichtsvorlagen `Edit`, `Details` und `Delete` hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="9da1c-158">You should add the `Rating` field to the `Edit`, `Details`, and `Delete` view templates.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="9da1c-159">[Zurück](search.md)
> [Weiter](validation.md)</span><span class="sxs-lookup"><span data-stu-id="9da1c-159">[Previous](search.md)
[Next](validation.md)</span></span>

---
title: Hinzufügen eines neuen Felds zu einer Razor-Seite in ASP.NET Core
author: rick-anderson
description: Veranschaulicht das Hinzufügen ein neues Felds zu einer Razor Page mit Entity Framework Core
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: a1c0622d97e0d2b0a5601e27688f4be7cbe068dc
ms.sourcegitcommit: 16502797ea749e2690feaa5e652a65b89c007c89
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/25/2019
ms.locfileid: "68483296"
---
# <a name="add-a-new-field-to-a-razor-page-in-aspnet-core"></a><span data-ttu-id="677ab-103">Hinzufügen eines neuen Felds zu einer Razor-Seite in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="677ab-103">Add a new field to a Razor Page in ASP.NET Core</span></span>

<span data-ttu-id="677ab-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="677ab-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="677ab-105">In diesem Abschnitt werden [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First-Migrationen für folgende Zwecke verwendet:</span><span class="sxs-lookup"><span data-stu-id="677ab-105">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="677ab-106">Dem Modell wird ein neues Feld hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="677ab-106">Add a new field to the model.</span></span>
* <span data-ttu-id="677ab-107">Die neue Änderung am Feldschema wird in die Datenbank migriert.</span><span class="sxs-lookup"><span data-stu-id="677ab-107">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="677ab-108">Bei der Verwendung von EF Code First für die automatische Erstellung einer Datenbank geht Code First wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="677ab-108">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="677ab-109">Es fügt eine Tabelle zur Datenbank hinzu, um nachzuverfolgen, ob das Schema der Datenbank mit den Modellklassen synchron ist, aus denen sie generiert wurde.</span><span class="sxs-lookup"><span data-stu-id="677ab-109">Adds a table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="677ab-110">Wenn die Datenbank mit den Modellklassen nicht synchron ist, löst EF eine Ausnahme aus.</span><span class="sxs-lookup"><span data-stu-id="677ab-110">If the model classes aren't in sync with the DB, EF throws an exception.</span></span>

<span data-ttu-id="677ab-111">Durch die automatische Überprüfung, ob das Schema und das Modell synchron sind, können Probleme inkonsistenter Datenbanken/Codes leichter ermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="677ab-111">Automatic verification of schema/model in sync makes it easier to find inconsistent database/code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="677ab-112">Hinzufügen einer Rating-Eigenschaft zum Movie-Modell</span><span class="sxs-lookup"><span data-stu-id="677ab-112">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="677ab-113">Öffnen Sie die Datei *Models/Movie.cs*, und fügen Sie eine `Rating`-Eigenschaft hinzu:</span><span class="sxs-lookup"><span data-stu-id="677ab-113">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="677ab-114">Erstellen Sie die App.</span><span class="sxs-lookup"><span data-stu-id="677ab-114">Build the app.</span></span>

<span data-ttu-id="677ab-115">Bearbeiten Sie *Pages/Movies/Index.cshtml*, und fügen ein `Rating`-Feld hinzu:</span><span class="sxs-lookup"><span data-stu-id="677ab-115">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,61-63)]

<span data-ttu-id="677ab-116">Aktualisieren Sie die folgenden Seiten:</span><span class="sxs-lookup"><span data-stu-id="677ab-116">Update the following pages:</span></span>

* <span data-ttu-id="677ab-117">Fügen Sie das `Rating`-Feld zu den Seiten „Delete“ und „Details“ hinzu.</span><span class="sxs-lookup"><span data-stu-id="677ab-117">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="677ab-118">Aktualisieren Sie die Datei [Create.cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) mit einem `Rating`-Feld.</span><span class="sxs-lookup"><span data-stu-id="677ab-118">Update [Create.cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="677ab-119">Fügen Sie das Feld `Rating` der Bearbeitungsseite hinzu.</span><span class="sxs-lookup"><span data-stu-id="677ab-119">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="677ab-120">Die App funktioniert erst, nachdem die Datenbank mit dem neuen Feld aktualisiert wurde.</span><span class="sxs-lookup"><span data-stu-id="677ab-120">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="677ab-121">Wenn sie jetzt ausgeführt wird, löst die App eine `SqlException` aus:</span><span class="sxs-lookup"><span data-stu-id="677ab-121">If run now, the app throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="677ab-122">Dieser Fehler wird dadurch verursacht, dass sich die aktualisierte Modellklasse „Movie“ vom Schema der Tabelle „Movie“ der Datenbank unterscheidet.</span><span class="sxs-lookup"><span data-stu-id="677ab-122">This error is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="677ab-123">(Die Datenbanktabelle enthält nicht die Spalte `Rating`.)</span><span class="sxs-lookup"><span data-stu-id="677ab-123">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="677ab-124">Es gibt mehrere Vorgehensweisen zum Beheben des Fehlers:</span><span class="sxs-lookup"><span data-stu-id="677ab-124">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="677ab-125">Lassen Sie die Datenbank von Entity Framework automatisch löschen und mit dem neuen Modellklassenschema neu erstellen.</span><span class="sxs-lookup"><span data-stu-id="677ab-125">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="677ab-126">Dieser Ansatz ist früh im Entwicklungszyklus sehr praktisch. Er ermöglicht Ihnen, das Modell und das Datenbankschema schnell gemeinsam weiterzuentwickeln.</span><span class="sxs-lookup"><span data-stu-id="677ab-126">This approach is convenient early in the development cycle; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="677ab-127">Der Nachteil ist, dass in der Datenbank vorhandene Daten verloren gehen.</span><span class="sxs-lookup"><span data-stu-id="677ab-127">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="677ab-128">Verwenden Sie diesen Ansatz nicht in einer Produktionsdatenbank!</span><span class="sxs-lookup"><span data-stu-id="677ab-128">Don't use this approach on a production database!</span></span> <span data-ttu-id="677ab-129">Das Löschen der Datenbank bei Schemaänderungen und das Verwenden eines Initialisierers zum automatischen Ausführen eines Seedings für eine Datenbank mit Testdaten ist häufig eine produktive Möglichkeit zum Entwickeln einer App.</span><span class="sxs-lookup"><span data-stu-id="677ab-129">Dropping the DB on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="677ab-130">Ändern Sie das Schema der vorhandenen Datenbank explizit so, dass es mit den Modellklassen übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="677ab-130">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="677ab-131">Der Vorteil dieses Ansatzes ist, dass Sie Ihre Daten behalten.</span><span class="sxs-lookup"><span data-stu-id="677ab-131">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="677ab-132">Sie können diese Änderung entweder manuell oder durch Erstellen eines Änderungsskripts für die Datenbank vornehmen.</span><span class="sxs-lookup"><span data-stu-id="677ab-132">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="677ab-133">Verwenden Sie Code First-Migrationen, um das Datenbankschema zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="677ab-133">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="677ab-134">Verwenden Sie für dieses Tutorial Code First-Migrationen.</span><span class="sxs-lookup"><span data-stu-id="677ab-134">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="677ab-135">Aktualisieren Sie die `SeedData`-Klasse so, dass sie einen Wert für die neue Spalte bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="677ab-135">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="677ab-136">Eine Beispieländerung ist nachstehend gezeigt, aber Sie sollten diese Änderung für jeden `new Movie`-Block vornehmen.</span><span class="sxs-lookup"><span data-stu-id="677ab-136">A sample change is shown below, but you'll want to make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="677ab-137">Sehen Sie sich die [fertige SeedData.cs-Datei an](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="677ab-137">See the [completed SeedData.cs file](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="677ab-138">Erstellen Sie die Projektmappe.</span><span class="sxs-lookup"><span data-stu-id="677ab-138">Build the solution.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="677ab-139">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="677ab-139">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="677ab-140">Hinzufügen einer Migration für das Bewertungsfeld</span><span class="sxs-lookup"><span data-stu-id="677ab-140">Add a migration for the rating field</span></span>

<span data-ttu-id="677ab-141">Öffnen Sie das Menü **Extras**. Wählen Sie dort **NuGet-Paket-Manager > Paket-Manager-Konsole** aus.</span><span class="sxs-lookup"><span data-stu-id="677ab-141">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="677ab-142">Geben Sie in der PMC die folgenden Befehle ein:</span><span class="sxs-lookup"><span data-stu-id="677ab-142">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="677ab-143">Der `Add-Migration`-Befehl weist das Framework an, Folgendes auszuführen:</span><span class="sxs-lookup"><span data-stu-id="677ab-143">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="677ab-144">Das `Movie`-Modell mit dem Schema der `Movie`-Datenbank vergleichen.</span><span class="sxs-lookup"><span data-stu-id="677ab-144">Compare the `Movie` model with the `Movie` DB schema.</span></span>
* <span data-ttu-id="677ab-145">Code erstellen, um das Schema der Datenbank in das neue Modell zu migrieren.</span><span class="sxs-lookup"><span data-stu-id="677ab-145">Create code to migrate the DB schema to the new model.</span></span>

<span data-ttu-id="677ab-146">Der Name „Rating“ ist beliebig und wird verwendet, um die Migrationsdatei zu benennen.</span><span class="sxs-lookup"><span data-stu-id="677ab-146">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="677ab-147">Es ist hilfreich, einen aussagekräftigen Namen für die Migrationsdatei zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="677ab-147">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="677ab-148">Der `Update-Database`-Befehl weist das Framework an, die Schemaänderungen auf die Datenbank anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="677ab-148">The `Update-Database` command tells the framework to apply the schema changes to the database.</span></span>

<a name="ssox"></a>

<span data-ttu-id="677ab-149">Wenn Sie alle Datensätze aus der Datenbank löschen, führt der Initialisierer ein Seeding für die Datenbank aus und fügt das Feld `Rating` hinzu.</span><span class="sxs-lookup"><span data-stu-id="677ab-149">If you delete all the records in the DB, the initializer will seed the DB and include the `Rating` field.</span></span> <span data-ttu-id="677ab-150">Dies ist über die Links „Löschen“ im Browser oder [SQL Server-Objekt-Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX) möglich.</span><span class="sxs-lookup"><span data-stu-id="677ab-150">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="677ab-151">Eine weitere Möglichkeit ist, die Datenbank zu löschen und Migrationen zu verwenden, um die Datenbank neu zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="677ab-151">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="677ab-152">So löschen Sie die Datenbank in SSOX</span><span class="sxs-lookup"><span data-stu-id="677ab-152">To delete the database in SSOX:</span></span>

* <span data-ttu-id="677ab-153">Wählen Sie die Datenbank in SSOX aus.</span><span class="sxs-lookup"><span data-stu-id="677ab-153">Select the database in SSOX.</span></span>
* <span data-ttu-id="677ab-154">Klicken Sie mit der rechten Maustaste auf die Datenbank, und wählen Sie *Löschen* aus.</span><span class="sxs-lookup"><span data-stu-id="677ab-154">Right click on the database, and select *Delete*.</span></span>
* <span data-ttu-id="677ab-155">Aktivieren Sie **Vorhandene Verbindungen schließen**.</span><span class="sxs-lookup"><span data-stu-id="677ab-155">Check **Close existing connections**.</span></span>
* <span data-ttu-id="677ab-156">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="677ab-156">Select **OK**.</span></span>
* <span data-ttu-id="677ab-157">Aktualisieren Sie die Datenbank in [PMC](xref:tutorials/razor-pages/new-field#pmc):</span><span class="sxs-lookup"><span data-stu-id="677ab-157">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="677ab-158">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="677ab-158">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="677ab-159">Löschen und Neuerstellen der Datenbank</span><span class="sxs-lookup"><span data-stu-id="677ab-159">Drop and re-create the database</span></span>

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="677ab-160">Löschen Sie den Ordner „Migration“.</span><span class="sxs-lookup"><span data-stu-id="677ab-160">Delete the migration folder.</span></span>  <span data-ttu-id="677ab-161">Verwenden Sie die folgenden Befehle, um die Datenbank neu zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="677ab-161">Use the following commands to recreate the database.</span></span>

```console
dotnet ef database drop
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="677ab-162">Führen Sie die App aus, und überprüfen Sie, ob Sie Filme mit dem Feld `Rating` erstellen/bearbeiten/anzeigen können.</span><span class="sxs-lookup"><span data-stu-id="677ab-162">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="677ab-163">Wenn für die Datenbank kein Seed ausgeführt wird, legen Sie einen Haltepunkt in der `SeedData.Initialize`-Methode fest.</span><span class="sxs-lookup"><span data-stu-id="677ab-163">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="677ab-164">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="677ab-164">Additional resources</span></span>

* [<span data-ttu-id="677ab-165">Dieses Tutorial auf YouTube</span><span class="sxs-lookup"><span data-stu-id="677ab-165">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="677ab-166">[Zurück: Hinzufügen der Suche](xref:tutorials/razor-pages/search)
> [Weiter: Hinzufügen der Validierung](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="677ab-166">[Previous: Adding Search](xref:tutorials/razor-pages/search)
[Next: Adding Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="677ab-167">In diesem Abschnitt werden [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First-Migrationen für folgende Zwecke verwendet:</span><span class="sxs-lookup"><span data-stu-id="677ab-167">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="677ab-168">Dem Modell wird ein neues Feld hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="677ab-168">Add a new field to the model.</span></span>
* <span data-ttu-id="677ab-169">Die neue Änderung am Feldschema wird in die Datenbank migriert.</span><span class="sxs-lookup"><span data-stu-id="677ab-169">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="677ab-170">Bei der Verwendung von EF Code First für die automatische Erstellung einer Datenbank geht Code First wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="677ab-170">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="677ab-171">Es fügt eine Tabelle zur Datenbank hinzu, um nachzuverfolgen, ob das Schema der Datenbank mit den Modellklassen synchron ist, aus denen sie generiert wurde.</span><span class="sxs-lookup"><span data-stu-id="677ab-171">Adds a table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="677ab-172">Wenn die Datenbank mit den Modellklassen nicht synchron ist, löst EF eine Ausnahme aus.</span><span class="sxs-lookup"><span data-stu-id="677ab-172">If the model classes aren't in sync with the DB, EF throws an exception.</span></span>

<span data-ttu-id="677ab-173">Durch die automatische Überprüfung, ob das Schema und das Modell synchron sind, können Probleme inkonsistenter Datenbanken/Codes leichter ermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="677ab-173">Automatic verification of schema/model in sync makes it easier to find inconsistent database/code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="677ab-174">Hinzufügen einer Rating-Eigenschaft zum Movie-Modell</span><span class="sxs-lookup"><span data-stu-id="677ab-174">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="677ab-175">Öffnen Sie die Datei *Models/Movie.cs*, und fügen Sie eine `Rating`-Eigenschaft hinzu:</span><span class="sxs-lookup"><span data-stu-id="677ab-175">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="677ab-176">Erstellen Sie die App.</span><span class="sxs-lookup"><span data-stu-id="677ab-176">Build the app.</span></span>

<span data-ttu-id="677ab-177">Bearbeiten Sie *Pages/Movies/Index.cshtml*, und fügen ein `Rating`-Feld hinzu:</span><span class="sxs-lookup"><span data-stu-id="677ab-177">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

<span data-ttu-id="677ab-178">Aktualisieren Sie die folgenden Seiten:</span><span class="sxs-lookup"><span data-stu-id="677ab-178">Update the following pages:</span></span>

* <span data-ttu-id="677ab-179">Fügen Sie das `Rating`-Feld zu den Seiten „Delete“ und „Details“ hinzu.</span><span class="sxs-lookup"><span data-stu-id="677ab-179">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="677ab-180">Aktualisieren Sie die Datei [Create.cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) mit einem `Rating`-Feld.</span><span class="sxs-lookup"><span data-stu-id="677ab-180">Update [Create.cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="677ab-181">Fügen Sie das Feld `Rating` der Bearbeitungsseite hinzu.</span><span class="sxs-lookup"><span data-stu-id="677ab-181">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="677ab-182">Die App funktioniert erst, nachdem die Datenbank mit dem neuen Feld aktualisiert wurde.</span><span class="sxs-lookup"><span data-stu-id="677ab-182">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="677ab-183">Wenn sie jetzt ausgeführt wird, löst die App eine `SqlException` aus:</span><span class="sxs-lookup"><span data-stu-id="677ab-183">If run now, the app throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="677ab-184">Dieser Fehler wird dadurch verursacht, dass sich die aktualisierte Modellklasse „Movie“ vom Schema der Tabelle „Movie“ der Datenbank unterscheidet.</span><span class="sxs-lookup"><span data-stu-id="677ab-184">This error is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="677ab-185">(Die Datenbanktabelle enthält nicht die Spalte `Rating`.)</span><span class="sxs-lookup"><span data-stu-id="677ab-185">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="677ab-186">Es gibt mehrere Vorgehensweisen zum Beheben des Fehlers:</span><span class="sxs-lookup"><span data-stu-id="677ab-186">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="677ab-187">Lassen Sie die Datenbank von Entity Framework automatisch löschen und mit dem neuen Modellklassenschema neu erstellen.</span><span class="sxs-lookup"><span data-stu-id="677ab-187">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="677ab-188">Dieser Ansatz ist früh im Entwicklungszyklus sehr praktisch. Er ermöglicht Ihnen, das Modell und das Datenbankschema schnell gemeinsam weiterzuentwickeln.</span><span class="sxs-lookup"><span data-stu-id="677ab-188">This approach is convenient early in the development cycle; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="677ab-189">Der Nachteil ist, dass in der Datenbank vorhandene Daten verloren gehen.</span><span class="sxs-lookup"><span data-stu-id="677ab-189">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="677ab-190">Verwenden Sie diesen Ansatz nicht in einer Produktionsdatenbank!</span><span class="sxs-lookup"><span data-stu-id="677ab-190">Don't use this approach on a production database!</span></span> <span data-ttu-id="677ab-191">Das Löschen der Datenbank bei Schemaänderungen und das Verwenden eines Initialisierers zum automatischen Ausführen eines Seedings für eine Datenbank mit Testdaten ist häufig eine produktive Möglichkeit zum Entwickeln einer App.</span><span class="sxs-lookup"><span data-stu-id="677ab-191">Dropping the DB on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="677ab-192">Ändern Sie das Schema der vorhandenen Datenbank explizit so, dass es mit den Modellklassen übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="677ab-192">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="677ab-193">Der Vorteil dieses Ansatzes ist, dass Sie Ihre Daten behalten.</span><span class="sxs-lookup"><span data-stu-id="677ab-193">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="677ab-194">Sie können diese Änderung entweder manuell oder durch Erstellen eines Änderungsskripts für die Datenbank vornehmen.</span><span class="sxs-lookup"><span data-stu-id="677ab-194">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="677ab-195">Verwenden Sie Code First-Migrationen, um das Datenbankschema zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="677ab-195">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="677ab-196">Verwenden Sie für dieses Tutorial Code First-Migrationen.</span><span class="sxs-lookup"><span data-stu-id="677ab-196">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="677ab-197">Aktualisieren Sie die `SeedData`-Klasse so, dass sie einen Wert für die neue Spalte bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="677ab-197">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="677ab-198">Eine Beispieländerung ist nachstehend gezeigt, aber Sie sollten diese Änderung für jeden `new Movie`-Block vornehmen.</span><span class="sxs-lookup"><span data-stu-id="677ab-198">A sample change is shown below, but you'll want to make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="677ab-199">Sehen Sie sich die [fertige SeedData.cs-Datei an](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="677ab-199">See the [completed SeedData.cs file](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="677ab-200">Erstellen Sie die Projektmappe.</span><span class="sxs-lookup"><span data-stu-id="677ab-200">Build the solution.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="677ab-201">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="677ab-201">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="677ab-202">Hinzufügen einer Migration für das Bewertungsfeld</span><span class="sxs-lookup"><span data-stu-id="677ab-202">Add a migration for the rating field</span></span>

<span data-ttu-id="677ab-203">Öffnen Sie das Menü **Extras**. Wählen Sie dort **NuGet-Paket-Manager > Paket-Manager-Konsole** aus.</span><span class="sxs-lookup"><span data-stu-id="677ab-203">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="677ab-204">Geben Sie in der PMC die folgenden Befehle ein:</span><span class="sxs-lookup"><span data-stu-id="677ab-204">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="677ab-205">Der `Add-Migration`-Befehl weist das Framework an, Folgendes auszuführen:</span><span class="sxs-lookup"><span data-stu-id="677ab-205">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="677ab-206">Das `Movie`-Modell mit dem Schema der `Movie`-Datenbank vergleichen.</span><span class="sxs-lookup"><span data-stu-id="677ab-206">Compare the `Movie` model with the `Movie` DB schema.</span></span>
* <span data-ttu-id="677ab-207">Code erstellen, um das Schema der Datenbank in das neue Modell zu migrieren.</span><span class="sxs-lookup"><span data-stu-id="677ab-207">Create code to migrate the DB schema to the new model.</span></span>

<span data-ttu-id="677ab-208">Der Name „Rating“ ist beliebig und wird verwendet, um die Migrationsdatei zu benennen.</span><span class="sxs-lookup"><span data-stu-id="677ab-208">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="677ab-209">Es ist hilfreich, einen aussagekräftigen Namen für die Migrationsdatei zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="677ab-209">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="677ab-210">Der `Update-Database`-Befehl weist das Framework an, die Schemaänderungen auf die Datenbank anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="677ab-210">The `Update-Database` command tells the framework to apply the schema changes to the database.</span></span>

<a name="ssox"></a>

<span data-ttu-id="677ab-211">Wenn Sie alle Datensätze aus der Datenbank löschen, führt der Initialisierer ein Seeding für die Datenbank aus und fügt das Feld `Rating` hinzu.</span><span class="sxs-lookup"><span data-stu-id="677ab-211">If you delete all the records in the DB, the initializer will seed the DB and include the `Rating` field.</span></span> <span data-ttu-id="677ab-212">Dies ist über die Links „Löschen“ im Browser oder [SQL Server-Objekt-Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX) möglich.</span><span class="sxs-lookup"><span data-stu-id="677ab-212">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="677ab-213">Eine weitere Möglichkeit ist, die Datenbank zu löschen und Migrationen zu verwenden, um die Datenbank neu zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="677ab-213">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="677ab-214">So löschen Sie die Datenbank in SSOX</span><span class="sxs-lookup"><span data-stu-id="677ab-214">To delete the database in SSOX:</span></span>

* <span data-ttu-id="677ab-215">Wählen Sie die Datenbank in SSOX aus.</span><span class="sxs-lookup"><span data-stu-id="677ab-215">Select the database in SSOX.</span></span>
* <span data-ttu-id="677ab-216">Klicken Sie mit der rechten Maustaste auf die Datenbank, und wählen Sie *Löschen* aus.</span><span class="sxs-lookup"><span data-stu-id="677ab-216">Right click on the database, and select *Delete*.</span></span>
* <span data-ttu-id="677ab-217">Aktivieren Sie **Vorhandene Verbindungen schließen**.</span><span class="sxs-lookup"><span data-stu-id="677ab-217">Check **Close existing connections**.</span></span>
* <span data-ttu-id="677ab-218">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="677ab-218">Select **OK**.</span></span>
* <span data-ttu-id="677ab-219">Aktualisieren Sie die Datenbank in [PMC](xref:tutorials/razor-pages/new-field#pmc):</span><span class="sxs-lookup"><span data-stu-id="677ab-219">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="677ab-220">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="677ab-220">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="677ab-221">Löschen und Neuerstellen der Datenbank</span><span class="sxs-lookup"><span data-stu-id="677ab-221">Drop and re-create the database</span></span>

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="677ab-222">Löschen Sie die Datenbank, und verwenden Sie Migrationen, um die Datenbank erneut zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="677ab-222">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="677ab-223">Um die Datenbank zu löschen, löschen Sie die Datenbankdatei (*MvcMovie.db*).</span><span class="sxs-lookup"><span data-stu-id="677ab-223">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="677ab-224">Führen Sie dann den `ef database update`-Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="677ab-224">Then run the `ef database update` command:</span></span>

```console
dotnet ef database update
```

---

<span data-ttu-id="677ab-225">Führen Sie die App aus, und überprüfen Sie, ob Sie Filme mit dem Feld `Rating` erstellen/bearbeiten/anzeigen können.</span><span class="sxs-lookup"><span data-stu-id="677ab-225">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="677ab-226">Wenn für die Datenbank kein Seed ausgeführt wird, legen Sie einen Haltepunkt in der `SeedData.Initialize`-Methode fest.</span><span class="sxs-lookup"><span data-stu-id="677ab-226">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="677ab-227">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="677ab-227">Additional resources</span></span>

* [<span data-ttu-id="677ab-228">Dieses Tutorial auf YouTube</span><span class="sxs-lookup"><span data-stu-id="677ab-228">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="677ab-229">[Zurück: Hinzufügen der Suche](xref:tutorials/razor-pages/search)
> [Weiter: Hinzufügen der Validierung](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="677ab-229">[Previous: Adding Search](xref:tutorials/razor-pages/search)
[Next: Adding Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

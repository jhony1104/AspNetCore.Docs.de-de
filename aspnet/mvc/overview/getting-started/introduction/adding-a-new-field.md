---
uid: mvc/overview/getting-started/introduction/adding-a-new-field
title: Hinzufügen eines neuen Felds | Microsoft-Dokumentation
author: Rick-Anderson
description: ''
ms.author: riande
ms.date: 10/17/2013
ms.assetid: 4085de68-d243-4378-8a64-86236ea8d2da
msc.legacyurl: /mvc/overview/getting-started/introduction/adding-a-new-field
msc.type: authoredcontent
ms.openlocfilehash: 950ae17ebd6b0f15520c2a4e9372703f5374dfbe
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54098687"
---
<a name="adding-a-new-field"></a><span data-ttu-id="d2927-102">Hinzufügen eines neuen Felds</span><span class="sxs-lookup"><span data-stu-id="d2927-102">Adding a New Field</span></span>
====================
<span data-ttu-id="d2927-103">durch [Rick Anderson]((https://twitter.com/RickAndMSFT))</span><span class="sxs-lookup"><span data-stu-id="d2927-103">by [Rick Anderson]((https://twitter.com/RickAndMSFT))</span></span>

[!INCLUDE [Tutorial Note](sample/code-location.md)]

<span data-ttu-id="d2927-104">In diesem Abschnitt verwenden Sie Entity Framework Code First-Migrationen, migrieren einige Änderungen in der ViewModel-Klassen, damit die Änderung auf die Datenbank angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="d2927-104">In this section you'll use Entity Framework Code First Migrations to migrate some changes to the model classes so the change is applied to the database.</span></span>

<span data-ttu-id="d2927-105">In der Standardeinstellung, wenn Sie Entity Framework Code First verwenden, um automatisch eine Datenbank erstellen, wie weiter oben in diesem Tutorial Code First eine Tabelle der Datenbank hinzugefügt, um nachzuverfolgen, ob das Schema der Datenbank mit den Modellklassen synchron ist, die sie aus generiert wurde.</span><span class="sxs-lookup"><span data-stu-id="d2927-105">By default, when you use Entity Framework Code First to automatically create a database, as you did earlier in this tutorial, Code First adds a table to the database to help track whether the schema of the database is in sync with the model classes it was generated from.</span></span> <span data-ttu-id="d2927-106">Wenn sie nicht synchron sind, löst Entity Framework einen Fehler aus.</span><span class="sxs-lookup"><span data-stu-id="d2927-106">If they aren't in sync, the Entity Framework throws an error.</span></span> <span data-ttu-id="d2927-107">Dies erleichtert es, die zum Zeitpunkt der Entwicklung Identifizieren von Problemen, die andernfalls nur (durch ungewöhnliche Fehler) zur Laufzeit auftreten.</span><span class="sxs-lookup"><span data-stu-id="d2927-107">This makes it easier to track down issues at development time that you might otherwise only find (by obscure errors) at run time.</span></span>

## <a name="setting-up-code-first-migrations-for-model-changes"></a><span data-ttu-id="d2927-108">Zum Einrichten von Code First-Migrationen Änderungen des Datenmodells</span><span class="sxs-lookup"><span data-stu-id="d2927-108">Setting up Code First Migrations for Model Changes</span></span>

<span data-ttu-id="d2927-109">Navigieren Sie zum Projektmappen-Explorer.</span><span class="sxs-lookup"><span data-stu-id="d2927-109">Navigate to Solution Explorer.</span></span> <span data-ttu-id="d2927-110">Klicken Sie mit der rechten Maustaste auf die *Movies.mdf* und wählen Sie **löschen** zum Entfernen der Datenbank Filme.</span><span class="sxs-lookup"><span data-stu-id="d2927-110">Right click on the *Movies.mdf* file and select **Delete** to remove the movies database.</span></span> <span data-ttu-id="d2927-111">Wenn Sie nicht sehen die *Movies.mdf* Datei, klicken Sie auf die **alle Dateien anzeigen** Symbol unten im roten Feld.</span><span class="sxs-lookup"><span data-stu-id="d2927-111">If you don't see the *Movies.mdf* file, click on the **Show All Files** icon shown below in the red outline.</span></span>

![](adding-a-new-field/_static/image1.png)

<span data-ttu-id="d2927-112">Erstellen Sie die Anwendung aus, um sicherzustellen, dass keine Fehler vorliegen.</span><span class="sxs-lookup"><span data-stu-id="d2927-112">Build the application to make sure there are no errors.</span></span>

<span data-ttu-id="d2927-113">Von der **Tools** Menü klicken Sie auf **NuGet Package Manager** und dann **-Paket-Manager-Konsole**.</span><span class="sxs-lookup"><span data-stu-id="d2927-113">From the **Tools** menu, click **NuGet Package Manager** and then **Package Manager Console**.</span></span>

![Pack Man hinzufügen](adding-a-new-field/_static/image2.png)

<span data-ttu-id="d2927-115">In der **-Paket-Manager-Konsole** Fenster beim der `PM>` Eingabeaufforderung eingeben.</span><span class="sxs-lookup"><span data-stu-id="d2927-115">In the **Package Manager Console** window at the `PM>` prompt enter</span></span>

<span data-ttu-id="d2927-116">Enable-Migrations "– ContextTypeName" MvcMovie.Models.MovieDBContext</span><span class="sxs-lookup"><span data-stu-id="d2927-116">Enable-Migrations -ContextTypeName MvcMovie.Models.MovieDBContext</span></span>

![](adding-a-new-field/_static/image3.png)

<span data-ttu-id="d2927-117">Die **Enable-Migrations** (siehe oben) Befehl erstellt eine *Configuration.cs* Datei in einem neuen *Migrationen* Ordner.</span><span class="sxs-lookup"><span data-stu-id="d2927-117">The **Enable-Migrations** command (shown above) creates a *Configuration.cs* file in a new *Migrations* folder.</span></span>

![](adding-a-new-field/_static/image4.png)

<span data-ttu-id="d2927-118">Visual Studio öffnet die *Configuration.cs* Datei.</span><span class="sxs-lookup"><span data-stu-id="d2927-118">Visual Studio opens the *Configuration.cs* file.</span></span> <span data-ttu-id="d2927-119">Ersetzen Sie die `Seed` -Methode in der die *Configuration.cs* -Datei mit den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="d2927-119">Replace the `Seed` method in the *Configuration.cs* file with the following code:</span></span>

[!code-csharp[Main](adding-a-new-field/samples/sample1.cs)]

<span data-ttu-id="d2927-120">Zeigen Sie auf die rote Wellenlinie unter `Movie` , und klicken Sie auf `Show Potential Fixes` , und klicken Sie dann auf **mit** **MvcMovie.Models;**</span><span class="sxs-lookup"><span data-stu-id="d2927-120">Hover over the red squiggly line under `Movie` and click `Show Potential Fixes` and then click **using** **MvcMovie.Models;**</span></span>

![](adding-a-new-field/_static/image5.png)

<span data-ttu-id="d2927-121">Auf diese Weise fügt die folgenden using-Anweisung:</span><span class="sxs-lookup"><span data-stu-id="d2927-121">Doing so adds the following using statement:</span></span>

[!code-csharp[Main](adding-a-new-field/samples/sample2.cs)]

> [!NOTE]
> 
> <span data-ttu-id="d2927-122">Code First-Migrationen Ruft die `Seed` -Methode auf, nachdem jede Migration (d. h. Aufrufen **Update-Database** in der Paket-Manager-Konsole), und diese Methode aktualisiert die Zeilen, die bereits eingefügt wurde, oder fügt sie an, wenn sie nicht noch vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="d2927-122">Code First Migrations calls the `Seed` method after every migration (that is, calling **update-database** in the Package Manager Console), and this method updates rows that have already been inserted, or inserts them if they don't exist yet.</span></span>
> 
> <span data-ttu-id="d2927-123">Die [AddOrUpdate](https://msdn.microsoft.com/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx) -Methode in der folgende Code führt einen "Upsert"-Vorgang:</span><span class="sxs-lookup"><span data-stu-id="d2927-123">The [AddOrUpdate](https://msdn.microsoft.com/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx) method in the following code performs an "upsert" operation:</span></span>
> 
> [!code-csharp[Main](adding-a-new-field/samples/sample3.cs)]
> 
> <span data-ttu-id="d2927-124">Da die [Ausgangswert](https://msdn.microsoft.com/library/hh829453(v=vs.103).aspx) Methode, die bei jeder Migration ausgeführt wird, Daten, können nicht nur eingefügt werden, da die Zeilen, die Sie hinzufügen möchten es nach der ersten Migration sein werden, der die Datenbank erstellt.</span><span class="sxs-lookup"><span data-stu-id="d2927-124">Because the [Seed](https://msdn.microsoft.com/library/hh829453(v=vs.103).aspx) method runs with every migration, you can't just insert data, because the rows you are trying to add will already be there after the first migration that creates the database.</span></span> <span data-ttu-id="d2927-125">Die "[" Upsert "](http://en.wikipedia.org/wiki/Upsert)" Vorgang wird verhindert, dass Fehler, die passieren würde, wenn Sie versuchen, eine Zeile einzufügen, die bereits vorhanden ist, aber überschreibt es alle Änderungen an Daten, die Sie möglicherweise beim Testen der Anwendung vorgenommen haben.</span><span class="sxs-lookup"><span data-stu-id="d2927-125">The "[upsert](http://en.wikipedia.org/wiki/Upsert)" operation prevents errors that would happen if you try to insert a row that already exists, but it overrides any changes to data that you may have made while testing the application.</span></span> <span data-ttu-id="d2927-126">Mit Testdaten in einigen Tabellen möchten Sie vielleicht nicht so: in einigen Fällen, wenn Sie Daten während des Tests ändern, sollen die Änderungen, um nach Datenbankupdates bleiben.</span><span class="sxs-lookup"><span data-stu-id="d2927-126">With test data in some tables you might not want that to happen: in some cases when you change data while testing you want your changes to remain after database updates.</span></span> <span data-ttu-id="d2927-127">In diesem Fall einen bedingte Insert-Vorgang ausgeführt werden soll: Fügen Sie eine Zeile nur dann, wenn er nicht bereits vorhanden.</span><span class="sxs-lookup"><span data-stu-id="d2927-127">In that case you want to do a conditional insert operation: insert a row only if it doesn't already exist.</span></span>   
> 
> <span data-ttu-id="d2927-128">Der erste Parameter zu übergeben, um die [AddOrUpdate](https://msdn.microsoft.com/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx) Methode gibt die Eigenschaft zu verwenden, um zu überprüfen, ob eine Zeile bereits vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="d2927-128">The first parameter passed to the [AddOrUpdate](https://msdn.microsoft.com/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx) method specifies the property to use to check if a row already exists.</span></span> <span data-ttu-id="d2927-129">Für die Testdaten für den Film, die Sie bereitstellen, die `Title` Eigenschaft kann für diesen Zweck verwendet werden, da jeder Titel in der Liste eindeutig ist:</span><span class="sxs-lookup"><span data-stu-id="d2927-129">For the test movie data that you are providing, the `Title` property can be used for this purpose since each title in the list is unique:</span></span>
> 
> [!code-csharp[Main](adding-a-new-field/samples/sample4.cs)]
> 
> <span data-ttu-id="d2927-130">Dieser Code wird davon ausgegangen, dass Titel eindeutig sind.</span><span class="sxs-lookup"><span data-stu-id="d2927-130">This code assumes that titles are unique.</span></span> <span data-ttu-id="d2927-131">Wenn Sie manuell einen doppelten Titel hinzufügen, erhalten Sie die folgende Ausnahme beim nächsten einer Migration ausführen.</span><span class="sxs-lookup"><span data-stu-id="d2927-131">If you manually add a duplicate title, you'll get the following exception the next time you perform a migration.</span></span>   
> 
>  <span data-ttu-id="d2927-132">*Die Sequenz enthält mehr als ein element*</span><span class="sxs-lookup"><span data-stu-id="d2927-132">*Sequence contains more than one element*</span></span>  
> 
> <span data-ttu-id="d2927-133">Weitere Informationen zu den [AddOrUpdate](https://msdn.microsoft.com/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx) -Methode finden Sie unter [kümmern mit EF 4.3 AddOrUpdate Methode](http://thedatafarm.com/blog/data-access/take-care-with-ef-4-3-addorupdate-method/)...</span><span class="sxs-lookup"><span data-stu-id="d2927-133">For more information about the [AddOrUpdate](https://msdn.microsoft.com/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx) method, see [Take care with EF 4.3 AddOrUpdate Method](http://thedatafarm.com/blog/data-access/take-care-with-ef-4-3-addorupdate-method/)..</span></span>


<span data-ttu-id="d2927-134">**Drücken Sie STRG + UMSCHALT + B, um das Projekt erstellen.** (Die folgenden Schritte aus schlägt fehl, wenn Sie an diesem Punkt nicht erstellt werden.)</span><span class="sxs-lookup"><span data-stu-id="d2927-134">**Press CTRL-SHIFT-B to build the project.**(The following steps will fail if you don't build at this point.)</span></span>

<span data-ttu-id="d2927-135">Der nächste Schritt ist die Erstellung einer `DbMigration` -Klasse für die anfängliche Migration.</span><span class="sxs-lookup"><span data-stu-id="d2927-135">The next step is to create a `DbMigration` class for the initial migration.</span></span> <span data-ttu-id="d2927-136">Diese Migration erstellt eine neue Datenbank, die daher wird Sie gelöscht der *movie.mdf* -Datei in einem vorherigen Schritt.</span><span class="sxs-lookup"><span data-stu-id="d2927-136">This migration creates a new database, that's why you deleted the *movie.mdf* file in a previous step.</span></span>

<span data-ttu-id="d2927-137">In der **-Paket-Manager-Konsole** Fenster, geben Sie den Befehl `add-migration Initial` zum Erstellen der anfänglichen Migrations.</span><span class="sxs-lookup"><span data-stu-id="d2927-137">In the **Package Manager Console** window, enter the command `add-migration Initial` to create the initial migration.</span></span> <span data-ttu-id="d2927-138">Der Name "Initial" ist willkürlich und wird verwendet, um die Namen der Migrationsdatei erstellt.</span><span class="sxs-lookup"><span data-stu-id="d2927-138">The name "Initial" is arbitrary and is used to name the migration file created.</span></span>

![](adding-a-new-field/_static/image6.png)

<span data-ttu-id="d2927-139">Code First-Migrationen wird eine weitere Klassendatei in der *Migrationen* Ordner (mit dem Namen *{Datumsstempel}\_Initial.cs* ), und diese Klasse enthält Code, der das Schema der Datenbank erstellt.</span><span class="sxs-lookup"><span data-stu-id="d2927-139">Code First Migrations creates another class file in the *Migrations* folder (with the name *{DateStamp}\_Initial.cs* ), and this class contains code that creates the database schema.</span></span> <span data-ttu-id="d2927-140">Der Dateiname für die Migration ist bereits mit einem Zeitstempel fest, um mit der Sortierung zu.</span><span class="sxs-lookup"><span data-stu-id="d2927-140">The migration filename is pre-fixed with a timestamp to help with ordering.</span></span> <span data-ttu-id="d2927-141">Überprüfen Sie die *{Datumsstempel}\_Initial.cs* Datei enthält sie die Anweisungen zum Erstellen der `Movies` Tabelle für die Movie-Datenbank.</span><span class="sxs-lookup"><span data-stu-id="d2927-141">Examine the *{DateStamp}\_Initial.cs* file, it contains the instructions to create the `Movies` table for the Movie DB.</span></span> <span data-ttu-id="d2927-142">Beim Aktualisieren der Datenbank in den Anweisungen unten, diese *{Datumsstempel}\_Initial.cs* Datei ausgeführt wird und das Datenbankschema zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="d2927-142">When you update the database in the instructions below, this *{DateStamp}\_Initial.cs* file will run and create the DB schema.</span></span> <span data-ttu-id="d2927-143">Die **Ausgangswert** Methode wird ausgeführt, um die Datenbank mit Testdaten aufzufüllen.</span><span class="sxs-lookup"><span data-stu-id="d2927-143">Then the **Seed** method will run to populate the DB with test data.</span></span>

<span data-ttu-id="d2927-144">In der **-Paket-Manager-Konsole**, geben Sie den Befehl `update-database` zum Erstellen der Datenbank und Ausführen der `Seed` Methode.</span><span class="sxs-lookup"><span data-stu-id="d2927-144">In the **Package Manager Console**, enter the command `update-database` to create the database and run the `Seed` method.</span></span>

![](adding-a-new-field/_static/image7.png)

<span data-ttu-id="d2927-145">Wenn Sie eine Fehlermeldung, der angibt erhalten, eine Tabelle ist bereits vorhanden und kann nicht erstellt werden, es ist wahrscheinlich, da Sie die Anwendung ausgeführt, nachdem Sie die Datenbank gelöscht und bevor Sie ausgeführt `update-database`.</span><span class="sxs-lookup"><span data-stu-id="d2927-145">If you get an error that indicates a table already exists and can't be created, it is probably because you ran the application after you deleted the database and before you executed `update-database`.</span></span> <span data-ttu-id="d2927-146">In diesem Fall löschen Sie die *Movies.mdf* -Datei erneut, und wiederholen Sie den `update-database` Befehl.</span><span class="sxs-lookup"><span data-stu-id="d2927-146">In that case, delete the *Movies.mdf* file again and retry the `update-database` command.</span></span> <span data-ttu-id="d2927-147">Wenn Sie weiterhin eine Fehlermeldung erhalten, löschen Sie den Ordner "Migrations" und den Inhalt, und beginnen Sie mit den Anweisungen am oberen Rand dieser Seite (d.h. Löschen der *Movies.mdf* -Datei, und fahren Sie mit der Enable-Migrations).</span><span class="sxs-lookup"><span data-stu-id="d2927-147">If you still get an error, delete the migrations folder and contents then start with the instructions at the top of this page (that is delete the *Movies.mdf* file then proceed to Enable-Migrations).</span></span> <span data-ttu-id="d2927-148">Wenn Sie weiterhin eine Fehlermeldung erhalten, öffnen Sie SQL Server-Objekt-Explorer, und entfernen Sie die Datenbank aus der Liste.</span><span class="sxs-lookup"><span data-stu-id="d2927-148">If you still get an error, open SQL Server Object Explorer and remove the database from the list.</span></span>

<span data-ttu-id="d2927-149">Führen Sie die Anwendung, und navigieren Sie zu der */Movies* URL.</span><span class="sxs-lookup"><span data-stu-id="d2927-149">Run the application and navigate to the */Movies* URL.</span></span> <span data-ttu-id="d2927-150">Die Seed-Daten werden angezeigt.</span><span class="sxs-lookup"><span data-stu-id="d2927-150">The seed data is displayed.</span></span>

![](adding-a-new-field/_static/image8.png)

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="d2927-151">Hinzufügen einer Rating-Eigenschaft zum Movie-Modell</span><span class="sxs-lookup"><span data-stu-id="d2927-151">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="d2927-152">Starten Sie durch Hinzufügen eines neuen `Rating` Eigenschaft, um die vorhandene `Movie` Klasse.</span><span class="sxs-lookup"><span data-stu-id="d2927-152">Start by adding a new `Rating` property to the existing `Movie` class.</span></span> <span data-ttu-id="d2927-153">Öffnen der *Models\Movie.cs* -Datei und fügen die `Rating` Eigenschaft dieser Art:</span><span class="sxs-lookup"><span data-stu-id="d2927-153">Open the *Models\Movie.cs* file and add the `Rating` property like this one:</span></span>

[!code-csharp[Main](adding-a-new-field/samples/sample5.cs)]

<span data-ttu-id="d2927-154">Die vollständige `Movie` -Klasse sieht wie im folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="d2927-154">The complete `Movie` class now looks like the following code:</span></span>

[!code-csharp[Main](adding-a-new-field/samples/sample6.cs?highlight=12)]

<span data-ttu-id="d2927-155">Erstellen Sie die Anwendung (STRG + UMSCHALT + B).</span><span class="sxs-lookup"><span data-stu-id="d2927-155">Build the application (Ctrl+Shift+B).</span></span>

<span data-ttu-id="d2927-156">Da Sie ein neues Feld hinzugefügt haben die `Movie` -Klasse, müssen Sie auch die Bindung zu aktualisieren *Zulassungsliste* , damit diese neue Eigenschaft eingeschlossen werden.</span><span class="sxs-lookup"><span data-stu-id="d2927-156">Because you've added a new field to the `Movie` class, you also need to update the binding *white list* so this new property will be included.</span></span> <span data-ttu-id="d2927-157">Update der `bind` Attribut für `Create` und `Edit` Aktionsmethoden anwenden, um das Einschließen der `Rating` Eigenschaft:</span><span class="sxs-lookup"><span data-stu-id="d2927-157">Update the `bind` attribute for `Create` and `Edit` action methods to include the `Rating` property:</span></span>

[!code-csharp[Main](adding-a-new-field/samples/sample7.cs?highlight=1)]

<span data-ttu-id="d2927-158">Sie müssen auch die Ansichtsvorlagen aktualisieren, um die neue `Rating`-Eigenschaft in der Browseransicht anzuzeigen, zu erstellen und zu bearbeiten.</span><span class="sxs-lookup"><span data-stu-id="d2927-158">You also need to update the view templates in order to display, create and edit the new `Rating` property in the browser view.</span></span>

<span data-ttu-id="d2927-159">Öffnen der *\Views\Movies\Index.cshtml* -Datei und fügen eine `<th>Rating</th>` Spaltenüberschrift direkt nach der **Preis** Spalte.</span><span class="sxs-lookup"><span data-stu-id="d2927-159">Open the *\Views\Movies\Index.cshtml* file and add a `<th>Rating</th>` column heading just after the **Price** column.</span></span> <span data-ttu-id="d2927-160">Fügen Sie dann eine `<td>` Spalte am Ende der Vorlage zum Rendern der `@item.Rating` Wert.</span><span class="sxs-lookup"><span data-stu-id="d2927-160">Then add a `<td>` column near the end of the template to render the `@item.Rating` value.</span></span> <span data-ttu-id="d2927-161">Im folgenden finden Sie welche die aktualisierte *"Index.cshtml"* ansichtsvorlage sieht wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="d2927-161">Below is what the updated *Index.cshtml* view template looks like:</span></span>

[!code-cshtml[Main](adding-a-new-field/samples/sample8.cshtml?highlight=31-33,52-54)]

<span data-ttu-id="d2927-162">Öffnen Sie als Nächstes die *\Views\Movies\Create.cshtml* -Datei und fügen die `Rating` Feld mit dem folgenden Markup der Aktivitätsbaum hervorgehoben.</span><span class="sxs-lookup"><span data-stu-id="d2927-162">Next, open the *\Views\Movies\Create.cshtml* file and add the `Rating` field with the following highlighed markup.</span></span> <span data-ttu-id="d2927-163">Dies rendert ein Textfeld, sodass Sie eine Bewertung angeben können, wenn Sie ein neuer Film erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="d2927-163">This renders a text box so that you can specify a rating when a new movie is created.</span></span>

[!code-cshtml[Main](adding-a-new-field/samples/sample9.cshtml?highlight=9-15)]

<span data-ttu-id="d2927-164">Sie haben jetzt den Anwendungscode so, dass die Unterstützung der neuen `Rating` Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="d2927-164">You've now updated the application code to support the new `Rating` property.</span></span>

<span data-ttu-id="d2927-165">Führen Sie die Anwendung, und navigieren Sie zu der */Movies* URL.</span><span class="sxs-lookup"><span data-stu-id="d2927-165">Run the application and navigate to the */Movies* URL.</span></span> <span data-ttu-id="d2927-166">Wenn Sie dies tun, wird jedoch eine der folgenden Fehlermeldungen angezeigt:</span><span class="sxs-lookup"><span data-stu-id="d2927-166">When you do this, though, you'll see one of the following errors:</span></span>

![](adding-a-new-field/_static/image9.png)  
  
<span data-ttu-id="d2927-167">Das Modell, das den Kontext 'MovieDBContext' unterstützt wurde geändert, da die Datenbank erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="d2927-167">The model backing the 'MovieDBContext' context has changed since the database was created.</span></span> <span data-ttu-id="d2927-168">Erwägen Sie die Verwendung von Code First-Migrationen zum Aktualisieren der Datenbank (https://go.microsoft.com/fwlink/?LinkId=238269).</span><span class="sxs-lookup"><span data-stu-id="d2927-168">Consider using Code First Migrations to update the database (https://go.microsoft.com/fwlink/?LinkId=238269).</span></span>

![](adding-a-new-field/_static/image10.png)

<span data-ttu-id="d2927-169">Sie sehen diesen Fehler, da die aktualisierte `Movie` Modellklasse, die in der Anwendung unterscheidet sich von dem Schema der der `Movie` Tabelle der vorhandenen Datenbank.</span><span class="sxs-lookup"><span data-stu-id="d2927-169">You're seeing this error because the updated `Movie` model class in the application is now different than the schema of the `Movie` table of the existing database.</span></span> <span data-ttu-id="d2927-170">(Die Datenbanktabelle enthält nicht die Spalte `Rating`.)</span><span class="sxs-lookup"><span data-stu-id="d2927-170">(There's no `Rating` column in the database table.)</span></span>


<span data-ttu-id="d2927-171">Es gibt mehrere Vorgehensweisen zum Beheben des Fehlers:</span><span class="sxs-lookup"><span data-stu-id="d2927-171">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="d2927-172">Lassen Sie die Datenbank von Entity Framework automatisch löschen und basierend auf dem neuen Modellklassenschema neu erstellen.</span><span class="sxs-lookup"><span data-stu-id="d2927-172">Have the Entity Framework automatically drop and re-create the database based on the new model class schema.</span></span> <span data-ttu-id="d2927-173">Dieser Ansatz ist früh im Entwicklungszyklus sehr praktisch, wenn die aktive Entwicklung in einer Testdatenbank erfolgt. Er ermöglicht Ihnen, das Modell und das Datenbankschema schnell weiterzuentwickeln.</span><span class="sxs-lookup"><span data-stu-id="d2927-173">This approach is very convenient early in the development cycle when you are doing active development on a test database; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="d2927-174">Der Nachteil ist jedoch, dass Sie in der Datenbank vorhandene Daten verloren gehen, damit Sie *nicht* dieser Ansatz in einer Produktionsdatenbank verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="d2927-174">The downside, though, is that you lose existing data in the database — so you *don't* want to use this approach on a production database!</span></span> <span data-ttu-id="d2927-175">Das Verwenden eines Initialisierers zum automatischen Ausführen eines Seedings für eine Datenbank mit Testdaten ist häufig eine produktive Möglichkeit zum Entwickeln einer Anwendung.</span><span class="sxs-lookup"><span data-stu-id="d2927-175">Using an initializer to automatically seed a database with test data is often a productive way to develop an application.</span></span> <span data-ttu-id="d2927-176">Weitere Informationen zu Entity Framework-Datenbankinitialisierer, finden Sie unter [ASP.NET MVC/Entity Framework-Tutorial](../getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md).</span><span class="sxs-lookup"><span data-stu-id="d2927-176">For more information on Entity Framework database initializers, see [ASP.NET MVC/Entity Framework tutorial](../getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md).</span></span>
2. <span data-ttu-id="d2927-177">Ändern Sie das Schema der vorhandenen Datenbank explizit so, dass es mit den Modellklassen übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="d2927-177">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="d2927-178">Der Vorteil dieses Ansatzes ist, dass Sie Ihre Daten behalten.</span><span class="sxs-lookup"><span data-stu-id="d2927-178">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="d2927-179">Sie können diese Änderung entweder manuell oder durch Erstellen eines Änderungsskripts für die Datenbank vornehmen.</span><span class="sxs-lookup"><span data-stu-id="d2927-179">You can make this change either manually or by creating a database change script.</span></span>
3. <span data-ttu-id="d2927-180">Verwenden Sie Code First-Migrationen, um das Datenbankschema zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="d2927-180">Use Code First Migrations to update the database schema.</span></span>


<span data-ttu-id="d2927-181">Für dieses Tutorial verwenden wir Code First-Migrationen.</span><span class="sxs-lookup"><span data-stu-id="d2927-181">For this tutorial, we'll use Code First Migrations.</span></span>

<span data-ttu-id="d2927-182">Aktualisieren Sie die Seed-Methode, sodass sie einen Wert für die neue Spalte bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="d2927-182">Update the Seed method so that it provides a value for the new column.</span></span> <span data-ttu-id="d2927-183">Öffnen Sie migrations\configuration. cs-Datei, und fügen ein Bewertungsfeld jedes Movie-Objekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="d2927-183">Open Migrations\Configuration.cs file and add a Rating field to each Movie object.</span></span>

[!code-csharp[Main](adding-a-new-field/samples/sample10.cs?highlight=6)]

<span data-ttu-id="d2927-184">Erstellen Sie die Projektmappe, und öffnen Sie die **-Paket-Manager-Konsole** Fenster, und geben Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="d2927-184">Build the solution, and then open the **Package Manager Console** window and enter the following command:</span></span>

`add-migration Rating`

<span data-ttu-id="d2927-185">Die `add-migration` Befehl weist das migrationsframework an das aktuelle Movie-Modell mit dem aktuellen Film DB Schema überprüfen und den erforderlichen Code zum Migrieren der Datenbank in das neue Modell zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="d2927-185">The `add-migration` command tells the migration framework to examine the current movie model with the current movie DB schema and create the necessary code to migrate the DB to the new model.</span></span> <span data-ttu-id="d2927-186">Der Name *Bewertung* ist willkürlich und wird verwendet, um die Migrationsdatei zu benennen.</span><span class="sxs-lookup"><span data-stu-id="d2927-186">The name *Rating* is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="d2927-187">Es ist hilfreich, einen aussagekräftigen Namen für den Migrationsschritt zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="d2927-187">It's helpful to use a meaningful name for the migration step.</span></span>

<span data-ttu-id="d2927-188">Wenn dieser Befehl abgeschlossen ist, handelt es sich bei Visual Studio öffnet die Klassendatei, die die neue definiert `DbMigration` abgeleitete Klasse sein, und klicken Sie in der `Up` Methode sehen Sie den Code, der die neue Spalte erstellt.</span><span class="sxs-lookup"><span data-stu-id="d2927-188">When this command finishes, Visual Studio opens the class file that defines the new `DbMigration` derived class, and in the `Up` method you can see the code that creates the new column.</span></span>

[!code-csharp[Main](adding-a-new-field/samples/sample11.cs)]

<span data-ttu-id="d2927-189">Erstellen Sie die Projektmappe, und geben Sie dann die `update-database` -Befehl in der **-Paket-Manager-Konsole** Fenster.</span><span class="sxs-lookup"><span data-stu-id="d2927-189">Build the solution, and then enter the `update-database` command in the **Package Manager Console** window.</span></span>

<span data-ttu-id="d2927-190">Die folgende Abbildung zeigt die Ausgabe in die **-Paket-Manager-Konsole** Fenster (der Stempel Datum voranstellen *Bewertung* unterscheiden.)</span><span class="sxs-lookup"><span data-stu-id="d2927-190">The following image shows the output in the **Package Manager Console** window (The date stamp prepending *Rating* will be different.)</span></span>

![](adding-a-new-field/_static/image11.png)

<span data-ttu-id="d2927-191">Führen Sie die Anwendung erneut aus, und navigieren Sie zu der URL /Movies.</span><span class="sxs-lookup"><span data-stu-id="d2927-191">Re-run the application and navigate to the /Movies URL.</span></span> <span data-ttu-id="d2927-192">Sie sehen, dass das neue Feld für die Bewertung.</span><span class="sxs-lookup"><span data-stu-id="d2927-192">You can see the new Rating field.</span></span>

![](adding-a-new-field/_static/image12.png)

<span data-ttu-id="d2927-193">Klicken Sie auf die **neu erstellen** Link, um einen neuen Film hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="d2927-193">Click the **Create New** link to add a new movie.</span></span> <span data-ttu-id="d2927-194">Beachten Sie, dass Sie eine Bewertung hinzufügen können.</span><span class="sxs-lookup"><span data-stu-id="d2927-194">Note that you can add a rating.</span></span>

![7_CreateRioII](adding-a-new-field/_static/image13.png)

<span data-ttu-id="d2927-196">Klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="d2927-196">Click **Create**.</span></span> <span data-ttu-id="d2927-197">Der neue Film, einschließlich "Rating", wird jetzt in den Filmen auflisten:</span><span class="sxs-lookup"><span data-stu-id="d2927-197">The new movie, including the rating, now shows up in the movies listing:</span></span>

![7_ourNewMovie_SM](adding-a-new-field/_static/image14.png)

<span data-ttu-id="d2927-199">Nun, da das Projekt Migrationen verwendet wird, müssen Sie nicht die Datenbank zu löschen, wenn Sie ein neues Feld hinzufügen oder aktualisieren Sie andernfalls das Schema.</span><span class="sxs-lookup"><span data-stu-id="d2927-199">Now that the project is using migrations, you won't need to drop the database when you add a new field or otherwise update the schema.</span></span> <span data-ttu-id="d2927-200">Im nächsten Abschnitt wir weitere schemaänderungen vornehmen und Migrationen zum Aktualisieren der Datenbank verwenden.</span><span class="sxs-lookup"><span data-stu-id="d2927-200">In the next section, we'll make more schema changes and use migrations to update the database.</span></span>

<span data-ttu-id="d2927-201">Sie sollten auch hinzufügen, die `Rating` Feld die Ansichtsvorlagen bearbeiten, Details und löschen.</span><span class="sxs-lookup"><span data-stu-id="d2927-201">You should also add the `Rating` field to the Edit, Details, and Delete view templates.</span></span>

<span data-ttu-id="d2927-202">Könnten Sie eingeben, den Befehl "Update-Database" in der **-Paket-Manager-Konsole** Fenster erneut und keinen Code für die Migration ausgeführt werden, da das Modell mit dem Schema übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="d2927-202">You could enter the "update-database" command in the **Package Manager Console** window again and no migration code would run, because the schema matches the model.</span></span> <span data-ttu-id="d2927-203">Ausführen von "Update-Database" wird jedoch ausgeführt der `Seed` -Methode erneut auf, und wenn Sie die Seed-Daten geändert haben, die Änderungen verloren da die `Seed` Upserts Methodendaten.</span><span class="sxs-lookup"><span data-stu-id="d2927-203">However, running "update-database" will run the `Seed` method again, and if you changed any of the Seed data, the changes will be lost because the `Seed` method upserts data.</span></span> <span data-ttu-id="d2927-204">Erfahren Sie mehr über die `Seed` -Methode in der Tom Dykstra beliebte [ASP.NET MVC/Entity Framework-Tutorial](../getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md).</span><span class="sxs-lookup"><span data-stu-id="d2927-204">You can read more about the `Seed` method in Tom Dykstra's popular [ASP.NET MVC/Entity Framework tutorial](../getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md).</span></span>

<span data-ttu-id="d2927-205">In diesem Abschnitt wurde erläutert, wie Sie Modellobjekte ändern und die Datenbank mit den Änderungen synchron zu halten.</span><span class="sxs-lookup"><span data-stu-id="d2927-205">In this section you saw how you can modify model objects and keep the database in sync with the changes.</span></span> <span data-ttu-id="d2927-206">Sie haben zudem eine Möglichkeit, eine neu erstellte Datenbank mit Beispieldaten aufzufüllen, damit Sie die Szenarien ausprobieren können.</span><span class="sxs-lookup"><span data-stu-id="d2927-206">You also learned a way to populate a newly created database with sample data so you can try out scenarios.</span></span> <span data-ttu-id="d2927-207">Dies war nur eine kurze Einführung in Code First, finden Sie unter [erstellen ein Entity Framework-Datenmodells für eine ASP.NET MVC-Anwendung](../getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md) ein vollständiges Tutorial zu diesem Thema.</span><span class="sxs-lookup"><span data-stu-id="d2927-207">This was just a quick introduction to Code First, see [Creating an Entity Framework Data Model for an ASP.NET MVC Application](../getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md) for a more complete tutorial on the subject.</span></span> <span data-ttu-id="d2927-208">Als Nächstes sehen wir uns, wie Sie die Modellklassen umfangreichere Validierungslogik hinzugefügt und einige Geschäftsregeln erzwungen werden zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="d2927-208">Next, let's look at how you can add richer validation logic to the model classes and enable some business rules to be enforced.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="d2927-209">[Zurück](adding-search.md)
> [Weiter](adding-validation.md)</span><span class="sxs-lookup"><span data-stu-id="d2927-209">[Previous](adding-search.md)
[Next](adding-validation.md)</span></span>

---
title: Hinzufügen eines Modells zu einer App mit Razor-Seiten in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie Klassen für das Verwalten von Filmen mithilfe von Entity Framework Core (EF Core) zu einer Datenbank hinzufügen.
ms.author: riande
ms.date: 11/05/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: 312b3d4eb13eb04453bf0c3256fc362918157a45
ms.sourcegitcommit: 897d4abff58505dae86b2947c5fe3d1b80d927f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73634173"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="e96dc-103">Hinzufügen eines Modells zu einer App mit Razor-Seiten in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e96dc-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="e96dc-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e96dc-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e96dc-105">In diesem Abschnitt werden Klassen für die Verwaltung von Filmen in einer plattformübergreifenden [SQLite-Datenbank](https://www.sqlite.org/index.html) hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="e96dc-105">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="e96dc-106">Apps, die aus einer ASP.NET Core-Vorlage erstellt werden, verwenden eine SQLite-Datenbank.</span><span class="sxs-lookup"><span data-stu-id="e96dc-106">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="e96dc-107">Die Modellklassen der App werden mit [Entity Framework Core (EF Core)](/ef/core) ([SQLite-EF Core-Datenbankanbieter](/ef/core/providers/sqlite)) verwendet, um mit der Datenbank zu arbeiten.</span><span class="sxs-lookup"><span data-stu-id="e96dc-107">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="e96dc-108">EF Core ist ein ORM-Framework (Objektrelationales Mapping, ORM), das den Datenzugriff vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="e96dc-108">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="e96dc-109">Die Modellklassen werden als POCO-Klassen (von „Plain-Old CLR Objects“) bezeichnet, da sie keinerlei Abhängigkeit von EF Core aufweisen.</span><span class="sxs-lookup"><span data-stu-id="e96dc-109">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="e96dc-110">Sie definieren die Eigenschaften einer Datei, die in der Datenbank gespeichert ist.</span><span class="sxs-lookup"><span data-stu-id="e96dc-110">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="e96dc-111">Hinzufügen eines Datenmodells</span><span class="sxs-lookup"><span data-stu-id="e96dc-111">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e96dc-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e96dc-112">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e96dc-113">Klicken Sie mit der rechten Maustaste auf das Projekt **RazorPagesMovie** > **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="e96dc-113">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="e96dc-114">Geben Sie dem Ordner den Namen *Models*.</span><span class="sxs-lookup"><span data-stu-id="e96dc-114">Name the folder *Models*.</span></span>

<span data-ttu-id="e96dc-115">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="e96dc-115">Right click the *Models* folder.</span></span> <span data-ttu-id="e96dc-116">Wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="e96dc-116">Select **Add** > **Class**.</span></span> <span data-ttu-id="e96dc-117">Nennen Sie die Klasse **Movie**.</span><span class="sxs-lookup"><span data-stu-id="e96dc-117">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e96dc-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e96dc-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e96dc-119">Fügen Sie einen Ordner namens *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="e96dc-119">Add a folder named *Models*.</span></span>
* <span data-ttu-id="e96dc-120">Fügen Sie zum Ordner *Modelle* eine Klasse mit dem Namen *Movie.cs* hinzu.</span><span class="sxs-lookup"><span data-stu-id="e96dc-120">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e96dc-121">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="e96dc-121">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e96dc-122">Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt**RazorPagesMovie**, und klicken Sie dann auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="e96dc-122">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="e96dc-123">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="e96dc-123">Name the folder *Models*.</span></span>
* <span data-ttu-id="e96dc-124">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und klicken Sie auf **Hinzufügen** > **Neue Datei**.</span><span class="sxs-lookup"><span data-stu-id="e96dc-124">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="e96dc-125">Führen Sie im Dialogfeld **Neue Datei** folgende Aktionen aus:</span><span class="sxs-lookup"><span data-stu-id="e96dc-125">In the **New File** dialog:</span></span>

  * <span data-ttu-id="e96dc-126">Klicken Sie im linken Bereich auf **Allgemein**.</span><span class="sxs-lookup"><span data-stu-id="e96dc-126">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="e96dc-127">Klicken Sie im mittleren Bereich auf **Leere Klasse**.</span><span class="sxs-lookup"><span data-stu-id="e96dc-127">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="e96dc-128">Geben Sie der Klasse den Namen **Film**, und wählen sie **Neu** aus.</span><span class="sxs-lookup"><span data-stu-id="e96dc-128">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

<span data-ttu-id="e96dc-129">Erstellen Sie das Projekt, um sicherzustellen, dass keine Kompilierungsfehler vorliegen.</span><span class="sxs-lookup"><span data-stu-id="e96dc-129">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="e96dc-130">Erstellen des Gerüsts für das Filmmodell</span><span class="sxs-lookup"><span data-stu-id="e96dc-130">Scaffold the movie model</span></span>

<span data-ttu-id="e96dc-131">In diesem Abschnitt wird das Gerüst für das Filmmodell erstellt.</span><span class="sxs-lookup"><span data-stu-id="e96dc-131">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="e96dc-132">Mit dem Tool für den Gerüstbau werden Seiten für die Vorgänge „Create“ (Erstellen), „Read“ (Lesen), „Update“ (Aktualisieren) und „Delete“ (Löschen), kurz CRUD-Vorgänge, für das Filmmodell erstellt.</span><span class="sxs-lookup"><span data-stu-id="e96dc-132">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e96dc-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e96dc-133">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e96dc-134">Erstellen Sie den Ordner *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="e96dc-134">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="e96dc-135">Klicken Sie mit der rechten Maustaste auf den Ordner *Pages* > **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="e96dc-135">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="e96dc-136">Geben Sie dem Ordner den Namen *Movies*</span><span class="sxs-lookup"><span data-stu-id="e96dc-136">Name the folder *Movies*</span></span>

<span data-ttu-id="e96dc-137">Klicken Sie mit der rechten Maustaste auf den Ordner *Pages/Movies* > **Hinzufügen** > **Neues Gerüstelement**.</span><span class="sxs-lookup"><span data-stu-id="e96dc-137">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Abbildung der vorherigen Anweisungen.](model/_static/sca.png)

<span data-ttu-id="e96dc-139">Wählen Sie im Dialogfeld **Gerüst hinzufügen** den Eintrag **Razor Pages mit Entity Framework (CRUD)** > **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="e96dc-139">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Abbildung der vorherigen Anweisungen.](model/_static/add_scaffold.png)

<span data-ttu-id="e96dc-141">Vervollständigen Sie das Dialogfeld **Razor Pages mit Entity Framework (CRUD) hinzufügen**:</span><span class="sxs-lookup"><span data-stu-id="e96dc-141">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="e96dc-142">Wählen Sie in der Dropdownliste **Modellklasse** den Eintrag **Film (RazorPagesMovie.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="e96dc-142">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="e96dc-143">Wählen Sie in der Zeile **Datenkontextklasse** das Zeichen **+** (Plus) aus, und ändern Sie den generierten Namen RazorPagesMovie.**Models**.RazorPagesMovieContext in RazorPagesMovie.**Data**.RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="e96dc-143">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="e96dc-144">[Dieses Änderung](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) ist nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="e96dc-144">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="e96dc-145">Sie erstellt die Datenbank-Kontextklasse mit dem korrekten Namespace.</span><span class="sxs-lookup"><span data-stu-id="e96dc-145">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="e96dc-146">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="e96dc-146">Select **Add**.</span></span>

![Abbildung der vorherigen Anweisungen.](model/_static/3/arp.png)

<span data-ttu-id="e96dc-148">Die Datei *appsettings.json* wird mit der Verbindungszeichenfolge aktualisiert, die zum Herstellen einer Verbindung mit einer lokalen Datenbank verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="e96dc-148">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e96dc-149">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e96dc-149">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="e96dc-150">Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs*, *Startup.cs*, und *CSPROJ*).</span><span class="sxs-lookup"><span data-stu-id="e96dc-150">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="e96dc-151">Installieren Sie das Gerüstbautool:</span><span class="sxs-lookup"><span data-stu-id="e96dc-151">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="e96dc-152">**Für Windows**: Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="e96dc-152">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="e96dc-153">**Für MacOS und Linux**: Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="e96dc-153">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e96dc-154">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="e96dc-154">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e96dc-155">Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs*, *Startup.cs*, und *CSPROJ*).</span><span class="sxs-lookup"><span data-stu-id="e96dc-155">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="e96dc-156">Installieren Sie das Gerüstbautool:</span><span class="sxs-lookup"><span data-stu-id="e96dc-156">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="e96dc-157">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="e96dc-157">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

### <a name="files-created"></a><span data-ttu-id="e96dc-158">Erstellte Dateien</span><span class="sxs-lookup"><span data-stu-id="e96dc-158">Files created</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e96dc-159">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e96dc-159">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e96dc-160">Der Gerüstprozess erstellt und ändert folgende Dateien:</span><span class="sxs-lookup"><span data-stu-id="e96dc-160">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="e96dc-161">*Pages/Movies*: „Create“, „Delete“, „Details“, „Edit“ und „Index“.</span><span class="sxs-lookup"><span data-stu-id="e96dc-161">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="e96dc-162">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="e96dc-162">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="e96dc-163">Aktualisiert</span><span class="sxs-lookup"><span data-stu-id="e96dc-163">Updated</span></span>

* <span data-ttu-id="e96dc-164">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="e96dc-164">*Startup.cs*</span></span>

<span data-ttu-id="e96dc-165">Die erstellten und aktualisierten Daten werden im nächsten Abschnitt erläutert.</span><span class="sxs-lookup"><span data-stu-id="e96dc-165">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="e96dc-166">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="e96dc-166">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="e96dc-167">Der Gerüstbauprozess erstellt die folgenden Dateien:</span><span class="sxs-lookup"><span data-stu-id="e96dc-167">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="e96dc-168">*Pages/Movies*: „Create“, „Delete“, „Details“, „Edit“ und „Index“.</span><span class="sxs-lookup"><span data-stu-id="e96dc-168">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="e96dc-169">Die erstellten Daten werden im nächsten Abschnitt erläutert.</span><span class="sxs-lookup"><span data-stu-id="e96dc-169">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="e96dc-170">Anfängliche Migration</span><span class="sxs-lookup"><span data-stu-id="e96dc-170">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e96dc-171">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e96dc-171">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e96dc-172">In diesem Abschnitt wird die Paket-Manager-Konsole (Package Manager Console, PMC) für Folgendes verwendet:</span><span class="sxs-lookup"><span data-stu-id="e96dc-172">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="e96dc-173">Fügen Sie eine anfängliche Migration hinzu.</span><span class="sxs-lookup"><span data-stu-id="e96dc-173">Add an initial migration.</span></span>
* <span data-ttu-id="e96dc-174">Aktualisieren Sie die Datenbank mit der anfänglichen Migration.</span><span class="sxs-lookup"><span data-stu-id="e96dc-174">Update the database with the initial migration.</span></span>

<span data-ttu-id="e96dc-175">Wählen Sie im Menü **Tools** die Option **NuGet-Paket-Manager** > **Paket-Manager-Konsole** aus.</span><span class="sxs-lookup"><span data-stu-id="e96dc-175">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC-Menü](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="e96dc-177">Geben Sie in der PMC die folgenden Befehle ein:</span><span class="sxs-lookup"><span data-stu-id="e96dc-177">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e96dc-178">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e96dc-178">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e96dc-179">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="e96dc-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="e96dc-180">Mit den vorherigen Befehlen wird die folgende Warnung erstellt: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="e96dc-180">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="e96dc-181">Dadurch werden Werte automatisch abgeschnitten, falls diese nicht der Standardgenauigkeit und -skalierung entsprechen.</span><span class="sxs-lookup"><span data-stu-id="e96dc-181">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="e96dc-182">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'." („Für die Spalte „Price“ mit Dezimalwerten beim Entitätstyp „Movie“ wurde kein Typ angegeben. Dadurch werden Werte automatisch abgeschnitten, falls diese nicht der Standardgenauigkeit und -skalierung entsprechen. Geben Sie den Spaltentyp von SQL-Server an, der durch „ForHasColumnType()“ sämtliche Werte unterstützen kann.")</span><span class="sxs-lookup"><span data-stu-id="e96dc-182">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="e96dc-183">Sie können diese Warnung ignorieren, sie wird in einem späteren Tutorial behoben.</span><span class="sxs-lookup"><span data-stu-id="e96dc-183">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="e96dc-184">Mit dem Migrationsbefehl wird Code generiert, um das anfängliche Datenbankschema zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="e96dc-184">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="e96dc-185">Das Schema basiert auf dem Modell, das in `DbContext` angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="e96dc-185">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="e96dc-186">Das Argument `InitialCreate` wird verwendet, um die Migrationen zu benennen.</span><span class="sxs-lookup"><span data-stu-id="e96dc-186">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="e96dc-187">Es kann jeder Name verwendet werden, aber per Konvention wird ein Name ausgewählt, der die Migration beschreibt.</span><span class="sxs-lookup"><span data-stu-id="e96dc-187">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="e96dc-188">Der `update`-Befehl führt die `Up`-Methode in Migrationen aus, die nicht angewendet wurden.</span><span class="sxs-lookup"><span data-stu-id="e96dc-188">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="e96dc-189">In diesem Fall führt `update` die `Up`-Methode in der Datei *Migrations/\<Zeitstempel>_InitialCreate.cs* aus, mit der die Datenbank erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="e96dc-189">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e96dc-190">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e96dc-190">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="e96dc-191">Überprüfen des mit Dependency Injection registrierten Kontexts</span><span class="sxs-lookup"><span data-stu-id="e96dc-191">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="e96dc-192">ASP.NET Core wird mit [Dependency Injection](xref:fundamentals/dependency-injection) erstellt.</span><span class="sxs-lookup"><span data-stu-id="e96dc-192">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="e96dc-193">Dienste (z.B. der Datenbankkontext Entity Framework Core) werden über Dependency Injection beim Anwendungsstart registriert.</span><span class="sxs-lookup"><span data-stu-id="e96dc-193">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="e96dc-194">Komponenten, die diese Dienste erfordern (z.B. Razor Pages), werden von diesen Diensten über Konstruktorparameter bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="e96dc-194">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="e96dc-195">Der Konstruktorcode, der eine Datenbankkontext-Instanz abruft, wird später in diesem Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="e96dc-195">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="e96dc-196">Das Gerüstbautool hat automatisch einen Datenbankkontext erstellt und diesen mit dem Dependency Injection-Container registriert.</span><span class="sxs-lookup"><span data-stu-id="e96dc-196">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="e96dc-197">Untersuchen Sie die Methode `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e96dc-197">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="e96dc-198">Die hervorgehobene Zeile wurde vom Gerüst hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="e96dc-198">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="e96dc-199">Der `RazorPagesMovieContext` koordiniert die EF Core-Funktionen (Create, Read, Update, Delete usw.) für das `Movie`-Modell.</span><span class="sxs-lookup"><span data-stu-id="e96dc-199">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="e96dc-200">Der Datenkontext (`RazorPagesMovieContext`) wird aus [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="e96dc-200">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="e96dc-201">Der Datenkontext gibt an, welche Entitäten im Datenmodell enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="e96dc-201">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="e96dc-202">Der vorangehende Code erstellt eine [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1)-Eigenschaft für die Entitätenmenge.</span><span class="sxs-lookup"><span data-stu-id="e96dc-202">The preceding code creates a [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="e96dc-203">In der Terminologie von Entity Framework entspricht eine Entitätenmenge in der Regel einer Datenbanktabelle.</span><span class="sxs-lookup"><span data-stu-id="e96dc-203">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="e96dc-204">Entitäten entsprechen Zeilen in Tabellen.</span><span class="sxs-lookup"><span data-stu-id="e96dc-204">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="e96dc-205">Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions)-Objekt aufrufen.</span><span class="sxs-lookup"><span data-stu-id="e96dc-205">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="e96dc-206">Für die lokale Entwicklung liest das [ASP.NET Core-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der *appsettings.json*-Datei.</span><span class="sxs-lookup"><span data-stu-id="e96dc-206">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e96dc-207">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e96dc-207">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e96dc-208">Untersuchen Sie die Methode `Up`.</span><span class="sxs-lookup"><span data-stu-id="e96dc-208">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e96dc-209">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="e96dc-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e96dc-210">Untersuchen Sie die Methode `Up`.</span><span class="sxs-lookup"><span data-stu-id="e96dc-210">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="e96dc-211">Testen der App</span><span class="sxs-lookup"><span data-stu-id="e96dc-211">Test the app</span></span>

* <span data-ttu-id="e96dc-212">Führen Sie die App aus, und fügen Sie `/Movies` an die URL im Browser an (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="e96dc-212">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="e96dc-213">Wenn Sie eine Fehlermeldung erhalten, müssen Sie Folgendes tun:</span><span class="sxs-lookup"><span data-stu-id="e96dc-213">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="e96dc-214">Sie haben den [Migrationsschritt](#pmc) verpasst.</span><span class="sxs-lookup"><span data-stu-id="e96dc-214">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="e96dc-215">Testen Sie den Link **Create** (Erstellen).</span><span class="sxs-lookup"><span data-stu-id="e96dc-215">Test the **Create** link.</span></span>

  ![Seite „Create“](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="e96dc-217">Sie können unter Umständen in das Feld `Price` keine Kommas als Dezimaltrennzeichen eingeben.</span><span class="sxs-lookup"><span data-stu-id="e96dc-217">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="e96dc-218">Zur Unterstützung der [jQuery-Validierung](https://jqueryvalidation.org/) für Gebietsschemas mit einer anderen Sprache als Englisch, in denen ein Komma („,“) als Dezimaltrennzeichen verwendet wird, und für Datums- und Uhrzeitformate, die nicht dem US-englischen Format entsprechen, muss die App globalisiert werden.</span><span class="sxs-lookup"><span data-stu-id="e96dc-218">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="e96dc-219">Globalisierungsanweisungen finden Sie unter [GitHub-Problem](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="e96dc-219">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="e96dc-220">Testen Sie die Links **Edit** (Bearbeiten), **Details** und **Delete** (Löschen).</span><span class="sxs-lookup"><span data-stu-id="e96dc-220">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="e96dc-221">Im nächsten Tutorial finden Sie Erläuterungen zu den Dateien, die durch den Gerüstbau erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="e96dc-221">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e96dc-222">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="e96dc-222">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="e96dc-223">[Zurück: Erste Schritte](xref:tutorials/razor-pages/razor-pages-start)
> [Weiter: Scaffolded Razor Pages (Gerüstbau mit Razor Pages)](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="e96dc-223">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e96dc-224">In diesem Abschnitt werden Klassen für die Verwaltung von Filmen in einer plattformübergreifenden [SQLite-Datenbank](https://www.sqlite.org/index.html) hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="e96dc-224">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="e96dc-225">Apps, die aus einer ASP.NET Core-Vorlage erstellt werden, verwenden eine SQLite-Datenbank.</span><span class="sxs-lookup"><span data-stu-id="e96dc-225">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="e96dc-226">Die Modellklassen der App werden mit [Entity Framework Core (EF Core)](/ef/core) ([SQLite-EF Core-Datenbankanbieter](/ef/core/providers/sqlite)) verwendet, um mit der Datenbank zu arbeiten.</span><span class="sxs-lookup"><span data-stu-id="e96dc-226">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="e96dc-227">EF Core ist ein ORM-Framework (Objektrelationales Mapping, ORM), das den Datenzugriff vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="e96dc-227">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="e96dc-228">Die Modellklassen werden als POCO-Klassen (von „Plain-Old CLR Objects“) bezeichnet, da sie keinerlei Abhängigkeit von EF Core aufweisen.</span><span class="sxs-lookup"><span data-stu-id="e96dc-228">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="e96dc-229">Sie definieren die Eigenschaften einer Datei, die in der Datenbank gespeichert ist.</span><span class="sxs-lookup"><span data-stu-id="e96dc-229">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="e96dc-230">Hinzufügen eines Datenmodells</span><span class="sxs-lookup"><span data-stu-id="e96dc-230">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e96dc-231">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e96dc-231">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e96dc-232">Klicken Sie mit der rechten Maustaste auf das Projekt **RazorPagesMovie** > **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="e96dc-232">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="e96dc-233">Geben Sie dem Ordner den Namen *Models*.</span><span class="sxs-lookup"><span data-stu-id="e96dc-233">Name the folder *Models*.</span></span>

<span data-ttu-id="e96dc-234">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="e96dc-234">Right click the *Models* folder.</span></span> <span data-ttu-id="e96dc-235">Wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="e96dc-235">Select **Add** > **Class**.</span></span> <span data-ttu-id="e96dc-236">Nennen Sie die Klasse **Movie**.</span><span class="sxs-lookup"><span data-stu-id="e96dc-236">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e96dc-237">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e96dc-237">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e96dc-238">Fügen Sie einen Ordner namens *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="e96dc-238">Add a folder named *Models*.</span></span>
* <span data-ttu-id="e96dc-239">Fügen Sie zum Ordner *Modelle* eine Klasse mit dem Namen *Movie.cs* hinzu.</span><span class="sxs-lookup"><span data-stu-id="e96dc-239">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e96dc-240">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="e96dc-240">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e96dc-241">Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt**RazorPagesMovie**, und klicken Sie dann auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="e96dc-241">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="e96dc-242">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="e96dc-242">Name the folder *Models*.</span></span>
* <span data-ttu-id="e96dc-243">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und klicken Sie auf **Hinzufügen** > **Neue Datei**.</span><span class="sxs-lookup"><span data-stu-id="e96dc-243">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="e96dc-244">Führen Sie im Dialogfeld **Neue Datei** folgende Aktionen aus:</span><span class="sxs-lookup"><span data-stu-id="e96dc-244">In the **New File** dialog:</span></span>

  * <span data-ttu-id="e96dc-245">Klicken Sie im linken Bereich auf **Allgemein**.</span><span class="sxs-lookup"><span data-stu-id="e96dc-245">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="e96dc-246">Klicken Sie im mittleren Bereich auf **Leere Klasse**.</span><span class="sxs-lookup"><span data-stu-id="e96dc-246">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="e96dc-247">Geben Sie der Klasse den Namen **Film**, und wählen sie **Neu** aus.</span><span class="sxs-lookup"><span data-stu-id="e96dc-247">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

<span data-ttu-id="e96dc-248">Erstellen Sie das Projekt, um sicherzustellen, dass keine Kompilierungsfehler vorliegen.</span><span class="sxs-lookup"><span data-stu-id="e96dc-248">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="e96dc-249">Erstellen des Gerüsts für das Filmmodell</span><span class="sxs-lookup"><span data-stu-id="e96dc-249">Scaffold the movie model</span></span>

<span data-ttu-id="e96dc-250">In diesem Abschnitt wird das Gerüst für das Filmmodell erstellt.</span><span class="sxs-lookup"><span data-stu-id="e96dc-250">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="e96dc-251">Mit dem Tool für den Gerüstbau werden Seiten für die Vorgänge „Create“ (Erstellen), „Read“ (Lesen), „Update“ (Aktualisieren) und „Delete“ (Löschen), kurz CRUD-Vorgänge, für das Filmmodell erstellt.</span><span class="sxs-lookup"><span data-stu-id="e96dc-251">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e96dc-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e96dc-252">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e96dc-253">Erstellen Sie den Ordner *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="e96dc-253">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="e96dc-254">Klicken Sie mit der rechten Maustaste auf den Ordner *Pages* > **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="e96dc-254">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="e96dc-255">Geben Sie dem Ordner den Namen *Movies*</span><span class="sxs-lookup"><span data-stu-id="e96dc-255">Name the folder *Movies*</span></span>

<span data-ttu-id="e96dc-256">Klicken Sie mit der rechten Maustaste auf den Ordner *Pages/Movies* > **Hinzufügen** > **Neues Gerüstelement**.</span><span class="sxs-lookup"><span data-stu-id="e96dc-256">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Abbildung der vorherigen Anweisungen.](model/_static/sca.png)

<span data-ttu-id="e96dc-258">Wählen Sie im Dialogfeld **Gerüst hinzufügen** den Eintrag **Razor Pages mit Entity Framework (CRUD)** > **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="e96dc-258">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Abbildung der vorherigen Anweisungen.](model/_static/add_scaffold.png)

<span data-ttu-id="e96dc-260">Vervollständigen Sie das Dialogfeld **Razor Pages mit Entity Framework (CRUD) hinzufügen**:</span><span class="sxs-lookup"><span data-stu-id="e96dc-260">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="e96dc-261">Wählen Sie in der Dropdownliste **Modellklasse** den Eintrag **Film (RazorPagesMovie.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="e96dc-261">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="e96dc-262">Wählen Sie in der Zeile **Datenkontextklasse** das Zeichen **+** (Plus) aus, und akzeptieren Sie den generierten Namen **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="e96dc-262">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="e96dc-263">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="e96dc-263">Select **Add**.</span></span>

![Abbildung der vorherigen Anweisungen.](model/_static/arp.png)

<span data-ttu-id="e96dc-265">Die Datei *appsettings.json* wird mit der Verbindungszeichenfolge aktualisiert, die zum Herstellen einer Verbindung mit einer lokalen Datenbank verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="e96dc-265">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e96dc-266">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e96dc-266">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="e96dc-267">Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs*, *Startup.cs*, und *CSPROJ*).</span><span class="sxs-lookup"><span data-stu-id="e96dc-267">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="e96dc-268">Installieren Sie das Gerüstbautool:</span><span class="sxs-lookup"><span data-stu-id="e96dc-268">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="e96dc-269">**Für Windows**: Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="e96dc-269">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="e96dc-270">**Für MacOS und Linux**: Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="e96dc-270">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e96dc-271">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="e96dc-271">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e96dc-272">Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs*, *Startup.cs*, und *CSPROJ*).</span><span class="sxs-lookup"><span data-stu-id="e96dc-272">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="e96dc-273">Installieren Sie das Gerüstbautool:</span><span class="sxs-lookup"><span data-stu-id="e96dc-273">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="e96dc-274">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="e96dc-274">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

<span data-ttu-id="e96dc-275">Der Gerüstprozess erstellt und ändert folgende Dateien:</span><span class="sxs-lookup"><span data-stu-id="e96dc-275">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="e96dc-276">Erstellte Dateien</span><span class="sxs-lookup"><span data-stu-id="e96dc-276">Files created</span></span>

* <span data-ttu-id="e96dc-277">*Pages/Movies*: „Create“, „Delete“, „Details“, „Edit“ und „Index“.</span><span class="sxs-lookup"><span data-stu-id="e96dc-277">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="e96dc-278">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="e96dc-278">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="e96dc-279">Datei aktualisiert</span><span class="sxs-lookup"><span data-stu-id="e96dc-279">File updated</span></span>

* <span data-ttu-id="e96dc-280">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="e96dc-280">*Startup.cs*</span></span>

<span data-ttu-id="e96dc-281">Die erstellten und aktualisierten Daten werden im nächsten Abschnitt erläutert.</span><span class="sxs-lookup"><span data-stu-id="e96dc-281">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="e96dc-282">Anfängliche Migration</span><span class="sxs-lookup"><span data-stu-id="e96dc-282">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e96dc-283">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e96dc-283">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e96dc-284">In diesem Abschnitt wird die Paket-Manager-Konsole (Package Manager Console, PMC) für Folgendes verwendet:</span><span class="sxs-lookup"><span data-stu-id="e96dc-284">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="e96dc-285">Fügen Sie eine anfängliche Migration hinzu.</span><span class="sxs-lookup"><span data-stu-id="e96dc-285">Add an initial migration.</span></span>
* <span data-ttu-id="e96dc-286">Aktualisieren Sie die Datenbank mit der anfänglichen Migration.</span><span class="sxs-lookup"><span data-stu-id="e96dc-286">Update the database with the initial migration.</span></span>

<span data-ttu-id="e96dc-287">Wählen Sie im Menü **Tools** die Option **NuGet-Paket-Manager** > **Paket-Manager-Konsole** aus.</span><span class="sxs-lookup"><span data-stu-id="e96dc-287">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC-Menü](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="e96dc-289">Geben Sie in der PMC die folgenden Befehle ein:</span><span class="sxs-lookup"><span data-stu-id="e96dc-289">In the PMC, enter the following commands:</span></span>

```Powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="e96dc-290">Mit dem Befehl `Add-Migration` wird Code generiert, um das anfängliche Datenbankschema zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="e96dc-290">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="e96dc-291">Das Schema basiert auf dem Modell, das in `DbContext` angegeben ist (in der Datei *RazorPagesMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="e96dc-291">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="e96dc-292">Das Argument `InitialCreate` wird verwendet, um die Migration zu benennen.</span><span class="sxs-lookup"><span data-stu-id="e96dc-292">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="e96dc-293">Es kann jeder Name verwendet werden, aber per Konvention wird ein Name verwendet, der die Migration beschreibt.</span><span class="sxs-lookup"><span data-stu-id="e96dc-293">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="e96dc-294">Weitere Informationen finden Sie unter <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="e96dc-294">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="e96dc-295">Der Befehl `Update-Database` führt die Methode `Up` in der Datei *Migrations/\<time-stamp>_InitialCreate.cs* aus.</span><span class="sxs-lookup"><span data-stu-id="e96dc-295">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="e96dc-296">Die Methode `Up` erstellt die Datenbank.</span><span class="sxs-lookup"><span data-stu-id="e96dc-296">The `Up` method creates the database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e96dc-297">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e96dc-297">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e96dc-298">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="e96dc-298">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> <span data-ttu-id="e96dc-299">Mit den vorherigen Befehlen wird die folgende Warnung erstellt: „*Für die Spalte „Preis“ mit Dezimalwerten beim Entitätstyp „Movie“ wurde kein Typ angegeben. Dadurch werden Werte automatisch abgeschnitten, falls diese nicht der Standardgenauigkeit und -skalierung entsprechen. Geben Sie den Spaltentyp von SQL-Server an, der durch „HasColumnType()“ sämtliche Werte unterstützen kann.* “ Sie können diese Warnung ignorieren, sie wird in einem zukünftigen Tutorial korrigiert.</span><span class="sxs-lookup"><span data-stu-id="e96dc-299">The preceding commands generate the following warning: "*No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.*" You can ignore that warning, it will be fixed in a later tutorial.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e96dc-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e96dc-300">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="e96dc-301">Überprüfen des mit Dependency Injection registrierten Kontexts</span><span class="sxs-lookup"><span data-stu-id="e96dc-301">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="e96dc-302">ASP.NET Core wird mit [Dependency Injection](xref:fundamentals/dependency-injection) erstellt.</span><span class="sxs-lookup"><span data-stu-id="e96dc-302">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="e96dc-303">Dienste (z.B. der Datenbankkontext Entity Framework Core) werden über Dependency Injection beim Anwendungsstart registriert.</span><span class="sxs-lookup"><span data-stu-id="e96dc-303">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="e96dc-304">Komponenten, die diese Dienste erfordern (z.B. Razor Pages), werden von diesen Diensten über Konstruktorparameter bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="e96dc-304">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="e96dc-305">Der Konstruktorcode, der eine Datenbankkontext-Instanz abruft, wird später in diesem Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="e96dc-305">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="e96dc-306">Das Gerüstbautool hat automatisch einen Datenbankkontext erstellt und diesen mit dem Dependency Injection-Container registriert.</span><span class="sxs-lookup"><span data-stu-id="e96dc-306">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="e96dc-307">Untersuchen Sie die Methode `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e96dc-307">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="e96dc-308">Die hervorgehobene Zeile wurde vom Gerüst hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="e96dc-308">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="e96dc-309">Der `RazorPagesMovieContext` koordiniert die EF Core-Funktionen (Create, Read, Update, Delete usw.) für das `Movie`-Modell.</span><span class="sxs-lookup"><span data-stu-id="e96dc-309">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="e96dc-310">Der Datenkontext (`RazorPagesMovieContext`) wird aus [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="e96dc-310">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="e96dc-311">Der Datenkontext gibt an, welche Entitäten im Datenmodell enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="e96dc-311">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="e96dc-312">Der vorangehende Code erstellt eine [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1)-Eigenschaft für die Entitätenmenge.</span><span class="sxs-lookup"><span data-stu-id="e96dc-312">The preceding code creates a [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="e96dc-313">In der Terminologie von Entity Framework entspricht eine Entitätenmenge in der Regel einer Datenbanktabelle.</span><span class="sxs-lookup"><span data-stu-id="e96dc-313">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="e96dc-314">Entitäten entsprechen Zeilen in Tabellen.</span><span class="sxs-lookup"><span data-stu-id="e96dc-314">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="e96dc-315">Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions)-Objekt aufrufen.</span><span class="sxs-lookup"><span data-stu-id="e96dc-315">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="e96dc-316">Für die lokale Entwicklung liest das [ASP.NET Core-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der *appsettings.json*-Datei.</span><span class="sxs-lookup"><span data-stu-id="e96dc-316">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e96dc-317">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e96dc-317">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e96dc-318">Untersuchen Sie die Methode `Up`.</span><span class="sxs-lookup"><span data-stu-id="e96dc-318">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e96dc-319">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="e96dc-319">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e96dc-320">Untersuchen Sie die Methode `Up`.</span><span class="sxs-lookup"><span data-stu-id="e96dc-320">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="e96dc-321">Testen der App</span><span class="sxs-lookup"><span data-stu-id="e96dc-321">Test the app</span></span>

* <span data-ttu-id="e96dc-322">Führen Sie die App aus, und fügen Sie `/Movies` an die URL im Browser an (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="e96dc-322">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="e96dc-323">Wenn Sie eine Fehlermeldung erhalten, müssen Sie Folgendes tun:</span><span class="sxs-lookup"><span data-stu-id="e96dc-323">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="e96dc-324">Sie haben den [Migrationsschritt](#pmc) verpasst.</span><span class="sxs-lookup"><span data-stu-id="e96dc-324">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="e96dc-325">Testen Sie den Link **Create** (Erstellen).</span><span class="sxs-lookup"><span data-stu-id="e96dc-325">Test the **Create** link.</span></span>

  ![Seite „Create“](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="e96dc-327">Sie können unter Umständen in das Feld `Price` keine Kommas als Dezimaltrennzeichen eingeben.</span><span class="sxs-lookup"><span data-stu-id="e96dc-327">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="e96dc-328">Zur Unterstützung der [jQuery-Validierung](https://jqueryvalidation.org/) für Gebietsschemas mit einer anderen Sprache als Englisch, in denen ein Komma („,“) als Dezimaltrennzeichen verwendet wird, und für Datums- und Uhrzeitformate, die nicht dem US-englischen Format entsprechen, muss die App globalisiert werden.</span><span class="sxs-lookup"><span data-stu-id="e96dc-328">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="e96dc-329">Globalisierungsanweisungen finden Sie unter [GitHub-Problem](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="e96dc-329">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="e96dc-330">Testen Sie die Links **Edit** (Bearbeiten), **Details** und **Delete** (Löschen).</span><span class="sxs-lookup"><span data-stu-id="e96dc-330">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="e96dc-331">Im nächsten Tutorial finden Sie Erläuterungen zu den Dateien, die durch den Gerüstbau erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="e96dc-331">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e96dc-332">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="e96dc-332">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="e96dc-333">[Zurück: Erste Schritte](xref:tutorials/razor-pages/razor-pages-start)
> [Weiter: Scaffolded Razor Pages (Gerüstbau mit Razor Pages)](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="e96dc-333">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

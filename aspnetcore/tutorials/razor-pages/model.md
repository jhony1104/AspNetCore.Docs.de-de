---
title: Hinzufügen eines Modells zu einer App mit Razor-Seiten in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie Klassen für das Verwalten von Filmen mithilfe von Entity Framework Core (EF Core) zu einer Datenbank hinzufügen.
ms.author: riande
ms.date: 02/12/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: be9f515178d0169a69487f917c7d39c6f11f1292
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815048"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="c52b7-103">Hinzufügen eines Modells zu einer App mit Razor-Seiten in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c52b7-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="c52b7-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c52b7-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="c52b7-105">In diesem Abschnitt werden Klassen für die Verwaltung von Filmen in einer Datenbank hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="c52b7-105">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="c52b7-106">Diese Klassen werden mit [Entity Framework Core](/ef/core) (EF Core) verwendet, um mit einer Datenbank zu arbeiten.</span><span class="sxs-lookup"><span data-stu-id="c52b7-106">These classes are used with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="c52b7-107">EF Core ist ein ORM-Framework (Objektrelationales Mapping, ORM), das den Datenzugriffscode vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="c52b7-107">EF Core is an object-relational mapping (ORM) framework that simplifies data access code.</span></span>

<span data-ttu-id="c52b7-108">Die Modellklassen werden als POCO-Klassen (von „Plain-Old CLR Objects“) bezeichnet, da sie keinerlei Abhängigkeit von EF Core aufweisen.</span><span class="sxs-lookup"><span data-stu-id="c52b7-108">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="c52b7-109">Sie definieren die Eigenschaften einer Datei, die in der Datenbank gespeichert ist.</span><span class="sxs-lookup"><span data-stu-id="c52b7-109">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="c52b7-110">Beispiel [Anzeigen oder Herunterladen](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="c52b7-110">[View or download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) sample.</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="c52b7-111">Hinzufügen eines Datenmodells</span><span class="sxs-lookup"><span data-stu-id="c52b7-111">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c52b7-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c52b7-112">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c52b7-113">Klicken Sie mit der rechten Maustaste auf das Projekt **RazorPagesMovie** > **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="c52b7-113">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="c52b7-114">Geben Sie dem Ordner den Namen *Models*.</span><span class="sxs-lookup"><span data-stu-id="c52b7-114">Name the folder *Models*.</span></span>

<span data-ttu-id="c52b7-115">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="c52b7-115">Right click the *Models* folder.</span></span> <span data-ttu-id="c52b7-116">Wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="c52b7-116">Select **Add** > **Class**.</span></span> <span data-ttu-id="c52b7-117">Nennen Sie die Klasse **Movie**.</span><span class="sxs-lookup"><span data-stu-id="c52b7-117">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c52b7-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c52b7-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c52b7-119">Fügen Sie einen Ordner namens *Models* hinzu.</span><span class="sxs-lookup"><span data-stu-id="c52b7-119">Add a folder named *Models*.</span></span>
* <span data-ttu-id="c52b7-120">Fügen Sie zum Ordner *Modelle* eine Klasse mit dem Namen *Movie.cs* hinzu.</span><span class="sxs-lookup"><span data-stu-id="c52b7-120">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c52b7-121">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="c52b7-121">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c52b7-122">Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt**RazorPagesMovie**, und klicken Sie dann auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="c52b7-122">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="c52b7-123">Geben Sie dem Ordner den Namen *Models*.</span><span class="sxs-lookup"><span data-stu-id="c52b7-123">Name the folder *Models*.</span></span>
* <span data-ttu-id="c52b7-124">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und klicken Sie auf **Hinzufügen** > **Neue Datei**.</span><span class="sxs-lookup"><span data-stu-id="c52b7-124">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="c52b7-125">Führen Sie im Dialogfeld **Neue Datei** folgende Aktionen aus:</span><span class="sxs-lookup"><span data-stu-id="c52b7-125">In the **New File** dialog:</span></span>

  * <span data-ttu-id="c52b7-126">Klicken Sie im linken Bereich auf **Allgemein**.</span><span class="sxs-lookup"><span data-stu-id="c52b7-126">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="c52b7-127">Klicken Sie im mittleren Bereich auf **Leere Klasse**.</span><span class="sxs-lookup"><span data-stu-id="c52b7-127">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="c52b7-128">Geben Sie der Klasse den Namen **Film**, und wählen sie **Neu** aus.</span><span class="sxs-lookup"><span data-stu-id="c52b7-128">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

<span data-ttu-id="c52b7-129">Erstellen Sie das Projekt, um sicherzustellen, dass keine Kompilierungsfehler vorliegen.</span><span class="sxs-lookup"><span data-stu-id="c52b7-129">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="c52b7-130">Erstellen des Gerüsts für das Filmmodell</span><span class="sxs-lookup"><span data-stu-id="c52b7-130">Scaffold the movie model</span></span>

<span data-ttu-id="c52b7-131">In diesem Abschnitt wird das Gerüst für das Filmmodell erstellt.</span><span class="sxs-lookup"><span data-stu-id="c52b7-131">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="c52b7-132">Mit dem Tool für den Gerüstbau werden Seiten für die Vorgänge „Create“ (Erstellen), „Read“ (Lesen), „Update“ (Aktualisieren) und „Delete“ (Löschen), kurz CRUD-Vorgänge, für das Filmmodell erstellt.</span><span class="sxs-lookup"><span data-stu-id="c52b7-132">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c52b7-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c52b7-133">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c52b7-134">Erstellen Sie den Ordner *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="c52b7-134">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="c52b7-135">Klicken Sie mit der rechten Maustaste auf den Ordner *Pages* > **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="c52b7-135">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="c52b7-136">Geben Sie dem Ordner den Namen *Movies*</span><span class="sxs-lookup"><span data-stu-id="c52b7-136">Name the folder *Movies*</span></span>

<span data-ttu-id="c52b7-137">Klicken Sie mit der rechten Maustaste auf den Ordner *Pages/Movies* > **Hinzufügen** > **Neues Gerüstelement**.</span><span class="sxs-lookup"><span data-stu-id="c52b7-137">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Abbildung der vorherigen Anweisungen.](model/_static/sca.png)

<span data-ttu-id="c52b7-139">Wählen Sie im Dialogfeld **Gerüst hinzufügen** den Eintrag **Razor Pages mit Entity Framework (CRUD)**  > **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="c52b7-139">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Abbildung der vorherigen Anweisungen.](model/_static/add_scaffold.png)

<span data-ttu-id="c52b7-141">Vervollständigen Sie das Dialogfeld **Razor Pages mit Entity Framework (CRUD) hinzufügen**:</span><span class="sxs-lookup"><span data-stu-id="c52b7-141">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="c52b7-142">Wählen Sie in der Dropdownliste **Modellklasse** den Eintrag **Film (RazorPagesMovie.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="c52b7-142">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="c52b7-143">Wählen Sie in der Zeile **Datenkontextklasse** das Zeichen **+** (Plus) aus, und akzeptieren Sie den generierten Namen **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="c52b7-143">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="c52b7-144">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="c52b7-144">Select **Add**.</span></span>

![Abbildung der vorherigen Anweisungen.](model/_static/arp.png)

<span data-ttu-id="c52b7-146">Die Datei *appsettings.json* wird mit der Verbindungszeichenfolge aktualisiert, die zum Herstellen einer Verbindung mit einer lokalen Datenbank verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="c52b7-146">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c52b7-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c52b7-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="c52b7-148">Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs*, *Startup.cs*, und *CSPROJ*).</span><span class="sxs-lookup"><span data-stu-id="c52b7-148">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="c52b7-149">Installieren Sie das Gerüstbautool:</span><span class="sxs-lookup"><span data-stu-id="c52b7-149">Install the scaffolding tool:</span></span>

  ```console
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="c52b7-150">**Für Windows**: Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="c52b7-150">**For Windows**: Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="c52b7-151">**Für MacOS und Linux**: Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="c52b7-151">**For macOS and Linux**: Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c52b7-152">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="c52b7-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c52b7-153">Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs*, *Startup.cs*, und *CSPROJ*).</span><span class="sxs-lookup"><span data-stu-id="c52b7-153">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="c52b7-154">Installieren Sie das Gerüstbautool:</span><span class="sxs-lookup"><span data-stu-id="c52b7-154">Install the scaffolding tool:</span></span>

  ```console
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="c52b7-155">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="c52b7-155">Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

<span data-ttu-id="c52b7-156">Der Gerüstprozess erstellt und ändert folgende Dateien:</span><span class="sxs-lookup"><span data-stu-id="c52b7-156">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="c52b7-157">Erstellte Dateien</span><span class="sxs-lookup"><span data-stu-id="c52b7-157">Files created</span></span>

* <span data-ttu-id="c52b7-158">*Pages/Movies*: „Create“, „Delete“, „Details“, „Edit“ und „Index“.</span><span class="sxs-lookup"><span data-stu-id="c52b7-158">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="c52b7-159">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="c52b7-159">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="c52b7-160">Datei aktualisiert</span><span class="sxs-lookup"><span data-stu-id="c52b7-160">File updated</span></span>

* <span data-ttu-id="c52b7-161">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="c52b7-161">*Startup.cs*</span></span>

<span data-ttu-id="c52b7-162">Die erstellten und aktualisierten Daten werden im nächsten Abschnitt erläutert.</span><span class="sxs-lookup"><span data-stu-id="c52b7-162">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="c52b7-163">Anfängliche Migration</span><span class="sxs-lookup"><span data-stu-id="c52b7-163">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c52b7-164">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c52b7-164">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c52b7-165">In diesem Abschnitt wird die Paket-Manager-Konsole (Package Manager Console, PMC) für Folgendes verwendet:</span><span class="sxs-lookup"><span data-stu-id="c52b7-165">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="c52b7-166">Fügen Sie eine anfängliche Migration hinzu.</span><span class="sxs-lookup"><span data-stu-id="c52b7-166">Add an initial migration.</span></span>
* <span data-ttu-id="c52b7-167">Aktualisieren Sie die Datenbank mit der anfänglichen Migration.</span><span class="sxs-lookup"><span data-stu-id="c52b7-167">Update the database with the initial migration.</span></span>

<span data-ttu-id="c52b7-168">Öffnen Sie das Menü **Extras**, und wählen Sie **NuGet-Paket-Manager** > **Paket-Manager-Konsole** aus.</span><span class="sxs-lookup"><span data-stu-id="c52b7-168">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC-Menü](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="c52b7-170">Geben Sie in der PMC die folgenden Befehle ein:</span><span class="sxs-lookup"><span data-stu-id="c52b7-170">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration Initial
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c52b7-171">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c52b7-171">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c52b7-172">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="c52b7-172">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="c52b7-173">Mit den vorherigen Befehlen wird die folgende Warnung erstellt: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="c52b7-173">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="c52b7-174">Dadurch werden Werte automatisch abgeschnitten, falls diese nicht der Standardgenauigkeit und -skalierung entsprechen.</span><span class="sxs-lookup"><span data-stu-id="c52b7-174">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="c52b7-175">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'." („Für die Spalte „Price“ mit Dezimalwerten beim Entitätstyp „Movie“ wurde kein Typ angegeben. Dadurch werden Werte automatisch abgeschnitten, falls diese nicht der Standardgenauigkeit und -skalierung entsprechen. Geben Sie den Spaltentyp von SQL-Server an, der durch „ForHasColumnType()“ sämtliche Werte unterstützen kann.")</span><span class="sxs-lookup"><span data-stu-id="c52b7-175">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="c52b7-176">Sie können diese Warnung ignorieren, sie wird in einem späteren Tutorial behoben.</span><span class="sxs-lookup"><span data-stu-id="c52b7-176">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="c52b7-177">Mit dem Befehl `ef migrations add InitialCreate` wird Code generiert, um das anfängliche Datenbankschema zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="c52b7-177">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span> <span data-ttu-id="c52b7-178">Das Schema basiert auf dem Modell, das in `DbContext` angegeben ist (in der Datei *RazorPagesMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="c52b7-178">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="c52b7-179">Das Argument `InitialCreate` wird verwendet, um die Migrationen zu benennen.</span><span class="sxs-lookup"><span data-stu-id="c52b7-179">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="c52b7-180">Es kann jeder Name verwendet werden, aber per Konvention wird ein Name ausgewählt, der die Migration beschreibt.</span><span class="sxs-lookup"><span data-stu-id="c52b7-180">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="c52b7-181">Der Befehl `ef database update` führt die Methode `Up` in der Datei *Migrations/\<time-stamp>_InitialCreate.cs* aus.</span><span class="sxs-lookup"><span data-stu-id="c52b7-181">The `ef database update` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="c52b7-182">Die Methode `Up` erstellt die Datenbank.</span><span class="sxs-lookup"><span data-stu-id="c52b7-182">The `Up` method creates the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c52b7-183">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c52b7-183">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="c52b7-184">Überprüfen des mit Dependency Injection registrierten Kontexts</span><span class="sxs-lookup"><span data-stu-id="c52b7-184">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="c52b7-185">ASP.NET Core wird mit [Dependency Injection](xref:fundamentals/dependency-injection) erstellt.</span><span class="sxs-lookup"><span data-stu-id="c52b7-185">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="c52b7-186">Dienste (z.B. der Datenbankkontext Entity Framework Core) werden über Dependency Injection beim Anwendungsstart registriert.</span><span class="sxs-lookup"><span data-stu-id="c52b7-186">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="c52b7-187">Komponenten, die diese Dienste erfordern (z.B. Razor Pages), werden von diesen Diensten über Konstruktorparameter bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="c52b7-187">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="c52b7-188">Der Konstruktorcode, der eine Datenbankkontext-Instanz abruft, wird später in diesem Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="c52b7-188">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="c52b7-189">Das Gerüstbautool hat automatisch einen Datenbankkontext erstellt und diesen mit dem Dependency Injection-Container registriert.</span><span class="sxs-lookup"><span data-stu-id="c52b7-189">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="c52b7-190">Untersuchen Sie die Methode `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="c52b7-190">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="c52b7-191">Die hervorgehobene Zeile wurde vom Gerüst hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="c52b7-191">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="c52b7-192">Der `RazorPagesMovieContext` koordiniert die EF Core-Funktionen (Create, Read, Update, Delete usw.) für das `Movie`-Modell.</span><span class="sxs-lookup"><span data-stu-id="c52b7-192">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="c52b7-193">Der Datenkontext (`RazorPagesMovieContext`) wird aus [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="c52b7-193">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="c52b7-194">Der Datenkontext gibt an, welche Entitäten im Datenmodell enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="c52b7-194">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="c52b7-195">Der vorangehende Code erstellt eine [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1)-Eigenschaft für die Entitätenmenge.</span><span class="sxs-lookup"><span data-stu-id="c52b7-195">The preceding code creates a [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="c52b7-196">In der Terminologie von Entity Framework entspricht eine Entitätenmenge in der Regel einer Datenbanktabelle.</span><span class="sxs-lookup"><span data-stu-id="c52b7-196">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="c52b7-197">Entitäten entsprechen Zeilen in Tabellen.</span><span class="sxs-lookup"><span data-stu-id="c52b7-197">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="c52b7-198">Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions)-Objekt aufrufen.</span><span class="sxs-lookup"><span data-stu-id="c52b7-198">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="c52b7-199">Für die lokale Entwicklung liest das [ASP.NET Core-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der *appsettings.json*-Datei.</span><span class="sxs-lookup"><span data-stu-id="c52b7-199">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c52b7-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c52b7-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c52b7-201">Untersuchen Sie die Methode `Up`.</span><span class="sxs-lookup"><span data-stu-id="c52b7-201">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c52b7-202">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="c52b7-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="c52b7-203">Untersuchen Sie die Methode `Up`.</span><span class="sxs-lookup"><span data-stu-id="c52b7-203">Examine the `Up` method.</span></span>

---

<span data-ttu-id="c52b7-204">Mit dem Befehl `Add-Migration` wird Code generiert, um das anfängliche Datenbankschema zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="c52b7-204">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="c52b7-205">Das Schema basiert auf dem Modell, das in der Datei *Data/RazorPagesMovieContext.cs* für `RazorPagesMovieContext` angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="c52b7-205">The schema is based on the model specified in the `RazorPagesMovieContext` (In the *Data/RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="c52b7-206">Das Argument `Initial` wird verwendet, um die Migrationen zu benennen.</span><span class="sxs-lookup"><span data-stu-id="c52b7-206">The `Initial` argument is used to name the migrations.</span></span> <span data-ttu-id="c52b7-207">Es kann jeder Name verwendet werden, aber per Konvention wird ein Name verwendet, der die Migration beschreibt.</span><span class="sxs-lookup"><span data-stu-id="c52b7-207">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="c52b7-208">Weitere Informationen finden Sie unter <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="c52b7-208">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="c52b7-209">Mit dem Befehl `Update-Database` führen Sie die Methode `Up` in der Datei *Migrations/{time-stamp}_InitialCreate.cs* aus, mit der die Datenbank erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="c52b7-209">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="c52b7-210">Testen der App</span><span class="sxs-lookup"><span data-stu-id="c52b7-210">Test the app</span></span>

* <span data-ttu-id="c52b7-211">Führen Sie die App aus, und fügen Sie `/Movies` an die URL im Browser an (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="c52b7-211">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="c52b7-212">Wenn Sie eine Fehlermeldung erhalten, müssen Sie Folgendes tun:</span><span class="sxs-lookup"><span data-stu-id="c52b7-212">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="c52b7-213">Sie haben den [Migrationsschritt](#pmc) verpasst.</span><span class="sxs-lookup"><span data-stu-id="c52b7-213">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="c52b7-214">Testen Sie den Link **Create** (Erstellen).</span><span class="sxs-lookup"><span data-stu-id="c52b7-214">Test the **Create** link.</span></span>

  ![Seite „Create“](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="c52b7-216">Sie können unter Umständen in das Feld `Price` keine Kommas als Dezimaltrennzeichen eingeben.</span><span class="sxs-lookup"><span data-stu-id="c52b7-216">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="c52b7-217">Zur Unterstützung der [jQuery-Validierung](https://jqueryvalidation.org/) für Gebietsschemas mit einer anderen Sprache als Englisch, in denen ein Komma („,“) als Dezimaltrennzeichen verwendet wird, und für Datums- und Uhrzeitformate, die nicht dem US-englischen Format entsprechen, muss die App globalisiert werden.</span><span class="sxs-lookup"><span data-stu-id="c52b7-217">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="c52b7-218">Globalisierungsanweisungen finden Sie unter [GitHub-Problem](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="c52b7-218">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="c52b7-219">Testen Sie die Links **Edit** (Bearbeiten), **Details** und **Delete** (Löschen).</span><span class="sxs-lookup"><span data-stu-id="c52b7-219">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="c52b7-220">Im nächsten Tutorial finden Sie Erläuterungen zu den Dateien, die durch den Gerüstbau erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="c52b7-220">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c52b7-221">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="c52b7-221">Additional resources</span></span>

* [<span data-ttu-id="c52b7-222">Dieses Tutorial auf YouTube</span><span class="sxs-lookup"><span data-stu-id="c52b7-222">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=sFVIsdR_RcM)

> [!div class="step-by-step"]
> <span data-ttu-id="c52b7-223">[Zurück: Erste Schritte](xref:tutorials/razor-pages/razor-pages-start)
> [Weiter: Scaffolded Razor Pages (Gerüstbau mit Razor Pages)](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="c52b7-223">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

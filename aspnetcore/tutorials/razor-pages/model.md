---
title: Hinzufügen eines Modells zu einer App mit Razor-Seiten in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie Klassen für das Verwalten von Filmen mithilfe von Entity Framework Core (EF Core) zu einer Datenbank hinzufügen.
ms.author: riande
ms.date: 12/05/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: 0934c94236b507f2f57200ded4344a71c483d356
ms.sourcegitcommit: 5fe17e54f7e4267a2fdecc6f9aa1d41166cecc34
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75737857"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="1842d-103">Hinzufügen eines Modells zu einer App mit Razor-Seiten in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1842d-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="1842d-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1842d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="1842d-105">In diesem Abschnitt werden Klassen für die Verwaltung von Filmen in einer plattformübergreifenden [SQLite-Datenbank](https://www.sqlite.org/index.html) hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="1842d-105">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="1842d-106">Apps, die aus einer ASP.NET Core-Vorlage erstellt werden, verwenden eine SQLite-Datenbank.</span><span class="sxs-lookup"><span data-stu-id="1842d-106">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="1842d-107">Die Modellklassen der App werden mit [Entity Framework Core (EF Core)](/ef/core) ([SQLite-EF Core-Datenbankanbieter](/ef/core/providers/sqlite)) verwendet, um mit der Datenbank zu arbeiten.</span><span class="sxs-lookup"><span data-stu-id="1842d-107">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="1842d-108">EF Core ist ein ORM-Framework (Objektrelationales Mapping, ORM), das den Datenzugriff vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="1842d-108">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="1842d-109">Die Modellklassen werden als POCO-Klassen (von „Plain-Old CLR Objects“) bezeichnet, da sie keinerlei Abhängigkeit von EF Core aufweisen.</span><span class="sxs-lookup"><span data-stu-id="1842d-109">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="1842d-110">Sie definieren die Eigenschaften einer Datei, die in der Datenbank gespeichert ist.</span><span class="sxs-lookup"><span data-stu-id="1842d-110">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="1842d-111">Hinzufügen eines Datenmodells</span><span class="sxs-lookup"><span data-stu-id="1842d-111">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1842d-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1842d-112">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1842d-113">Klicken Sie mit der rechten Maustaste auf das Projekt **RazorPagesMovie** > **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="1842d-113">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="1842d-114">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="1842d-114">Name the folder *Models*.</span></span>

<span data-ttu-id="1842d-115">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="1842d-115">Right click the *Models* folder.</span></span> <span data-ttu-id="1842d-116">Wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="1842d-116">Select **Add** > **Class**.</span></span> <span data-ttu-id="1842d-117">Nennen Sie die Klasse **Movie**.</span><span class="sxs-lookup"><span data-stu-id="1842d-117">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1842d-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1842d-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1842d-119">Fügen Sie einen Ordner namens *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="1842d-119">Add a folder named *Models*.</span></span>
* <span data-ttu-id="1842d-120">Fügen Sie zum Ordner *Modelle* eine Klasse mit dem Namen *Movie.cs* hinzu.</span><span class="sxs-lookup"><span data-stu-id="1842d-120">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1842d-121">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="1842d-121">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="1842d-122">Klicken Sie im Lösungspad mit der rechten Maustaste auf das Projekt **RazorPagesMovie**, und wählen Sie dann **Hinzufügen** > **Neuer Ordner...** aus. Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="1842d-122">In Solution Pad, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="1842d-123">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und klicken Sie auf **Hinzufügen** > **Neue Datei...**.</span><span class="sxs-lookup"><span data-stu-id="1842d-123">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="1842d-124">Führen Sie im Dialogfeld **Neue Datei** folgende Aktionen aus:</span><span class="sxs-lookup"><span data-stu-id="1842d-124">In the **New File** dialog:</span></span>

  * <span data-ttu-id="1842d-125">Klicken Sie im linken Bereich auf **Allgemein**.</span><span class="sxs-lookup"><span data-stu-id="1842d-125">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="1842d-126">Klicken Sie im mittleren Bereich auf **Leere Klasse**.</span><span class="sxs-lookup"><span data-stu-id="1842d-126">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="1842d-127">Geben Sie der Klasse den Namen **Film**, und wählen sie **Neu** aus.</span><span class="sxs-lookup"><span data-stu-id="1842d-127">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="1842d-128">Erstellen Sie das Projekt, um sicherzustellen, dass keine Kompilierungsfehler vorliegen.</span><span class="sxs-lookup"><span data-stu-id="1842d-128">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="1842d-129">Erstellen des Gerüsts für das Filmmodell</span><span class="sxs-lookup"><span data-stu-id="1842d-129">Scaffold the movie model</span></span>

<span data-ttu-id="1842d-130">In diesem Abschnitt wird das Gerüst für das Filmmodell erstellt.</span><span class="sxs-lookup"><span data-stu-id="1842d-130">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="1842d-131">Mit dem Tool für den Gerüstbau werden Seiten für die Vorgänge „Create“ (Erstellen), „Read“ (Lesen), „Update“ (Aktualisieren) und „Delete“ (Löschen), kurz CRUD-Vorgänge, für das Filmmodell erstellt.</span><span class="sxs-lookup"><span data-stu-id="1842d-131">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1842d-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1842d-132">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1842d-133">Erstellen Sie den Ordner *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="1842d-133">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="1842d-134">Klicken Sie mit der rechten Maustaste auf den Ordner *Pages* > **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="1842d-134">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="1842d-135">Geben Sie dem Ordner den Namen *Movies*</span><span class="sxs-lookup"><span data-stu-id="1842d-135">Name the folder *Movies*</span></span>

<span data-ttu-id="1842d-136">Klicken Sie mit der rechten Maustaste auf den Ordner *Pages/Movies* > **Hinzufügen** > **Neues Gerüstelement**.</span><span class="sxs-lookup"><span data-stu-id="1842d-136">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Abbildung der vorherigen Anweisungen.](model/_static/sca.png)

<span data-ttu-id="1842d-138">Wählen Sie im Dialogfeld **Gerüst hinzufügen** den Eintrag **Razor Pages mit Entity Framework (CRUD)** > **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="1842d-138">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Abbildung der vorherigen Anweisungen.](model/_static/add_scaffold.png)

<span data-ttu-id="1842d-140">Vervollständigen Sie das Dialogfeld **Razor Pages mit Entity Framework (CRUD) hinzufügen**:</span><span class="sxs-lookup"><span data-stu-id="1842d-140">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="1842d-141">Wählen Sie in der Dropdownliste **Modellklasse** den Eintrag **Film (RazorPagesMovie.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="1842d-141">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="1842d-142">Wählen Sie in der Zeile **Datenkontextklasse** das Zeichen **+** (Plus) aus, und ändern Sie den generierten Namen RazorPagesMovie.**Models**.RazorPagesMovieContext in RazorPagesMovie.**Data**.RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="1842d-142">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="1842d-143">[Dieses Änderung](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) ist nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="1842d-143">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="1842d-144">Sie erstellt die Datenbank-Kontextklasse mit dem korrekten Namespace.</span><span class="sxs-lookup"><span data-stu-id="1842d-144">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="1842d-145">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="1842d-145">Select **Add**.</span></span>

![Abbildung der vorherigen Anweisungen.](model/_static/3/arp.png)

<span data-ttu-id="1842d-147">Die Datei *appsettings.json* wird mit der Verbindungszeichenfolge aktualisiert, die zum Herstellen einer Verbindung mit einer lokalen Datenbank verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="1842d-147">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1842d-148">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1842d-148">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="1842d-149">Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs*, *Startup.cs*, und *CSPROJ*).</span><span class="sxs-lookup"><span data-stu-id="1842d-149">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="1842d-150">Installieren Sie das Gerüstbautool:</span><span class="sxs-lookup"><span data-stu-id="1842d-150">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="1842d-151">**Für Windows**: Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="1842d-151">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="1842d-152">**Für MacOS und Linux**: Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="1842d-152">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1842d-153">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="1842d-153">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="1842d-154">Erstellen Sie den Ordner *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="1842d-154">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="1842d-155">Klicken Sie mit der rechten Maustaste auf den Ordner *Pages* > **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="1842d-155">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="1842d-156">Geben Sie dem Ordner den Namen *Movies*</span><span class="sxs-lookup"><span data-stu-id="1842d-156">Name the folder *Movies*</span></span>

<span data-ttu-id="1842d-157">Klicken Sie mit der rechten Maustaste auf den Ordner *Pages/Movies* > **Hinzufügen** > **Neuer Gerüstbau...**.</span><span class="sxs-lookup"><span data-stu-id="1842d-157">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![Abbildung der vorherigen Anweisungen.](model/_static/scaMac.png)

<span data-ttu-id="1842d-159">Wählen Sie im Dialogfeld **Neuer Gerüstbau** den Eintrag **Razor Pages mit Entity Framework (CRUD)** > **Weiter** aus.</span><span class="sxs-lookup"><span data-stu-id="1842d-159">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![Abbildung der vorherigen Anweisungen.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="1842d-161">Vervollständigen Sie das Dialogfeld **Razor Pages mit Entity Framework (CRUD) hinzufügen**:</span><span class="sxs-lookup"><span data-stu-id="1842d-161">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="1842d-162">Wählen Sie in der Dropdownliste **Modellklasse** den Eintrag **Film (RazorPagesMovie.Models)** aus, oder geben Sie ihn ein.</span><span class="sxs-lookup"><span data-stu-id="1842d-162">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="1842d-163">Geben Sie in der Zeile **Datenkontextklasse** den Namen der neuen Klasse „RazorPagesMovie.**Data**.RazorPagesMovieContext“ ein.</span><span class="sxs-lookup"><span data-stu-id="1842d-163">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="1842d-164">[Dieses Änderung](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) ist nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="1842d-164">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="1842d-165">Sie erstellt die Datenbank-Kontextklasse mit dem korrekten Namespace.</span><span class="sxs-lookup"><span data-stu-id="1842d-165">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="1842d-166">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="1842d-166">Select **Add**.</span></span>

![Abbildung der vorherigen Anweisungen.](model/_static/arpMac.png)

<span data-ttu-id="1842d-168">Die Datei *appsettings.json* wird mit der Verbindungszeichenfolge aktualisiert, die zum Herstellen einer Verbindung mit einer lokalen Datenbank verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="1842d-168">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

### <a name="files-created"></a><span data-ttu-id="1842d-169">Erstellte Dateien</span><span class="sxs-lookup"><span data-stu-id="1842d-169">Files created</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1842d-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1842d-170">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1842d-171">Der Gerüstprozess erstellt und ändert folgende Dateien:</span><span class="sxs-lookup"><span data-stu-id="1842d-171">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="1842d-172">*Pages/Movies*: „Create“, „Delete“, „Details“, „Edit“ und „Index“.</span><span class="sxs-lookup"><span data-stu-id="1842d-172">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="1842d-173">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="1842d-173">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="1842d-174">Aktualisiert</span><span class="sxs-lookup"><span data-stu-id="1842d-174">Updated</span></span>

* <span data-ttu-id="1842d-175">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="1842d-175">*Startup.cs*</span></span>

<span data-ttu-id="1842d-176">Die erstellten und aktualisierten Daten werden im nächsten Abschnitt erläutert.</span><span class="sxs-lookup"><span data-stu-id="1842d-176">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1842d-177">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="1842d-177">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="1842d-178">Der Gerüstprozess erstellt und ändert folgende Dateien:</span><span class="sxs-lookup"><span data-stu-id="1842d-178">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="1842d-179">*Pages/Movies*: „Create“, „Delete“, „Details“, „Edit“ und „Index“.</span><span class="sxs-lookup"><span data-stu-id="1842d-179">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="1842d-180">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="1842d-180">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="1842d-181">Aktualisiert</span><span class="sxs-lookup"><span data-stu-id="1842d-181">Updated</span></span>

* <span data-ttu-id="1842d-182">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="1842d-182">*Startup.cs*</span></span>

<span data-ttu-id="1842d-183">Die erstellten und aktualisierten Daten werden im nächsten Abschnitt erläutert.</span><span class="sxs-lookup"><span data-stu-id="1842d-183">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1842d-184">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1842d-184">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1842d-185">Der Gerüstbauprozess erstellt die folgenden Dateien:</span><span class="sxs-lookup"><span data-stu-id="1842d-185">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="1842d-186">*Pages/Movies*: „Create“, „Delete“, „Details“, „Edit“ und „Index“.</span><span class="sxs-lookup"><span data-stu-id="1842d-186">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="1842d-187">Die erstellten Daten werden im nächsten Abschnitt erläutert.</span><span class="sxs-lookup"><span data-stu-id="1842d-187">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="1842d-188">Anfängliche Migration</span><span class="sxs-lookup"><span data-stu-id="1842d-188">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1842d-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1842d-189">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1842d-190">In diesem Abschnitt wird die Paket-Manager-Konsole (Package Manager Console, PMC) für Folgendes verwendet:</span><span class="sxs-lookup"><span data-stu-id="1842d-190">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="1842d-191">Fügen Sie eine anfängliche Migration hinzu.</span><span class="sxs-lookup"><span data-stu-id="1842d-191">Add an initial migration.</span></span>
* <span data-ttu-id="1842d-192">Aktualisieren Sie die Datenbank mit der anfänglichen Migration.</span><span class="sxs-lookup"><span data-stu-id="1842d-192">Update the database with the initial migration.</span></span>

<span data-ttu-id="1842d-193">Wählen Sie im Menü **Tools** **NuGet-Paket-Manager** > **Paket-Manager-Konsole** aus.</span><span class="sxs-lookup"><span data-stu-id="1842d-193">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC-Menü](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="1842d-195">Geben Sie in der PMC die folgenden Befehle ein:</span><span class="sxs-lookup"><span data-stu-id="1842d-195">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1842d-196">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1842d-196">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1842d-197">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="1842d-197">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="1842d-198">Mit den vorherigen Befehlen wird die folgende Warnung erstellt: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="1842d-198">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="1842d-199">Dadurch werden Werte automatisch abgeschnitten, falls diese nicht der Standardgenauigkeit und -skalierung entsprechen.</span><span class="sxs-lookup"><span data-stu-id="1842d-199">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="1842d-200">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'." („Für die Spalte „Price“ mit Dezimalwerten beim Entitätstyp „Movie“ wurde kein Typ angegeben. Dadurch werden Werte automatisch abgeschnitten, falls diese nicht der Standardgenauigkeit und -skalierung entsprechen. Geben Sie den Spaltentyp von SQL-Server an, der durch „ForHasColumnType()“ sämtliche Werte unterstützen kann.")</span><span class="sxs-lookup"><span data-stu-id="1842d-200">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="1842d-201">Sie können diese Warnung ignorieren, sie wird in einem späteren Tutorial behoben.</span><span class="sxs-lookup"><span data-stu-id="1842d-201">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="1842d-202">Mit dem Migrationsbefehl wird Code generiert, um das anfängliche Datenbankschema zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="1842d-202">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="1842d-203">Das Schema basiert auf dem Modell, das in `DbContext` angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="1842d-203">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="1842d-204">Das Argument `InitialCreate` wird verwendet, um die Migrationen zu benennen.</span><span class="sxs-lookup"><span data-stu-id="1842d-204">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="1842d-205">Es kann jeder Name verwendet werden, aber per Konvention wird ein Name ausgewählt, der die Migration beschreibt.</span><span class="sxs-lookup"><span data-stu-id="1842d-205">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="1842d-206">Der `update`-Befehl führt die `Up`-Methode in Migrationen aus, die nicht angewendet wurden.</span><span class="sxs-lookup"><span data-stu-id="1842d-206">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="1842d-207">In diesem Fall führt `update` die `Up`-Methode in der Datei *Migrations/\<Zeitstempel>_InitialCreate.cs* aus, mit der die Datenbank erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="1842d-207">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1842d-208">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1842d-208">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="1842d-209">Überprüfen des mit Dependency Injection registrierten Kontexts</span><span class="sxs-lookup"><span data-stu-id="1842d-209">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="1842d-210">ASP.NET Core wird mit [Dependency Injection](xref:fundamentals/dependency-injection) erstellt.</span><span class="sxs-lookup"><span data-stu-id="1842d-210">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="1842d-211">Dienste (z.B. der Datenbankkontext Entity Framework Core) werden über Dependency Injection beim Anwendungsstart registriert.</span><span class="sxs-lookup"><span data-stu-id="1842d-211">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="1842d-212">Komponenten, die diese Dienste erfordern (z.B. Razor Pages), werden von diesen Diensten über Konstruktorparameter bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="1842d-212">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="1842d-213">Der Konstruktorcode, der eine Datenbankkontext-Instanz abruft, wird später in diesem Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="1842d-213">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="1842d-214">Das Gerüstbautool hat automatisch einen Datenbankkontext erstellt und diesen mit dem Dependency Injection-Container registriert.</span><span class="sxs-lookup"><span data-stu-id="1842d-214">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="1842d-215">Untersuchen Sie die Methode `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="1842d-215">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="1842d-216">Die hervorgehobene Zeile wurde vom Gerüst hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="1842d-216">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="1842d-217">Der `RazorPagesMovieContext` koordiniert die EF Core-Funktionen (Create, Read, Update, Delete usw.) für das `Movie`-Modell.</span><span class="sxs-lookup"><span data-stu-id="1842d-217">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="1842d-218">Der Datenkontext (`RazorPagesMovieContext`) wird aus [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="1842d-218">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="1842d-219">Der Datenkontext gibt an, welche Entitäten im Datenmodell enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="1842d-219">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="1842d-220">Der vorangehende Code erstellt eine [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1)-Eigenschaft für die Entitätenmenge.</span><span class="sxs-lookup"><span data-stu-id="1842d-220">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="1842d-221">In der Terminologie von Entity Framework entspricht eine Entitätenmenge in der Regel einer Datenbanktabelle.</span><span class="sxs-lookup"><span data-stu-id="1842d-221">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="1842d-222">Entitäten entsprechen Zeilen in Tabellen.</span><span class="sxs-lookup"><span data-stu-id="1842d-222">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="1842d-223">Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions)-Objekt aufrufen.</span><span class="sxs-lookup"><span data-stu-id="1842d-223">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="1842d-224">Für die lokale Entwicklung liest das [ASP.NET Core-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der *appsettings.json*-Datei.</span><span class="sxs-lookup"><span data-stu-id="1842d-224">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1842d-225">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1842d-225">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1842d-226">Untersuchen Sie die Methode `Up`.</span><span class="sxs-lookup"><span data-stu-id="1842d-226">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1842d-227">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="1842d-227">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="1842d-228">Untersuchen Sie die Methode `Up`.</span><span class="sxs-lookup"><span data-stu-id="1842d-228">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="1842d-229">Testen der App</span><span class="sxs-lookup"><span data-stu-id="1842d-229">Test the app</span></span>

* <span data-ttu-id="1842d-230">Führen Sie die App aus, und fügen Sie `/Movies` an die URL im Browser an (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="1842d-230">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="1842d-231">Wenn Sie eine Fehlermeldung erhalten, müssen Sie Folgendes tun:</span><span class="sxs-lookup"><span data-stu-id="1842d-231">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="1842d-232">Sie haben den [Migrationsschritt](#pmc) verpasst.</span><span class="sxs-lookup"><span data-stu-id="1842d-232">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="1842d-233">Testen Sie den Link **Create** (Erstellen).</span><span class="sxs-lookup"><span data-stu-id="1842d-233">Test the **Create** link.</span></span>

  ![Seite „Create“](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="1842d-235">Sie können unter Umständen in das Feld `Price` keine Kommas als Dezimaltrennzeichen eingeben.</span><span class="sxs-lookup"><span data-stu-id="1842d-235">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="1842d-236">Zur Unterstützung der [jQuery-Validierung](https://jqueryvalidation.org/) für Gebietsschemas mit einer anderen Sprache als Englisch, in denen ein Komma („,“) als Dezimaltrennzeichen verwendet wird, und für Datums- und Uhrzeitformate, die nicht dem US-englischen Format entsprechen, muss die App globalisiert werden.</span><span class="sxs-lookup"><span data-stu-id="1842d-236">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="1842d-237">Globalisierungsanweisungen finden Sie unter [GitHub-Problem](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="1842d-237">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="1842d-238">Testen Sie die Links **Edit** (Bearbeiten), **Details** und **Delete** (Löschen).</span><span class="sxs-lookup"><span data-stu-id="1842d-238">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="1842d-239">Im nächsten Tutorial finden Sie Erläuterungen zu den Dateien, die durch den Gerüstbau erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="1842d-239">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1842d-240">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="1842d-240">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="1842d-241">[Zurück: Erste Schritte](xref:tutorials/razor-pages/razor-pages-start)
> [Weiter: Scaffolded Razor Pages (Gerüstbau mit Razor Pages)](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="1842d-241">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1842d-242">In diesem Abschnitt werden Klassen für die Verwaltung von Filmen in einer plattformübergreifenden [SQLite-Datenbank](https://www.sqlite.org/index.html) hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="1842d-242">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="1842d-243">Apps, die aus einer ASP.NET Core-Vorlage erstellt werden, verwenden eine SQLite-Datenbank.</span><span class="sxs-lookup"><span data-stu-id="1842d-243">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="1842d-244">Die Modellklassen der App werden mit [Entity Framework Core (EF Core)](/ef/core) ([SQLite-EF Core-Datenbankanbieter](/ef/core/providers/sqlite)) verwendet, um mit der Datenbank zu arbeiten.</span><span class="sxs-lookup"><span data-stu-id="1842d-244">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="1842d-245">EF Core ist ein ORM-Framework (Objektrelationales Mapping, ORM), das den Datenzugriff vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="1842d-245">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="1842d-246">Die Modellklassen werden als POCO-Klassen (von „Plain-Old CLR Objects“) bezeichnet, da sie keinerlei Abhängigkeit von EF Core aufweisen.</span><span class="sxs-lookup"><span data-stu-id="1842d-246">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="1842d-247">Sie definieren die Eigenschaften einer Datei, die in der Datenbank gespeichert ist.</span><span class="sxs-lookup"><span data-stu-id="1842d-247">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="1842d-248">Hinzufügen eines Datenmodells</span><span class="sxs-lookup"><span data-stu-id="1842d-248">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1842d-249">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1842d-249">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1842d-250">Klicken Sie mit der rechten Maustaste auf das Projekt **RazorPagesMovie** > **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="1842d-250">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="1842d-251">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="1842d-251">Name the folder *Models*.</span></span>

<span data-ttu-id="1842d-252">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="1842d-252">Right click the *Models* folder.</span></span> <span data-ttu-id="1842d-253">Wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="1842d-253">Select **Add** > **Class**.</span></span> <span data-ttu-id="1842d-254">Nennen Sie die Klasse **Movie**.</span><span class="sxs-lookup"><span data-stu-id="1842d-254">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1842d-255">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1842d-255">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1842d-256">Fügen Sie einen Ordner namens *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="1842d-256">Add a folder named *Models*.</span></span>
* <span data-ttu-id="1842d-257">Fügen Sie zum Ordner *Modelle* eine Klasse mit dem Namen *Movie.cs* hinzu.</span><span class="sxs-lookup"><span data-stu-id="1842d-257">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1842d-258">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="1842d-258">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="1842d-259">Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt**RazorPagesMovie**, und klicken Sie dann auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="1842d-259">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="1842d-260">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="1842d-260">Name the folder *Models*.</span></span>
* <span data-ttu-id="1842d-261">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und klicken Sie auf **Hinzufügen** > **Neue Datei**.</span><span class="sxs-lookup"><span data-stu-id="1842d-261">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="1842d-262">Führen Sie im Dialogfeld **Neue Datei** folgende Aktionen aus:</span><span class="sxs-lookup"><span data-stu-id="1842d-262">In the **New File** dialog:</span></span>

  * <span data-ttu-id="1842d-263">Klicken Sie im linken Bereich auf **Allgemein**.</span><span class="sxs-lookup"><span data-stu-id="1842d-263">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="1842d-264">Klicken Sie im mittleren Bereich auf **Leere Klasse**.</span><span class="sxs-lookup"><span data-stu-id="1842d-264">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="1842d-265">Geben Sie der Klasse den Namen **Film**, und wählen sie **Neu** aus.</span><span class="sxs-lookup"><span data-stu-id="1842d-265">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="1842d-266">Erstellen Sie das Projekt, um sicherzustellen, dass keine Kompilierungsfehler vorliegen.</span><span class="sxs-lookup"><span data-stu-id="1842d-266">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="1842d-267">Erstellen des Gerüsts für das Filmmodell</span><span class="sxs-lookup"><span data-stu-id="1842d-267">Scaffold the movie model</span></span>

<span data-ttu-id="1842d-268">In diesem Abschnitt wird das Gerüst für das Filmmodell erstellt.</span><span class="sxs-lookup"><span data-stu-id="1842d-268">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="1842d-269">Mit dem Tool für den Gerüstbau werden Seiten für die Vorgänge „Create“ (Erstellen), „Read“ (Lesen), „Update“ (Aktualisieren) und „Delete“ (Löschen), kurz CRUD-Vorgänge, für das Filmmodell erstellt.</span><span class="sxs-lookup"><span data-stu-id="1842d-269">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1842d-270">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1842d-270">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1842d-271">Erstellen Sie den Ordner *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="1842d-271">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="1842d-272">Klicken Sie mit der rechten Maustaste auf den Ordner *Pages* > **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="1842d-272">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="1842d-273">Geben Sie dem Ordner den Namen *Movies*</span><span class="sxs-lookup"><span data-stu-id="1842d-273">Name the folder *Movies*</span></span>

<span data-ttu-id="1842d-274">Klicken Sie mit der rechten Maustaste auf den Ordner *Pages/Movies* > **Hinzufügen** > **Neues Gerüstelement**.</span><span class="sxs-lookup"><span data-stu-id="1842d-274">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Abbildung der vorherigen Anweisungen.](model/_static/sca.png)

<span data-ttu-id="1842d-276">Wählen Sie im Dialogfeld **Gerüst hinzufügen** den Eintrag **Razor Pages mit Entity Framework (CRUD)** > **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="1842d-276">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Abbildung der vorherigen Anweisungen.](model/_static/add_scaffold.png)

<span data-ttu-id="1842d-278">Vervollständigen Sie das Dialogfeld **Razor Pages mit Entity Framework (CRUD) hinzufügen**:</span><span class="sxs-lookup"><span data-stu-id="1842d-278">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="1842d-279">Wählen Sie in der Dropdownliste **Modellklasse** den Eintrag **Film (RazorPagesMovie.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="1842d-279">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="1842d-280">Wählen Sie in der Zeile **Datenkontextklasse** das Zeichen **+** (Plus) aus, und akzeptieren Sie den generierten Namen **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="1842d-280">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="1842d-281">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="1842d-281">Select **Add**.</span></span>

![Abbildung der vorherigen Anweisungen.](model/_static/arp.png)

<span data-ttu-id="1842d-283">Die Datei *appsettings.json* wird mit der Verbindungszeichenfolge aktualisiert, die zum Herstellen einer Verbindung mit einer lokalen Datenbank verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="1842d-283">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1842d-284">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1842d-284">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="1842d-285">Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs*, *Startup.cs*, und *CSPROJ*).</span><span class="sxs-lookup"><span data-stu-id="1842d-285">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="1842d-286">**Für Windows**: Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="1842d-286">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="1842d-287">**Für MacOS und Linux**: Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="1842d-287">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1842d-288">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="1842d-288">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="1842d-289">Erstellen Sie den Ordner *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="1842d-289">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="1842d-290">Klicken Sie mit der rechten Maustaste auf den Ordner *Pages* > **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="1842d-290">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="1842d-291">Geben Sie dem Ordner den Namen *Movies*</span><span class="sxs-lookup"><span data-stu-id="1842d-291">Name the folder *Movies*</span></span>

<span data-ttu-id="1842d-292">Klicken Sie mit der rechten Maustaste auf den Ordner *Pages/Movies* > **Hinzufügen** > **Neues Gerüstelement**.</span><span class="sxs-lookup"><span data-stu-id="1842d-292">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Abbildung der vorherigen Anweisungen.](model/_static/scaMac.png)

<span data-ttu-id="1842d-294">Wählen Sie im Dialogfeld **Neuen Gerüstbau hinzufügen** den Eintrag **Razor Pages mit Entity Framework (CRUD)** > **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="1842d-294">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Abbildung der vorherigen Anweisungen.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="1842d-296">Vervollständigen Sie das Dialogfeld **Razor Pages mit Entity Framework (CRUD) hinzufügen**:</span><span class="sxs-lookup"><span data-stu-id="1842d-296">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="1842d-297">Wählen Sie in der Dropdownliste **Modellklasse** den Eintrag **Film** aus, oder geben Sie ihn ein.</span><span class="sxs-lookup"><span data-stu-id="1842d-297">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="1842d-298">Wählen Sie in der Zeile **Datenkontextklasse** **RazorPagesMovieContext** aus, um eine neue Datenbankkontext-Klasse mit dem richtigen Namespace zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="1842d-298">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new db context class with the correct namespace.</span></span> <span data-ttu-id="1842d-299">In diesem Fall ist das **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="1842d-299">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="1842d-300">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="1842d-300">Select **Add**.</span></span>

![Abbildung der vorherigen Anweisungen.](model/_static/arpMac.png)

<span data-ttu-id="1842d-302">Die Datei *appsettings.json* wird mit der Verbindungszeichenfolge aktualisiert, die zum Herstellen einer Verbindung mit einer lokalen Datenbank verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="1842d-302">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="1842d-303">Der Gerüstprozess erstellt und ändert folgende Dateien:</span><span class="sxs-lookup"><span data-stu-id="1842d-303">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="1842d-304">Erstellte Dateien</span><span class="sxs-lookup"><span data-stu-id="1842d-304">Files created</span></span>

* <span data-ttu-id="1842d-305">*Pages/Movies*: „Create“, „Delete“, „Details“, „Edit“ und „Index“.</span><span class="sxs-lookup"><span data-stu-id="1842d-305">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="1842d-306">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="1842d-306">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="1842d-307">Datei aktualisiert</span><span class="sxs-lookup"><span data-stu-id="1842d-307">File updated</span></span>

* <span data-ttu-id="1842d-308">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="1842d-308">*Startup.cs*</span></span>

<span data-ttu-id="1842d-309">Die erstellten und aktualisierten Daten werden im nächsten Abschnitt erläutert.</span><span class="sxs-lookup"><span data-stu-id="1842d-309">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="1842d-310">Anfängliche Migration</span><span class="sxs-lookup"><span data-stu-id="1842d-310">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1842d-311">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1842d-311">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1842d-312">In diesem Abschnitt wird die Paket-Manager-Konsole (Package Manager Console, PMC) für Folgendes verwendet:</span><span class="sxs-lookup"><span data-stu-id="1842d-312">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="1842d-313">Fügen Sie eine anfängliche Migration hinzu.</span><span class="sxs-lookup"><span data-stu-id="1842d-313">Add an initial migration.</span></span>
* <span data-ttu-id="1842d-314">Aktualisieren Sie die Datenbank mit der anfänglichen Migration.</span><span class="sxs-lookup"><span data-stu-id="1842d-314">Update the database with the initial migration.</span></span>

<span data-ttu-id="1842d-315">Wählen Sie im Menü **Tools** **NuGet-Paket-Manager** > **Paket-Manager-Konsole** aus.</span><span class="sxs-lookup"><span data-stu-id="1842d-315">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC-Menü](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="1842d-317">Geben Sie in der PMC die folgenden Befehle ein:</span><span class="sxs-lookup"><span data-stu-id="1842d-317">In the PMC, enter the following commands:</span></span>

```Powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="1842d-318">Mit dem Befehl `Add-Migration` wird Code generiert, um das anfängliche Datenbankschema zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="1842d-318">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="1842d-319">Das Schema basiert auf dem Modell, das in `DbContext` angegeben ist (in der Datei *RazorPagesMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="1842d-319">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="1842d-320">Das Argument `InitialCreate` wird verwendet, um die Migration zu benennen.</span><span class="sxs-lookup"><span data-stu-id="1842d-320">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="1842d-321">Es kann jeder Name verwendet werden, aber per Konvention wird ein Name verwendet, der die Migration beschreibt.</span><span class="sxs-lookup"><span data-stu-id="1842d-321">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="1842d-322">Weitere Informationen finden Sie unter <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="1842d-322">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="1842d-323">Der Befehl `Update-Database` führt die Methode `Up` in der Datei *Migrations/\<time-stamp>_InitialCreate.cs* aus.</span><span class="sxs-lookup"><span data-stu-id="1842d-323">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="1842d-324">Die Methode `Up` erstellt die Datenbank.</span><span class="sxs-lookup"><span data-stu-id="1842d-324">The `Up` method creates the database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1842d-325">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1842d-325">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1842d-326">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="1842d-326">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> <span data-ttu-id="1842d-327">Mit den vorherigen Befehlen wird die folgende Warnung erstellt: „*Für die Spalte „Preis“ mit Dezimalwerten beim Entitätstyp „Movie“ wurde kein Typ angegeben. Dadurch werden Werte automatisch abgeschnitten, falls diese nicht der Standardgenauigkeit und -skalierung entsprechen. Geben Sie den Spaltentyp von SQL-Server an, der durch „HasColumnType()“ sämtliche Werte unterstützen kann.*“ Sie können diese Warnung ignorieren, sie wird in einem zukünftigen Tutorial korrigiert.</span><span class="sxs-lookup"><span data-stu-id="1842d-327">The preceding commands generate the following warning: "*No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.*" You can ignore that warning, it will be fixed in a later tutorial.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1842d-328">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1842d-328">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="1842d-329">Überprüfen des mit Dependency Injection registrierten Kontexts</span><span class="sxs-lookup"><span data-stu-id="1842d-329">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="1842d-330">ASP.NET Core wird mit [Dependency Injection](xref:fundamentals/dependency-injection) erstellt.</span><span class="sxs-lookup"><span data-stu-id="1842d-330">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="1842d-331">Dienste (z.B. der Datenbankkontext Entity Framework Core) werden über Dependency Injection beim Anwendungsstart registriert.</span><span class="sxs-lookup"><span data-stu-id="1842d-331">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="1842d-332">Komponenten, die diese Dienste erfordern (z.B. Razor Pages), werden von diesen Diensten über Konstruktorparameter bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="1842d-332">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="1842d-333">Der Konstruktorcode, der eine Datenbankkontext-Instanz abruft, wird später in diesem Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="1842d-333">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="1842d-334">Das Gerüstbautool hat automatisch einen Datenbankkontext erstellt und diesen mit dem Dependency Injection-Container registriert.</span><span class="sxs-lookup"><span data-stu-id="1842d-334">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="1842d-335">Untersuchen Sie die Methode `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="1842d-335">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="1842d-336">Die hervorgehobene Zeile wurde vom Gerüst hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="1842d-336">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="1842d-337">Der `RazorPagesMovieContext` koordiniert die EF Core-Funktionen (Create, Read, Update, Delete usw.) für das `Movie`-Modell.</span><span class="sxs-lookup"><span data-stu-id="1842d-337">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="1842d-338">Der Datenkontext (`RazorPagesMovieContext`) wird aus [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="1842d-338">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="1842d-339">Der Datenkontext gibt an, welche Entitäten im Datenmodell enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="1842d-339">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="1842d-340">Der vorangehende Code erstellt eine [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1)-Eigenschaft für die Entitätenmenge.</span><span class="sxs-lookup"><span data-stu-id="1842d-340">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="1842d-341">In der Terminologie von Entity Framework entspricht eine Entitätenmenge in der Regel einer Datenbanktabelle.</span><span class="sxs-lookup"><span data-stu-id="1842d-341">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="1842d-342">Entitäten entsprechen Zeilen in Tabellen.</span><span class="sxs-lookup"><span data-stu-id="1842d-342">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="1842d-343">Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions)-Objekt aufrufen.</span><span class="sxs-lookup"><span data-stu-id="1842d-343">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="1842d-344">Für die lokale Entwicklung liest das [ASP.NET Core-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der *appsettings.json*-Datei.</span><span class="sxs-lookup"><span data-stu-id="1842d-344">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1842d-345">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1842d-345">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1842d-346">Untersuchen Sie die Methode `Up`.</span><span class="sxs-lookup"><span data-stu-id="1842d-346">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1842d-347">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="1842d-347">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="1842d-348">Untersuchen Sie die Methode `Up`.</span><span class="sxs-lookup"><span data-stu-id="1842d-348">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="1842d-349">Testen der App</span><span class="sxs-lookup"><span data-stu-id="1842d-349">Test the app</span></span>

* <span data-ttu-id="1842d-350">Führen Sie die App aus, und fügen Sie `/Movies` an die URL im Browser an (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="1842d-350">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="1842d-351">Wenn Sie eine Fehlermeldung erhalten, müssen Sie Folgendes tun:</span><span class="sxs-lookup"><span data-stu-id="1842d-351">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="1842d-352">Sie haben den [Migrationsschritt](#pmc) verpasst.</span><span class="sxs-lookup"><span data-stu-id="1842d-352">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="1842d-353">Testen Sie den Link **Create** (Erstellen).</span><span class="sxs-lookup"><span data-stu-id="1842d-353">Test the **Create** link.</span></span>

  ![Seite „Create“](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="1842d-355">Sie können unter Umständen in das Feld `Price` keine Kommas als Dezimaltrennzeichen eingeben.</span><span class="sxs-lookup"><span data-stu-id="1842d-355">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="1842d-356">Zur Unterstützung der [jQuery-Validierung](https://jqueryvalidation.org/) für Gebietsschemas mit einer anderen Sprache als Englisch, in denen ein Komma („,“) als Dezimaltrennzeichen verwendet wird, und für Datums- und Uhrzeitformate, die nicht dem US-englischen Format entsprechen, muss die App globalisiert werden.</span><span class="sxs-lookup"><span data-stu-id="1842d-356">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="1842d-357">Globalisierungsanweisungen finden Sie unter [GitHub-Problem](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="1842d-357">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="1842d-358">Testen Sie die Links **Edit** (Bearbeiten), **Details** und **Delete** (Löschen).</span><span class="sxs-lookup"><span data-stu-id="1842d-358">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="1842d-359">Im nächsten Tutorial finden Sie Erläuterungen zu den Dateien, die durch den Gerüstbau erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="1842d-359">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1842d-360">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="1842d-360">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="1842d-361">[Zurück: Erste Schritte](xref:tutorials/razor-pages/razor-pages-start)
> [Weiter: Scaffolded Razor Pages (Gerüstbau mit Razor Pages)](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="1842d-361">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

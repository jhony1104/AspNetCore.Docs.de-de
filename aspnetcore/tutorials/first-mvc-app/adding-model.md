---
title: Hinzufügen eines Modells zu einer ASP.NET Core MVC-App
author: rick-anderson
description: Fügen Sie ein Modell zu einer einfachen ASP.NET Core-App hinzu.
ms.author: riande
ms.date: 8/15/2019
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: 5ad31a2536ad70590eaa767cf20068512241f36b
ms.sourcegitcommit: 14b25156e34c82ed0495b4aff5776ac5b1950b5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/26/2019
ms.locfileid: "71295476"
---
# <a name="add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="ae8f7-103">Hinzufügen eines Modells zu einer ASP.NET Core MVC-App</span><span class="sxs-lookup"><span data-stu-id="ae8f7-103">Add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="ae8f7-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="ae8f7-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="ae8f7-105">In diesem Abschnitt fügen Sie die Klassen für die Verwaltung von Filmen in einer Datenbank hinzu.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="ae8f7-106">Diese Klassen bilden den „**M**odell“-Teil der **M**VC-App.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-106">These classes will be the "**M**odel" part of the **M**VC app.</span></span>

<span data-ttu-id="ae8f7-107">Verwenden Sie diese Klassen mit [Entity Framework Core](/ef/core) (EF Core), um mit einer Datenbank zu arbeiten.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="ae8f7-108">EF Core ist ein ORM-Framework (Objektrelationales Mapping, ORM), das den Datenzugriffscode vereinfacht, den Sie schreiben müssen.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="ae8f7-109">Die Modellklassen, die Sie erstellen, werden als POCO-Klassen (von **P**lain **O**ld **C**LR **O**bjects) bezeichnet, da sie nicht über eine Abhängigkeit von EF Core verfügen.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-109">The model classes you create are known as POCO classes (from **P**lain **O**ld **C**LR **O**bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="ae8f7-110">Sie definieren lediglich die Eigenschaften der Daten, die in der Datenbank gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="ae8f7-111">In diesem Tutorial schreiben Sie zuerst die Modellklassen. Anschließend erstellt EF Core die Datenbank.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span> <span data-ttu-id="ae8f7-112">Eine alternative Methode, die hier nicht aufgeführt ist, besteht darin, Modellklassen aus einer vorhandenen Datenbank zu generieren.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-112">An alternate approach not covered here is to generate model classes from an existing database.</span></span> <span data-ttu-id="ae8f7-113">Weitere Informationen zu diesem Verfahren finden Sie unter [ASP.NET Core: Vorhandene Datenbank](/ef/core/get-started/aspnetcore/existing-db).</span><span class="sxs-lookup"><span data-stu-id="ae8f7-113">For information about that approach, see [ASP.NET Core - Existing Database](/ef/core/get-started/aspnetcore/existing-db).</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="ae8f7-114">Hinzufügen einer Datenmodellklasse</span><span class="sxs-lookup"><span data-stu-id="ae8f7-114">Add a data model class</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ae8f7-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ae8f7-115">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ae8f7-116">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*>**Hinzufügen** > **Klasse**.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-116">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="ae8f7-117">Nennen Sie die Datei *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-117">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="ae8f7-118">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ae8f7-118">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="ae8f7-119">Fügen Sie dem Ordner *Models* eine Datei mit dem Namen *Movie.cs* hinzu.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-119">Add a file named *Movie.cs* to the *Models* folder.</span></span>

---

<span data-ttu-id="ae8f7-120">Aktualisieren Sie die Datei *Movie.cs* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-120">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="ae8f7-121">Die `Movie`-Klasse enthält ein `Id`-Feld, das von der Datenbank für den Primärschlüssel benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-121">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="ae8f7-122">Das [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter)-Attribut für `ReleaseDate` gibt den Typ der Daten (`Date`) an.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-122">The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="ae8f7-123">Mit diesem Attribut:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-123">With this attribute:</span></span>

  * <span data-ttu-id="ae8f7-124">Es ist nicht erforderlich, dass der Benutzer Zeitinformationen in das Datumsfeld eingibt.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-124">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="ae8f7-125">Nur das Datum wird angezeigt, keine Zeitinformationen.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-125">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="ae8f7-126">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) werden in einem späteren Tutorial behandelt.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-126">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="ae8f7-127">Hinzufügen von NuGet-Paketen</span><span class="sxs-lookup"><span data-stu-id="ae8f7-127">Add NuGet packages</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ae8f7-128">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ae8f7-128">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ae8f7-129">Wählen Sie im Menü **Extras** die Option **NuGet-Paket-Manager** > **Paket-Manager-Konsole** (PMC) aus.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-129">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![PMC-Menü](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="ae8f7-131">Führen Sie den folgenden Befehl in der PMC aus:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-131">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="ae8f7-132">Mit dem vorangehenden Befehl wird der EF Core SQL Server-Anbieter hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-132">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="ae8f7-133">Das Anbieterpaket installiert das EF Core-Paket als Abhängigkeit.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-133">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="ae8f7-134">Weitere Pakete werden im weiteren Verlauf des Tutorials automatisch im Gerüstbauschritt installiert.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-134">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="ae8f7-135">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ae8f7-135">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="ae8f7-136">Erstellen einer Datenbankkontextklasse</span><span class="sxs-lookup"><span data-stu-id="ae8f7-136">Create a database context class</span></span>

<span data-ttu-id="ae8f7-137">Eine Datenbankkontextklasse ist erforderlich, um EF Core-Funktionalität (Erstellen, Lesen, Aktualisieren, Löschen) für das `Movie`-Modell zu koordinieren.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-137">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="ae8f7-138">Der Datenbankkontext wird von [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) abgeleitet und gibt die Entitäten an, die im Datenmodell enthalten sein sollen.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-138">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="ae8f7-139">Erstellen Sie einen Ordner *Data*.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-139">Create a *Data* folder.</span></span>

<span data-ttu-id="ae8f7-140">Fügen Sie eine Datei *Data/MvcMovieContext.cs* mit dem folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-140">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="ae8f7-141">Der vorangehende Code erstellt eine [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1)-Eigenschaft für die Entitätenmenge.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-141">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="ae8f7-142">In der Terminologie von Entity Framework entspricht eine Entitätenmenge in der Regel einer Datenbanktabelle.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-142">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="ae8f7-143">Entitäten entsprechen Zeilen in Tabellen.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-143">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="ae8f7-144">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="ae8f7-144">Register the database context</span></span>

<span data-ttu-id="ae8f7-145">ASP.NET Core wird mit [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (Dependency Injection, DI) erstellt.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-145">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="ae8f7-146">Dienste (z.B. der EF Core-Datenbankkontext) müssen über DI beim Anwendungsstart registriert werden.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-146">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="ae8f7-147">Komponenten, die diese Dienste erfordern (z.B. Razor Pages), werden von diesen Diensten über Konstruktorparameter bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-147">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="ae8f7-148">Der Konstruktorcode, der eine Datenbankkontext-Instanz abruft, wird später in diesem Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-148">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="ae8f7-149">In diesem Abschnitt registrieren Sie den Datenbankkontext mit dem DI-Container.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-149">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="ae8f7-150">Fügen Sie am Anfang der Datei *Startup.cs* die folgenden `using`-Anweisungen ein.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-150">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="ae8f7-151">Fügen Sie folgenden hervorgehobenen Code in `Startup.ConfigureServices` hinzu:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-151">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ae8f7-152">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ae8f7-152">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="ae8f7-153">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ae8f7-153">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="ae8f7-154">Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions)-Objekt aufrufen.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-154">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="ae8f7-155">Für die lokale Entwicklung liest das [ASP.NET Core-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der *appsettings.json*-Datei.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-155">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="ae8f7-156">Hinzufügen einer Datenbank-Verbindungszeichenfolge</span><span class="sxs-lookup"><span data-stu-id="ae8f7-156">Add a database connection string</span></span>

<span data-ttu-id="ae8f7-157">Fügen Sie der Datei *appsettings.json* eine Verbindungszeichenfolge hinzu:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-157">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ae8f7-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ae8f7-158">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="ae8f7-159">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ae8f7-159">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="ae8f7-160">Erstellen Sie das Projekt, um zu ermitteln, ob Compilerfehler vorliegen.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-160">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="ae8f7-161">Gerüstbau der Movie-Seiten</span><span class="sxs-lookup"><span data-stu-id="ae8f7-161">Scaffold movie pages</span></span>

<span data-ttu-id="ae8f7-162">Verwenden Sie das Tool für den Gerüstbau, um CRUD-Seiten („Create“ (Erstellen), „Read“ (Lesen), „Update“ (Aktualisieren) und „Delete“ (Löschen)) für das Movie-Modell zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-162">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ae8f7-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ae8f7-163">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ae8f7-164">Klicken Sie im *Projektmappen-Explorer* mit der rechten Maustaste auf den Ordner **Controllers** und anschließend auf **Hinzufügen > Neues Gerüstelement**.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-164">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![Screenshot für oben genannten Schritt](adding-model/_static/add_controller21.png)

<span data-ttu-id="ae8f7-166">Wählen Sie im Dialogfeld **Gerüst hinzufügen** die Option **MVC-Controller mit Ansichten unter Verwendung von Entity Framework > Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-166">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Dialogfeld „Gerüst hinzufügen“](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="ae8f7-168">Bearbeiten Sie das Dialogfeld **Controller hinzufügen**:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-168">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="ae8f7-169">**Modellklasse:** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="ae8f7-169">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="ae8f7-170">**Datenkontextklasse:** *MvcMovieContext (MvcMovie.Data)*</span><span class="sxs-lookup"><span data-stu-id="ae8f7-170">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Datenkontext hinzufügen](adding-model/_static/dc3.png)

* <span data-ttu-id="ae8f7-172">**Ansichten:** Belassen Sie den Standardwert der einzelnen Optionen aktiviert.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-172">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="ae8f7-173">**Controllername:** Übernehmen Sie den Standardnamen *MoviesController*.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-173">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="ae8f7-174">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-174">Select **Add**</span></span>

<span data-ttu-id="ae8f7-175">Visual Studio erstellt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-175">Visual Studio creates:</span></span>

* <span data-ttu-id="ae8f7-176">Einen Movies-Controller (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="ae8f7-176">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="ae8f7-177">Razor-Ansichtsdateien für die Seiten „Erstellen“, „Löschen“, „Details“ „Bearbeiten“ und „Index“ (*Views/Movies/\*.cshtml*)</span><span class="sxs-lookup"><span data-stu-id="ae8f7-177">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="ae8f7-178">Die automatische Erstellung dieser Dateien wird als *Gerüstbau* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-178">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ae8f7-179">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ae8f7-179">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="ae8f7-180">Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs*, *Startup.cs*, und *CSPROJ*).</span><span class="sxs-lookup"><span data-stu-id="ae8f7-180">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="ae8f7-181">Exportieren Sie unter Linux den Pfad des Gerüstbautools:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-181">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="ae8f7-182">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-182">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="ae8f7-183">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ae8f7-183">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ae8f7-184">Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs*, *Startup.cs*, und *CSPROJ*).</span><span class="sxs-lookup"><span data-stu-id="ae8f7-184">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="ae8f7-185">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-185">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="ae8f7-186">Sie können die Gerüstbauseiten noch nicht verwenden, da die Datenbank nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-186">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="ae8f7-187">Wenn Sie die App ausführen und auf den Link **Movie App** klicken, erhalten Sie eine Fehlermeldung des Typs *Datenbank kann nicht geöffnet werden* oder *Keine solche Tabelle vorhanden: Movie*.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-187">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="ae8f7-188">Anfängliche Migration</span><span class="sxs-lookup"><span data-stu-id="ae8f7-188">Initial migration</span></span>

<span data-ttu-id="ae8f7-189">Verwenden Sie das Feature [Migrations](xref:data/ef-mvc/migrations) von EF Core, um die Datenbank zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-189">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="ae8f7-190">Migrations sind eine Reihe von Tools, mit denen Sie eine Datenbank erstellen und aktualisieren können, damit die Ihrem Datenmodell entspricht.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-190">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ae8f7-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ae8f7-191">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ae8f7-192">Wählen Sie im Menü **Extras** die Option **NuGet-Paket-Manager** > **Paket-Manager-Konsole** (PMC) aus.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-192">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="ae8f7-193">Geben Sie in der PMC die folgenden Befehle ein:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-193">In the PMC, enter the following commands:</span></span>

```console
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="ae8f7-194">`Add-Migration InitialCreate`: Generiert eine Migrationsdatei *Migrations/{zeitstempel}_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-194">`Add-Migration InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="ae8f7-195">Das `InitialCreate`-Argument ist der Migrationsname.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-195">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="ae8f7-196">Es kann jeder Name verwendet werden, aber per Konvention wird ein Name ausgewählt, der die Migration beschreibt.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-196">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="ae8f7-197">Da dies die erste Migration ist, enthält die generierte Klasse Code zum Erstellen des Datenbankschemas.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-197">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="ae8f7-198">Das Datenbankschema basiert auf dem Modell, das in der Klasse `MvcMovieContext` angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-198">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="ae8f7-199">`Update-Database`: Aktualisiert die Datenbank auf die neueste Migration, die der vorherige Befehl erstellt hat.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-199">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="ae8f7-200">Der Befehl führt die `Up`-Methode in der Datei *Migrations/{zeitstempel}_InitialCreate.cs* aus, mit der die Datenbank erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-200">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="ae8f7-201">Der Befehl zum Aktualisieren der Datenbank generiert folgende Warnung:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-201">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="ae8f7-202">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-202">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="ae8f7-203">Dadurch werden Werte automatisch abgeschnitten, falls diese nicht der Standardgenauigkeit und -skalierung entsprechen.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-203">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="ae8f7-204">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'. (Für die Spalte „Price“ mit Dezimalwerten für den Entitätstyp „Movie“ wurde kein Typ angegeben. Dadurch werden Werte automatisch abgeschnitten, falls diese nicht der Standardgenauigkeit und -skalierung entsprechen. Geben Sie den Spaltentyp von SQL-Server explizit an, der durch „ForHasColumnType()“ sämtliche Werte unterstützen kann.)</span><span class="sxs-lookup"><span data-stu-id="ae8f7-204">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="ae8f7-205">Sie können diese Warnung ignorieren, sie wird in einem späteren Tutorial behoben.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-205">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="ae8f7-206">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ae8f7-206">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="ae8f7-207">Führen Sie die folgenden .NET Core-CLI-Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-207">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="ae8f7-208">`ef migrations add InitialCreate`: Generiert eine Migrationsdatei *Migrations/{zeitstempel}_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-208">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="ae8f7-209">Das `InitialCreate`-Argument ist der Migrationsname.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-209">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="ae8f7-210">Es kann jeder Name verwendet werden, aber per Konvention wird ein Name ausgewählt, der die Migration beschreibt.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-210">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="ae8f7-211">Da dies die erste Migration ist, enthält die generierte Klasse Code zum Erstellen des Datenbankschemas.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-211">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="ae8f7-212">Das Datenbankschema basiert auf dem Modell, das in der `MvcMovieContext`-Klasse (in der Datei *Data/MvcMovieContext.cs*) angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-212">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="ae8f7-213">`ef database update`: Aktualisiert die Datenbank auf die neueste Migration, die der vorherige Befehl erstellt hat.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-213">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="ae8f7-214">Der Befehl führt die `Up`-Methode in der Datei *Migrations/{zeitstempel}_InitialCreate.cs* aus, mit der die Datenbank erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-214">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

[!INCLUDE [ more information on the CLI tools for EF Core](~/includes/ef-cli.md)]

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="ae8f7-215">Die InitialCreate-Klasse</span><span class="sxs-lookup"><span data-stu-id="ae8f7-215">The InitialCreate class</span></span>

<span data-ttu-id="ae8f7-216">Untersuchen Sie die Migrationsdatei *Migrations/{zeitstempel}_InitialCreate.cs*:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-216">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

 <span data-ttu-id="ae8f7-217">Die `Up`-Methode erstellt die Movie-Tabelle und konfiguriert `Id` als Primärschlüssel.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-217">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="ae8f7-218">Die `Down`-Methode macht die von der `Up`-Migration vorgenommenen Schemaänderungen rückgängig.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-218">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="ae8f7-219">Testen der App</span><span class="sxs-lookup"><span data-stu-id="ae8f7-219">Test the app</span></span>

* <span data-ttu-id="ae8f7-220">Führen Sie die APP aus, und klicken Sie auf den Link **Movie App**.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-220">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="ae8f7-221">Wenn eine Ausnahme auftritt, die der folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-221">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ae8f7-222">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ae8f7-222">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="ae8f7-223">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ae8f7-223">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="ae8f7-224">Sie haben möglicherweise den [Migrationsschritt](#migration) ausgelassen.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-224">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="ae8f7-225">Testen Sie die Seite **Create** (Erstellen).</span><span class="sxs-lookup"><span data-stu-id="ae8f7-225">Test the **Create** page.</span></span> <span data-ttu-id="ae8f7-226">Geben Sie die Daten ein, und senden Sie sie.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-226">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="ae8f7-227">Sie können unter Umständen in das Feld `Price` keine Kommas als Dezimaltrennzeichen eingeben.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-227">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="ae8f7-228">Zur Unterstützung der [jQuery-Validierung](https://jqueryvalidation.org/) für Gebietsschemas mit einer anderen Sprache als Englisch, in denen ein Komma („,“) als Dezimaltrennzeichen verwendet wird, und für Datums- und Uhrzeitformate, die nicht dem US-englischen Format entsprechen, muss die App globalisiert werden.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-228">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="ae8f7-229">Globalisierungsanweisungen finden Sie unter [GitHub-Problem](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="ae8f7-229">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="ae8f7-230">Testen Sie die Seiten **Edit** (Bearbeiten), **Details** und **Delete** (Löschen).</span><span class="sxs-lookup"><span data-stu-id="ae8f7-230">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="ae8f7-231">Abhängigkeitsinjektion im Controller</span><span class="sxs-lookup"><span data-stu-id="ae8f7-231">Dependency injection in the controller</span></span>

<span data-ttu-id="ae8f7-232">Öffnen Sie die Datei *Controllers/MoviesController.cs*, und überprüfen Sie den Konstruktor:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-232">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="ae8f7-233">Der Konstruktor verwendet die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) zum Einfügen des Datenbankkontexts (`MvcMovieContext`) in den Controller.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-233">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="ae8f7-234">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-234">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="ae8f7-235">Stark typisierte Modelle und das Schlüsselwort @model</span><span class="sxs-lookup"><span data-stu-id="ae8f7-235">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="ae8f7-236">Weiter oben in diesem Tutorial haben Sie gesehen, wie ein Controller mithilfe des Wörterbuchs `ViewData` Daten oder Objekte an eine Ansicht übergeben kann.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-236">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="ae8f7-237">Das Wörterbuch `ViewData` ist ein dynamisches Objekt, das eine praktische Möglichkeit zur späten Bindung bietet, um Informationen an eine Ansicht zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-237">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="ae8f7-238">MVC bietet außerdem die Möglichkeit, stark typisierte Modellobjekte an eine Ansicht zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-238">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="ae8f7-239">Dieser stark typisierte Ansatz ermöglicht eine Überprüfung Ihres Codes zur Kompilierzeit.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-239">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="ae8f7-240">Der Gerüstbaumechnismus hat diesen Ansatz (d.h. das Übergeben eines stark typisierten Modells) mit der `MoviesController`-Klasse und Ansichten genutzt.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-240">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="ae8f7-241">Untersuchen Sie die generierte `Details`-Methode in der Datei *Controllers/MoviesController.cs*:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-241">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="ae8f7-242">Der `id`-Parameter wird im Allgemeinen als Routendaten übergeben.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-242">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="ae8f7-243">`https://localhost:5001/movies/details/1` legt beispielsweise Folgendes fest:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-243">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="ae8f7-244">Den Controller auf den Controller `movies` (das erste URL-Segment).</span><span class="sxs-lookup"><span data-stu-id="ae8f7-244">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="ae8f7-245">Die Aktion auf `details` (das zweite URL-Segment).</span><span class="sxs-lookup"><span data-stu-id="ae8f7-245">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="ae8f7-246">Die ID auf 1 (das letzte URL-Segment).</span><span class="sxs-lookup"><span data-stu-id="ae8f7-246">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="ae8f7-247">Sie können die `id` auch mithilfe einer Abfragezeichenfolge übergeben:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-247">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="ae8f7-248">Der `id`-Parameter wird als [Nullable-Typ](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) für den Fall definiert, dass kein ID-Wert angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-248">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="ae8f7-249">Ein [Lambdaausdruck](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) wird an `FirstOrDefaultAsync` übergeben, um Filmentitäten auszuwählen, die mit den Routendaten oder dem Wert der Abfragezeichenfolge übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-249">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="ae8f7-250">Wenn ein Film gefunden wird, wird eine Instanz des `Movie`-Modells an die Ansicht `Details` übergeben:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-250">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="ae8f7-251">Untersuchen Sie den Inhalt der Datei *Views/Movies/Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-251">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="ae8f7-252">Die `@model`-Anweisung am Anfang der Ansichtsdatei gibt den Typ des Objekts an, den die Ansicht erwartet.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-252">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="ae8f7-253">Beim Erstellen des Movie-Controllers wurde die folgende `@model`-Anweisung eingeschlossen:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-253">When the movie controller was created, the following `@model` statement was included:</span></span>

```HTML
@model MvcMovie.Models.Movie
   ```

<span data-ttu-id="ae8f7-254">Diese `@model`-Anweisung ermöglicht den Zugriff auf den Film, den der Controller an die Ansicht übergeben hat.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-254">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="ae8f7-255">Das `Model`-Objekt ist stark typisiert.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-255">The `Model` object is strongly typed.</span></span> <span data-ttu-id="ae8f7-256">In der Ansicht *Details.cshtml* übergibt der Code jedes Filmfeld an die HTML-Hilfsprogramme `DisplayNameFor` und `DisplayFor` mit dem stark typisierten `Model`-Objekt.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-256">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="ae8f7-257">Die Methoden `Create` und `Edit` und Ansichten übergeben außerdem ein `Movie`-Modelobjekt.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-257">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="ae8f7-258">Überprüfen Sie die Datei *Index.cshtml* und die `Index`-Methode im „Movies“-Controller.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-258">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="ae8f7-259">Beachten Sie, wie der Code beim Aufrufen der `View`-Methode ein `List`-Objekt erstellt.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-259">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="ae8f7-260">Der Code übergibt diese Liste `Movies` aus der Aktionsmethode `Index` an die Ansicht:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-260">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="ae8f7-261">Als der Movies-Controller erstellt wurde, hat der Gerüstbau automatisch die folgende `@model`-Anweisung am Anfang der Datei *Index.cshtml* hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-261">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="ae8f7-262">Diese `@model`-Direktive ermöglicht Ihnen den Zugriff auf die Liste der Filme, die der Controller an die Ansicht übergeben hat, indem ein stark typisiertes `Model`-Objekt verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-262">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="ae8f7-263">In der Ansicht *Index.cshtml* durchläuft der Code z.B. die Filme mithilfe einer `foreach`-Anweisung für das stark typisierte `Model`-Objekt:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-263">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="ae8f7-264">Da das `Model`-Objekt stark typisiert ist (als ein `IEnumerable<Movie>`-Objekt), wird jedes Element in der Schleife als `Movie` typisiert.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-264">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="ae8f7-265">Neben anderen Vorteilen bedeutet dies, dass Sie eine Überprüfung des Codes zur Kompilierzeit erhalten.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-265">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ae8f7-266">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="ae8f7-266">Additional resources</span></span>

* [<span data-ttu-id="ae8f7-267">Taghilfsprogramme</span><span class="sxs-lookup"><span data-stu-id="ae8f7-267">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="ae8f7-268">Globalisierung und Lokalisierung</span><span class="sxs-lookup"><span data-stu-id="ae8f7-268">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="ae8f7-269">[Vorheriges Tutorial: Hinzufügen einer Ansicht](adding-view.md)
> [Nächstes Tutorial: Arbeiten mit SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="ae8f7-269">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="ae8f7-270">Hinzufügen einer Datenmodellklasse</span><span class="sxs-lookup"><span data-stu-id="ae8f7-270">Add a data model class</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ae8f7-271">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ae8f7-271">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ae8f7-272">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*>**Hinzufügen** > **Klasse**.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-272">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="ae8f7-273">Nennen Sie die Klasse **Movie**.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-273">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="ae8f7-274">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ae8f7-274">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="ae8f7-275">Fügen Sie zum Ordner *Modelle* eine Klasse mit dem Namen *Movie.cs* hinzu.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-275">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="ae8f7-276">Erstellen des Gerüsts für das Filmmodell</span><span class="sxs-lookup"><span data-stu-id="ae8f7-276">Scaffold the movie model</span></span>

<span data-ttu-id="ae8f7-277">In diesem Abschnitt wird das Gerüst für das Filmmodell erstellt.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-277">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="ae8f7-278">Mit dem Tool für den Gerüstbau werden Seiten für die Vorgänge „Create“ (Erstellen), „Read“ (Lesen), „Update“ (Aktualisieren) und „Delete“ (Löschen), kurz CRUD-Vorgänge, für das Filmmodell erstellt.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-278">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ae8f7-279">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ae8f7-279">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ae8f7-280">Klicken Sie im *Projektmappen-Explorer* mit der rechten Maustaste auf den Ordner **Controllers** und anschließend auf **Hinzufügen > Neues Gerüstelement**.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-280">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![Screenshot für oben genannten Schritt](adding-model/_static/add_controller21.png)

<span data-ttu-id="ae8f7-282">Wählen Sie im Dialogfeld **Gerüst hinzufügen** die Option **MVC-Controller mit Ansichten unter Verwendung von Entity Framework > Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-282">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Dialogfeld „Gerüst hinzufügen“](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="ae8f7-284">Bearbeiten Sie das Dialogfeld **Controller hinzufügen**:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-284">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="ae8f7-285">**Modellklasse:** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="ae8f7-285">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="ae8f7-286">**Datenkontextklasse:** Wählen Sie das Symbol **+** aus, und fügen Sie den Standardtyp **MvcMovie.Models.MvcMovieContext** hinzu.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-286">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![Datenkontext hinzufügen](adding-model/_static/dc.png)

* <span data-ttu-id="ae8f7-288">**Ansichten:** Belassen Sie den Standardwert der einzelnen Optionen aktiviert.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-288">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="ae8f7-289">**Controllername:** Übernehmen Sie den Standardnamen *MoviesController*.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-289">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="ae8f7-290">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-290">Select **Add**</span></span>

![Dialogfeld „Controller hinzufügen“](adding-model/_static/add_controller2.png)

<span data-ttu-id="ae8f7-292">Visual Studio erstellt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-292">Visual Studio creates:</span></span>

* <span data-ttu-id="ae8f7-293">Eine Entity Framework Core-[Datenbankkontext-Klasse](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span><span class="sxs-lookup"><span data-stu-id="ae8f7-293">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span></span>
* <span data-ttu-id="ae8f7-294">Einen Movies-Controller (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="ae8f7-294">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="ae8f7-295">Razor-Ansichtsdateien für die Seiten „Erstellen“, „Löschen“, „Details“ „Bearbeiten“ und „Index“ (*Views/Movies/\*.cshtml*)</span><span class="sxs-lookup"><span data-stu-id="ae8f7-295">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="ae8f7-296">Die automatische Erstellung des Datenbankkontexts und der [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Aktionsmethoden (create, read, update and delete – Erstellen, Lesen, Aktualisieren und Löschen) und Ansichten wird als *Gerüstbau* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-296">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ae8f7-297">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ae8f7-297">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="ae8f7-298">Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs*, *Startup.cs*, und *CSPROJ*).</span><span class="sxs-lookup"><span data-stu-id="ae8f7-298">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="ae8f7-299">Installieren Sie das Gerüstbautool:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-299">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="ae8f7-300">Exportieren Sie unter Linux den Pfad des Gerüstbautools:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-300">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="ae8f7-301">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-301">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="ae8f7-302">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ae8f7-302">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ae8f7-303">Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs*, *Startup.cs*, und *CSPROJ*).</span><span class="sxs-lookup"><span data-stu-id="ae8f7-303">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="ae8f7-304">Installieren Sie das Gerüstbautool:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-304">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="ae8f7-305">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-305">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="ae8f7-306">Wenn Sie die App ausführen und auf den Link **Mvc Movie** klicken, erhalten Sie eine Fehlermeldung wie die Folgende:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-306">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ae8f7-307">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ae8f7-307">Visual Studio</span></span>](#tab/visual-studio)

``` error
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="ae8f7-308">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ae8f7-308">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

``` error
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

<span data-ttu-id="ae8f7-309">Sie müssen die Datenbank erstellen und verwenden dazu das EF Core-Feature [Migrationen](xref:data/ef-mvc/migrations).</span><span class="sxs-lookup"><span data-stu-id="ae8f7-309">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="ae8f7-310">Mit „Migrationen“ können Sie eine Datenbank erstellen, die mit Ihrem Datenmodell übereinstimmt, und das Datenbankschema aktualisieren, wenn sich Ihr Datenmodell ändert.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-310">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="ae8f7-311">Anfängliche Migration</span><span class="sxs-lookup"><span data-stu-id="ae8f7-311">Initial migration</span></span>

<span data-ttu-id="ae8f7-312">In diesem Abschnitt werden die folgenden Aufgaben ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-312">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="ae8f7-313">Fügen Sie eine anfängliche Migration hinzu.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-313">Add an initial migration.</span></span>
* <span data-ttu-id="ae8f7-314">Aktualisieren Sie die Datenbank mit der anfänglichen Migration.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-314">Update the database with the initial migration.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ae8f7-315">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ae8f7-315">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="ae8f7-316">Wählen Sie im Menü **Extras** die Option **NuGet-Paket-Manager** > **Paket-Manager-Konsole** (PMC) aus.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-316">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![PMC-Menü](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="ae8f7-318">Geben Sie in der PMC die folgenden Befehle ein:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-318">In the PMC, enter the following commands:</span></span>

   ```console
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="ae8f7-319">Mit dem Befehl `Add-Migration` wird Code generiert, um das anfängliche Datenbankschema zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-319">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="ae8f7-320">Das Datenbankschema basiert auf dem Modell, das in der Klasse `MvcMovieContext` angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-320">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span> <span data-ttu-id="ae8f7-321">Das `Initial`-Argument ist der Migrationsname.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-321">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="ae8f7-322">Es kann jeder Name verwendet werden, aber per Konvention wird ein Name verwendet, der die Migration beschreibt.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-322">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="ae8f7-323">Weitere Informationen finden Sie unter <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-323">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="ae8f7-324">Mit dem Befehl `Update-Database` führen Sie die Methode `Up` in der Datei *Migrations/{time-stamp}_InitialCreate.cs* aus, mit der die Datenbank erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-324">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="ae8f7-325">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ae8f7-325">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="ae8f7-326">Mit dem Befehl `ef migrations add InitialCreate` wird Code generiert, um das anfängliche Datenbankschema zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-326">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="ae8f7-327">Das Datenbankschema basiert auf dem Modell, das in der `MvcMovieContext`-Klasse (in der Datei *Data/MvcMovieContext.cs*) angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-327">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="ae8f7-328">Das `InitialCreate`-Argument ist der Migrationsname.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-328">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="ae8f7-329">Es kann jeder Name verwendet werden, aber per Konvention wird ein Name ausgewählt, der die Migration beschreibt.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-329">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="ae8f7-330">Überprüfen des mit Dependency Injection registrierten Kontexts</span><span class="sxs-lookup"><span data-stu-id="ae8f7-330">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="ae8f7-331">ASP.NET Core wird mit [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (Dependency Injection, DI) erstellt.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-331">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="ae8f7-332">Dienste (z.B. der EF Core-Datenbankkontext) werden über DI beim Anwendungsstart registriert.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-332">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="ae8f7-333">Komponenten, die diese Dienste erfordern (z.B. Razor Pages), werden von diesen Diensten über Konstruktorparameter bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-333">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="ae8f7-334">Der Konstruktorcode, der eine Datenbankkontext-Instanz abruft, wird später in diesem Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-334">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ae8f7-335">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ae8f7-335">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ae8f7-336">Das Gerüstbautool hat automatisch einen Datenbankkontext erstellt und diesen mit dem DI-Container registriert.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-336">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="ae8f7-337">Untersuchen Sie die folgende `Startup.ConfigureServices`-Methode.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-337">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="ae8f7-338">Die hervorgehobene Zeile wurde vom Gerüst hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-338">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

<span data-ttu-id="ae8f7-339">Der `MvcMovieContext` koordiniert die EF Core-Funktionen (Create, Read, Update, Delete usw.) für das `Movie`-Modell.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-339">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="ae8f7-340">Der Datenkontext (`MvcMovieContext`) wird aus [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-340">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="ae8f7-341">Der Datenkontext gibt an, welche Entitäten im Datenmodell enthalten sind:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-341">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

<span data-ttu-id="ae8f7-342">Der vorangehende Code erstellt eine [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1)-Eigenschaft für die Entitätenmenge.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-342">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="ae8f7-343">In der Terminologie von Entity Framework entspricht eine Entitätenmenge in der Regel einer Datenbanktabelle.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-343">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="ae8f7-344">Entitäten entsprechen Zeilen in Tabellen.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-344">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="ae8f7-345">Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions)-Objekt aufrufen.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-345">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="ae8f7-346">Für die lokale Entwicklung liest das [ASP.NET Core-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der *appsettings.json*-Datei.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-346">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="ae8f7-347">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ae8f7-347">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="ae8f7-348">Sie haben einen Datenbankkontext erstellt und diesen mit dem DI-Container registriert.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-348">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="ae8f7-349">Testen der App</span><span class="sxs-lookup"><span data-stu-id="ae8f7-349">Test the app</span></span>

* <span data-ttu-id="ae8f7-350">Führen Sie die App aus, und fügen Sie `/Movies` an die URL im Browser an (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="ae8f7-350">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="ae8f7-351">Wenn eine Datenbankausnahme auftritt, die der folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-351">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="ae8f7-352">Sie haben den [Migrationsschritt](#pmc) verpasst.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-352">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="ae8f7-353">Testen Sie den Link **Create** (Erstellen).</span><span class="sxs-lookup"><span data-stu-id="ae8f7-353">Test the **Create** link.</span></span> <span data-ttu-id="ae8f7-354">Geben Sie die Daten ein, und senden Sie sie.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-354">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="ae8f7-355">Sie können unter Umständen in das Feld `Price` keine Kommas als Dezimaltrennzeichen eingeben.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-355">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="ae8f7-356">Zur Unterstützung der [jQuery-Validierung](https://jqueryvalidation.org/) für Gebietsschemas mit einer anderen Sprache als Englisch, in denen ein Komma („,“) als Dezimaltrennzeichen verwendet wird, und für Datums- und Uhrzeitformate, die nicht dem US-englischen Format entsprechen, muss die App globalisiert werden.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-356">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="ae8f7-357">Globalisierungsanweisungen finden Sie unter [GitHub-Problem](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="ae8f7-357">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="ae8f7-358">Testen Sie die Links **Edit** (Bearbeiten), **Details** und **Delete** (Löschen).</span><span class="sxs-lookup"><span data-stu-id="ae8f7-358">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="ae8f7-359">Überprüfen Sie die `Startup`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-359">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="ae8f7-360">Der oberhalb hervorgehobene Code zeigt den Filmdatenbankkontext, der dem [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)-Container hinzugefügt wird:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-360">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="ae8f7-361">`services.AddDbContext<MvcMovieContext>(options =>` gibt die zu verwendende Datenbank und die Verbindungszeichenfolge an.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-361">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="ae8f7-362">`=>` ist ein [Lambdaoperator](/dotnet/articles/csharp/language-reference/operators/lambda-operator).</span><span class="sxs-lookup"><span data-stu-id="ae8f7-362">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="ae8f7-363">Öffnen Sie die Datei *Controllers/MoviesController.cs*, und überprüfen Sie den Konstruktor:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-363">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="ae8f7-364">Der Konstruktor verwendet die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) zum Einfügen des Datenbankkontexts (`MvcMovieContext`) in den Controller.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-364">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="ae8f7-365">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-365">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="ae8f7-366">Stark typisierte Modelle und das Schlüsselwort @model</span><span class="sxs-lookup"><span data-stu-id="ae8f7-366">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="ae8f7-367">Weiter oben in diesem Tutorial haben Sie gesehen, wie ein Controller mithilfe des Wörterbuchs `ViewData` Daten oder Objekte an eine Ansicht übergeben kann.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-367">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="ae8f7-368">Das Wörterbuch `ViewData` ist ein dynamisches Objekt, das eine praktische Möglichkeit zur späten Bindung bietet, um Informationen an eine Ansicht zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-368">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="ae8f7-369">MVC bietet außerdem die Möglichkeit, stark typisierte Modellobjekte an eine Ansicht zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-369">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="ae8f7-370">Dieser stark typisierte Ansatz ermöglicht eine bessere Überprüfung Ihres Codes zur Kompilierzeit.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-370">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="ae8f7-371">Der Gerüstbaumechnismus hat diesen Ansatz (d.h. das Übergeben eines stark typisierten Modells) mit der `MoviesController`-Klasse und Ansichten genutzt, als die Methoden und Ansichten erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-371">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="ae8f7-372">Untersuchen Sie die generierte `Details`-Methode in der Datei *Controllers/MoviesController.cs*:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-372">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="ae8f7-373">Der `id`-Parameter wird im Allgemeinen als Routendaten übergeben.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-373">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="ae8f7-374">`https://localhost:5001/movies/details/1` legt beispielsweise Folgendes fest:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-374">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="ae8f7-375">Den Controller auf den Controller `movies` (das erste URL-Segment).</span><span class="sxs-lookup"><span data-stu-id="ae8f7-375">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="ae8f7-376">Die Aktion auf `details` (das zweite URL-Segment).</span><span class="sxs-lookup"><span data-stu-id="ae8f7-376">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="ae8f7-377">Die ID auf 1 (das letzte URL-Segment).</span><span class="sxs-lookup"><span data-stu-id="ae8f7-377">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="ae8f7-378">Sie können die `id` auch mithilfe einer Abfragezeichenfolge übergeben:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-378">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="ae8f7-379">Der `id`-Parameter wird als [Nullable-Typ](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) für den Fall definiert, dass kein ID-Wert angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-379">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="ae8f7-380">Ein [Lambdaausdruck](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) wird an `FirstOrDefaultAsync` übergeben, um Filmentitäten auszuwählen, die mit den Routendaten oder dem Wert der Abfragezeichenfolge übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-380">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="ae8f7-381">Wenn ein Film gefunden wird, wird eine Instanz des `Movie`-Modells an die Ansicht `Details` übergeben:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-381">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="ae8f7-382">Untersuchen Sie den Inhalt der Datei *Views/Movies/Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-382">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="ae8f7-383">Durch Einschließen einer `@model`-Anweisung am Anfang der Ansichtsdatei können Sie den Typ des Objekts angeben, den die Ansicht erwartet.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-383">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="ae8f7-384">Beim Erstellen des „Movie“-Controllers wurde die folgende `@model`-Anweisung automatisch am Anfang der Datei *Details.cshtml* hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-384">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```HTML
@model MvcMovie.Models.Movie
   ```

<span data-ttu-id="ae8f7-385">Diese `@model`-Direktive ermöglicht Ihnen den Zugriff auf den Film, den der Controller an die Ansicht übergeben hat, indem ein stark typisiertes `Model`-Objekt verwendet wir.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-385">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="ae8f7-386">In der Ansicht *Details.cshtml* übergibt der Code jedes Filmfeld an die HTML-Hilfsprogramme `DisplayNameFor` und `DisplayFor` mit dem stark typisierten `Model`-Objekt.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-386">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="ae8f7-387">Die Methoden `Create` und `Edit` und Ansichten übergeben außerdem ein `Movie`-Modelobjekt.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-387">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="ae8f7-388">Überprüfen Sie die Datei *Index.cshtml* und die `Index`-Methode im „Movies“-Controller.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-388">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="ae8f7-389">Beachten Sie, wie der Code beim Aufrufen der `View`-Methode ein `List`-Objekt erstellt.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-389">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="ae8f7-390">Der Code übergibt diese Liste `Movies` aus der Aktionsmethode `Index` an die Ansicht:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-390">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="ae8f7-391">Beim Erstellen des „Movies“-Controllers hat der Gerüstbau automatisch die folgende `@model`-Anweisung am Anfang der Datei *Index.cshtml* hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-391">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="ae8f7-392">Diese `@model`-Direktive ermöglicht Ihnen den Zugriff auf die Liste der Filme, die der Controller an die Ansicht übergeben hat, indem ein stark typisiertes `Model`-Objekt verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-392">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="ae8f7-393">In der Ansicht *Index.cshtml* durchläuft der Code z.B. die Filme mithilfe einer `foreach`-Anweisung für das stark typisierte `Model`-Objekt:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-393">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="ae8f7-394">Da das `Model`-Objekt stark typisiert ist (als ein `IEnumerable<Movie>`-Objekt), wird jedes Element in der Schleife als `Movie` typisiert.</span><span class="sxs-lookup"><span data-stu-id="ae8f7-394">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="ae8f7-395">Neben anderen Vorteilen bedeutet dies, dass Sie eine Überprüfung des Codes zur Kompilierzeit erhalten:</span><span class="sxs-lookup"><span data-stu-id="ae8f7-395">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ae8f7-396">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="ae8f7-396">Additional resources</span></span>

* [<span data-ttu-id="ae8f7-397">Taghilfsprogramme</span><span class="sxs-lookup"><span data-stu-id="ae8f7-397">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="ae8f7-398">Globalisierung und Lokalisierung</span><span class="sxs-lookup"><span data-stu-id="ae8f7-398">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="ae8f7-399">[Vorheriges Tutorial: Hinzufügen einer Ansicht](adding-view.md)
> [Nächstes Tutorial: Arbeiten mit SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="ae8f7-399">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

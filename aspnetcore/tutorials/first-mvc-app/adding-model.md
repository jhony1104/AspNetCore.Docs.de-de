---
title: Hinzufügen eines Modells zu einer ASP.NET Core MVC-App
author: rick-anderson
description: Fügen Sie ein Modell zu einer einfachen ASP.NET Core-App hinzu.
ms.author: riande
ms.date: 01/13/2020
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: adf313418e82cc265304262f7a751273fa0e139f
ms.sourcegitcommit: 2388c2a7334ce66b6be3ffbab06dd7923df18f60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/14/2020
ms.locfileid: "75952109"
---
# <a name="add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="0a880-103">Hinzufügen eines Modells zu einer ASP.NET Core MVC-App</span><span class="sxs-lookup"><span data-stu-id="0a880-103">Add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="0a880-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="0a880-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="0a880-105">In diesem Abschnitt fügen Sie die Klassen für die Verwaltung von Filmen in einer Datenbank hinzu.</span><span class="sxs-lookup"><span data-stu-id="0a880-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="0a880-106">Diese Klassen bilden den „**M**odell“-Teil der **M**VC-App.</span><span class="sxs-lookup"><span data-stu-id="0a880-106">These classes will be the "**M**odel" part of the **M**VC app.</span></span>

<span data-ttu-id="0a880-107">Verwenden Sie diese Klassen mit [Entity Framework Core](/ef/core) (EF Core), um mit einer Datenbank zu arbeiten.</span><span class="sxs-lookup"><span data-stu-id="0a880-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="0a880-108">EF Core ist ein ORM-Framework (Objektrelationales Mapping, ORM), das den Datenzugriffscode vereinfacht, den Sie schreiben müssen.</span><span class="sxs-lookup"><span data-stu-id="0a880-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="0a880-109">Die Modellklassen, die Sie erstellen, werden als POCO-Klassen (von **P**lain **O**ld **C**LR **O**bjects) bezeichnet, da sie nicht über eine Abhängigkeit von EF Core verfügen.</span><span class="sxs-lookup"><span data-stu-id="0a880-109">The model classes you create are known as POCO classes (from **P**lain **O**ld **C**LR **O**bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="0a880-110">Sie definieren lediglich die Eigenschaften der Daten, die in der Datenbank gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="0a880-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="0a880-111">In diesem Tutorial schreiben Sie zuerst die Modellklassen. Anschließend erstellt EF Core die Datenbank.</span><span class="sxs-lookup"><span data-stu-id="0a880-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span> <span data-ttu-id="0a880-112">Eine alternative Methode, die hier nicht aufgeführt ist, besteht darin, Modellklassen aus einer vorhandenen Datenbank zu generieren.</span><span class="sxs-lookup"><span data-stu-id="0a880-112">An alternate approach not covered here is to generate model classes from an existing database.</span></span> <span data-ttu-id="0a880-113">Weitere Informationen zu diesem Verfahren finden Sie unter [ASP.NET Core: Vorhandene Datenbank](/ef/core/get-started/aspnetcore/existing-db).</span><span class="sxs-lookup"><span data-stu-id="0a880-113">For information about that approach, see [ASP.NET Core - Existing Database](/ef/core/get-started/aspnetcore/existing-db).</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="0a880-114">Hinzufügen einer Datenmodellklasse</span><span class="sxs-lookup"><span data-stu-id="0a880-114">Add a data model class</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0a880-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a880-115">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0a880-116">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*>**Hinzufügen** > **Klasse**.</span><span class="sxs-lookup"><span data-stu-id="0a880-116">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="0a880-117">Nennen Sie die Datei *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="0a880-117">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0a880-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a880-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0a880-119">Fügen Sie dem Ordner *Models* eine Datei mit dem Namen *Movie.cs* hinzu.</span><span class="sxs-lookup"><span data-stu-id="0a880-119">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="0a880-120">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="0a880-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0a880-121">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle* > **Hinzufügen** > **Neue Klasse** > **Leere Klasse**.</span><span class="sxs-lookup"><span data-stu-id="0a880-121">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="0a880-122">Nennen Sie die Datei *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="0a880-122">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="0a880-123">Aktualisieren Sie die Datei *Movie.cs* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="0a880-123">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="0a880-124">Die `Movie`-Klasse enthält ein `Id`-Feld, das von der Datenbank für den Primärschlüssel benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="0a880-124">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="0a880-125">Das [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter)-Attribut für `ReleaseDate` gibt den Typ der Daten (`Date`) an.</span><span class="sxs-lookup"><span data-stu-id="0a880-125">The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="0a880-126">Mit diesem Attribut:</span><span class="sxs-lookup"><span data-stu-id="0a880-126">With this attribute:</span></span>

* <span data-ttu-id="0a880-127">Es ist nicht erforderlich, dass der Benutzer Zeitinformationen in das Datumsfeld eingibt.</span><span class="sxs-lookup"><span data-stu-id="0a880-127">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="0a880-128">Nur das Datum wird angezeigt, keine Zeitinformationen.</span><span class="sxs-lookup"><span data-stu-id="0a880-128">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="0a880-129">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) werden in einem späteren Tutorial behandelt.</span><span class="sxs-lookup"><span data-stu-id="0a880-129">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="0a880-130">Hinzufügen von NuGet-Paketen</span><span class="sxs-lookup"><span data-stu-id="0a880-130">Add NuGet packages</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0a880-131">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a880-131">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0a880-132">Wählen Sie im Menü **Tools** **NuGet-Paket-Manager** > **Paket-Manager-Konsole** (PMC) aus.</span><span class="sxs-lookup"><span data-stu-id="0a880-132">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![PMC-Menü](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="0a880-134">Führen Sie den folgenden Befehl in der PMC aus:</span><span class="sxs-lookup"><span data-stu-id="0a880-134">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="0a880-135">Mit dem vorangehenden Befehl wird der EF Core SQL Server-Anbieter hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="0a880-135">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="0a880-136">Das Anbieterpaket installiert das EF Core-Paket als Abhängigkeit.</span><span class="sxs-lookup"><span data-stu-id="0a880-136">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="0a880-137">Weitere Pakete werden im weiteren Verlauf des Tutorials automatisch im Gerüstbauschritt installiert.</span><span class="sxs-lookup"><span data-stu-id="0a880-137">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0a880-138">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a880-138">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="0a880-139">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="0a880-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0a880-140">Wählen Sie im Menü **Projekt** die Option **NuGet-Pakete verwalten** aus.</span><span class="sxs-lookup"><span data-stu-id="0a880-140">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="0a880-141">Geben Sie in das Feld **Suchen** in der oberen rechten Ecke `Microsoft.EntityFrameworkCore.SQLite` ein, und drücken Sie die **EINGABETASTE**, um zu suchen.</span><span class="sxs-lookup"><span data-stu-id="0a880-141">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="0a880-142">Wählen Sie das entsprechende NuGet-Paket aus, und klicken Sie auf die Schaltfläche **Paket hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="0a880-142">Select the matching NuGet package and press the **Add Package** button.</span></span>

![NuGet-Paket für Entity Framework Core hinzufügen](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="0a880-144">Das Dialogfeld **Projekte auswählen** wird angezeigt, und das Projekt `MvcMovie` ist ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="0a880-144">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="0a880-145">Klicken Sie auf die Schaltfläche **OK**.</span><span class="sxs-lookup"><span data-stu-id="0a880-145">Press the **Ok** button.</span></span>

<span data-ttu-id="0a880-146">Ein Dialogfeld **Zustimmen zur Lizenz** wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="0a880-146">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="0a880-147">Überprüfen Sie die Lizenzen nach Wunsch, und klicken Sie dann auf die Schaltfläche **Akzeptieren**.</span><span class="sxs-lookup"><span data-stu-id="0a880-147">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="0a880-148">Wiederholen Sie die Schritte oben, um die folgenden NuGet-Pakete zu installieren:</span><span class="sxs-lookup"><span data-stu-id="0a880-148">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="0a880-149">Erstellen einer Datenbankkontextklasse</span><span class="sxs-lookup"><span data-stu-id="0a880-149">Create a database context class</span></span>

<span data-ttu-id="0a880-150">Eine Datenbankkontextklasse ist erforderlich, um EF Core-Funktionalität (Erstellen, Lesen, Aktualisieren, Löschen) für das `Movie`-Modell zu koordinieren.</span><span class="sxs-lookup"><span data-stu-id="0a880-150">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="0a880-151">Der Datenbankkontext wird von [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) abgeleitet und gibt die Entitäten an, die im Datenmodell enthalten sein sollen.</span><span class="sxs-lookup"><span data-stu-id="0a880-151">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="0a880-152">Erstellen Sie einen Ordner *Data*.</span><span class="sxs-lookup"><span data-stu-id="0a880-152">Create a *Data* folder.</span></span>

<span data-ttu-id="0a880-153">Fügen Sie eine Datei *Data/MvcMovieContext.cs* mit dem folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="0a880-153">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="0a880-154">Der vorangehende Code erstellt eine [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1)-Eigenschaft für die Entitätenmenge.</span><span class="sxs-lookup"><span data-stu-id="0a880-154">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="0a880-155">In der Terminologie von Entity Framework entspricht eine Entitätenmenge in der Regel einer Datenbanktabelle.</span><span class="sxs-lookup"><span data-stu-id="0a880-155">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="0a880-156">Entitäten entsprechen Zeilen in Tabellen.</span><span class="sxs-lookup"><span data-stu-id="0a880-156">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="0a880-157">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="0a880-157">Register the database context</span></span>

<span data-ttu-id="0a880-158">ASP.NET Core wird mit [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (Dependency Injection, DI) erstellt.</span><span class="sxs-lookup"><span data-stu-id="0a880-158">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="0a880-159">Dienste (z.B. der EF Core-Datenbankkontext) müssen über DI beim Anwendungsstart registriert werden.</span><span class="sxs-lookup"><span data-stu-id="0a880-159">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="0a880-160">Komponenten, die diese Dienste erfordern (z.B. Razor Pages), werden von diesen Diensten über Konstruktorparameter bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="0a880-160">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="0a880-161">Der Konstruktorcode, der eine Datenbankkontext-Instanz abruft, wird später in diesem Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="0a880-161">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="0a880-162">In diesem Abschnitt registrieren Sie den Datenbankkontext mit dem DI-Container.</span><span class="sxs-lookup"><span data-stu-id="0a880-162">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="0a880-163">Fügen Sie am Anfang der Datei *Startup.cs* die folgenden `using`-Anweisungen ein.</span><span class="sxs-lookup"><span data-stu-id="0a880-163">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="0a880-164">Fügen Sie folgenden hervorgehobenen Code in `Startup.ConfigureServices` hinzu:</span><span class="sxs-lookup"><span data-stu-id="0a880-164">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0a880-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a880-165">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="0a880-166">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="0a880-166">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="0a880-167">Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions)-Objekt aufrufen.</span><span class="sxs-lookup"><span data-stu-id="0a880-167">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="0a880-168">Für die lokale Entwicklung liest das [ASP.NET Core-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der *appsettings.json*-Datei.</span><span class="sxs-lookup"><span data-stu-id="0a880-168">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="0a880-169">Hinzufügen einer Datenbank-Verbindungszeichenfolge</span><span class="sxs-lookup"><span data-stu-id="0a880-169">Add a database connection string</span></span>

<span data-ttu-id="0a880-170">Fügen Sie der Datei *appsettings.json* eine Verbindungszeichenfolge hinzu:</span><span class="sxs-lookup"><span data-stu-id="0a880-170">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0a880-171">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a880-171">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="0a880-172">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="0a880-172">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="0a880-173">Erstellen Sie das Projekt, um zu ermitteln, ob Compilerfehler vorliegen.</span><span class="sxs-lookup"><span data-stu-id="0a880-173">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="0a880-174">Gerüstbau der Movie-Seiten</span><span class="sxs-lookup"><span data-stu-id="0a880-174">Scaffold movie pages</span></span>

<span data-ttu-id="0a880-175">Verwenden Sie das Tool für den Gerüstbau, um CRUD-Seiten („Create“ (Erstellen), „Read“ (Lesen), „Update“ (Aktualisieren) und „Delete“ (Löschen)) für das Movie-Modell zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="0a880-175">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0a880-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a880-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0a880-177">Klicken Sie im *Projektmappen-Explorer* mit der rechten Maustaste auf den Ordner **Controllers** und anschließend auf **Hinzufügen > Neues Gerüstelement**.</span><span class="sxs-lookup"><span data-stu-id="0a880-177">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![Screenshot für oben genannten Schritt](adding-model/_static/add_controller21.png)

<span data-ttu-id="0a880-179">Wählen Sie im Dialogfeld **Gerüst hinzufügen** die Option **MVC-Controller mit Ansichten unter Verwendung von Entity Framework > Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="0a880-179">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Dialogfeld „Gerüst hinzufügen“](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="0a880-181">Bearbeiten Sie das Dialogfeld **Controller hinzufügen**:</span><span class="sxs-lookup"><span data-stu-id="0a880-181">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="0a880-182">**Modellklasse:** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="0a880-182">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="0a880-183">**Datenkontextklasse:** *MvcMovieContext (MvcMovie.Data)*</span><span class="sxs-lookup"><span data-stu-id="0a880-183">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Datenkontext hinzufügen](adding-model/_static/dc3.png)

* <span data-ttu-id="0a880-185">**Ansichten:** Belassen Sie den Standardwert der einzelnen Optionen aktiviert.</span><span class="sxs-lookup"><span data-stu-id="0a880-185">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="0a880-186">**Controllername:** Übernehmen Sie den Standardnamen *MoviesController*.</span><span class="sxs-lookup"><span data-stu-id="0a880-186">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="0a880-187">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="0a880-187">Select **Add**</span></span>

<span data-ttu-id="0a880-188">Visual Studio erstellt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="0a880-188">Visual Studio creates:</span></span>

* <span data-ttu-id="0a880-189">Einen Movies-Controller (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="0a880-189">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="0a880-190">Razor-Ansichtsdateien für die Seiten „Erstellen“, „Löschen“, „Details“ „Bearbeiten“ und „Index“ (*Views/Movies/\*.cshtml*)</span><span class="sxs-lookup"><span data-stu-id="0a880-190">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="0a880-191">Die automatische Erstellung dieser Dateien wird als *Gerüstbau* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="0a880-191">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0a880-192">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a880-192">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="0a880-193">Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs*, *Startup.cs*, und *CSPROJ*).</span><span class="sxs-lookup"><span data-stu-id="0a880-193">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="0a880-194">Exportieren Sie unter Linux den Pfad des Gerüstbautools:</span><span class="sxs-lookup"><span data-stu-id="0a880-194">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="0a880-195">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="0a880-195">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="0a880-196">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="0a880-196">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0a880-197">Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs*, *Startup.cs*, und *CSPROJ*).</span><span class="sxs-lookup"><span data-stu-id="0a880-197">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="0a880-198">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="0a880-198">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="0a880-199">Sie können die Gerüstbauseiten noch nicht verwenden, da die Datenbank nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="0a880-199">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="0a880-200">Wenn Sie die App ausführen und auf den Link **Movie App** klicken, erhalten Sie eine Fehlermeldung des Typs *Datenbank kann nicht geöffnet werden* oder *Keine solche Tabelle vorhanden: Movie*.</span><span class="sxs-lookup"><span data-stu-id="0a880-200">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="0a880-201">Anfängliche Migration</span><span class="sxs-lookup"><span data-stu-id="0a880-201">Initial migration</span></span>

<span data-ttu-id="0a880-202">Verwenden Sie das Feature [Migrations](xref:data/ef-mvc/migrations) von EF Core, um die Datenbank zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="0a880-202">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="0a880-203">Migrations sind eine Reihe von Tools, mit denen Sie eine Datenbank erstellen und aktualisieren können, damit die Ihrem Datenmodell entspricht.</span><span class="sxs-lookup"><span data-stu-id="0a880-203">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0a880-204">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a880-204">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0a880-205">Wählen Sie im Menü **Tools** **NuGet-Paket-Manager** > **Paket-Manager-Konsole** (PMC) aus.</span><span class="sxs-lookup"><span data-stu-id="0a880-205">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="0a880-206">Geben Sie in der PMC die folgenden Befehle ein:</span><span class="sxs-lookup"><span data-stu-id="0a880-206">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="0a880-207">`Add-Migration InitialCreate`: Generiert die Migrationsdatei *Migrations/{timestamp}_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="0a880-207">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="0a880-208">Das `InitialCreate`-Argument ist der Migrationsname.</span><span class="sxs-lookup"><span data-stu-id="0a880-208">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="0a880-209">Es kann jeder Name verwendet werden, aber per Konvention wird ein Name ausgewählt, der die Migration beschreibt.</span><span class="sxs-lookup"><span data-stu-id="0a880-209">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="0a880-210">Da dies die erste Migration ist, enthält die generierte Klasse Code zum Erstellen des Datenbankschemas.</span><span class="sxs-lookup"><span data-stu-id="0a880-210">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="0a880-211">Das Datenbankschema basiert auf dem Modell, das in der Klasse `MvcMovieContext` angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="0a880-211">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="0a880-212">`Update-Database`: Aktualisiert die Datenbank auf die neueste Migration, die der vorherige Befehl erstellt hat.</span><span class="sxs-lookup"><span data-stu-id="0a880-212">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="0a880-213">Der Befehl führt die `Up`-Methode in der Datei *Migrations/{zeitstempel}_InitialCreate.cs* aus, mit der die Datenbank erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="0a880-213">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="0a880-214">Der Befehl zum Aktualisieren der Datenbank generiert folgende Warnung:</span><span class="sxs-lookup"><span data-stu-id="0a880-214">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="0a880-215">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="0a880-215">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="0a880-216">Dadurch werden Werte automatisch abgeschnitten, falls diese nicht der Standardgenauigkeit und -skalierung entsprechen.</span><span class="sxs-lookup"><span data-stu-id="0a880-216">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="0a880-217">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'. (Für die Spalte „Price“ mit Dezimalwerten für den Entitätstyp „Movie“ wurde kein Typ angegeben. Dadurch werden Werte automatisch abgeschnitten, falls diese nicht der Standardgenauigkeit und -skalierung entsprechen. Geben Sie den Spaltentyp von SQL-Server explizit an, der durch „ForHasColumnType()“ sämtliche Werte unterstützen kann.)</span><span class="sxs-lookup"><span data-stu-id="0a880-217">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="0a880-218">Sie können diese Warnung ignorieren, sie wird in einem späteren Tutorial behoben.</span><span class="sxs-lookup"><span data-stu-id="0a880-218">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="0a880-219">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="0a880-219">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="0a880-220">Führen Sie die folgenden .NET Core-CLI-Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="0a880-220">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="0a880-221">`ef migrations add InitialCreate`: Generiert eine Migrationsdatei *Migrations/{zeitstempel}_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="0a880-221">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="0a880-222">Das `InitialCreate`-Argument ist der Migrationsname.</span><span class="sxs-lookup"><span data-stu-id="0a880-222">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="0a880-223">Es kann jeder Name verwendet werden, aber per Konvention wird ein Name ausgewählt, der die Migration beschreibt.</span><span class="sxs-lookup"><span data-stu-id="0a880-223">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="0a880-224">Da dies die erste Migration ist, enthält die generierte Klasse Code zum Erstellen des Datenbankschemas.</span><span class="sxs-lookup"><span data-stu-id="0a880-224">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="0a880-225">Das Datenbankschema basiert auf dem Modell, das in der `MvcMovieContext`-Klasse (in der Datei *Data/MvcMovieContext.cs*) angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="0a880-225">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="0a880-226">`ef database update`: Aktualisiert die Datenbank auf die neueste Migration, die der vorherige Befehl erstellt hat.</span><span class="sxs-lookup"><span data-stu-id="0a880-226">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="0a880-227">Der Befehl führt die `Up`-Methode in der Datei *Migrations/{zeitstempel}_InitialCreate.cs* aus, mit der die Datenbank erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="0a880-227">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

[!INCLUDE [more information on the CLI tools for EF Core](~/includes/ef-cli.md)]

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="0a880-228">Die InitialCreate-Klasse</span><span class="sxs-lookup"><span data-stu-id="0a880-228">The InitialCreate class</span></span>

<span data-ttu-id="0a880-229">Untersuchen Sie die Migrationsdatei *Migrations/{zeitstempel}_InitialCreate.cs*:</span><span class="sxs-lookup"><span data-stu-id="0a880-229">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="0a880-230">Die `Up`-Methode erstellt die Movie-Tabelle und konfiguriert `Id` als Primärschlüssel.</span><span class="sxs-lookup"><span data-stu-id="0a880-230">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="0a880-231">Die `Down`-Methode macht die von der `Up`-Migration vorgenommenen Schemaänderungen rückgängig.</span><span class="sxs-lookup"><span data-stu-id="0a880-231">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="0a880-232">Testen der App</span><span class="sxs-lookup"><span data-stu-id="0a880-232">Test the app</span></span>

* <span data-ttu-id="0a880-233">Führen Sie die APP aus, und klicken Sie auf den Link **Movie App**.</span><span class="sxs-lookup"><span data-stu-id="0a880-233">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="0a880-234">Wenn eine Ausnahme auftritt, die der folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="0a880-234">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0a880-235">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a880-235">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="0a880-236">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="0a880-236">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="0a880-237">Sie haben möglicherweise den [Migrationsschritt](#migration) ausgelassen.</span><span class="sxs-lookup"><span data-stu-id="0a880-237">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="0a880-238">Testen Sie die Seite **Create** (Erstellen).</span><span class="sxs-lookup"><span data-stu-id="0a880-238">Test the **Create** page.</span></span> <span data-ttu-id="0a880-239">Geben Sie die Daten ein, und senden Sie sie.</span><span class="sxs-lookup"><span data-stu-id="0a880-239">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="0a880-240">Sie können unter Umständen in das Feld `Price` keine Kommas als Dezimaltrennzeichen eingeben.</span><span class="sxs-lookup"><span data-stu-id="0a880-240">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="0a880-241">Zur Unterstützung der [jQuery-Validierung](https://jqueryvalidation.org/) für Gebietsschemas mit einer anderen Sprache als Englisch, in denen ein Komma („,“) als Dezimaltrennzeichen verwendet wird, und für Datums- und Uhrzeitformate, die nicht dem US-englischen Format entsprechen, muss die App globalisiert werden.</span><span class="sxs-lookup"><span data-stu-id="0a880-241">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="0a880-242">Globalisierungsanweisungen finden Sie unter [GitHub-Problem](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="0a880-242">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="0a880-243">Testen Sie die Seiten **Edit** (Bearbeiten), **Details** und **Delete** (Löschen).</span><span class="sxs-lookup"><span data-stu-id="0a880-243">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="0a880-244">Abhängigkeitsinjektion im Controller</span><span class="sxs-lookup"><span data-stu-id="0a880-244">Dependency injection in the controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0a880-245">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a880-245">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0a880-246">Öffnen Sie die Datei *Controllers/MoviesController.cs*, und überprüfen Sie den Konstruktor:</span><span class="sxs-lookup"><span data-stu-id="0a880-246">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="0a880-247">Der Konstruktor verwendet die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) zum Einfügen des Datenbankkontexts (`MvcMovieContext`) in den Controller.</span><span class="sxs-lookup"><span data-stu-id="0a880-247">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="0a880-248">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="0a880-248">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="0a880-249">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="0a880-249">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="0a880-250">Der Konstruktor verwendet die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) zum Einfügen des Datenbankkontexts (`MvcMovieContext`) in den Controller.</span><span class="sxs-lookup"><span data-stu-id="0a880-250">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="0a880-251">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="0a880-251">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="0a880-252">Verwenden von SQLite für die Entwicklung und von SQL Server für die Produktion</span><span class="sxs-lookup"><span data-stu-id="0a880-252">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="0a880-253">Wenn SQLite ausgewählt wurde, ist der von der Vorlage generierte Code für die Entwicklung bereit.</span><span class="sxs-lookup"><span data-stu-id="0a880-253">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="0a880-254">Der folgende Code zeigt das Injizieren von <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> in Startup.</span><span class="sxs-lookup"><span data-stu-id="0a880-254">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="0a880-255">`IWebHostEnvironment` wird eingefügt, sodass `ConfigureServices` SQLite bei der Entwicklung und SQL Server für die Produktion verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="0a880-255">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="0a880-256">Stark typisierte Modelle und das Schlüsselwort @model</span><span class="sxs-lookup"><span data-stu-id="0a880-256">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="0a880-257">Weiter oben in diesem Tutorial haben Sie gesehen, wie ein Controller mithilfe des Wörterbuchs `ViewData` Daten oder Objekte an eine Ansicht übergeben kann.</span><span class="sxs-lookup"><span data-stu-id="0a880-257">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="0a880-258">Das Wörterbuch `ViewData` ist ein dynamisches Objekt, das eine praktische Möglichkeit zur späten Bindung bietet, um Informationen an eine Ansicht zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="0a880-258">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="0a880-259">MVC bietet außerdem die Möglichkeit, stark typisierte Modellobjekte an eine Ansicht zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="0a880-259">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="0a880-260">Dieser stark typisierte Ansatz ermöglicht eine Überprüfung Ihres Codes zur Kompilierzeit.</span><span class="sxs-lookup"><span data-stu-id="0a880-260">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="0a880-261">Der Gerüstbaumechnismus hat diesen Ansatz (d.h. das Übergeben eines stark typisierten Modells) mit der `MoviesController`-Klasse und Ansichten genutzt.</span><span class="sxs-lookup"><span data-stu-id="0a880-261">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="0a880-262">Untersuchen Sie die generierte `Details`-Methode in der Datei *Controllers/MoviesController.cs*:</span><span class="sxs-lookup"><span data-stu-id="0a880-262">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="0a880-263">Der `id`-Parameter wird im Allgemeinen als Routendaten übergeben.</span><span class="sxs-lookup"><span data-stu-id="0a880-263">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="0a880-264">`https://localhost:5001/movies/details/1` legt beispielsweise Folgendes fest:</span><span class="sxs-lookup"><span data-stu-id="0a880-264">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="0a880-265">Den Controller auf den Controller `movies` (das erste URL-Segment).</span><span class="sxs-lookup"><span data-stu-id="0a880-265">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="0a880-266">Die Aktion auf `details` (das zweite URL-Segment).</span><span class="sxs-lookup"><span data-stu-id="0a880-266">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="0a880-267">Die ID auf 1 (das letzte URL-Segment).</span><span class="sxs-lookup"><span data-stu-id="0a880-267">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="0a880-268">Sie können die `id` auch mithilfe einer Abfragezeichenfolge übergeben:</span><span class="sxs-lookup"><span data-stu-id="0a880-268">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="0a880-269">Der `id`-Parameter wird als [Nullable-Typ](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) für den Fall definiert, dass kein ID-Wert angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="0a880-269">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="0a880-270">Ein [Lambdaausdruck](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) wird an `FirstOrDefaultAsync` übergeben, um Filmentitäten auszuwählen, die mit den Routendaten oder dem Wert der Abfragezeichenfolge übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="0a880-270">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="0a880-271">Wenn ein Film gefunden wird, wird eine Instanz des `Movie`-Modells an die Ansicht `Details` übergeben:</span><span class="sxs-lookup"><span data-stu-id="0a880-271">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="0a880-272">Untersuchen Sie den Inhalt der Datei *Views/Movies/Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="0a880-272">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="0a880-273">Die `@model`-Anweisung am Anfang der Ansichtsdatei gibt den Typ des Objekts an, den die Ansicht erwartet.</span><span class="sxs-lookup"><span data-stu-id="0a880-273">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="0a880-274">Beim Erstellen des Movie-Controllers wurde die folgende `@model`-Anweisung eingeschlossen:</span><span class="sxs-lookup"><span data-stu-id="0a880-274">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="0a880-275">Diese `@model`-Anweisung ermöglicht den Zugriff auf den Film, den der Controller an die Ansicht übergeben hat.</span><span class="sxs-lookup"><span data-stu-id="0a880-275">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="0a880-276">Das `Model`-Objekt ist stark typisiert.</span><span class="sxs-lookup"><span data-stu-id="0a880-276">The `Model` object is strongly typed.</span></span> <span data-ttu-id="0a880-277">In der Ansicht *Details.cshtml* übergibt der Code jedes Filmfeld an die HTML-Hilfsprogramme `DisplayNameFor` und `DisplayFor` mit dem stark typisierten `Model`-Objekt.</span><span class="sxs-lookup"><span data-stu-id="0a880-277">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="0a880-278">Die Methoden `Create` und `Edit` und Ansichten übergeben außerdem ein `Movie`-Modelobjekt.</span><span class="sxs-lookup"><span data-stu-id="0a880-278">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="0a880-279">Überprüfen Sie die Datei *Index.cshtml* und die `Index`-Methode im „Movies“-Controller.</span><span class="sxs-lookup"><span data-stu-id="0a880-279">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="0a880-280">Beachten Sie, wie der Code beim Aufrufen der `View`-Methode ein `List`-Objekt erstellt.</span><span class="sxs-lookup"><span data-stu-id="0a880-280">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="0a880-281">Der Code übergibt diese Liste `Movies` aus der Aktionsmethode `Index` an die Ansicht:</span><span class="sxs-lookup"><span data-stu-id="0a880-281">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="0a880-282">Als der Movies-Controller erstellt wurde, hat der Gerüstbau automatisch die folgende `@model`-Anweisung am Anfang der Datei *Index.cshtml* hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="0a880-282">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="0a880-283">Diese `@model`-Direktive ermöglicht Ihnen den Zugriff auf die Liste der Filme, die der Controller an die Ansicht übergeben hat, indem ein stark typisiertes `Model`-Objekt verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="0a880-283">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="0a880-284">In der Ansicht *Index.cshtml* durchläuft der Code z.B. die Filme mithilfe einer `foreach`-Anweisung für das stark typisierte `Model`-Objekt:</span><span class="sxs-lookup"><span data-stu-id="0a880-284">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="0a880-285">Da das `Model`-Objekt stark typisiert ist (als ein `IEnumerable<Movie>`-Objekt), wird jedes Element in der Schleife als `Movie` typisiert.</span><span class="sxs-lookup"><span data-stu-id="0a880-285">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="0a880-286">Neben anderen Vorteilen bedeutet dies, dass Sie eine Überprüfung des Codes zur Kompilierzeit erhalten.</span><span class="sxs-lookup"><span data-stu-id="0a880-286">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0a880-287">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="0a880-287">Additional resources</span></span>

* [<span data-ttu-id="0a880-288">Taghilfsprogramme</span><span class="sxs-lookup"><span data-stu-id="0a880-288">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="0a880-289">Globalisierung und Lokalisierung</span><span class="sxs-lookup"><span data-stu-id="0a880-289">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="0a880-290">[Vorheriges Tutorial: Hinzufügen einer Ansicht](adding-view.md)
> [Nächstes Tutorial: Arbeiten mit SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="0a880-290">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="0a880-291">Hinzufügen einer Datenmodellklasse</span><span class="sxs-lookup"><span data-stu-id="0a880-291">Add a data model class</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0a880-292">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a880-292">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0a880-293">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*>**Hinzufügen** > **Klasse**.</span><span class="sxs-lookup"><span data-stu-id="0a880-293">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="0a880-294">Nennen Sie die Klasse **Movie**.</span><span class="sxs-lookup"><span data-stu-id="0a880-294">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="0a880-295">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="0a880-295">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="0a880-296">Fügen Sie zum Ordner *Modelle* eine Klasse mit dem Namen *Movie.cs* hinzu.</span><span class="sxs-lookup"><span data-stu-id="0a880-296">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="0a880-297">Erstellen des Gerüsts für das Filmmodell</span><span class="sxs-lookup"><span data-stu-id="0a880-297">Scaffold the movie model</span></span>

<span data-ttu-id="0a880-298">In diesem Abschnitt wird das Gerüst für das Filmmodell erstellt.</span><span class="sxs-lookup"><span data-stu-id="0a880-298">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="0a880-299">Mit dem Tool für den Gerüstbau werden Seiten für die Vorgänge „Create“ (Erstellen), „Read“ (Lesen), „Update“ (Aktualisieren) und „Delete“ (Löschen), kurz CRUD-Vorgänge, für das Filmmodell erstellt.</span><span class="sxs-lookup"><span data-stu-id="0a880-299">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0a880-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a880-300">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0a880-301">Klicken Sie im *Projektmappen-Explorer* mit der rechten Maustaste auf den Ordner **Controllers** und anschließend auf **Hinzufügen > Neues Gerüstelement**.</span><span class="sxs-lookup"><span data-stu-id="0a880-301">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![Screenshot für oben genannten Schritt](adding-model/_static/add_controller21.png)

<span data-ttu-id="0a880-303">Wählen Sie im Dialogfeld **Gerüst hinzufügen** die Option **MVC-Controller mit Ansichten unter Verwendung von Entity Framework > Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="0a880-303">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Dialogfeld „Gerüst hinzufügen“](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="0a880-305">Bearbeiten Sie das Dialogfeld **Controller hinzufügen**:</span><span class="sxs-lookup"><span data-stu-id="0a880-305">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="0a880-306">**Modellklasse:** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="0a880-306">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="0a880-307">**Datenkontextklasse:** Wählen Sie das Symbol **+** aus, und fügen Sie den Standardtyp **MvcMovie.Models.MvcMovieContext** hinzu.</span><span class="sxs-lookup"><span data-stu-id="0a880-307">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![Datenkontext hinzufügen](adding-model/_static/dc.png)

* <span data-ttu-id="0a880-309">**Ansichten:** Belassen Sie den Standardwert der einzelnen Optionen aktiviert.</span><span class="sxs-lookup"><span data-stu-id="0a880-309">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="0a880-310">**Controllername:** Übernehmen Sie den Standardnamen *MoviesController*.</span><span class="sxs-lookup"><span data-stu-id="0a880-310">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="0a880-311">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="0a880-311">Select **Add**</span></span>

![Dialogfeld „Controller hinzufügen“](adding-model/_static/add_controller2.png)

<span data-ttu-id="0a880-313">Visual Studio erstellt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="0a880-313">Visual Studio creates:</span></span>

* <span data-ttu-id="0a880-314">Eine Entity Framework Core-[Datenbankkontext-Klasse](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span><span class="sxs-lookup"><span data-stu-id="0a880-314">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span></span>
* <span data-ttu-id="0a880-315">Einen Movies-Controller (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="0a880-315">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="0a880-316">Razor-Ansichtsdateien für die Seiten „Erstellen“, „Löschen“, „Details“ „Bearbeiten“ und „Index“ (*Views/Movies/\*.cshtml*)</span><span class="sxs-lookup"><span data-stu-id="0a880-316">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="0a880-317">Die automatische Erstellung des Datenbankkontexts und der [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Aktionsmethoden (create, read, update and delete – Erstellen, Lesen, Aktualisieren und Löschen) und Ansichten wird als *Gerüstbau* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="0a880-317">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0a880-318">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a880-318">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="0a880-319">Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs*, *Startup.cs*, und *CSPROJ*).</span><span class="sxs-lookup"><span data-stu-id="0a880-319">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="0a880-320">Installieren Sie das Gerüstbautool:</span><span class="sxs-lookup"><span data-stu-id="0a880-320">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="0a880-321">Exportieren Sie unter Linux den Pfad des Gerüstbautools:</span><span class="sxs-lookup"><span data-stu-id="0a880-321">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="0a880-322">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="0a880-322">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="0a880-323">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="0a880-323">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0a880-324">Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs*, *Startup.cs*, und *CSPROJ*).</span><span class="sxs-lookup"><span data-stu-id="0a880-324">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="0a880-325">Installieren Sie das Gerüstbautool:</span><span class="sxs-lookup"><span data-stu-id="0a880-325">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="0a880-326">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="0a880-326">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="0a880-327">Wenn Sie die App ausführen und auf den Link **Mvc Movie** klicken, erhalten Sie eine Fehlermeldung wie die Folgende:</span><span class="sxs-lookup"><span data-stu-id="0a880-327">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0a880-328">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a880-328">Visual Studio</span></span>](#tab/visual-studio)

``` error
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="0a880-329">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="0a880-329">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

``` error
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

<span data-ttu-id="0a880-330">Sie müssen die Datenbank erstellen und verwenden dazu das EF Core-Feature [Migrationen](xref:data/ef-mvc/migrations).</span><span class="sxs-lookup"><span data-stu-id="0a880-330">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="0a880-331">Mit „Migrationen“ können Sie eine Datenbank erstellen, die mit Ihrem Datenmodell übereinstimmt, und das Datenbankschema aktualisieren, wenn sich Ihr Datenmodell ändert.</span><span class="sxs-lookup"><span data-stu-id="0a880-331">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="0a880-332">Anfängliche Migration</span><span class="sxs-lookup"><span data-stu-id="0a880-332">Initial migration</span></span>

<span data-ttu-id="0a880-333">In diesem Abschnitt werden die folgenden Aufgaben ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="0a880-333">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="0a880-334">Fügen Sie eine anfängliche Migration hinzu.</span><span class="sxs-lookup"><span data-stu-id="0a880-334">Add an initial migration.</span></span>
* <span data-ttu-id="0a880-335">Aktualisieren Sie die Datenbank mit der anfänglichen Migration.</span><span class="sxs-lookup"><span data-stu-id="0a880-335">Update the database with the initial migration.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0a880-336">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a880-336">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="0a880-337">Wählen Sie im Menü **Tools** **NuGet-Paket-Manager** > **Paket-Manager-Konsole** (PMC) aus.</span><span class="sxs-lookup"><span data-stu-id="0a880-337">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![PMC-Menü](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="0a880-339">Geben Sie in der PMC die folgenden Befehle ein:</span><span class="sxs-lookup"><span data-stu-id="0a880-339">In the PMC, enter the following commands:</span></span>

   ```PMC
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="0a880-340">Mit dem Befehl `Add-Migration` wird Code generiert, um das anfängliche Datenbankschema zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="0a880-340">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="0a880-341">Das Datenbankschema basiert auf dem Modell, das in der Klasse `MvcMovieContext` angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="0a880-341">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span> <span data-ttu-id="0a880-342">Das `Initial`-Argument ist der Migrationsname.</span><span class="sxs-lookup"><span data-stu-id="0a880-342">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="0a880-343">Es kann jeder Name verwendet werden, aber per Konvention wird ein Name verwendet, der die Migration beschreibt.</span><span class="sxs-lookup"><span data-stu-id="0a880-343">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="0a880-344">Weitere Informationen finden Sie unter <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="0a880-344">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="0a880-345">Mit dem Befehl `Update-Database` führen Sie die Methode `Up` in der Datei *Migrations/{time-stamp}_InitialCreate.cs* aus, mit der die Datenbank erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="0a880-345">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="0a880-346">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="0a880-346">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="0a880-347">Mit dem Befehl `ef migrations add InitialCreate` wird Code generiert, um das anfängliche Datenbankschema zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="0a880-347">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="0a880-348">Das Datenbankschema basiert auf dem Modell, das in der `MvcMovieContext`-Klasse (in der Datei *Data/MvcMovieContext.cs*) angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="0a880-348">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="0a880-349">Das `InitialCreate`-Argument ist der Migrationsname.</span><span class="sxs-lookup"><span data-stu-id="0a880-349">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="0a880-350">Es kann jeder Name verwendet werden, aber per Konvention wird ein Name ausgewählt, der die Migration beschreibt.</span><span class="sxs-lookup"><span data-stu-id="0a880-350">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="0a880-351">Überprüfen des mit Dependency Injection registrierten Kontexts</span><span class="sxs-lookup"><span data-stu-id="0a880-351">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="0a880-352">ASP.NET Core wird mit [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (Dependency Injection, DI) erstellt.</span><span class="sxs-lookup"><span data-stu-id="0a880-352">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="0a880-353">Dienste (z.B. der EF Core-Datenbankkontext) werden über DI beim Anwendungsstart registriert.</span><span class="sxs-lookup"><span data-stu-id="0a880-353">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="0a880-354">Komponenten, die diese Dienste erfordern (z.B. Razor Pages), werden von diesen Diensten über Konstruktorparameter bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="0a880-354">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="0a880-355">Der Konstruktorcode, der eine Datenbankkontext-Instanz abruft, wird später in diesem Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="0a880-355">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0a880-356">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a880-356">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0a880-357">Das Gerüstbautool hat automatisch einen Datenbankkontext erstellt und diesen mit dem DI-Container registriert.</span><span class="sxs-lookup"><span data-stu-id="0a880-357">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="0a880-358">Untersuchen Sie die folgende `Startup.ConfigureServices`-Methode.</span><span class="sxs-lookup"><span data-stu-id="0a880-358">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="0a880-359">Die hervorgehobene Zeile wurde vom Gerüst hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="0a880-359">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

<span data-ttu-id="0a880-360">Der `MvcMovieContext` koordiniert die EF Core-Funktionen (Create, Read, Update, Delete usw.) für das `Movie`-Modell.</span><span class="sxs-lookup"><span data-stu-id="0a880-360">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="0a880-361">Der Datenkontext (`MvcMovieContext`) wird aus [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="0a880-361">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="0a880-362">Der Datenkontext gibt an, welche Entitäten im Datenmodell enthalten sind:</span><span class="sxs-lookup"><span data-stu-id="0a880-362">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

<span data-ttu-id="0a880-363">Der vorangehende Code erstellt eine [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1)-Eigenschaft für die Entitätenmenge.</span><span class="sxs-lookup"><span data-stu-id="0a880-363">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="0a880-364">In der Terminologie von Entity Framework entspricht eine Entitätenmenge in der Regel einer Datenbanktabelle.</span><span class="sxs-lookup"><span data-stu-id="0a880-364">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="0a880-365">Entitäten entsprechen Zeilen in Tabellen.</span><span class="sxs-lookup"><span data-stu-id="0a880-365">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="0a880-366">Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions)-Objekt aufrufen.</span><span class="sxs-lookup"><span data-stu-id="0a880-366">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="0a880-367">Für die lokale Entwicklung liest das [ASP.NET Core-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der *appsettings.json*-Datei.</span><span class="sxs-lookup"><span data-stu-id="0a880-367">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="0a880-368">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="0a880-368">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="0a880-369">Sie haben einen Datenbankkontext erstellt und diesen mit dem DI-Container registriert.</span><span class="sxs-lookup"><span data-stu-id="0a880-369">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="0a880-370">Testen der App</span><span class="sxs-lookup"><span data-stu-id="0a880-370">Test the app</span></span>

* <span data-ttu-id="0a880-371">Führen Sie die App aus, und fügen Sie `/Movies` an die URL im Browser an (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="0a880-371">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="0a880-372">Wenn eine Datenbankausnahme auftritt, die der folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="0a880-372">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="0a880-373">Sie haben den [Migrationsschritt](#pmc) verpasst.</span><span class="sxs-lookup"><span data-stu-id="0a880-373">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="0a880-374">Testen Sie den Link **Create** (Erstellen).</span><span class="sxs-lookup"><span data-stu-id="0a880-374">Test the **Create** link.</span></span> <span data-ttu-id="0a880-375">Geben Sie die Daten ein, und senden Sie sie.</span><span class="sxs-lookup"><span data-stu-id="0a880-375">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="0a880-376">Sie können unter Umständen in das Feld `Price` keine Kommas als Dezimaltrennzeichen eingeben.</span><span class="sxs-lookup"><span data-stu-id="0a880-376">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="0a880-377">Zur Unterstützung der [jQuery-Validierung](https://jqueryvalidation.org/) für Gebietsschemas mit einer anderen Sprache als Englisch, in denen ein Komma („,“) als Dezimaltrennzeichen verwendet wird, und für Datums- und Uhrzeitformate, die nicht dem US-englischen Format entsprechen, muss die App globalisiert werden.</span><span class="sxs-lookup"><span data-stu-id="0a880-377">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="0a880-378">Globalisierungsanweisungen finden Sie unter [GitHub-Problem](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="0a880-378">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="0a880-379">Testen Sie die Links **Edit** (Bearbeiten), **Details** und **Delete** (Löschen).</span><span class="sxs-lookup"><span data-stu-id="0a880-379">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="0a880-380">Überprüfen Sie die `Startup`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="0a880-380">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="0a880-381">Der oberhalb hervorgehobene Code zeigt den Filmdatenbankkontext, der dem [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)-Container hinzugefügt wird:</span><span class="sxs-lookup"><span data-stu-id="0a880-381">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="0a880-382">`services.AddDbContext<MvcMovieContext>(options =>` gibt die zu verwendende Datenbank und die Verbindungszeichenfolge an.</span><span class="sxs-lookup"><span data-stu-id="0a880-382">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="0a880-383">`=>` ist ein [Lambdaoperator](/dotnet/articles/csharp/language-reference/operators/lambda-operator).</span><span class="sxs-lookup"><span data-stu-id="0a880-383">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="0a880-384">Öffnen Sie die Datei *Controllers/MoviesController.cs*, und überprüfen Sie den Konstruktor:</span><span class="sxs-lookup"><span data-stu-id="0a880-384">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="0a880-385">Der Konstruktor verwendet die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) zum Einfügen des Datenbankkontexts (`MvcMovieContext`) in den Controller.</span><span class="sxs-lookup"><span data-stu-id="0a880-385">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="0a880-386">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="0a880-386">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="0a880-387">Stark typisierte Modelle und das Schlüsselwort @model</span><span class="sxs-lookup"><span data-stu-id="0a880-387">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="0a880-388">Weiter oben in diesem Tutorial haben Sie gesehen, wie ein Controller mithilfe des Wörterbuchs `ViewData` Daten oder Objekte an eine Ansicht übergeben kann.</span><span class="sxs-lookup"><span data-stu-id="0a880-388">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="0a880-389">Das Wörterbuch `ViewData` ist ein dynamisches Objekt, das eine praktische Möglichkeit zur späten Bindung bietet, um Informationen an eine Ansicht zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="0a880-389">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="0a880-390">MVC bietet außerdem die Möglichkeit, stark typisierte Modellobjekte an eine Ansicht zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="0a880-390">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="0a880-391">Dieser stark typisierte Ansatz ermöglicht eine bessere Überprüfung Ihres Codes zur Kompilierzeit.</span><span class="sxs-lookup"><span data-stu-id="0a880-391">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="0a880-392">Der Gerüstbaumechnismus hat diesen Ansatz (d.h. das Übergeben eines stark typisierten Modells) mit der `MoviesController`-Klasse und Ansichten genutzt, als die Methoden und Ansichten erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="0a880-392">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="0a880-393">Untersuchen Sie die generierte `Details`-Methode in der Datei *Controllers/MoviesController.cs*:</span><span class="sxs-lookup"><span data-stu-id="0a880-393">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="0a880-394">Der `id`-Parameter wird im Allgemeinen als Routendaten übergeben.</span><span class="sxs-lookup"><span data-stu-id="0a880-394">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="0a880-395">`https://localhost:5001/movies/details/1` legt beispielsweise Folgendes fest:</span><span class="sxs-lookup"><span data-stu-id="0a880-395">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="0a880-396">Den Controller auf den Controller `movies` (das erste URL-Segment).</span><span class="sxs-lookup"><span data-stu-id="0a880-396">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="0a880-397">Die Aktion auf `details` (das zweite URL-Segment).</span><span class="sxs-lookup"><span data-stu-id="0a880-397">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="0a880-398">Die ID auf 1 (das letzte URL-Segment).</span><span class="sxs-lookup"><span data-stu-id="0a880-398">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="0a880-399">Sie können die `id` auch mithilfe einer Abfragezeichenfolge übergeben:</span><span class="sxs-lookup"><span data-stu-id="0a880-399">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="0a880-400">Der `id`-Parameter wird als [Nullable-Typ](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) für den Fall definiert, dass kein ID-Wert angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="0a880-400">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="0a880-401">Ein [Lambdaausdruck](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) wird an `FirstOrDefaultAsync` übergeben, um Filmentitäten auszuwählen, die mit den Routendaten oder dem Wert der Abfragezeichenfolge übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="0a880-401">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="0a880-402">Wenn ein Film gefunden wird, wird eine Instanz des `Movie`-Modells an die Ansicht `Details` übergeben:</span><span class="sxs-lookup"><span data-stu-id="0a880-402">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="0a880-403">Untersuchen Sie den Inhalt der Datei *Views/Movies/Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="0a880-403">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="0a880-404">Durch Einschließen einer `@model`-Anweisung am Anfang der Ansichtsdatei können Sie den Typ des Objekts angeben, den die Ansicht erwartet.</span><span class="sxs-lookup"><span data-stu-id="0a880-404">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="0a880-405">Beim Erstellen des „Movie“-Controllers wurde die folgende `@model`-Anweisung automatisch am Anfang der Datei *Details.cshtml* hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="0a880-405">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```HTML
@model MvcMovie.Models.Movie
   ```

<span data-ttu-id="0a880-406">Diese `@model`-Direktive ermöglicht Ihnen den Zugriff auf den Film, den der Controller an die Ansicht übergeben hat, indem ein stark typisiertes `Model`-Objekt verwendet wir.</span><span class="sxs-lookup"><span data-stu-id="0a880-406">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="0a880-407">In der Ansicht *Details.cshtml* übergibt der Code jedes Filmfeld an die HTML-Hilfsprogramme `DisplayNameFor` und `DisplayFor` mit dem stark typisierten `Model`-Objekt.</span><span class="sxs-lookup"><span data-stu-id="0a880-407">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="0a880-408">Die Methoden `Create` und `Edit` und Ansichten übergeben außerdem ein `Movie`-Modelobjekt.</span><span class="sxs-lookup"><span data-stu-id="0a880-408">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="0a880-409">Überprüfen Sie die Datei *Index.cshtml* und die `Index`-Methode im „Movies“-Controller.</span><span class="sxs-lookup"><span data-stu-id="0a880-409">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="0a880-410">Beachten Sie, wie der Code beim Aufrufen der `View`-Methode ein `List`-Objekt erstellt.</span><span class="sxs-lookup"><span data-stu-id="0a880-410">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="0a880-411">Der Code übergibt diese Liste `Movies` aus der Aktionsmethode `Index` an die Ansicht:</span><span class="sxs-lookup"><span data-stu-id="0a880-411">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="0a880-412">Beim Erstellen des „Movies“-Controllers hat der Gerüstbau automatisch die folgende `@model`-Anweisung am Anfang der Datei *Index.cshtml* hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="0a880-412">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="0a880-413">Diese `@model`-Direktive ermöglicht Ihnen den Zugriff auf die Liste der Filme, die der Controller an die Ansicht übergeben hat, indem ein stark typisiertes `Model`-Objekt verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="0a880-413">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="0a880-414">In der Ansicht *Index.cshtml* durchläuft der Code z.B. die Filme mithilfe einer `foreach`-Anweisung für das stark typisierte `Model`-Objekt:</span><span class="sxs-lookup"><span data-stu-id="0a880-414">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="0a880-415">Da das `Model`-Objekt stark typisiert ist (als ein `IEnumerable<Movie>`-Objekt), wird jedes Element in der Schleife als `Movie` typisiert.</span><span class="sxs-lookup"><span data-stu-id="0a880-415">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="0a880-416">Neben anderen Vorteilen bedeutet dies, dass Sie eine Überprüfung des Codes zur Kompilierzeit erhalten:</span><span class="sxs-lookup"><span data-stu-id="0a880-416">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0a880-417">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="0a880-417">Additional resources</span></span>

* [<span data-ttu-id="0a880-418">Taghilfsprogramme</span><span class="sxs-lookup"><span data-stu-id="0a880-418">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="0a880-419">Globalisierung und Lokalisierung</span><span class="sxs-lookup"><span data-stu-id="0a880-419">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="0a880-420">[Vorheriges Tutorial: Hinzufügen einer Ansicht](adding-view.md)
> [Nächstes Tutorial: Arbeiten mit einer Datenbank](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="0a880-420">[Previous Adding a View](adding-view.md)
[Next Working with a database](working-with-sql.md)</span></span>

::: moniker-end

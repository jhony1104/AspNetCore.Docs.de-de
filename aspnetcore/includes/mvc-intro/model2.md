::: moniker range=">= aspnetcore-3.0"

<a name="dc"></a>

<span data-ttu-id="81810-101">Erstellen Sie einen Ordner *Data*.</span><span class="sxs-lookup"><span data-stu-id="81810-101">Create a *Data* folder.</span></span>

<span data-ttu-id="81810-102">Fügen Sie dem Ordner *Data* (Daten) die folgende `MvcMovieContext`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="81810-102">Add the following `MvcMovieContext` class to the *Data* folder:</span></span>  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="81810-103">Der vorangehende Code erstellt eine `DbSet`-Eigenschaft für die Entitätenmenge.</span><span class="sxs-lookup"><span data-stu-id="81810-103">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="81810-104">In der Terminologie von Entity Framework entspricht eine Entitätenmenge in der Regel einer Datenbanktabelle, und eine Entität entspricht einer Zeile in einer Tabelle.</span><span class="sxs-lookup"><span data-stu-id="81810-104">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="81810-105">Hinzufügen einer Datenbank-Verbindungszeichenfolge</span><span class="sxs-lookup"><span data-stu-id="81810-105">Add a database connection string</span></span>

<span data-ttu-id="81810-106">Fügen Sie der Datei *appsettings.json* eine Verbindungszeichenfolge hinzu:</span><span class="sxs-lookup"><span data-stu-id="81810-106">Add a connection string to the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="81810-107">Hinzufügen von NuGet-Paketen und EF-Tools</span><span class="sxs-lookup"><span data-stu-id="81810-107">Add NuGet packages and EF tools</span></span>

<span data-ttu-id="81810-108">Führen Sie die folgenden .NET Core-CLI-Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="81810-108">Run the following .NET Core CLI commands:</span></span>

```console
dotnet tool install --global dotnet-ef --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.SQLite --version 3.0.0-*
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.0.0-*
```

<span data-ttu-id="81810-109">Die vorhergehenden Befehle fügen dem Projekt Entity Framework Core-Tools für die .NET-CLI und mehrere Pakete hinzu.</span><span class="sxs-lookup"><span data-stu-id="81810-109">The preceding commands add Entity Framework Core Tools for the .NET CLI and several packages to the project.</span></span> <span data-ttu-id="81810-110">Das `Microsoft.VisualStudio.Web.CodeGeneration.Design`-Paket wird für den Gerüstbau benötigt.</span><span class="sxs-lookup"><span data-stu-id="81810-110">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="81810-111">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="81810-111">Register the database context</span></span>

<span data-ttu-id="81810-112">Fügen Sie am Anfang der Datei *Startup.cs* die folgenden `using`-Anweisungen ein.</span><span class="sxs-lookup"><span data-stu-id="81810-112">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="81810-113">Registrieren Sie den Datenbankkontext mit dem [Dependency Injection-Container](xref:fundamentals/dependency-injection) in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="81810-113">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

<span data-ttu-id="81810-114">Erstellen Sie das Projekt, um zu ermitteln, ob Compilerfehler vorliegen.</span><span class="sxs-lookup"><span data-stu-id="81810-114">Build the project as a check for compiler errors.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="81810-115">Fügen Sie dem Ordner *Models* die folgende `MvcMovieContext`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="81810-115">Add the following `MvcMovieContext` class to the *Models* folder:</span></span>  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Data/MvcMovieContext.cs)]

<span data-ttu-id="81810-116">Der vorangehende Code erstellt eine `DbSet`-Eigenschaft für die Entitätenmenge.</span><span class="sxs-lookup"><span data-stu-id="81810-116">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="81810-117">In der Terminologie von Entity Framework entspricht eine Entitätenmenge in der Regel einer Datenbanktabelle, und eine Entität entspricht einer Zeile in einer Tabelle.</span><span class="sxs-lookup"><span data-stu-id="81810-117">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="81810-118">Hinzufügen einer Datenbank-Verbindungszeichenfolge</span><span class="sxs-lookup"><span data-stu-id="81810-118">Add a database connection string</span></span>

<span data-ttu-id="81810-119">Fügen Sie der Datei *appsettings.json* eine Verbindungszeichenfolge hinzu:</span><span class="sxs-lookup"><span data-stu-id="81810-119">Add a connection string to the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="81810-120">Hinzufügen der erforderlichen NuGet-Pakete</span><span class="sxs-lookup"><span data-stu-id="81810-120">Add required NuGet packages</span></span>

<span data-ttu-id="81810-121">Führen Sie den folgenden .NET Core-CLI-Befehl aus, um dem Projekt „SQLite“ und „CodeGeneration.Design“ hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="81810-121">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design  to the project:</span></span>

```console
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
```

<span data-ttu-id="81810-122">Das `Microsoft.VisualStudio.Web.CodeGeneration.Design`-Paket wird für den Gerüstbau benötigt.</span><span class="sxs-lookup"><span data-stu-id="81810-122">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="81810-123">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="81810-123">Register the database context</span></span>

<span data-ttu-id="81810-124">Fügen Sie am Anfang der Datei *Startup.cs* die folgenden `using`-Anweisungen ein.</span><span class="sxs-lookup"><span data-stu-id="81810-124">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="81810-125">Registrieren Sie den Datenbankkontext mit dem [Dependency Injection-Container](xref:fundamentals/dependency-injection) in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="81810-125">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="81810-126">Erstellen Sie das Projekt zur Fehlerüberprüfung.</span><span class="sxs-lookup"><span data-stu-id="81810-126">Build the project as a check for errors.</span></span>
::: moniker-end
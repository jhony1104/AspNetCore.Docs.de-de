<a name="dc"></a>

### <a name="add-a-database-context-class"></a><span data-ttu-id="75f5c-101">Hinzufügen einer Datenbankkontext-Klasse</span><span class="sxs-lookup"><span data-stu-id="75f5c-101">Add a database context class</span></span>

<span data-ttu-id="75f5c-102">Erstellen Sie im RazorPagesMovie-Projekt einen neuen Ordner namens *Data*.</span><span class="sxs-lookup"><span data-stu-id="75f5c-102">In the RazorPagesMovie project, create a new folder called *Data*.</span></span> <span data-ttu-id="75f5c-103">Fügen Sie dem Ordner *Data* (Daten) die folgende `RazorPagesMovieContext`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="75f5c-103">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="75f5c-104">Der vorangehende Code erstellt eine `DbSet`-Eigenschaft für die Entitätenmenge.</span><span class="sxs-lookup"><span data-stu-id="75f5c-104">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="75f5c-105">In der Terminologie von Entity Framework entspricht eine Entitätenmenge in der Regel einer Datenbanktabelle, und eine Entität entspricht einer Zeile in einer Tabelle.</span><span class="sxs-lookup"><span data-stu-id="75f5c-105">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="75f5c-106">Hinzufügen einer Datenbank-Verbindungszeichenfolge</span><span class="sxs-lookup"><span data-stu-id="75f5c-106">Add a database connection string</span></span>

<span data-ttu-id="75f5c-107">Fügen Sie zur Datei *appsettings.json* wie im folgenden hervorgehobenen Code dargestellt eine Verbindungszeichenfolge hinzu:</span><span class="sxs-lookup"><span data-stu-id="75f5c-107">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="75f5c-108">Hinzufügen von NuGet-Paketen und EF-Tools</span><span class="sxs-lookup"><span data-stu-id="75f5c-108">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="75f5c-109">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="75f5c-109">Register the database context</span></span>

<span data-ttu-id="75f5c-110">Fügen Sie am Anfang der Datei *Startup.cs* die folgenden `using`-Anweisungen ein.</span><span class="sxs-lookup"><span data-stu-id="75f5c-110">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="75f5c-111">Registrieren Sie den Datenbankkontext mit dem [Dependency Injection-Container](xref:fundamentals/dependency-injection) in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="75f5c-111">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-9)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="75f5c-112">Hinzufügen der erforderlichen NuGet-Pakete</span><span class="sxs-lookup"><span data-stu-id="75f5c-112">Add required NuGet packages</span></span>

<span data-ttu-id="75f5c-113">Führen Sie den folgenden .NET Core-CLI-Befehl aus, um dem Projekt „SQLite“ und „CodeGeneration.Design“ hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="75f5c-113">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design  to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
```

<span data-ttu-id="75f5c-114">Das `Microsoft.VisualStudio.Web.CodeGeneration.Design`-Paket wird für den Gerüstbau benötigt.</span><span class="sxs-lookup"><span data-stu-id="75f5c-114">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="75f5c-115">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="75f5c-115">Register the database context</span></span>

<span data-ttu-id="75f5c-116">Fügen Sie am Anfang der Datei *Startup.cs* die folgenden `using`-Anweisungen ein.</span><span class="sxs-lookup"><span data-stu-id="75f5c-116">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="75f5c-117">Registrieren Sie den Datenbankkontext mit dem [Dependency Injection-Container](xref:fundamentals/dependency-injection) in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="75f5c-117">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="75f5c-118">Erstellen Sie das Projekt zur Fehlerüberprüfung.</span><span class="sxs-lookup"><span data-stu-id="75f5c-118">Build the project as a check for errors.</span></span>
::: moniker-end

---
ms.openlocfilehash: 86cf1874677dc8b79e3223fb0819eb1881c69a11
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58265598"
---
<a name="dc"></a>

### <a name="add-a-database-context-class"></a><span data-ttu-id="ccccf-101">Hinzufügen einer Datenbankkontext-Klasse</span><span class="sxs-lookup"><span data-stu-id="ccccf-101">Add a database context class</span></span>

<span data-ttu-id="ccccf-102">Fügen Sie dem Ordner *Models* die folgende `RazorPagesMovieContext`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="ccccf-102">Add the following `RazorPagesMovieContext` class to the *Models* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="ccccf-103">Der vorangehende Code erstellt eine `DbSet`-Eigenschaft für die Entitätenmenge.</span><span class="sxs-lookup"><span data-stu-id="ccccf-103">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="ccccf-104">In der Terminologie von Entity Framework entspricht eine Entitätenmenge in der Regel einer Datenbanktabelle, und eine Entität entspricht einer Zeile in einer Tabelle.</span><span class="sxs-lookup"><span data-stu-id="ccccf-104">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="ccccf-105">Hinzufügen einer Datenbank-Verbindungszeichenfolge</span><span class="sxs-lookup"><span data-stu-id="ccccf-105">Add a database connection string</span></span>

<span data-ttu-id="ccccf-106">Fügen Sie der Datei *appsettings.json* eine Verbindungszeichenfolge hinzu:</span><span class="sxs-lookup"><span data-stu-id="ccccf-106">Add a connection string to the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="ccccf-107">Hinzufügen der erforderlichen NuGet-Pakete</span><span class="sxs-lookup"><span data-stu-id="ccccf-107">Add required NuGet packages</span></span>

<span data-ttu-id="ccccf-108">Führen Sie den folgenden .NET Core-CLI-Befehl aus, um dem Projekt „SQLite“ und „CodeGeneration.Design“ hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="ccccf-108">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design  to the project:</span></span>

```console
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design

```

<span data-ttu-id="ccccf-109">Das `Microsoft.VisualStudio.Web.CodeGeneration.Design`-Paket wird für den Gerüstbau benötigt.</span><span class="sxs-lookup"><span data-stu-id="ccccf-109">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="ccccf-110">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="ccccf-110">Register the database context</span></span>

<span data-ttu-id="ccccf-111">Fügen Sie am Anfang der Datei *Startup.cs* die folgenden `using`-Anweisungen ein.</span><span class="sxs-lookup"><span data-stu-id="ccccf-111">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="ccccf-112">Registrieren Sie den Datenbankkontext mit dem [Dependency Injection-Container](xref:fundamentals/dependency-injection) in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ccccf-112">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="ccccf-113">Erstellen Sie das Projekt zur Fehlerüberprüfung.</span><span class="sxs-lookup"><span data-stu-id="ccccf-113">Build the project as a check for errors.</span></span>

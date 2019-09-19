::: moniker range=">= aspnetcore-3.0"

<a name="dc"></a>

Erstellen Sie einen Ordner *Data*.

Fügen Sie dem Ordner *Data* (Daten) die folgende `MvcMovieContext`-Klasse hinzu:  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

Der vorangehende Code erstellt eine `DbSet`-Eigenschaft für die Entitätenmenge. In der Terminologie von Entity Framework entspricht eine Entitätenmenge in der Regel einer Datenbanktabelle, und eine Entität entspricht einer Zeile in einer Tabelle.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>Hinzufügen einer Datenbank-Verbindungszeichenfolge

Fügen Sie der Datei *appsettings.json* eine Verbindungszeichenfolge hinzu:

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

### <a name="add-nuget-packages-and-ef-tools"></a>Hinzufügen von NuGet-Paketen und EF-Tools

Führen Sie die folgenden .NET Core-CLI-Befehle aus:

```dotnetcli
dotnet tool install --global dotnet-ef --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.SQLite --version 3.0.0-*
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.0.0-*
```

Die vorhergehenden Befehle fügen dem Projekt Entity Framework Core-Tools für die .NET-CLI und mehrere Pakete hinzu. Das `Microsoft.VisualStudio.Web.CodeGeneration.Design`-Paket wird für den Gerüstbau benötigt.

<a name="reg"></a>

### <a name="register-the-database-context"></a>Registrieren des Datenbankkontexts

Fügen Sie am Anfang der Datei *Startup.cs* die folgenden `using`-Anweisungen ein.

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

Registrieren Sie den Datenbankkontext mit dem [Dependency Injection-Container](xref:fundamentals/dependency-injection) in `Startup.ConfigureServices`.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

Erstellen Sie das Projekt, um zu ermitteln, ob Compilerfehler vorliegen.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Fügen Sie dem Ordner *Models* die folgende `MvcMovieContext`-Klasse hinzu:  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Data/MvcMovieContext.cs)]

Der vorangehende Code erstellt eine `DbSet`-Eigenschaft für die Entitätenmenge. In der Terminologie von Entity Framework entspricht eine Entitätenmenge in der Regel einer Datenbanktabelle, und eine Entität entspricht einer Zeile in einer Tabelle.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>Hinzufügen einer Datenbank-Verbindungszeichenfolge

Fügen Sie der Datei *appsettings.json* eine Verbindungszeichenfolge hinzu:

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a>Hinzufügen der erforderlichen NuGet-Pakete

Führen Sie den folgenden .NET Core-CLI-Befehl aus, um dem Projekt „SQLite“ und „CodeGeneration.Design“ hinzuzufügen:

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
```

Das `Microsoft.VisualStudio.Web.CodeGeneration.Design`-Paket wird für den Gerüstbau benötigt.

<a name="reg"></a>

### <a name="register-the-database-context"></a>Registrieren des Datenbankkontexts

Fügen Sie am Anfang der Datei *Startup.cs* die folgenden `using`-Anweisungen ein.

```csharp
using MvcMovie.Models;
using Microsoft.EntityFrameworkCore;
```

Registrieren Sie den Datenbankkontext mit dem [Dependency Injection-Container](xref:fundamentals/dependency-injection) in `Startup.ConfigureServices`.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

Erstellen Sie das Projekt zur Fehlerüberprüfung.
::: moniker-end
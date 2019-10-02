Führen Sie die folgenden .NET Core-CLI-Befehle aus:

```dotnetcli
dotnet tool install --global dotnet-ef
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

Die oben aufgeführten Befehle fügen Folgendes hinzu:

* Das [Gerüstbautool „aspnet-codegenerator“](xref:fundamentals/tools/dotnet-aspnet-codegenerator).
* Die Entity Framework Core-Tools für die .NET Core-CLI.
* Den EF Core SQLite-Anbieter, der das EF Core-Paket als Abhängigkeit installiert.
* Für Gerüstbau erforderliche Pakete: `Microsoft.VisualStudio.Web.CodeGeneration.Design` und `Microsoft.EntityFrameworkCore.SqlServer`.

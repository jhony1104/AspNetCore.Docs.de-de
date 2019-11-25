### <a name="use-sqlite-for-development-sql-server-for-production"></a>Verwenden von SQLite für die Entwicklung und von SQL Server für die Produktion

Wenn SQLite ausgewählt wurde, ist der von der Vorlage generierte Code für die Entwicklung bereit. Der folgende Code zeigt das Injizieren von <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> in Startup. `IWebHostEnvironment` wird eingefügt, sodass `ConfigureServices` SQLite bei der Entwicklung und SQL Server für die Produktion verwenden kann.

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

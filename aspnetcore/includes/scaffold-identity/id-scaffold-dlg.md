Führen Sie das Identitäts Gerüst aus:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klicken Sie in **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, > **Add**  >  **Neues Gerüst Element**hinzufügen.
* Wählen Sie im linken Bereich des Dialog Felds **Neues Gerüst Element hinzufügen** die Option **Identität**  >  **Hinzufügen**aus.
* Wählen Sie im Dialogfeld **Identität hinzufügen** die gewünschten Optionen aus.
  * Wählen Sie die vorhandene Layoutseite aus, oder die Layoutdatei wird mit einem falschen Markup überschrieben:
    * `~/Pages/Shared/_Layout.cshtml`für Razor Pages
    * `~/Views/Shared/_Layout.cshtml`für MVC-Projekte
    * Blazor-Server-apps, die aus der blazor-Server Vorlage () erstellt wurden, `blazorserver` sind nicht standardmäßig für Razor Pages oder MVC konfiguriert. Lassen Sie den Eintrag Layoutseite leer.
  * Wählen Sie die **+** Schaltfläche aus, um eine neue **Datenkontext Klasse**zu erstellen. Übernehmen Sie den Standardwert, oder geben Sie eine Klasse an (z `MyApplication.Data.ApplicationDbContext` . b.).
* Wählen Sie **Hinzufügen**.

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Wenn Sie das ASP.net Core Gerüst nicht bereits installiert haben, installieren Sie es jetzt:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Fügen Sie die erforderlichen nuget-Paket Verweise der Projektdatei (*. csproj*) hinzu. Führen Sie im Projektverzeichnis die folgenden Befehle aus:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

Führen Sie den folgenden Befehl aus, um die Einstellungen für das Identitäts Gerüst aufzulisten:

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

Führen Sie im Projektordner das Identitäts Gerüst mit den gewünschten Optionen aus. Führen Sie beispielsweise den folgenden Befehl aus, um die Identität mit der Standardbenutzer Oberfläche und der minimalen Anzahl von Dateien einzurichten:

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---

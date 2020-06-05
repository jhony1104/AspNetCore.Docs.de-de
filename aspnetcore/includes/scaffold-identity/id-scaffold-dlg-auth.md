::: moniker range=">= aspnetcore-3.0"

Führen Sie das Identitäts Gerüst aus:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klicken Sie in **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, > **Add** > **Neues Gerüst Element**hinzufügen.
* Wählen Sie im linken Bereich des Dialog Felds **Gerüst hinzufügen** die Option **Identität** > **Hinzufügen**aus.
* Wählen Sie im Dialogfeld **Identität hinzufügen** die gewünschten Optionen aus.
  * Wählen Sie die vorhandene Layoutseite aus, sodass die Layoutdatei nicht mit einem falschen Markup überschrieben wird Wenn eine vorhandene Datei " * \_ Layout. cshtml* " ausgewählt ist, wird Sie **nicht** überschrieben. Beispiel:
    * `~/Pages/Shared/_Layout.cshtml`für Razor Pages-oder blazor-Server Projekte mit vorhandener Razor Pages-Infrastruktur
    * `~/Views/Shared/_Layout.cshtml`für MVC-Projekte oder blazor-Server Projekte mit vorhandener MVC-Infrastruktur
* Um den vorhandenen Datenkontext zu verwenden, wählen Sie mindestens eine Datei aus, die Sie außer Kraft setzen möchten. Sie müssen mindestens eine Datei auswählen, um den Datenkontext hinzuzufügen.
  * Wählen Sie Ihre Datenkontext Klasse aus.
  * Wählen Sie **Hinzufügen**.
* So erstellen Sie einen neuen Benutzer Kontext und erstellen möglicherweise eine benutzerdefinierte Benutzerklasse für die Identität:
  * Wählen Sie die **+** Schaltfläche aus, um eine neue **Datenkontext Klasse**zu erstellen. Übernehmen Sie den Standardwert, oder geben Sie eine Klasse an (z `MyApplication.Data.ApplicationDbContext` . b.).
  * Wählen Sie **Hinzufügen**.

Hinweis: Wenn Sie einen neuen Benutzer Kontext erstellen, müssen Sie keine Datei zum Überschreiben auswählen.

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

Führen Sie im Projektordner das Identitäts Gerüst mit den gewünschten Optionen aus. Führen Sie beispielsweise den folgenden Befehl aus, um die Identität mit der Standardbenutzer Oberfläche und der minimalen Anzahl von Dateien einzurichten. Verwenden Sie den richtigen voll qualifizierten Namen für Ihren Daten Bank Kontext:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

PowerShell verwendet ein Semikolon als Befehls Trennzeichen. Wenn Sie PowerShell verwenden, versehen Sie die Semikolons in der Datei Liste, oder fügen Sie die Datei Liste in doppelte Anführungszeichen ein. Beispiel:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

Wenn Sie das Identitäts Gerüst ausführen, ohne das `--files` Flag oder das `--useDefaultUI` Flag anzugeben, werden alle verfügbaren Seiten der Identitäts Benutzeroberfläche in Ihrem Projekt erstellt.

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Führen Sie das Identitäts Gerüst aus:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klicken Sie in **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, > **Add** > **Neues Gerüst Element**hinzufügen.
* Wählen Sie im linken Bereich des Dialog Felds **Gerüst hinzufügen** die Option **Identität** > **Hinzufügen**aus.
* Wählen Sie im Dialogfeld **Identität hinzufügen** die gewünschten Optionen aus.
  * Wählen Sie die vorhandene Layoutseite aus, oder die Layoutdatei wird mit einem falschen Markup überschrieben. Wenn eine vorhandene Datei " * \_ Layout. cshtml* " ausgewählt ist, wird Sie **nicht** überschrieben. Beispiel:
    * `~/Pages/Shared/_Layout.cshtml`für Razor Pages
    * `~/Views/Shared/_Layout.cshtml`für MVC-Projekte
* Um den vorhandenen Datenkontext zu verwenden, wählen Sie mindestens eine Datei aus, die Sie außer Kraft setzen möchten. Sie müssen mindestens eine Datei auswählen, um den Datenkontext hinzuzufügen.
  * Wählen Sie Ihre Datenkontext Klasse aus.
  * Wählen Sie **Hinzufügen**.
* So erstellen Sie einen neuen Benutzer Kontext und erstellen möglicherweise eine benutzerdefinierte Benutzerklasse für die Identität:
  * Wählen Sie die **+** Schaltfläche aus, um eine neue **Datenkontext Klasse**zu erstellen. Übernehmen Sie den Standardwert, oder geben Sie eine Klasse an (z `MyApplication.Data.ApplicationDbContext` . b.).
  * Wählen Sie **Hinzufügen**.

Hinweis: Wenn Sie einen neuen Benutzer Kontext erstellen, müssen Sie keine Datei zum Überschreiben auswählen.

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Wenn Sie das ASP.net Core Gerüst nicht bereits installiert haben, installieren Sie es jetzt:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Fügen Sie der Projektdatei (*. csproj*) einen Paket Verweis auf [Microsoft. VisualStudio. Web. CodeGeneration. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) hinzu. Führen Sie im Projektverzeichnis die folgenden Befehle aus:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

Führen Sie den folgenden Befehl aus, um die Einstellungen für das Identitäts Gerüst aufzulisten:

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

Führen Sie im Projektordner das Identitäts Gerüst mit den gewünschten Optionen aus. Führen Sie beispielsweise den folgenden Befehl aus, um die Identität mit der Standardbenutzer Oberfläche und der minimalen Anzahl von Dateien einzurichten. Verwenden Sie den richtigen voll qualifizierten Namen für Ihren Daten Bank Kontext:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

PowerShell verwendet ein Semikolon als Befehls Trennzeichen. Wenn Sie PowerShell verwenden, versehen Sie die Semikolons in der Datei Liste, oder fügen Sie die Datei Liste in doppelte Anführungszeichen ein. Beispiel:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

Wenn Sie das Identitäts Gerüst ausführen, ohne das `--files` Flag oder das `--useDefaultUI` Flag anzugeben, werden alle verfügbaren Seiten der Identitäts Benutzeroberfläche in Ihrem Projekt erstellt.

---

::: moniker-end

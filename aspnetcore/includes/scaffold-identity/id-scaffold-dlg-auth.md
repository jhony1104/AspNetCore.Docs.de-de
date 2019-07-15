Führen Sie die Identity-gerüstbauer:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Von **Projektmappen-Explorer**, mit der rechten Maustaste auf das Projekt > **hinzufügen** > **neues Gerüstelement**.
* Im linken Bereich, der die **Gerüst hinzufügen** wählen Sie im Dialogfeld **Identität** > **hinzufügen**.
* In der **ADD Identity** Dialogfeld Wählen Sie die gewünschten Optionen.
  * Wählen Sie Ihre vorhandene Layoutseite, oder durch falsche Markup die Layoutdatei überschrieben werden. Wenn ein vorhandenes  *\_Layout.cshtml* Datei ausgewählt ist, wird es **nicht** überschrieben.

 Z. B. `~/Pages/Shared/_Layout.cshtml` für Razor-Seiten `~/Views/Shared/_Layout.cshtml` für MVC-Projekte
* Um Ihre vorhandenen Datenkontext zu verwenden, wählen Sie mindestens eine Datei zu überschreiben. Sie müssen mindestens eine Datei auf Ihrem Datenkontext hinzufügen auswählen.
  * Wählen Sie Ihre Daten Context-Klasse.
  * Wählen Sie **hinzufügen**.
* So erstellen einen neuen Benutzerkontext, und möglicherweise eine benutzerdefinierte Klasse erstellen, für die Identität:
  * Wählen Sie die **+** Schaltfläche zum Erstellen eines neuen **Datenkontextklasse**.
  * Wählen Sie **hinzufügen**.

Hinweis: Wenn Sie einen neuen Benutzerkontext erstellen, müssen Sie eine Datei überschreiben möchten.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Wenn Sie die ASP.NET Core-gerüstbauer noch nicht installiert haben, installieren Sie es jetzt:

```console
dotnet tool install -g dotnet-aspnet-codegenerator
```

Fügen Sie einen Paketverweis auf [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) auf das Projekt (\*csproj) Datei. Führen Sie den folgenden Befehl im Verzeichnis Projekts ein:

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

Führen Sie den folgenden Befehl zum Auflisten von Optionen gerüstbauer Identität:

```console
dotnet aspnet-codegenerator identity -h
```

Führen Sie im Projektordner der gerüstbauer Identität, mit der gewünschten Optionen. Z. B. um die Identität mit der standardmäßigen UI und die minimale Anzahl von Dateien einrichten, führen Sie den folgenden Befehl an. Verwenden Sie den richtigen vollqualifizierten Namen für den DB-Kontext:

```console
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files Account.Register
```

PowerShell arbeitet mit Semikolon als Befehlstrennzeichen. Wenn Sie PowerShell verwenden, wird versehen Sie die Semikolon in der Liste mit Escapezeichen, oder fügen Sie die Liste der Dateien in doppelte Anführungszeichen. Beispiel:

```console
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

Wenn das Ausführen der Identity-gerüstbauer ohne Angabe der `--files` Flag oder das `--useDefaultUI` kennzeichnen, die alle die verfügbaren Identity-UI-Seiten in Ihrem Projekt erstellt werden.

---

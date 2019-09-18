Führen Sie das Identitäts Gerüst aus:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Von **Projektmappen-Explorer**, mit der rechten Maustaste auf das Projekt > **hinzufügen** > **neues Gerüstelement**.
* Wählen Sie im linken Bereich des Dialog Felds **Gerüst hinzufügen** die Option **Identität** > **Hinzufügen**aus.
* Wählen Sie im Dialogfeld **Identität hinzufügen** die gewünschten Optionen aus.
  * Wählen Sie die vorhandene Layoutseite aus, oder die Layoutdatei wird mit einem falschen Markup überschrieben. Wenn eine vorhandene  *\_Datei "Layout. cshtml* " ausgewählt ist, wird Sie **nicht** überschrieben.

 Beispiel: `~/Pages/Shared/_Layout.cshtml` für Razor pages `~/Views/Shared/_Layout.cshtml` für MVC-Projekte
* Um den vorhandenen Datenkontext zu verwenden, wählen Sie mindestens eine Datei aus, die Sie außer Kraft setzen möchten. Sie müssen mindestens eine Datei auswählen, um den Datenkontext hinzuzufügen.
  * Wählen Sie Ihre Datenkontext Klasse aus.
  * Wählen Sie **Hinzufügen** aus.
* So erstellen Sie einen neuen Benutzer Kontext und erstellen möglicherweise eine benutzerdefinierte Benutzerklasse für die Identität:
  * Wählen Sie die **+** Schaltfläche zum Erstellen eines neuen **Datenkontextklasse**.
  * Wählen Sie **Hinzufügen** aus.

Hinweis: Wenn Sie einen neuen Benutzer Kontext erstellen, müssen Sie keine Datei zum Überschreiben auswählen.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Wenn Sie die ASP.NET Core-gerüstbauer noch nicht installiert haben, installieren Sie es jetzt:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Fügen Sie der Projektdatei (\*. csproj) einen Paket Verweis auf [Microsoft. VisualStudio. Web. CodeGeneration. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) hinzu. Führen Sie den folgenden Befehl im Verzeichnis Projekts ein:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

Führen Sie den folgenden Befehl zum Auflisten von Optionen gerüstbauer Identität:

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

Führen Sie im Projektordner das Identitäts Gerüst mit den gewünschten Optionen aus. Führen Sie beispielsweise den folgenden Befehl aus, um die Identität mit der Standardbenutzer Oberfläche und der minimalen Anzahl von Dateien einzurichten. Verwenden Sie den richtigen voll qualifizierten Namen für Ihren Daten Bank Kontext:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files Account.Register
```

PowerShell verwendet ein Semikolon als Befehls Trennzeichen. Wenn Sie PowerShell verwenden, versehen Sie die Semikolons in der Datei Liste, oder fügen Sie die Datei Liste in doppelte Anführungszeichen ein. Beispiel:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

Wenn Sie das Identitäts Gerüst ausführen, ohne das `--files` Flag oder das `--useDefaultUI` Flag anzugeben, werden alle verfügbaren Seiten der Identitäts Benutzeroberfläche in Ihrem Projekt erstellt.

---

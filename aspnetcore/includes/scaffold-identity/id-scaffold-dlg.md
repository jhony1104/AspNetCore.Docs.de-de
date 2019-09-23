Führen Sie das Identitäts Gerüst aus:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Von **Projektmappen-Explorer**, mit der rechten Maustaste auf das Projekt > **hinzufügen** > **neues Gerüstelement**.
* Im linken Bereich, der die **Gerüst hinzufügen** wählen Sie im Dialogfeld **Identität** > **hinzufügen**.
* Wählen Sie im Dialogfeld **Identität hinzufügen** die gewünschten Optionen aus.
  * Wählen Sie die vorhandene Layoutseite aus, oder die Layoutdatei wird mit einem falschen Markup überschrieben. Beispiel `~/Pages/Shared/_Layout.cshtml` für Razor pages `~/Views/Shared/_Layout.cshtml` für MVC-Projekte
  * Wählen Sie die **+** Schaltfläche zum Erstellen eines neuen **Datenkontextklasse**.
* Wählen Sie **hinzufügen**.

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

Führen Sie im Projektordner das Identitäts Gerüst mit den gewünschten Optionen aus. Führen Sie beispielsweise den folgenden Befehl aus, um die Identität mit der Standardbenutzer Oberfläche und der minimalen Anzahl von Dateien einzurichten:

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---

<span data-ttu-id="af8fc-101">Führen Sie das Identitäts Gerüst aus:</span><span class="sxs-lookup"><span data-stu-id="af8fc-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="af8fc-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="af8fc-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="af8fc-103">Von **Projektmappen-Explorer**, mit der rechten Maustaste auf das Projekt > **hinzufügen** > **neues Gerüstelement**.</span><span class="sxs-lookup"><span data-stu-id="af8fc-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="af8fc-104">Wählen Sie im linken Bereich des Dialog Felds **Gerüst hinzufügen** die Option **Identität** > **Hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="af8fc-104">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="af8fc-105">Wählen Sie im Dialogfeld **Identität hinzufügen** die gewünschten Optionen aus.</span><span class="sxs-lookup"><span data-stu-id="af8fc-105">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="af8fc-106">Wählen Sie die vorhandene Layoutseite aus, oder die Layoutdatei wird mit einem falschen Markup überschrieben.</span><span class="sxs-lookup"><span data-stu-id="af8fc-106">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="af8fc-107">Wenn eine vorhandene  *\_Datei "Layout. cshtml* " ausgewählt ist, wird Sie **nicht** überschrieben.</span><span class="sxs-lookup"><span data-stu-id="af8fc-107">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span>

 <span data-ttu-id="af8fc-108">Beispiel: `~/Pages/Shared/_Layout.cshtml` für Razor pages `~/Views/Shared/_Layout.cshtml` für MVC-Projekte</span><span class="sxs-lookup"><span data-stu-id="af8fc-108">For example: `~/Pages/Shared/_Layout.cshtml` for Razor Pages `~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
* <span data-ttu-id="af8fc-109">Um den vorhandenen Datenkontext zu verwenden, wählen Sie mindestens eine Datei aus, die Sie außer Kraft setzen möchten.</span><span class="sxs-lookup"><span data-stu-id="af8fc-109">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="af8fc-110">Sie müssen mindestens eine Datei auswählen, um den Datenkontext hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="af8fc-110">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="af8fc-111">Wählen Sie Ihre Datenkontext Klasse aus.</span><span class="sxs-lookup"><span data-stu-id="af8fc-111">Select your data context class.</span></span>
  * <span data-ttu-id="af8fc-112">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="af8fc-112">Select **Add**.</span></span>
* <span data-ttu-id="af8fc-113">So erstellen Sie einen neuen Benutzer Kontext und erstellen möglicherweise eine benutzerdefinierte Benutzerklasse für die Identität:</span><span class="sxs-lookup"><span data-stu-id="af8fc-113">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="af8fc-114">Wählen Sie die **+** Schaltfläche zum Erstellen eines neuen **Datenkontextklasse**.</span><span class="sxs-lookup"><span data-stu-id="af8fc-114">Select the **+** button to create a new **Data context class**.</span></span>
  * <span data-ttu-id="af8fc-115">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="af8fc-115">Select **Add**.</span></span>

<span data-ttu-id="af8fc-116">Hinweis: Wenn Sie einen neuen Benutzer Kontext erstellen, müssen Sie keine Datei zum Überschreiben auswählen.</span><span class="sxs-lookup"><span data-stu-id="af8fc-116">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="af8fc-117">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="af8fc-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="af8fc-118">Wenn Sie die ASP.NET Core-gerüstbauer noch nicht installiert haben, installieren Sie es jetzt:</span><span class="sxs-lookup"><span data-stu-id="af8fc-118">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="af8fc-119">Fügen Sie der Projektdatei (\*. csproj) einen Paket Verweis auf [Microsoft. VisualStudio. Web. CodeGeneration. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) hinzu.</span><span class="sxs-lookup"><span data-stu-id="af8fc-119">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (\*.csproj) file.</span></span> <span data-ttu-id="af8fc-120">Führen Sie den folgenden Befehl im Verzeichnis Projekts ein:</span><span class="sxs-lookup"><span data-stu-id="af8fc-120">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="af8fc-121">Führen Sie den folgenden Befehl zum Auflisten von Optionen gerüstbauer Identität:</span><span class="sxs-lookup"><span data-stu-id="af8fc-121">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="af8fc-122">Führen Sie im Projektordner das Identitäts Gerüst mit den gewünschten Optionen aus.</span><span class="sxs-lookup"><span data-stu-id="af8fc-122">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="af8fc-123">Führen Sie beispielsweise den folgenden Befehl aus, um die Identität mit der Standardbenutzer Oberfläche und der minimalen Anzahl von Dateien einzurichten.</span><span class="sxs-lookup"><span data-stu-id="af8fc-123">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="af8fc-124">Verwenden Sie den richtigen voll qualifizierten Namen für Ihren Daten Bank Kontext:</span><span class="sxs-lookup"><span data-stu-id="af8fc-124">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files Account.Register
```

<span data-ttu-id="af8fc-125">PowerShell verwendet ein Semikolon als Befehls Trennzeichen.</span><span class="sxs-lookup"><span data-stu-id="af8fc-125">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="af8fc-126">Wenn Sie PowerShell verwenden, versehen Sie die Semikolons in der Datei Liste, oder fügen Sie die Datei Liste in doppelte Anführungszeichen ein.</span><span class="sxs-lookup"><span data-stu-id="af8fc-126">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="af8fc-127">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="af8fc-127">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="af8fc-128">Wenn Sie das Identitäts Gerüst ausführen, ohne das `--files` Flag oder das `--useDefaultUI` Flag anzugeben, werden alle verfügbaren Seiten der Identitäts Benutzeroberfläche in Ihrem Projekt erstellt.</span><span class="sxs-lookup"><span data-stu-id="af8fc-128">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---

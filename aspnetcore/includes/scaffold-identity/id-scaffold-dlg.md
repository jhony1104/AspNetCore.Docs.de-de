<span data-ttu-id="d9cb0-101">Führen Sie das Identitäts Gerüst aus:</span><span class="sxs-lookup"><span data-stu-id="d9cb0-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d9cb0-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d9cb0-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d9cb0-103">Klicken Sie in **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, > **Add**  >  **Neues Gerüst Element**hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="d9cb0-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="d9cb0-104">Wählen Sie im linken Bereich des Dialog Felds **Neues Gerüst Element hinzufügen** die Option **Identität**  >  **Hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="d9cb0-104">From the left pane of the **Add New Scaffolded Item** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="d9cb0-105">Wählen Sie im Dialogfeld **Identität hinzufügen** die gewünschten Optionen aus.</span><span class="sxs-lookup"><span data-stu-id="d9cb0-105">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="d9cb0-106">Wählen Sie die vorhandene Layoutseite aus, oder die Layoutdatei wird mit einem falschen Markup überschrieben:</span><span class="sxs-lookup"><span data-stu-id="d9cb0-106">Select your existing layout page, or your layout file will be overwritten with incorrect markup:</span></span>
    * <span data-ttu-id="d9cb0-107">`~/Pages/Shared/_Layout.cshtml`für Razor Pages</span><span class="sxs-lookup"><span data-stu-id="d9cb0-107">`~/Pages/Shared/_Layout.cshtml` for Razor Pages</span></span>
    * <span data-ttu-id="d9cb0-108">`~/Views/Shared/_Layout.cshtml`für MVC-Projekte</span><span class="sxs-lookup"><span data-stu-id="d9cb0-108">`~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
    * <span data-ttu-id="d9cb0-109">Blazor-Server-apps, die aus der blazor-Server Vorlage () erstellt wurden, `blazorserver` sind nicht standardmäßig für Razor Pages oder MVC konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="d9cb0-109">Blazor Server apps created from the Blazor Server template (`blazorserver`) aren't configured for Razor Pages or MVC by default.</span></span> <span data-ttu-id="d9cb0-110">Lassen Sie den Eintrag Layoutseite leer.</span><span class="sxs-lookup"><span data-stu-id="d9cb0-110">Leave the layout page entry blank.</span></span>
  * <span data-ttu-id="d9cb0-111">Wählen Sie die **+** Schaltfläche aus, um eine neue **Datenkontext Klasse**zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="d9cb0-111">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="d9cb0-112">Übernehmen Sie den Standardwert, oder geben Sie eine Klasse an (z `MyApplication.Data.ApplicationDbContext` . b.).</span><span class="sxs-lookup"><span data-stu-id="d9cb0-112">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
* <span data-ttu-id="d9cb0-113">Wählen Sie **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="d9cb0-113">Select **Add**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d9cb0-114">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="d9cb0-114">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="d9cb0-115">Wenn Sie das ASP.net Core Gerüst nicht bereits installiert haben, installieren Sie es jetzt:</span><span class="sxs-lookup"><span data-stu-id="d9cb0-115">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="d9cb0-116">Fügen Sie die erforderlichen nuget-Paket Verweise der Projektdatei (*. csproj*) hinzu.</span><span class="sxs-lookup"><span data-stu-id="d9cb0-116">Add required NuGet package references to the project file (*.csproj*).</span></span> <span data-ttu-id="d9cb0-117">Führen Sie im Projektverzeichnis die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="d9cb0-117">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="d9cb0-118">Führen Sie den folgenden Befehl aus, um die Einstellungen für das Identitäts Gerüst aufzulisten:</span><span class="sxs-lookup"><span data-stu-id="d9cb0-118">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

<span data-ttu-id="d9cb0-119">Führen Sie im Projektordner das Identitäts Gerüst mit den gewünschten Optionen aus.</span><span class="sxs-lookup"><span data-stu-id="d9cb0-119">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="d9cb0-120">Führen Sie beispielsweise den folgenden Befehl aus, um die Identität mit der Standardbenutzer Oberfläche und der minimalen Anzahl von Dateien einzurichten:</span><span class="sxs-lookup"><span data-stu-id="d9cb0-120">For example, to setup identity with the default UI and the minimum number of files, run the following command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---

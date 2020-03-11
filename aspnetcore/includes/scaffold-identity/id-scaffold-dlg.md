<span data-ttu-id="9a965-101">Führen Sie das Identitäts Gerüst aus:</span><span class="sxs-lookup"><span data-stu-id="9a965-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9a965-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9a965-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9a965-103">Klicken Sie in **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, > fügen Sie > **Neues Gerüst Element** **hinzu** .</span><span class="sxs-lookup"><span data-stu-id="9a965-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="9a965-104">Wählen Sie im linken Bereich des Dialog Felds **Gerüst hinzufügen** die Option **Identität** > **Hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="9a965-104">From the left pane of the **Add Scaffold** dialog, select **Identity** > **ADD**.</span></span>
* <span data-ttu-id="9a965-105">Wählen Sie im Dialogfeld **Identität hinzufügen** die gewünschten Optionen aus.</span><span class="sxs-lookup"><span data-stu-id="9a965-105">In the **ADD Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="9a965-106">Wählen Sie die vorhandene Layoutseite aus, oder die Layoutdatei wird mit einem falschen Markup überschrieben.</span><span class="sxs-lookup"><span data-stu-id="9a965-106">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="9a965-107">Beispielsweise `~/Pages/Shared/_Layout.cshtml` für Razor Pages `~/Views/Shared/_Layout.cshtml` für MVC-Projekte.</span><span class="sxs-lookup"><span data-stu-id="9a965-107">For example `~/Pages/Shared/_Layout.cshtml` for Razor Pages `~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
  * <span data-ttu-id="9a965-108">Wählen Sie die Schaltfläche **+** aus, um eine neue **Datenkontext Klasse**zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="9a965-108">Select the **+** button to create a new **Data context class**.</span></span>
* <span data-ttu-id="9a965-109">Wählen Sie **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="9a965-109">Select **ADD**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="9a965-110">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="9a965-110">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="9a965-111">Wenn Sie die ASP.NET Core-gerüstbauer noch nicht installiert haben, installieren Sie es jetzt:</span><span class="sxs-lookup"><span data-stu-id="9a965-111">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="9a965-112">Fügen Sie die erforderlichen nuget-Paket Verweise der Projektdatei (\*. csproj) hinzu.</span><span class="sxs-lookup"><span data-stu-id="9a965-112">Add required NuGet package references to the project (\*.csproj) file.</span></span> <span data-ttu-id="9a965-113">Führen Sie den folgenden Befehl im Verzeichnis Projekts ein:</span><span class="sxs-lookup"><span data-stu-id="9a965-113">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="9a965-114">Führen Sie den folgenden Befehl zum Auflisten von Optionen gerüstbauer Identität:</span><span class="sxs-lookup"><span data-stu-id="9a965-114">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

<span data-ttu-id="9a965-115">Führen Sie im Projektordner das Identitäts Gerüst mit den gewünschten Optionen aus.</span><span class="sxs-lookup"><span data-stu-id="9a965-115">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="9a965-116">Führen Sie beispielsweise den folgenden Befehl aus, um die Identität mit der Standardbenutzer Oberfläche und der minimalen Anzahl von Dateien einzurichten:</span><span class="sxs-lookup"><span data-stu-id="9a965-116">For example, to setup identity with the default UI and the minimum number of files, run the following command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="47d03-101">Führen Sie das Identitäts Gerüst aus:</span><span class="sxs-lookup"><span data-stu-id="47d03-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="47d03-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="47d03-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="47d03-103">Klicken Sie in **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, > **Add** > **Neues Gerüst Element**hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="47d03-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="47d03-104">Wählen Sie im linken Bereich des Dialog Felds **Gerüst hinzufügen** die Option **Identität** > **Hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="47d03-104">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="47d03-105">Wählen Sie im Dialogfeld **Identität hinzufügen** die gewünschten Optionen aus.</span><span class="sxs-lookup"><span data-stu-id="47d03-105">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="47d03-106">Wählen Sie die vorhandene Layoutseite aus, sodass die Layoutdatei nicht mit einem falschen Markup überschrieben wird</span><span class="sxs-lookup"><span data-stu-id="47d03-106">Select your existing layout page so your layout file isn't overwritten with incorrect markup.</span></span> <span data-ttu-id="47d03-107">Wenn eine vorhandene Datei " \* \_ Layout. cshtml\* " ausgewählt ist, wird Sie **nicht** überschrieben.</span><span class="sxs-lookup"><span data-stu-id="47d03-107">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span> <span data-ttu-id="47d03-108">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="47d03-108">For example:</span></span>
    * <span data-ttu-id="47d03-109">`~/Pages/Shared/_Layout.cshtml`für Razor Pages-oder blazor-Server Projekte mit vorhandener Razor Pages-Infrastruktur</span><span class="sxs-lookup"><span data-stu-id="47d03-109">`~/Pages/Shared/_Layout.cshtml` for Razor Pages or Blazor Server projects with existing Razor Pages infrastructure</span></span>
    * <span data-ttu-id="47d03-110">`~/Views/Shared/_Layout.cshtml`für MVC-Projekte oder blazor-Server Projekte mit vorhandener MVC-Infrastruktur</span><span class="sxs-lookup"><span data-stu-id="47d03-110">`~/Views/Shared/_Layout.cshtml` for MVC projects or Blazor Server projects with existing MVC infrastructure</span></span>
* <span data-ttu-id="47d03-111">Um den vorhandenen Datenkontext zu verwenden, wählen Sie mindestens eine Datei aus, die Sie außer Kraft setzen möchten.</span><span class="sxs-lookup"><span data-stu-id="47d03-111">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="47d03-112">Sie müssen mindestens eine Datei auswählen, um den Datenkontext hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="47d03-112">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="47d03-113">Wählen Sie Ihre Datenkontext Klasse aus.</span><span class="sxs-lookup"><span data-stu-id="47d03-113">Select your data context class.</span></span>
  * <span data-ttu-id="47d03-114">Wählen Sie **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="47d03-114">Select **Add**.</span></span>
* <span data-ttu-id="47d03-115">So erstellen Sie einen neuen Benutzer Kontext und erstellen möglicherweise eine benutzerdefinierte Benutzerklasse für die Identität:</span><span class="sxs-lookup"><span data-stu-id="47d03-115">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="47d03-116">Wählen Sie die **+** Schaltfläche aus, um eine neue **Datenkontext Klasse**zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="47d03-116">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="47d03-117">Übernehmen Sie den Standardwert, oder geben Sie eine Klasse an (z `MyApplication.Data.ApplicationDbContext` . b.).</span><span class="sxs-lookup"><span data-stu-id="47d03-117">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
  * <span data-ttu-id="47d03-118">Wählen Sie **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="47d03-118">Select **Add**.</span></span>

<span data-ttu-id="47d03-119">Hinweis: Wenn Sie einen neuen Benutzer Kontext erstellen, müssen Sie keine Datei zum Überschreiben auswählen.</span><span class="sxs-lookup"><span data-stu-id="47d03-119">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="47d03-120">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="47d03-120">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="47d03-121">Wenn Sie das ASP.net Core Gerüst nicht bereits installiert haben, installieren Sie es jetzt:</span><span class="sxs-lookup"><span data-stu-id="47d03-121">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="47d03-122">Fügen Sie die erforderlichen nuget-Paket Verweise der Projektdatei (*. csproj*) hinzu.</span><span class="sxs-lookup"><span data-stu-id="47d03-122">Add required NuGet package references to the project file (*.csproj*).</span></span> <span data-ttu-id="47d03-123">Führen Sie im Projektverzeichnis die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="47d03-123">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="47d03-124">Führen Sie den folgenden Befehl aus, um die Einstellungen für das Identitäts Gerüst aufzulisten:</span><span class="sxs-lookup"><span data-stu-id="47d03-124">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

<span data-ttu-id="47d03-125">Führen Sie im Projektordner das Identitäts Gerüst mit den gewünschten Optionen aus.</span><span class="sxs-lookup"><span data-stu-id="47d03-125">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="47d03-126">Führen Sie beispielsweise den folgenden Befehl aus, um die Identität mit der Standardbenutzer Oberfläche und der minimalen Anzahl von Dateien einzurichten.</span><span class="sxs-lookup"><span data-stu-id="47d03-126">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="47d03-127">Verwenden Sie den richtigen voll qualifizierten Namen für Ihren Daten Bank Kontext:</span><span class="sxs-lookup"><span data-stu-id="47d03-127">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

<span data-ttu-id="47d03-128">PowerShell verwendet ein Semikolon als Befehls Trennzeichen.</span><span class="sxs-lookup"><span data-stu-id="47d03-128">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="47d03-129">Wenn Sie PowerShell verwenden, versehen Sie die Semikolons in der Datei Liste, oder fügen Sie die Datei Liste in doppelte Anführungszeichen ein.</span><span class="sxs-lookup"><span data-stu-id="47d03-129">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="47d03-130">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="47d03-130">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="47d03-131">Wenn Sie das Identitäts Gerüst ausführen, ohne das `--files` Flag oder das `--useDefaultUI` Flag anzugeben, werden alle verfügbaren Seiten der Identitäts Benutzeroberfläche in Ihrem Projekt erstellt.</span><span class="sxs-lookup"><span data-stu-id="47d03-131">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="47d03-132">Führen Sie das Identitäts Gerüst aus:</span><span class="sxs-lookup"><span data-stu-id="47d03-132">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="47d03-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="47d03-133">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="47d03-134">Klicken Sie in **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, > **Add** > **Neues Gerüst Element**hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="47d03-134">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="47d03-135">Wählen Sie im linken Bereich des Dialog Felds **Gerüst hinzufügen** die Option **Identität** > **Hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="47d03-135">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="47d03-136">Wählen Sie im Dialogfeld **Identität hinzufügen** die gewünschten Optionen aus.</span><span class="sxs-lookup"><span data-stu-id="47d03-136">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="47d03-137">Wählen Sie die vorhandene Layoutseite aus, oder die Layoutdatei wird mit einem falschen Markup überschrieben.</span><span class="sxs-lookup"><span data-stu-id="47d03-137">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="47d03-138">Wenn eine vorhandene Datei " \* \_ Layout. cshtml\* " ausgewählt ist, wird Sie **nicht** überschrieben.</span><span class="sxs-lookup"><span data-stu-id="47d03-138">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span> <span data-ttu-id="47d03-139">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="47d03-139">For example:</span></span>
    * <span data-ttu-id="47d03-140">`~/Pages/Shared/_Layout.cshtml`für Razor Pages</span><span class="sxs-lookup"><span data-stu-id="47d03-140">`~/Pages/Shared/_Layout.cshtml` for Razor Pages</span></span>
    * <span data-ttu-id="47d03-141">`~/Views/Shared/_Layout.cshtml`für MVC-Projekte</span><span class="sxs-lookup"><span data-stu-id="47d03-141">`~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
* <span data-ttu-id="47d03-142">Um den vorhandenen Datenkontext zu verwenden, wählen Sie mindestens eine Datei aus, die Sie außer Kraft setzen möchten.</span><span class="sxs-lookup"><span data-stu-id="47d03-142">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="47d03-143">Sie müssen mindestens eine Datei auswählen, um den Datenkontext hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="47d03-143">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="47d03-144">Wählen Sie Ihre Datenkontext Klasse aus.</span><span class="sxs-lookup"><span data-stu-id="47d03-144">Select your data context class.</span></span>
  * <span data-ttu-id="47d03-145">Wählen Sie **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="47d03-145">Select **Add**.</span></span>
* <span data-ttu-id="47d03-146">So erstellen Sie einen neuen Benutzer Kontext und erstellen möglicherweise eine benutzerdefinierte Benutzerklasse für die Identität:</span><span class="sxs-lookup"><span data-stu-id="47d03-146">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="47d03-147">Wählen Sie die **+** Schaltfläche aus, um eine neue **Datenkontext Klasse**zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="47d03-147">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="47d03-148">Übernehmen Sie den Standardwert, oder geben Sie eine Klasse an (z `MyApplication.Data.ApplicationDbContext` . b.).</span><span class="sxs-lookup"><span data-stu-id="47d03-148">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
  * <span data-ttu-id="47d03-149">Wählen Sie **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="47d03-149">Select **Add**.</span></span>

<span data-ttu-id="47d03-150">Hinweis: Wenn Sie einen neuen Benutzer Kontext erstellen, müssen Sie keine Datei zum Überschreiben auswählen.</span><span class="sxs-lookup"><span data-stu-id="47d03-150">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="47d03-151">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="47d03-151">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="47d03-152">Wenn Sie das ASP.net Core Gerüst nicht bereits installiert haben, installieren Sie es jetzt:</span><span class="sxs-lookup"><span data-stu-id="47d03-152">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="47d03-153">Fügen Sie der Projektdatei (*. csproj*) einen Paket Verweis auf [Microsoft. VisualStudio. Web. CodeGeneration. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) hinzu.</span><span class="sxs-lookup"><span data-stu-id="47d03-153">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project file (*.csproj*).</span></span> <span data-ttu-id="47d03-154">Führen Sie im Projektverzeichnis die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="47d03-154">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="47d03-155">Führen Sie den folgenden Befehl aus, um die Einstellungen für das Identitäts Gerüst aufzulisten:</span><span class="sxs-lookup"><span data-stu-id="47d03-155">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="47d03-156">Führen Sie im Projektordner das Identitäts Gerüst mit den gewünschten Optionen aus.</span><span class="sxs-lookup"><span data-stu-id="47d03-156">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="47d03-157">Führen Sie beispielsweise den folgenden Befehl aus, um die Identität mit der Standardbenutzer Oberfläche und der minimalen Anzahl von Dateien einzurichten.</span><span class="sxs-lookup"><span data-stu-id="47d03-157">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="47d03-158">Verwenden Sie den richtigen voll qualifizierten Namen für Ihren Daten Bank Kontext:</span><span class="sxs-lookup"><span data-stu-id="47d03-158">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

<span data-ttu-id="47d03-159">PowerShell verwendet ein Semikolon als Befehls Trennzeichen.</span><span class="sxs-lookup"><span data-stu-id="47d03-159">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="47d03-160">Wenn Sie PowerShell verwenden, versehen Sie die Semikolons in der Datei Liste, oder fügen Sie die Datei Liste in doppelte Anführungszeichen ein.</span><span class="sxs-lookup"><span data-stu-id="47d03-160">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="47d03-161">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="47d03-161">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="47d03-162">Wenn Sie das Identitäts Gerüst ausführen, ohne das `--files` Flag oder das `--useDefaultUI` Flag anzugeben, werden alle verfügbaren Seiten der Identitäts Benutzeroberfläche in Ihrem Projekt erstellt.</span><span class="sxs-lookup"><span data-stu-id="47d03-162">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---

::: moniker-end

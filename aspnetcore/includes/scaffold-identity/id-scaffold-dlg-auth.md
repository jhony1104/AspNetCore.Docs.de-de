---
ms.openlocfilehash: 53774177030adf8a61606a696af85cd1f57d6ab9
ms.sourcegitcommit: 088e6744cd67a62f214f25146313a53949b17d35
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58320291"
---
<span data-ttu-id="9f23f-101">Führen Sie die Identity-gerüstbauer:</span><span class="sxs-lookup"><span data-stu-id="9f23f-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9f23f-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9f23f-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9f23f-103">Von **Projektmappen-Explorer**, mit der rechten Maustaste auf das Projekt > **hinzufügen** > **neues Gerüstelement**.</span><span class="sxs-lookup"><span data-stu-id="9f23f-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="9f23f-104">Im linken Bereich, der die **Gerüst hinzufügen** wählen Sie im Dialogfeld **Identität** > **hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="9f23f-104">From the left pane of the **Add Scaffold** dialog, select **Identity** > **ADD**.</span></span>
* <span data-ttu-id="9f23f-105">In der **ADD Identity** Dialogfeld Wählen Sie die gewünschten Optionen.</span><span class="sxs-lookup"><span data-stu-id="9f23f-105">In the **ADD Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="9f23f-106">Wählen Sie Ihre vorhandene Layoutseite, oder durch falsche Markup die Layoutdatei überschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="9f23f-106">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="9f23f-107">Wenn ein vorhandenes  *\_Layout.cshtml* Datei ausgewählt ist, wird es **nicht** überschrieben.</span><span class="sxs-lookup"><span data-stu-id="9f23f-107">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span>

 <span data-ttu-id="9f23f-108">Z. B. `~/Pages/Shared/_Layout.cshtml` für Razor-Seiten `~/Views/Shared/_Layout.cshtml` für MVC-Projekte</span><span class="sxs-lookup"><span data-stu-id="9f23f-108">For example `~/Pages/Shared/_Layout.cshtml` for Razor Pages `~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
* <span data-ttu-id="9f23f-109">Um Ihre vorhandenen Datenkontext zu verwenden, wählen Sie mindestens eine Datei zu überschreiben.</span><span class="sxs-lookup"><span data-stu-id="9f23f-109">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="9f23f-110">Sie müssen mindestens eine Datei auf Ihrem Datenkontext hinzufügen auswählen.</span><span class="sxs-lookup"><span data-stu-id="9f23f-110">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="9f23f-111">Wählen Sie Ihre Daten Context-Klasse.</span><span class="sxs-lookup"><span data-stu-id="9f23f-111">Select your data context class.</span></span>
  * <span data-ttu-id="9f23f-112">Wählen Sie **hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="9f23f-112">Select **ADD**.</span></span>
* <span data-ttu-id="9f23f-113">So erstellen einen neuen Benutzerkontext, und möglicherweise eine benutzerdefinierte Klasse erstellen, für die Identität:</span><span class="sxs-lookup"><span data-stu-id="9f23f-113">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="9f23f-114">Wählen Sie die **+** Schaltfläche zum Erstellen eines neuen **Datenkontextklasse**.</span><span class="sxs-lookup"><span data-stu-id="9f23f-114">Select the **+** button to create a new **Data context class**.</span></span>
  * <span data-ttu-id="9f23f-115">Wählen Sie **hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="9f23f-115">Select **ADD**.</span></span>

<span data-ttu-id="9f23f-116">Hinweis: Wenn Sie einen neuen Benutzerkontext erstellen, müssen Sie eine Datei überschreiben möchten.</span><span class="sxs-lookup"><span data-stu-id="9f23f-116">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="9f23f-117">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="9f23f-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="9f23f-118">Wenn Sie die ASP.NET Core-gerüstbauer noch nicht installiert haben, installieren Sie es jetzt:</span><span class="sxs-lookup"><span data-stu-id="9f23f-118">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```console
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="9f23f-119">Fügen Sie einen Paketverweis auf [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) auf das Projekt (\*csproj) Datei.</span><span class="sxs-lookup"><span data-stu-id="9f23f-119">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (\*.csproj) file.</span></span> <span data-ttu-id="9f23f-120">Führen Sie den folgenden Befehl im Verzeichnis Projekts ein:</span><span class="sxs-lookup"><span data-stu-id="9f23f-120">Run the following command in the project directory:</span></span>

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="9f23f-121">Führen Sie den folgenden Befehl zum Auflisten von Optionen gerüstbauer Identität:</span><span class="sxs-lookup"><span data-stu-id="9f23f-121">Run the following command to list the Identity scaffolder options:</span></span>

```console
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="9f23f-122">Führen Sie im Projektordner der gerüstbauer Identität, mit der gewünschten Optionen.</span><span class="sxs-lookup"><span data-stu-id="9f23f-122">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="9f23f-123">Z. B. um die Identität mit der standardmäßigen UI und die minimale Anzahl von Dateien einrichten, führen Sie den folgenden Befehl an.</span><span class="sxs-lookup"><span data-stu-id="9f23f-123">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="9f23f-124">Verwenden Sie den richtigen vollqualifizierten Namen für den DB-Kontext:</span><span class="sxs-lookup"><span data-stu-id="9f23f-124">Use the correct fully qualified name for your DB context:</span></span>

```console
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files Account.Register
```

<span data-ttu-id="9f23f-125">PowerShell arbeitet mit Semikolon als Befehlstrennzeichen.</span><span class="sxs-lookup"><span data-stu-id="9f23f-125">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="9f23f-126">Wenn Sie PowerShell verwenden, wird versehen Sie die Semikolon in der Liste mit Escapezeichen, oder fügen Sie die Liste der Dateien in doppelte Anführungszeichen.</span><span class="sxs-lookup"><span data-stu-id="9f23f-126">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="9f23f-127">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9f23f-127">For example:</span></span>

```console
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="9f23f-128">Wenn das Ausführen der Identity-gerüstbauer ohne Angabe der `--files` Flag oder das `--useDefaultUI` kennzeichnen, die alle die verfügbaren Identity-UI-Seiten in Ihrem Projekt erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="9f23f-128">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---

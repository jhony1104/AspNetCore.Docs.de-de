---
title: Wiederverwendbare Razor-Benutzeroberfläche in Klassenbibliotheken mit ASP.NET Core
author: Rick-Anderson
description: Es wird erläutert, wie wiederverwendbare Teilansichten in einer Klassenbibliothek in ASP.NET Core mit Razor-Benutzeroberfläche zu erstellen.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 08/22/2019
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: 5b83cb44302a5900ec7b2ccc049790b4c1ca57e5
ms.sourcegitcommit: 6189b0ced9c115248c6ede02efcd0b29d31f2115
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2019
ms.locfileid: "69985381"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a><span data-ttu-id="741e4-103">Erstellen wiederverwendbarer Benutzeroberflächen mithilfe des Razor-Klassen Bibliotheks Projekts in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="741e4-103">Create reusable UI using the Razor class library project in ASP.NET Core</span></span>

<span data-ttu-id="741e4-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="741e4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="741e4-105">Razor-Ansichten, Seiten, Controller, Seiten Modelle, [Razor-Komponenten](xref:blazor/class-libraries), Ansichts [Komponenten](xref:mvc/views/view-components)und Datenmodelle können in eine Razor-Klassenbibliothek (RCL) integriert werden.</span><span class="sxs-lookup"><span data-stu-id="741e4-105">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="741e4-106">Die RCL kann verpackt und wiederverwendet werden.</span><span class="sxs-lookup"><span data-stu-id="741e4-106">The RCL can be packaged and reused.</span></span> <span data-ttu-id="741e4-107">Sie kann in Anwendungen eingebunden werden, wodurch sich die in der RCL enthaltenen Ansichten und Seiten überschreiben lassen.</span><span class="sxs-lookup"><span data-stu-id="741e4-107">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="741e4-108">Wenn eine Ansicht, Teilansicht oder Razor-Seite sowohl in der Web-App als auch in der RCL enthalten ist, hat das Razor-Markup (die *CSHTML*-Datei) in der Web-App Vorrang.</span><span class="sxs-lookup"><span data-stu-id="741e4-108">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="741e4-109">Dieses Feature erfordert [!INCLUDE[](~/includes/2.1-SDK.md)]</span><span class="sxs-lookup"><span data-stu-id="741e4-109">This feature requires [!INCLUDE[](~/includes/2.1-SDK.md)]</span></span>

<span data-ttu-id="741e4-110">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="741e4-110">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="741e4-111">Erstellen einer Klassenbibliothek, die eine Razor-Benutzeroberfläche enthält</span><span class="sxs-lookup"><span data-stu-id="741e4-111">Create a class library containing Razor UI</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="741e4-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="741e4-112">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="741e4-113">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="741e4-113">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="741e4-114">Wählen Sie **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="741e4-114">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="741e4-115">Geben Sie der Bibliothek einen Namen (z.B. „RazorClassLib“), und klicken Sie anschließend auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="741e4-115">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="741e4-116">Stellen Sie sicher, dass der Bibliotheksname nicht auf `.Views` endet, um zu verhindern, dass es zu einem Dateinamenkonflikt mit der generierten Ansichtsbibliothek kommt.</span><span class="sxs-lookup"><span data-stu-id="741e4-116">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="741e4-117">Überprüfen Sie, ob **ASP.NET Core 2.1** oder höher ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="741e4-117">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="741e4-118">Klicken Sie auf **Razor Class Library** (Razor-Klassenbibliothek) > **OK**.</span><span class="sxs-lookup"><span data-stu-id="741e4-118">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="741e4-119">Eine RCL hat die folgende Projektdatei:</span><span class="sxs-lookup"><span data-stu-id="741e4-119">An RCL has the following project file:</span></span>

[!code-xml[Main](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="741e4-120">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="741e4-120">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="741e4-121">Führen Sie `dotnet new razorclasslib` über die Befehlszeile aus.</span><span class="sxs-lookup"><span data-stu-id="741e4-121">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="741e4-122">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="741e4-122">For example:</span></span>

```console
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="741e4-123">Weitere Informationen finden Sie unter [dotnet new](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="741e4-123">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="741e4-124">Stellen Sie sicher, dass der Bibliotheksname nicht auf `.Views` endet, um zu verhindern, dass es zu einem Dateinamenkonflikt mit der generierten Ansichtsbibliothek kommt.</span><span class="sxs-lookup"><span data-stu-id="741e4-124">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="741e4-125">Fügen Sie der RCL Razor-Dateien hinzu.</span><span class="sxs-lookup"><span data-stu-id="741e4-125">Add Razor files to the RCL.</span></span>

<span data-ttu-id="741e4-126">ASP.NET Core-Vorlagen wird davon ausgegangen, der RCL Inhalt befindet sich in der *Bereiche* Ordner.</span><span class="sxs-lookup"><span data-stu-id="741e4-126">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="741e4-127">Weitere Informationen finden Sie unter [RCL Pages Layout](#afs) zum Erstellen einer RCL `~/Pages` , die `~/Areas/Pages`Inhalte in anstelle von verfügbar macht.</span><span class="sxs-lookup"><span data-stu-id="741e4-127">See [RCL Pages layout](#afs) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="referencing-rcl-content"></a><span data-ttu-id="741e4-128">Verweisen auf den RCL-Inhalt</span><span class="sxs-lookup"><span data-stu-id="741e4-128">Referencing RCL content</span></span>

<span data-ttu-id="741e4-129">Folgende Komponenten können auf die RCL verweisen:</span><span class="sxs-lookup"><span data-stu-id="741e4-129">The RCL can be referenced by:</span></span>

* <span data-ttu-id="741e4-130">NuGet-Pakete.</span><span class="sxs-lookup"><span data-stu-id="741e4-130">NuGet package.</span></span> <span data-ttu-id="741e4-131">Weitere Informationen finden Sie unter [Creating NuGet packages](/nuget/create-packages/creating-a-package) (Erstellen von NuGet-Paketen), [dotnet add package](/dotnet/core/tools/dotnet-add-package) und [Erstellen und Veröffentlichen eines NuGet-Pakets](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="741e4-131">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="741e4-132">*{ProjectName}.csproj*-Dateien.</span><span class="sxs-lookup"><span data-stu-id="741e4-132">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="741e4-133">Weitere Informationen finden Sie unter [dotnet add reference](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="741e4-133">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a><span data-ttu-id="741e4-134">Exemplarische Vorgehensweise: Erstellen eines RCL-Projekts und verwenden aus einem Razor Pages Projekt</span><span class="sxs-lookup"><span data-stu-id="741e4-134">Walkthrough: Create an RCL project and use from a Razor Pages project</span></span>

<span data-ttu-id="741e4-135">Sie können das [vollständige Projekt](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) herunterladen und testen, anstatt es zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="741e4-135">You can download the [complete project](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) and test it rather than creating it.</span></span> <span data-ttu-id="741e4-136">Der Beispieldownload enthält zusätzlichen Code sowie Links, die das Testen des Projekts erleichtern.</span><span class="sxs-lookup"><span data-stu-id="741e4-136">The sample download contains additional code and links that make the project easy to test.</span></span> <span data-ttu-id="741e4-137">[In diesem GitHub-Issue](https://github.com/aspnet/AspNetCore.Docs/issues/6098) können Sie in Form von Kommentaren Feedback zu Unterschieden zwischen Beispieldownloads und ausführlichen Anleitungen geben.</span><span class="sxs-lookup"><span data-stu-id="741e4-137">You can leave feedback in [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/6098) with your comments on download samples versus step-by-step instructions.</span></span>

### <a name="test-the-download-app"></a><span data-ttu-id="741e4-138">Testen der heruntergeladenen App</span><span class="sxs-lookup"><span data-stu-id="741e4-138">Test the download app</span></span>

<span data-ttu-id="741e4-139">Wenn Sie die vollständige App nicht heruntergeladen haben und das Beispielprojekt selbst erstellen möchten, können Sie mit dem [nächsten Abschnitt](#create-an-rcl) fortfahren.</span><span class="sxs-lookup"><span data-stu-id="741e4-139">If you haven't downloaded the completed app and would rather create the walkthrough project, skip to the [next section](#create-an-rcl).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="741e4-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="741e4-140">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="741e4-141">Öffnen Sie die *SLN*-Datei in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="741e4-141">Open the *.sln* file in Visual Studio.</span></span> <span data-ttu-id="741e4-142">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="741e4-142">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="741e4-143">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="741e4-143">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="741e4-144">Erstellen Sie über die Eingabeaufforderung im *cli*-Verzeichnis die RCL und die Web-App.</span><span class="sxs-lookup"><span data-stu-id="741e4-144">From a command prompt in the *cli* directory, build the RCL and web app.</span></span>

```console
dotnet build
```

<span data-ttu-id="741e4-145">Wechseln Sie ins Verzeichnis *WebApp1*, und führen Sie die App aus:</span><span class="sxs-lookup"><span data-stu-id="741e4-145">Move to the *WebApp1* directory and run the app:</span></span>

```console
dotnet run
```

---

<span data-ttu-id="741e4-146">Folgen Sie den Anweisungen in [Testen des WebApp1-Projekts](#test).</span><span class="sxs-lookup"><span data-stu-id="741e4-146">Follow the instructions in [Test WebApp1](#test)</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="741e4-147">Erstellen einer RCL</span><span class="sxs-lookup"><span data-stu-id="741e4-147">Create an RCL</span></span>

<span data-ttu-id="741e4-148">In diesem Abschnitt wird eine RCL erstellt.</span><span class="sxs-lookup"><span data-stu-id="741e4-148">In this section, an RCL is created.</span></span> <span data-ttu-id="741e4-149">Dabei werden der RCL Razor-Dateien hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="741e4-149">Razor files are added to the RCL.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="741e4-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="741e4-150">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="741e4-151">Erstellen Sie das RCL-Projekt:</span><span class="sxs-lookup"><span data-stu-id="741e4-151">Create the RCL project:</span></span>

* <span data-ttu-id="741e4-152">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="741e4-152">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="741e4-153">Wählen Sie **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="741e4-153">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="741e4-154">Benennen Sie die app **RazorUIClassLib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="741e4-154">Name the app **RazorUIClassLib** > **OK**.</span></span>
* <span data-ttu-id="741e4-155">Überprüfen Sie, ob **ASP.NET Core 2.1** oder höher ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="741e4-155">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="741e4-156">Klicken Sie auf **Razor Class Library** (Razor-Klassenbibliothek) > **OK**.</span><span class="sxs-lookup"><span data-stu-id="741e4-156">Select **Razor Class Library** > **OK**.</span></span>
* <span data-ttu-id="741e4-157">Fügen Sie eine Razor-Datei für die Teilansicht mit dem Namen „_Message.cshtml“ unter *RazorUIClassLib/Areas/MyFeature/Pages/Shared* hinzu.</span><span class="sxs-lookup"><span data-stu-id="741e4-157">Add a Razor partial view file named *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="741e4-158">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="741e4-158">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="741e4-159">Führen Sie die folgenden Befehle über die Befehlszeile aus:</span><span class="sxs-lookup"><span data-stu-id="741e4-159">From the command line, run the following:</span></span>

```console
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="741e4-160">Die obenstehenden Befehle haben folgende Konsequenzen:</span><span class="sxs-lookup"><span data-stu-id="741e4-160">The preceding commands:</span></span>

* <span data-ttu-id="741e4-161">Erstellt die `RazorUIClassLib` RCL.</span><span class="sxs-lookup"><span data-stu-id="741e4-161">Creates the `RazorUIClassLib` RCL.</span></span>
* <span data-ttu-id="741e4-162">Die Seite „Razor_Message“ wird erstellt und der RCL hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="741e4-162">Creates a Razor _Message page, and adds it to the RCL.</span></span> <span data-ttu-id="741e4-163">Durch den `-np`-Parameter wird die Seite ohne `PageModel` erstellt.</span><span class="sxs-lookup"><span data-stu-id="741e4-163">The `-np` parameter creates the page without a `PageModel`.</span></span>
* <span data-ttu-id="741e4-164">Erstellt eine [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) Datei und fügt es der RCL hinzu.</span><span class="sxs-lookup"><span data-stu-id="741e4-164">Creates a [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) file and adds it to the RCL.</span></span>

<span data-ttu-id="741e4-165">Die *_ViewStart.cshtml* Datei ist erforderlich, um das Layout des Projekts mit Razor Pages verwenden (die im nächsten Abschnitt hinzugefügt wird).</span><span class="sxs-lookup"><span data-stu-id="741e4-165">The *_ViewStart.cshtml* file is required to use the layout of the Razor Pages project (which is added in the next section).</span></span>

---

### <a name="add-razor-files-and-folders-to-the-project"></a><span data-ttu-id="741e4-166">Razor-Dateien und Ordner zum Projekt hinzufügen</span><span class="sxs-lookup"><span data-stu-id="741e4-166">Add Razor files and folders to the project</span></span>

* <span data-ttu-id="741e4-167">Ersetzen Sie das Markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="741e4-167">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

[!code-cshtml[Main](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="741e4-168">Ersetzen Sie das Markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="741e4-168">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* with the following code:</span></span>

[!code-cshtml[Main](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

<span data-ttu-id="741e4-169">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` ist erforderlich, um die Teilansicht `<partial name="_Message" />` verwenden zu können.</span><span class="sxs-lookup"><span data-stu-id="741e4-169">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` is required to use the partial view (`<partial name="_Message" />`).</span></span> <span data-ttu-id="741e4-170">Wenn Sie nicht die `@addTagHelper`-Anweisung einbinden möchten, können Sie die Datei *_ViewImports.cshtml* hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="741e4-170">Rather than including the `@addTagHelper` directive, you can add a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="741e4-171">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="741e4-171">For example:</span></span>

```console
dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="741e4-172">Weitere Informationen zu *_ViewImports.cshtml*, finden Sie unter [freigegebenen Richtlinien importieren](xref:mvc/views/layout#importing-shared-directives)</span><span class="sxs-lookup"><span data-stu-id="741e4-172">For more information on *_ViewImports.cshtml*, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives)</span></span>

* <span data-ttu-id="741e4-173">Erstellen Sie die Klassenbibliothek, um sicherzustellen, dass keine Compilerfehler vorliegen:</span><span class="sxs-lookup"><span data-stu-id="741e4-173">Build the class library to verify there are no compiler errors:</span></span>

```console
dotnet build RazorUIClassLib
```

<span data-ttu-id="741e4-174">Die Buildausgabe enthält *RazorUIClassLib.dll* und *RazorUIClassLib.Views.dll*.</span><span class="sxs-lookup"><span data-stu-id="741e4-174">The build output contains *RazorUIClassLib.dll* and *RazorUIClassLib.Views.dll*.</span></span> <span data-ttu-id="741e4-175">*RazorUIClassLib.Views.dll* enthält den kompilierten Razor-Inhalt.</span><span class="sxs-lookup"><span data-stu-id="741e4-175">*RazorUIClassLib.Views.dll* contains the compiled Razor content.</span></span>

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a><span data-ttu-id="741e4-176">Verwenden der Razor-Benutzeroberflächenbibliothek über ein Razor-Seiten-Projekt</span><span class="sxs-lookup"><span data-stu-id="741e4-176">Use the Razor UI library from a Razor Pages project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="741e4-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="741e4-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="741e4-178">Erstellen Sie die Web-App mit Razor-Seiten:</span><span class="sxs-lookup"><span data-stu-id="741e4-178">Create the Razor Pages web app:</span></span>

* <span data-ttu-id="741e4-179">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf die Projektmappe und anschließend auf **Hinzufügen** >  **Neues Projekt**.</span><span class="sxs-lookup"><span data-stu-id="741e4-179">From **Solution Explorer**, right-click the solution > **Add** >  **New Project**.</span></span>
* <span data-ttu-id="741e4-180">Wählen Sie **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="741e4-180">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="741e4-181">Legen Sie als Namen für die App **WebApp1** fest.</span><span class="sxs-lookup"><span data-stu-id="741e4-181">Name the app **WebApp1**.</span></span>
* <span data-ttu-id="741e4-182">Überprüfen Sie, ob **ASP.NET Core 2.1** oder höher ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="741e4-182">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="741e4-183">Klicken Sie auf **Webanwendung** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="741e4-183">Select **Web Application** > **OK**.</span></span>

* <span data-ttu-id="741e4-184">Klicken Sie im **Projektmappen-Explorer** zuerst mit der rechten Maustaste auf **WebApp1** und anschließend auf **Als Startprojekt festlegen**.</span><span class="sxs-lookup"><span data-stu-id="741e4-184">From **Solution Explorer**, right-click on **WebApp1** and select **Set as StartUp Project**.</span></span>
* <span data-ttu-id="741e4-185">Klicken Sie im **Projektmappen-Explorer** zuerst mit der rechten Maustaste auf **WebApp1** und anschließend auf **Buildabhängigkeiten** > **Projektabhängigkeiten**.</span><span class="sxs-lookup"><span data-stu-id="741e4-185">From **Solution Explorer**, right-click on **WebApp1** and select **Build Dependencies** > **Project Dependencies**.</span></span>
* <span data-ttu-id="741e4-186">Aktivieren Sie für **WebApp1** die Abhängigkeit **RazorUIClassLib**.</span><span class="sxs-lookup"><span data-stu-id="741e4-186">Check **RazorUIClassLib** as a dependency of **WebApp1**.</span></span>
* <span data-ttu-id="741e4-187">Klicken Sie im **Projektmappen-Explorer** zuerst mit der rechten Maustaste auf **WebApp1** und anschließend auf **Hinzufügen** > **Verweis**.</span><span class="sxs-lookup"><span data-stu-id="741e4-187">From **Solution Explorer**, right-click on **WebApp1** and select **Add** > **Reference**.</span></span>
* <span data-ttu-id="741e4-188">Aktivieren Sie im Dialogfeld **Verweis-Manager** das Kontrollkästchen neben **RazorUIClassLib**, und klicken Sie anschließend auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="741e4-188">In the **Reference Manager** dialog, check **RazorUIClassLib** > **OK**.</span></span>

<span data-ttu-id="741e4-189">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="741e4-189">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="741e4-190">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="741e4-190">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="741e4-191">Erstellen Sie eine Razor Pages Web-App und eine Projektmappendatei, die die Razor Pages-APP und die RCL enthält:</span><span class="sxs-lookup"><span data-stu-id="741e4-191">Create a Razor Pages web app and a solution file containing the Razor Pages app and the RCL:</span></span>

```console
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

<span data-ttu-id="741e4-192">Erstellen Sie die Web-App, und führen Sie sie aus:</span><span class="sxs-lookup"><span data-stu-id="741e4-192">Build and run the web app:</span></span>

```console
cd WebApp1
dotnet run
```

---

<a name="test"></a>

### <a name="test-webapp1"></a><span data-ttu-id="741e4-193">Testen des WebApp1-Projekts</span><span class="sxs-lookup"><span data-stu-id="741e4-193">Test WebApp1</span></span>

<span data-ttu-id="741e4-194">Vergewissern Sie sich, dass die Razor-UI-Klassenbibliothek verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="741e4-194">Verify the Razor UI class library is in use:</span></span>

* <span data-ttu-id="741e4-195">Wechseln Sie zu `/MyFeature/Page1`.</span><span class="sxs-lookup"><span data-stu-id="741e4-195">Browse to `/MyFeature/Page1`.</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="741e4-196">Überschreiben von Ansichten, Teilansichten und Seiten</span><span class="sxs-lookup"><span data-stu-id="741e4-196">Override views, partial views, and pages</span></span>

<span data-ttu-id="741e4-197">Wenn eine Ansicht, Teilansicht oder Razor-Seite sowohl in der Web-App als auch in der RCL enthalten ist, hat das Razor-Markup (die *CSHTML*-Datei) in der Web-App Vorrang.</span><span class="sxs-lookup"><span data-stu-id="741e4-197">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="741e4-198">Fügen Sie z. b. *"WebApp1"/Areas/myfeature/Pages/"Page1. cshtml* zu" WebApp1 "hinzu, und" Page1 im "WebApp1" hat Vorrang vor "Page1 in der RCL.</span><span class="sxs-lookup"><span data-stu-id="741e4-198">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="741e4-199">Ob Page1 in WebApp1 tatsächlich die Priorität zugeordnet wird, können Sie testen, indem Sie im Beispieldownload *WebApp1/Areas/MyFeature2* in *WebApp1/Areas/MyFeature* umbenennen.</span><span class="sxs-lookup"><span data-stu-id="741e4-199">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="741e4-200">Kopieren Sie die Teilansicht *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* nach *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="741e4-200">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="741e4-201">Aktualisieren Sie das Markup, um den neuen Speicherort anzugeben.</span><span class="sxs-lookup"><span data-stu-id="741e4-201">Update the markup to indicate the new location.</span></span> <span data-ttu-id="741e4-202">Erstellen Sie die App, und führen Sie sie aus, um sicherzustellen, dass die Version mit der Teilansicht der App verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="741e4-202">Build and run the app to verify the app's version of the partial is being used.</span></span>

<a name="afs"></a>

### <a name="rcl-pages-layout"></a><span data-ttu-id="741e4-203">RCL Seitenlayout</span><span class="sxs-lookup"><span data-stu-id="741e4-203">RCL Pages layout</span></span>

<span data-ttu-id="741e4-204">Auf Inhalt RCL, als wäre es Teil der Web-app-Verweis *Seiten* Ordner das RCL-Projekt erstellen, mit der folgenden Dateistruktur:</span><span class="sxs-lookup"><span data-stu-id="741e4-204">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="741e4-205">*RazorUIClassLib/Seiten*</span><span class="sxs-lookup"><span data-stu-id="741e4-205">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="741e4-206">*RazorUIClassLib/Pages/Shared*</span><span class="sxs-lookup"><span data-stu-id="741e4-206">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="741e4-207">Nehmen wir an *RazorUIClassLib/Pages/Shared* enthält zwei partielle Dateien: *_Header.cshtml* und *_Footer.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="741e4-207">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="741e4-208">Die `<partial>` können Tags hinzugefügt werden *"_Layout.cshtml"* Datei:</span><span class="sxs-lookup"><span data-stu-id="741e4-208">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker range=">= aspnetcore-3.0"

## <a name="create-an-rcl-with-static-assets"></a><span data-ttu-id="741e4-209">Erstellen einer RCL mit statischen Assets</span><span class="sxs-lookup"><span data-stu-id="741e4-209">Create an RCL with static assets</span></span>

<span data-ttu-id="741e4-210">Eine RCL erfordert möglicherweise begleitende statische Ressourcen, auf die von der Verb raubenden App der RCL verwiesen werden kann.</span><span class="sxs-lookup"><span data-stu-id="741e4-210">An RCL may require companion static assets that can be referenced by the consuming app of the RCL.</span></span> <span data-ttu-id="741e4-211">ASP.net Core ermöglicht das Erstellen von rcls, die statische Ressourcen enthalten, die für eine verarbeitende app verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="741e4-211">ASP.NET Core allows creating RCLs that include static assets that are available to a consuming app.</span></span>

<span data-ttu-id="741e4-212">Wenn Sie begleitende Assets als Teil einer RCL einschließen möchten, erstellen Sie in der Klassenbibliothek einen Ordner *wwwroot* , und fügen Sie alle erforderlichen Dateien in diesen Ordner ein.</span><span class="sxs-lookup"><span data-stu-id="741e4-212">To include companion assets as part of an RCL, create a *wwwroot* folder in the class library and include any required files in that folder.</span></span>

<span data-ttu-id="741e4-213">Beim Packen einer RCL werden alle Begleit Objekte im Ordner " *wwwroot* " automatisch in das Paket eingefügt.</span><span class="sxs-lookup"><span data-stu-id="741e4-213">When packing an RCL, all companion assets in the *wwwroot* folder are automatically included in the package.</span></span>

### <a name="exclude-static-assets"></a><span data-ttu-id="741e4-214">Statische Assets ausschließen</span><span class="sxs-lookup"><span data-stu-id="741e4-214">Exclude static assets</span></span>

<span data-ttu-id="741e4-215">Fügen Sie der `$(DefaultItemExcludes)` Eigenschaften Gruppe in der Projektdatei den gewünschten Ausschluss Pfad hinzu, um statische Assets auszuschließen.</span><span class="sxs-lookup"><span data-stu-id="741e4-215">To exclude static assets, add the desired exclusion path to the `$(DefaultItemExcludes)` property group in the project file.</span></span> <span data-ttu-id="741e4-216">Trennen Sie Einträge durch ein Semikolon (`;`).</span><span class="sxs-lookup"><span data-stu-id="741e4-216">Separate entries with a semicolon (`;`).</span></span>

<span data-ttu-id="741e4-217">Im folgenden Beispiel wird das *lib. CSS* -Stylesheet im Ordner " *wwwroot* " nicht als statisches Asset angesehen und ist nicht in der veröffentlichten RCL enthalten:</span><span class="sxs-lookup"><span data-stu-id="741e4-217">In the following example, the *lib.css* stylesheet in the *wwwroot* folder isn't considered a static asset and isn't included in the published RCL:</span></span>

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a><span data-ttu-id="741e4-218">Typescript-Integration</span><span class="sxs-lookup"><span data-stu-id="741e4-218">Typescript integration</span></span>

<span data-ttu-id="741e4-219">So fügen Sie typescript-Dateien in eine RCL ein:</span><span class="sxs-lookup"><span data-stu-id="741e4-219">To include TypeScript files in an RCL:</span></span>

1. <span data-ttu-id="741e4-220">Platzieren Sie die typescript-Dateien (*TS*) außerhalb des Ordners *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="741e4-220">Place the TypeScript files (*.ts*) outside of the *wwwroot* folder.</span></span> <span data-ttu-id="741e4-221">Platzieren Sie die Dateien z. b. in einem *Client* Ordner.</span><span class="sxs-lookup"><span data-stu-id="741e4-221">For example, place the files in a *Client* folder.</span></span>

1. <span data-ttu-id="741e4-222">Konfigurieren Sie die typescript-Buildausgabe für den Ordner " *wwwroot* ".</span><span class="sxs-lookup"><span data-stu-id="741e4-222">Configure the TypeScript build output for the *wwwroot* folder.</span></span> <span data-ttu-id="741e4-223">Legen Sie `TypescriptOutDir` die-Eigenschaft innerhalb `PropertyGroup` einer in der Projektdatei fest:</span><span class="sxs-lookup"><span data-stu-id="741e4-223">Set the `TypescriptOutDir` property inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. <span data-ttu-id="741e4-224">Fügen Sie das typescript-Ziel als Abhängigkeit des `ResolveCurrentProjectStaticWebAssets` Ziels ein, indem Sie das folgende Ziel in `PropertyGroup` einer in der Projektdatei hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="741e4-224">Include the TypeScript target as a dependency of the `ResolveCurrentProjectStaticWebAssets` target by adding the following target inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     TypeScriptCompile;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a><span data-ttu-id="741e4-225">Nutzen von Inhalten aus einer referenzierten RCL</span><span class="sxs-lookup"><span data-stu-id="741e4-225">Consume content from a referenced RCL</span></span>

<span data-ttu-id="741e4-226">Die Dateien, die im Ordner " *wwwroot* " der RCL enthalten sind, werden für die verbrauchende app unter dem Präfix `_content/{LIBRARY NAME}/`verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="741e4-226">The files included in the *wwwroot* folder of the RCL are exposed to the consuming app under the prefix `_content/{LIBRARY NAME}/`.</span></span> <span data-ttu-id="741e4-227">Eine Bibliothek mit dem Namen *Razor. Class. lib* führt z. b. zu einem Pfad zu `_content/Razor.Class.Lib/`statischem Inhalt unter.</span><span class="sxs-lookup"><span data-stu-id="741e4-227">For example, a library named *Razor.Class.Lib* results in a path to static content at `_content/Razor.Class.Lib/`.</span></span>

<span data-ttu-id="741e4-228">Die verarbeitende App verweist auf statische Ressourcen, die von der `<script>`Bibliothek `<style>`mit `<img>`,, und anderen HTML-Tags bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="741e4-228">The consuming app references static assets provided by the library with `<script>`, `<style>`, `<img>`, and other HTML tags.</span></span> <span data-ttu-id="741e4-229">In der nutzenden app muss die [Unterstützung statischer Dateien](xref:fundamentals/static-files) in `Startup.Configure`aktiviert sein:</span><span class="sxs-lookup"><span data-stu-id="741e4-229">The consuming app must have [static file support](xref:fundamentals/static-files) enabled in `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

<span data-ttu-id="741e4-230">Beim Ausführen der nutzenden App aus der Buildausgabe (`dotnet run`) werden statische Webressourcen standardmäßig in der Entwicklungsumgebung aktiviert.</span><span class="sxs-lookup"><span data-stu-id="741e4-230">When running the consuming app from build output (`dotnet run`), static web assets are enabled by default in the Development environment.</span></span> <span data-ttu-id="741e4-231">Um Assets in anderen Umgebungen bei Ausführung `UseStaticWebAssets` über die Buildausgabe zu unterstützen, müssen Sie auf dem Host-Generator in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="741e4-231">To support assets in other environments when running from build output, call `UseStaticWebAssets` on the host builder in *Program.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStaticWebAssets();
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="741e4-232">Das `UseStaticWebAssets` Aufrufen von ist nicht erforderlich, wenn eine APP aus`dotnet publish`der veröffentlichten Ausgabe () ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="741e4-232">Calling `UseStaticWebAssets` isn't required when running an app from published output (`dotnet publish`).</span></span>

### <a name="multi-project-development-flow"></a><span data-ttu-id="741e4-233">Entwicklungsfluss für mehrere Projekte</span><span class="sxs-lookup"><span data-stu-id="741e4-233">Multi-project development flow</span></span>

<span data-ttu-id="741e4-234">Wenn die verarbeitende app ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="741e4-234">When the consuming app runs:</span></span>

* <span data-ttu-id="741e4-235">Die Assets in der RCL bleiben in ihren ursprünglichen Ordnern.</span><span class="sxs-lookup"><span data-stu-id="741e4-235">The assets in the RCL stay in their original folders.</span></span> <span data-ttu-id="741e4-236">Die Assets werden nicht in die Verb raubende App verschoben.</span><span class="sxs-lookup"><span data-stu-id="741e4-236">The assets aren't moved to the consuming app.</span></span>
* <span data-ttu-id="741e4-237">Jede Änderung im Ordner " *wwwroot* " der RCL wird in der nutzenden App widergespiegelt, nachdem die RCL neu erstellt wurde und ohne die Verb raubende APP neu zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="741e4-237">Any change within the RCL's *wwwroot* folder is reflected in the consuming app after the RCL is rebuilt and without rebuilding the consuming app.</span></span>

<span data-ttu-id="741e4-238">Wenn die RCL erstellt wurde, wird ein Manifest erstellt, in dem die statischen webasset-Speicherorte beschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="741e4-238">When the RCL is built, a manifest is produced that describes the static web asset locations.</span></span> <span data-ttu-id="741e4-239">Die Verb raubende App liest das Manifest zur Laufzeit, um die Assets aus referenzierten Projekten und Paketen zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="741e4-239">The consuming app reads the manifest at runtime to consume the assets from referenced projects and packages.</span></span> <span data-ttu-id="741e4-240">Wenn einer RCL ein neues Asset hinzugefügt wird, muss die RCL neu erstellt werden, um das Manifest zu aktualisieren, bevor eine verarbeitende App auf das neue Medienobjekt zugreifen kann.</span><span class="sxs-lookup"><span data-stu-id="741e4-240">When a new asset is added to an RCL, the RCL must be rebuilt to update its manifest before a consuming app can access the new asset.</span></span>

### <a name="publish"></a><span data-ttu-id="741e4-241">Veröffentlichen</span><span class="sxs-lookup"><span data-stu-id="741e4-241">Publish</span></span>

<span data-ttu-id="741e4-242">Wenn die App veröffentlicht wird, werden die begleitenden Objekte aus allen referenzierten Projekten und Paketen in den Ordner *wwwroot* der veröffentlichten app unter `_content/{LIBRARY NAME}/`kopiert.</span><span class="sxs-lookup"><span data-stu-id="741e4-242">When the app is published, the companion assets from all referenced projects and packages are copied into the *wwwroot* folder of the published app under `_content/{LIBRARY NAME}/`.</span></span>

::: moniker-end

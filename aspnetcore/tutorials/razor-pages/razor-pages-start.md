---
title: 'Tutorial: Erste Schritte mit Razor Pages in ASP.NET Core'
author: rick-anderson
description: Diese Reihe von Tutorials zeigt, wie Sie Razor Pages in ASP.NET Core verwenden können. Erfahren Sie, wie Sie ein Modell erstellen, Code für Razor-Seiten generieren, Entity Framework Core und SQL Server für den Datenzugriff verwenden, Suchfunktionen hinzufügen, Eingabeüberprüfung hinzufügen und Migrationen verwenden, um das Modell zu aktualisieren.
ms.author: riande
ms.date: 11/12/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 6e1d58ccd83f7d7c1083dc2bf9ce7476650812a1
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78646903"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="04726-104">Tutorial: Erste Schritte mit Razor Pages in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="04726-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="04726-105">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="04726-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="04726-106">Dies ist das erste Tutorial einer Reihe, in der Sie Grundlegendes zur Erstellung einer ASP.NET Core-Webapp mit Razor Pages erfahren.</span><span class="sxs-lookup"><span data-stu-id="04726-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="04726-107">Am Ende der Reihe verfügen Sie über eine App, mit der eine Filmdatenbank verwaltet werden kann.</span><span class="sxs-lookup"><span data-stu-id="04726-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="04726-108">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="04726-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="04726-109">Erstellen einer Razor Pages-Web-App</span><span class="sxs-lookup"><span data-stu-id="04726-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="04726-110">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="04726-110">Run the app.</span></span>
> * <span data-ttu-id="04726-111">Überprüfen Sie die Projektdateien.</span><span class="sxs-lookup"><span data-stu-id="04726-111">Examine the project files.</span></span>

<span data-ttu-id="04726-112">Am Ende dieses Tutorials verfügen Sie über eine funktionsfähige Razor Pages-Web-App, auf der Sie in späteren Tutorials aufbauen werden.</span><span class="sxs-lookup"><span data-stu-id="04726-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="04726-114">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="04726-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="04726-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="04726-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="04726-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="04726-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="04726-117">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="04726-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="04726-118">Erstellen einer Razor Pages-Web-App</span><span class="sxs-lookup"><span data-stu-id="04726-118">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="04726-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="04726-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="04726-120">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="04726-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="04726-121">Erstellen Sie eine neue ASP.NET Core-Webanwendung, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="04726-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="04726-122">![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="04726-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="04726-123">Nennen Sie das Projekt **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="04726-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="04726-124">Es ist wichtig, den Namen *RazorPagesMovie* zu verwenden, damit die Namespaces übereinstimmen, wenn Sie Code kopieren und einfügen.</span><span class="sxs-lookup"><span data-stu-id="04726-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="04726-125">![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="04726-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="04726-126">Klicken Sie in der Dropdownliste nacheinander auf **ASP.NET Core 3.1**, **Webanwendung** und **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="04726-126">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="04726-128">Das folgende Startprojekt wird erstellt:</span><span class="sxs-lookup"><span data-stu-id="04726-128">The following starter project is created:</span></span>

  ![Projektmappen-Explorer](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="04726-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="04726-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="04726-131">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="04726-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="04726-132">Wechseln Sie zum Verzeichnis (`cd`), das das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="04726-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="04726-133">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="04726-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="04726-134">Der Befehl `dotnet new` erstellt ein neues Razor Pages-Projekt im Ordner *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="04726-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="04726-135">Der Befehl `code` öffnet den Ordner *RazorPagesMovie* in der aktuellen Instanz von Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="04726-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="04726-136">Wenn das OmniSharp-Flammensymbol in der Statusleiste grün angezeigt wird, wird ein Dialogfeld mit folgender Meldung angezeigt: **Required assets to build and debug are missing from 'RazorPagesMovie'. (Die erforderlichen Objekte zum Erstellen und Debuggen sind in „RazorPagesMovie“ nicht vorhanden. Sollen sie hinzugefügt werden?**</span><span class="sxs-lookup"><span data-stu-id="04726-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="04726-137">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="04726-137">Select **Yes**.</span></span>

  <span data-ttu-id="04726-138">Ein *VSCODE*-Verzeichnis, das die Dateien *launch.json* und *tasks.json* enthält, wird dem Stammverzeichnis des Projekts hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="04726-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="04726-139">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="04726-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="04726-140">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="04726-140">Select **File** > **New Solution**.</span></span>

![Neue Projektmappe in macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="04726-142">Wählen Sie **.NET Core** > **App** > **Webanwendung** > **Weiter** aus.</span><span class="sxs-lookup"><span data-stu-id="04726-142">Select **.NET Core** > **App** > **Web Application** > **Next**.</span></span>

  ![Dialogfeld „Neue Projektmappe“ in macOS](razor-pages-start/_static/webapp.png)

* <span data-ttu-id="04726-144">Legen Sie im Dialogfeld **Neue Webanwendung konfigurieren** das **Zielframework** auf **.NET Core 3.1** fest.</span><span class="sxs-lookup"><span data-stu-id="04726-144">In the **Configure your new Web Application** dialog, set the  **Target Framework** to **.NET Core 3.1**.</span></span>

  ![Auswahl für .NET Core 3.1 in macOS](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="04726-146">Nennen Sie das Projekt **RazorPagesMovie**, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="04726-146">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="04726-148">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="04726-148">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="04726-149">Überprüfen der Projektdateien</span><span class="sxs-lookup"><span data-stu-id="04726-149">Examine the project files</span></span>

<span data-ttu-id="04726-150">Es folgt eine Übersicht über die Hauptprojektordner und -dateien, mit denen Sie in späteren Tutorials arbeiten werden.</span><span class="sxs-lookup"><span data-stu-id="04726-150">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="04726-151">Ordner „Seiten“</span><span class="sxs-lookup"><span data-stu-id="04726-151">Pages folder</span></span>

<span data-ttu-id="04726-152">Enthält Razor-Seiten und unterstützende Dateien.</span><span class="sxs-lookup"><span data-stu-id="04726-152">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="04726-153">Jede Razor-Seite besteht aus einem Dateienpaar:</span><span class="sxs-lookup"><span data-stu-id="04726-153">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="04726-154">Eine *.cshtml*-Datei, die HTML-Markup mit C#-Code in Razor-Syntax enthält</span><span class="sxs-lookup"><span data-stu-id="04726-154">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="04726-155">Eine *.cshtml.cs*-Datei mit C# Code, in dem Seitenereignisse verarbeitet werden</span><span class="sxs-lookup"><span data-stu-id="04726-155">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="04726-156">Unterstützende Dateien haben Namen, die mit einem Unterstrich beginnen.</span><span class="sxs-lookup"><span data-stu-id="04726-156">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="04726-157">Zum Beispiel sind in der Datei *_Layout.cshtml* Benutzeroberflächenelemente konfiguriert, die für alle Seiten gelten.</span><span class="sxs-lookup"><span data-stu-id="04726-157">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="04726-158">Mit dieser Datei werden das Navigationsmenü oben auf der Seite und der Urheberrechtshinweis unten auf der Seite eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="04726-158">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="04726-159">Weitere Informationen finden Sie unter <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="04726-159">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="04726-160">Ordner „wwwroot“</span><span class="sxs-lookup"><span data-stu-id="04726-160">wwwroot folder</span></span>

<span data-ttu-id="04726-161">Enthält statische Dateien, z. B. HTML-Dateien, JavaScript-Dateien und CSS-Dateien.</span><span class="sxs-lookup"><span data-stu-id="04726-161">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="04726-162">Weitere Informationen finden Sie unter <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="04726-162">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="04726-163">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="04726-163">appSettings.json</span></span>

<span data-ttu-id="04726-164">Enthält Konfigurationsdaten, z. B. Verbindungszeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="04726-164">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="04726-165">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="04726-165">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="04726-166">Program.cs</span><span class="sxs-lookup"><span data-stu-id="04726-166">Program.cs</span></span>

<span data-ttu-id="04726-167">Enthält den Einstiegspunkt für das Programm.</span><span class="sxs-lookup"><span data-stu-id="04726-167">Contains the entry point for the program.</span></span> <span data-ttu-id="04726-168">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="04726-168">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="04726-169">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="04726-169">Startup.cs</span></span>

<span data-ttu-id="04726-170">Diese Datei enthält Code, mit dem das App-Verhalten konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="04726-170">Contains code that configures app behavior.</span></span> <span data-ttu-id="04726-171">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="04726-171">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="04726-172">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="04726-172">Next steps</span></span>

<span data-ttu-id="04726-173">Wechseln Sie zum nächsten Tutorial in der Reihe:</span><span class="sxs-lookup"><span data-stu-id="04726-173">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="04726-174">Hinzufügen eines Modells</span><span class="sxs-lookup"><span data-stu-id="04726-174">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="04726-175">Dies ist das erste Tutorial aus einer Reihe.</span><span class="sxs-lookup"><span data-stu-id="04726-175">This is the first tutorial of a series.</span></span> <span data-ttu-id="04726-176">[In der Reihe](xref:tutorials/razor-pages/index) lernen Sie die Grundlagen zum Erstellen einer Razor Pages-Web-App mit ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="04726-176">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="04726-177">Am Ende der Reihe verfügen Sie über eine App, mit der eine Filmdatenbank verwaltet werden kann.</span><span class="sxs-lookup"><span data-stu-id="04726-177">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="04726-178">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="04726-178">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="04726-179">Erstellen einer Razor Pages-Web-App</span><span class="sxs-lookup"><span data-stu-id="04726-179">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="04726-180">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="04726-180">Run the app.</span></span>
> * <span data-ttu-id="04726-181">Überprüfen Sie die Projektdateien.</span><span class="sxs-lookup"><span data-stu-id="04726-181">Examine the project files.</span></span>

<span data-ttu-id="04726-182">Am Ende dieses Tutorials verfügen Sie über eine funktionsfähige Razor Pages-Web-App, auf der Sie in späteren Tutorials aufbauen werden.</span><span class="sxs-lookup"><span data-stu-id="04726-182">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="04726-184">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="04726-184">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="04726-185">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="04726-185">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="04726-186">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="04726-186">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="04726-187">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="04726-187">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="04726-188">Erstellen einer Razor Pages-Web-App</span><span class="sxs-lookup"><span data-stu-id="04726-188">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="04726-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="04726-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="04726-190">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="04726-190">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="04726-191">Erstellen Sie eine neue ASP.NET Core-Webanwendung, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="04726-191">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="04726-193">Nennen Sie das Projekt **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="04726-193">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="04726-194">Es ist wichtig, den Namen *RazorPagesMovie* zu verwenden, damit die Namespaces übereinstimmen, wenn Sie Code kopieren und einfügen.</span><span class="sxs-lookup"><span data-stu-id="04726-194">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/config.png)

* <span data-ttu-id="04726-196">Klicken Sie in der Dropdownliste auf **ASP.NET Core 2.2**, auf **Webanwendung** und anschließend auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="04726-196">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="04726-198">Das folgende Startprojekt wird erstellt:</span><span class="sxs-lookup"><span data-stu-id="04726-198">The following starter project is created:</span></span>

  ![Projektmappen-Explorer](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="04726-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="04726-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="04726-201">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="04726-201">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="04726-202">Wechseln Sie zum Verzeichnis (`cd`), das das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="04726-202">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="04726-203">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="04726-203">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="04726-204">Der Befehl `dotnet new` erstellt ein neues Razor Pages-Projekt im Ordner *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="04726-204">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="04726-205">Der Befehl `code` öffnet den Ordner *RazorPagesMovie* in der aktuellen Instanz von Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="04726-205">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="04726-206">Wenn das OmniSharp-Flammensymbol in der Statusleiste grün angezeigt wird, wird ein Dialogfeld mit folgender Meldung angezeigt: **Required assets to build and debug are missing from 'RazorPagesMovie'. (Die erforderlichen Objekte zum Erstellen und Debuggen sind in „RazorPagesMovie“ nicht vorhanden. Sollen sie hinzugefügt werden?**</span><span class="sxs-lookup"><span data-stu-id="04726-206">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="04726-207">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="04726-207">Select **Yes**.</span></span>

  <span data-ttu-id="04726-208">Ein *VSCODE*-Verzeichnis, das die Dateien *launch.json* und *tasks.json* enthält, wird dem Stammverzeichnis des Projekts hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="04726-208">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="04726-209">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="04726-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="04726-210">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="04726-210">Select **File** > **New Solution**.</span></span>

![Neue Projektmappe in macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="04726-212">Wählen Sie **.NET Core** > **App** > **Webanwendung** > **Weiter** aus.</span><span class="sxs-lookup"><span data-stu-id="04726-212">Select **.NET Core** > **App** > **Web Application** > **Next**.</span></span>

  ![Dialogfeld „Neue Projektmappe“ in macOS](razor-pages-start/_static/webapp.png)

* <span data-ttu-id="04726-214">Legen Sie im Dialogfeld **Neue ASP.NET Core-Web-API konfigurieren** das **Zielframework** auf **.NET Core 3.1** fest.</span><span class="sxs-lookup"><span data-stu-id="04726-214">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** to **.NET Core 3.1**.</span></span>

  ![Auswahl für .NET Core 3.0 in macOS](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="04726-216">Nennen Sie das Projekt **RazorPagesMovie**, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="04726-216">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="04726-218">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="04726-218">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="04726-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="04726-219">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="04726-220">Drücken Sie STRG+F5, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="04726-220">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="04726-221">Visual Studio startet [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) und führt die App aus.</span><span class="sxs-lookup"><span data-stu-id="04726-221">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="04726-222">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="04726-222">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="04726-223">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="04726-223">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="04726-224">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="04726-224">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="04726-225">Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.</span><span class="sxs-lookup"><span data-stu-id="04726-225">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="04726-226">Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="04726-226">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="04726-227">Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.</span><span class="sxs-lookup"><span data-stu-id="04726-227">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="04726-229">Die folgende Abbildung zeigt die App, nachdem Sie das Einverständnis zur Nachverfolgung gegeben haben:</span><span class="sxs-lookup"><span data-stu-id="04726-229">The following image shows the app after you give consent to tracking:</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="04726-231">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="04726-231">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="04726-232">Drücken Sie **STRG+F5**, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="04726-232">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="04726-233">Visual Studio Code startet [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="04726-233">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="04726-234">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="04726-234">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="04726-235">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="04726-235">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="04726-236">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="04726-236">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="04726-237">Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="04726-237">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="04726-238">Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.</span><span class="sxs-lookup"><span data-stu-id="04726-238">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="04726-240">Die folgende Abbildung zeigt die App, nachdem Sie das Einverständnis zur Nachverfolgung gegeben haben:</span><span class="sxs-lookup"><span data-stu-id="04726-240">The following image shows the app after you give consent to tracking:</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="04726-242">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="04726-242">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="04726-243">Drücken Sie **CMD+OPT+F5** für eine Ausführung ohne Debugger.</span><span class="sxs-lookup"><span data-stu-id="04726-243">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="04726-244">Visual Studio startet dann [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="04726-244">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="04726-245">Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="04726-245">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="04726-246">Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.</span><span class="sxs-lookup"><span data-stu-id="04726-246">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="04726-248">Die folgende Abbildung zeigt die App, nachdem Sie das Einverständnis zur Nachverfolgung gegeben haben:</span><span class="sxs-lookup"><span data-stu-id="04726-248">The following image shows the app after you give consent to tracking:</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="04726-250">Überprüfen der Projektdateien</span><span class="sxs-lookup"><span data-stu-id="04726-250">Examine the project files</span></span>

<span data-ttu-id="04726-251">Es folgt eine Übersicht über die Hauptprojektordner und -dateien, mit denen Sie in späteren Tutorials arbeiten werden.</span><span class="sxs-lookup"><span data-stu-id="04726-251">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="04726-252">Ordner „Seiten“</span><span class="sxs-lookup"><span data-stu-id="04726-252">Pages folder</span></span>

<span data-ttu-id="04726-253">Enthält Razor-Seiten und unterstützende Dateien.</span><span class="sxs-lookup"><span data-stu-id="04726-253">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="04726-254">Jede Razor-Seite besteht aus einem Dateienpaar:</span><span class="sxs-lookup"><span data-stu-id="04726-254">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="04726-255">Eine *.cshtml*-Datei, die HTML-Markup mit C#-Code in Razor-Syntax enthält</span><span class="sxs-lookup"><span data-stu-id="04726-255">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="04726-256">Eine *.cshtml.cs*-Datei mit C# Code, in dem Seitenereignisse verarbeitet werden</span><span class="sxs-lookup"><span data-stu-id="04726-256">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="04726-257">Unterstützende Dateien haben Namen, die mit einem Unterstrich beginnen.</span><span class="sxs-lookup"><span data-stu-id="04726-257">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="04726-258">Zum Beispiel sind in der Datei *_Layout.cshtml* Benutzeroberflächenelemente konfiguriert, die für alle Seiten gelten.</span><span class="sxs-lookup"><span data-stu-id="04726-258">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="04726-259">Mit dieser Datei werden das Navigationsmenü oben auf der Seite und der Urheberrechtshinweis unten auf der Seite eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="04726-259">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="04726-260">Weitere Informationen finden Sie unter <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="04726-260">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="04726-261">Ordner „wwwroot“</span><span class="sxs-lookup"><span data-stu-id="04726-261">wwwroot folder</span></span>

<span data-ttu-id="04726-262">Enthält statische Dateien, z. B. HTML-Dateien, JavaScript-Dateien und CSS-Dateien.</span><span class="sxs-lookup"><span data-stu-id="04726-262">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="04726-263">Weitere Informationen finden Sie unter <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="04726-263">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="04726-264">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="04726-264">appSettings.json</span></span>

<span data-ttu-id="04726-265">Enthält Konfigurationsdaten, z. B. Verbindungszeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="04726-265">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="04726-266">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="04726-266">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="04726-267">Program.cs</span><span class="sxs-lookup"><span data-stu-id="04726-267">Program.cs</span></span>

<span data-ttu-id="04726-268">Enthält den Einstiegspunkt für das Programm.</span><span class="sxs-lookup"><span data-stu-id="04726-268">Contains the entry point for the program.</span></span> <span data-ttu-id="04726-269">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="04726-269">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="04726-270">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="04726-270">Startup.cs</span></span>

<span data-ttu-id="04726-271">Enthält Code, mit dem das App-Verhalten konfiguriert wird, beispielsweise, ob die App Zustimmung für Cookies erfordert.</span><span class="sxs-lookup"><span data-stu-id="04726-271">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="04726-272">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="04726-272">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="04726-273">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="04726-273">Additional resources</span></span>

* [<span data-ttu-id="04726-274">Dieses Tutorials auf YouTube</span><span class="sxs-lookup"><span data-stu-id="04726-274">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="04726-275">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="04726-275">Next steps</span></span>

<span data-ttu-id="04726-276">Wechseln Sie zum nächsten Tutorial in der Reihe:</span><span class="sxs-lookup"><span data-stu-id="04726-276">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="04726-277">Hinzufügen eines Modells</span><span class="sxs-lookup"><span data-stu-id="04726-277">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

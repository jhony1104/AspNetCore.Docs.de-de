---
title: 'Tutorial: Erste Schritte mit Razor Pages in ASP.NET Core'
author: rick-anderson
description: Diese Reihe von Tutorials zeigt, wie Sie Razor Pages in ASP.NET Core verwenden können. Erfahren Sie, wie Sie ein Modell erstellen, Code für Razor-Seiten generieren, Entity Framework Core und SQL Server für den Datenzugriff verwenden, Suchfunktionen hinzufügen, Eingabeüberprüfung hinzufügen und Migrationen verwenden, um das Modell zu aktualisieren.
ms.author: riande
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 8ed12b1778673962fe0b174e005bd6d8a7f54168
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774872"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="9a894-104">Tutorial: Erste Schritte mit Razor Pages in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9a894-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="9a894-105">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9a894-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="9a894-106">Dies ist das erste Tutorial einer Reihe, in der Sie Grundlegendes zur Erstellung einer ASP.NET Core-Webapp mit Razor Pages erfahren.</span><span class="sxs-lookup"><span data-stu-id="9a894-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="9a894-107">Am Ende der Reihe verfügen Sie über eine App, mit der eine Filmdatenbank verwaltet werden kann.</span><span class="sxs-lookup"><span data-stu-id="9a894-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="9a894-108">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="9a894-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9a894-109">Erstellen einer Razor Pages-Web-App</span><span class="sxs-lookup"><span data-stu-id="9a894-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="9a894-110">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="9a894-110">Run the app.</span></span>
> * <span data-ttu-id="9a894-111">Überprüfen Sie die Projektdateien.</span><span class="sxs-lookup"><span data-stu-id="9a894-111">Examine the project files.</span></span>

<span data-ttu-id="9a894-112">Am Ende dieses Tutorials verfügen Sie über eine funktionsfähige Razor Pages-Web-App, auf der Sie in späteren Tutorials aufbauen werden.</span><span class="sxs-lookup"><span data-stu-id="9a894-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="9a894-114">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="9a894-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9a894-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9a894-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="9a894-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9a894-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9a894-117">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="9a894-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="9a894-118">Erstellen einer Razor Pages-Web-App</span><span class="sxs-lookup"><span data-stu-id="9a894-118">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9a894-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9a894-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9a894-120">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="9a894-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="9a894-121">Erstellen Sie eine neue ASP.NET Core-Webanwendung, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="9a894-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="9a894-122">![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="9a894-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="9a894-123">Nennen Sie das Projekt **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="9a894-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="9a894-124">Es ist wichtig, den Namen *RazorPagesMovie* zu verwenden, damit die Namespaces übereinstimmen, wenn Sie Code kopieren und einfügen.</span><span class="sxs-lookup"><span data-stu-id="9a894-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="9a894-125">![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="9a894-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="9a894-126">Klicken Sie in der Dropdownliste nacheinander auf **ASP.NET Core 3.1**, **Webanwendung** und **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="9a894-126">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="9a894-128">Das folgende Startprojekt wird erstellt:</span><span class="sxs-lookup"><span data-stu-id="9a894-128">The following starter project is created:</span></span>

  ![Projektmappen-Explorer](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="9a894-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9a894-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9a894-131">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="9a894-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="9a894-132">Wechseln Sie zum Verzeichnis (`cd`), das das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="9a894-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="9a894-133">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="9a894-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="9a894-134">Der Befehl `dotnet new` erstellt ein neues Razor Pages-Projekt im Ordner *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="9a894-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="9a894-135">Der Befehl `code` öffnet den Ordner *RazorPagesMovie* in der aktuellen Instanz von Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="9a894-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="9a894-136">Wenn das OmniSharp-Flammensymbol in der Statusleiste grün angezeigt wird, wird ein Dialogfeld mit folgender Meldung angezeigt: **Required assets to build and debug are missing from 'RazorPagesMovie'. (Die erforderlichen Objekte zum Erstellen und Debuggen sind in „RazorPagesMovie“ nicht vorhanden. Sollen sie hinzugefügt werden?**</span><span class="sxs-lookup"><span data-stu-id="9a894-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="9a894-137">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="9a894-137">Select **Yes**.</span></span>

  <span data-ttu-id="9a894-138">Ein *VSCODE*-Verzeichnis, das die Dateien *launch.json* und *tasks.json* enthält, wird dem Stammverzeichnis des Projekts hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="9a894-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9a894-139">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="9a894-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="9a894-140">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="9a894-140">Select **File** > **New Solution**.</span></span>

![Neue Projektmappe in macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="9a894-142">Wählen Sie **.NET Core** > **App** > **Webanwendung** > **Weiter** aus.</span><span class="sxs-lookup"><span data-stu-id="9a894-142">Select **.NET Core** > **App** > **Web Application** > **Next**.</span></span>

  ![Dialogfeld „Neue Projektmappe“ in macOS](razor-pages-start/_static/webapp.png)

* <span data-ttu-id="9a894-144">Legen Sie im Dialogfeld **Neue Webanwendung konfigurieren** das **Zielframework** auf **.NET Core 3.1** fest.</span><span class="sxs-lookup"><span data-stu-id="9a894-144">In the **Configure your new Web Application** dialog, set the  **Target Framework** to **.NET Core 3.1**.</span></span>

  ![Auswahl für .NET Core 3.1 in macOS](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="9a894-146">Nennen Sie das Projekt **RazorPagesMovie**, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="9a894-146">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="9a894-148">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="9a894-148">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="9a894-149">Überprüfen der Projektdateien</span><span class="sxs-lookup"><span data-stu-id="9a894-149">Examine the project files</span></span>

<span data-ttu-id="9a894-150">Es folgt eine Übersicht über die Hauptprojektordner und -dateien, mit denen Sie in späteren Tutorials arbeiten werden.</span><span class="sxs-lookup"><span data-stu-id="9a894-150">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="9a894-151">Ordner „Seiten“</span><span class="sxs-lookup"><span data-stu-id="9a894-151">Pages folder</span></span>

<span data-ttu-id="9a894-152">Enthält Razor-Seiten und unterstützende Dateien.</span><span class="sxs-lookup"><span data-stu-id="9a894-152">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="9a894-153">Jede Razor-Seite besteht aus einem Dateienpaar:</span><span class="sxs-lookup"><span data-stu-id="9a894-153">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="9a894-154">Eine *.cshtml*-Datei, die HTML-Markup mit C#-Code in Razor-Syntax enthält</span><span class="sxs-lookup"><span data-stu-id="9a894-154">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="9a894-155">Eine *.cshtml.cs*-Datei mit C# Code, in dem Seitenereignisse verarbeitet werden</span><span class="sxs-lookup"><span data-stu-id="9a894-155">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="9a894-156">Unterstützende Dateien haben Namen, die mit einem Unterstrich beginnen.</span><span class="sxs-lookup"><span data-stu-id="9a894-156">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="9a894-157">Zum Beispiel sind in der Datei *_Layout.cshtml* Benutzeroberflächenelemente konfiguriert, die für alle Seiten gelten.</span><span class="sxs-lookup"><span data-stu-id="9a894-157">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="9a894-158">Mit dieser Datei werden das Navigationsmenü oben auf der Seite und der Urheberrechtshinweis unten auf der Seite eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="9a894-158">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="9a894-159">Weitere Informationen finden Sie unter <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="9a894-159">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="9a894-160">Ordner „wwwroot“</span><span class="sxs-lookup"><span data-stu-id="9a894-160">wwwroot folder</span></span>

<span data-ttu-id="9a894-161">Enthält statische Dateien, z. B. HTML-Dateien, JavaScript-Dateien und CSS-Dateien.</span><span class="sxs-lookup"><span data-stu-id="9a894-161">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="9a894-162">Weitere Informationen finden Sie unter <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="9a894-162">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="9a894-163">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="9a894-163">appSettings.json</span></span>

<span data-ttu-id="9a894-164">Enthält Konfigurationsdaten, z. B. Verbindungszeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="9a894-164">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="9a894-165">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="9a894-165">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="9a894-166">Program.cs</span><span class="sxs-lookup"><span data-stu-id="9a894-166">Program.cs</span></span>

<span data-ttu-id="9a894-167">Enthält den Einstiegspunkt für das Programm.</span><span class="sxs-lookup"><span data-stu-id="9a894-167">Contains the entry point for the program.</span></span> <span data-ttu-id="9a894-168">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="9a894-168">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="9a894-169">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="9a894-169">Startup.cs</span></span>

<span data-ttu-id="9a894-170">Diese Datei enthält Code, mit dem das App-Verhalten konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="9a894-170">Contains code that configures app behavior.</span></span> <span data-ttu-id="9a894-171">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="9a894-171">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="9a894-172">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="9a894-172">Next steps</span></span>

<span data-ttu-id="9a894-173">Wechseln Sie zum nächsten Tutorial in der Reihe:</span><span class="sxs-lookup"><span data-stu-id="9a894-173">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="9a894-174">Hinzufügen eines Modells</span><span class="sxs-lookup"><span data-stu-id="9a894-174">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9a894-175">Dies ist das erste Tutorial aus einer Reihe.</span><span class="sxs-lookup"><span data-stu-id="9a894-175">This is the first tutorial of a series.</span></span> <span data-ttu-id="9a894-176">[In der Reihe](xref:tutorials/razor-pages/index) lernen Sie die Grundlagen zum Erstellen einer Razor Pages-Web-App mit ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9a894-176">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="9a894-177">Am Ende der Reihe verfügen Sie über eine App, mit der eine Filmdatenbank verwaltet werden kann.</span><span class="sxs-lookup"><span data-stu-id="9a894-177">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="9a894-178">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="9a894-178">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9a894-179">Erstellen einer Razor Pages-Web-App</span><span class="sxs-lookup"><span data-stu-id="9a894-179">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="9a894-180">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="9a894-180">Run the app.</span></span>
> * <span data-ttu-id="9a894-181">Überprüfen Sie die Projektdateien.</span><span class="sxs-lookup"><span data-stu-id="9a894-181">Examine the project files.</span></span>

<span data-ttu-id="9a894-182">Am Ende dieses Tutorials verfügen Sie über eine funktionsfähige Razor Pages-Web-App, auf der Sie in späteren Tutorials aufbauen werden.</span><span class="sxs-lookup"><span data-stu-id="9a894-182">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="9a894-184">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="9a894-184">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9a894-185">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9a894-185">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="9a894-186">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9a894-186">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9a894-187">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="9a894-187">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="9a894-188">Erstellen einer Razor Pages-Web-App</span><span class="sxs-lookup"><span data-stu-id="9a894-188">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9a894-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9a894-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9a894-190">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="9a894-190">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="9a894-191">Erstellen Sie eine neue ASP.NET Core-Webanwendung, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="9a894-191">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="9a894-193">Nennen Sie das Projekt **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="9a894-193">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="9a894-194">Es ist wichtig, den Namen *RazorPagesMovie* zu verwenden, damit die Namespaces übereinstimmen, wenn Sie Code kopieren und einfügen.</span><span class="sxs-lookup"><span data-stu-id="9a894-194">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/config.png)

* <span data-ttu-id="9a894-196">Klicken Sie in der Dropdownliste auf **ASP.NET Core 2.2**, auf **Webanwendung** und anschließend auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="9a894-196">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="9a894-198">Das folgende Startprojekt wird erstellt:</span><span class="sxs-lookup"><span data-stu-id="9a894-198">The following starter project is created:</span></span>

  ![Projektmappen-Explorer](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="9a894-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9a894-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9a894-201">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="9a894-201">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="9a894-202">Wechseln Sie zum Verzeichnis (`cd`), das das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="9a894-202">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="9a894-203">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="9a894-203">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="9a894-204">Der Befehl `dotnet new` erstellt ein neues Razor Pages-Projekt im Ordner *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="9a894-204">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="9a894-205">Der Befehl `code` öffnet den Ordner *RazorPagesMovie* in der aktuellen Instanz von Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="9a894-205">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="9a894-206">Wenn das OmniSharp-Flammensymbol in der Statusleiste grün angezeigt wird, wird ein Dialogfeld mit folgender Meldung angezeigt: **Required assets to build and debug are missing from 'RazorPagesMovie'. (Die erforderlichen Objekte zum Erstellen und Debuggen sind in „RazorPagesMovie“ nicht vorhanden. Sollen sie hinzugefügt werden?**</span><span class="sxs-lookup"><span data-stu-id="9a894-206">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="9a894-207">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="9a894-207">Select **Yes**.</span></span>

  <span data-ttu-id="9a894-208">Ein *VSCODE*-Verzeichnis, das die Dateien *launch.json* und *tasks.json* enthält, wird dem Stammverzeichnis des Projekts hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="9a894-208">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9a894-209">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="9a894-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="9a894-210">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="9a894-210">Select **File** > **New Solution**.</span></span>

![Neue Projektmappe in macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="9a894-212">Wählen Sie **.NET Core** > **App** > **Webanwendung** > **Weiter** aus.</span><span class="sxs-lookup"><span data-stu-id="9a894-212">Select **.NET Core** > **App** > **Web Application** > **Next**.</span></span>

  ![Dialogfeld „Neue Projektmappe“ in macOS](razor-pages-start/_static/webapp.png)

* <span data-ttu-id="9a894-214">Legen Sie im Dialogfeld **Neue ASP.NET Core-Web-API konfigurieren** das **Zielframework** auf **.NET Core 3.1** fest.</span><span class="sxs-lookup"><span data-stu-id="9a894-214">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** to **.NET Core 3.1**.</span></span>

  ![Auswahl für .NET Core 3.0 in macOS](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="9a894-216">Nennen Sie das Projekt **RazorPagesMovie**, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="9a894-216">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="9a894-218">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="9a894-218">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9a894-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9a894-219">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9a894-220">Drücken Sie STRG+F5, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="9a894-220">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="9a894-221">Visual Studio startet [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) und führt die App aus.</span><span class="sxs-lookup"><span data-stu-id="9a894-221">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="9a894-222">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="9a894-222">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="9a894-223">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="9a894-223">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="9a894-224">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="9a894-224">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="9a894-225">Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.</span><span class="sxs-lookup"><span data-stu-id="9a894-225">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="9a894-226">Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="9a894-226">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="9a894-227">Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.</span><span class="sxs-lookup"><span data-stu-id="9a894-227">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="9a894-229">Die folgende Abbildung zeigt die App, nachdem Sie das Einverständnis zur Nachverfolgung gegeben haben:</span><span class="sxs-lookup"><span data-stu-id="9a894-229">The following image shows the app after you give consent to tracking:</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="9a894-231">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9a894-231">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="9a894-232">Drücken Sie **STRG+F5**, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="9a894-232">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="9a894-233">Visual Studio Code startet [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="9a894-233">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="9a894-234">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="9a894-234">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="9a894-235">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="9a894-235">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="9a894-236">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="9a894-236">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="9a894-237">Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="9a894-237">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="9a894-238">Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.</span><span class="sxs-lookup"><span data-stu-id="9a894-238">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="9a894-240">Die folgende Abbildung zeigt die App, nachdem Sie das Einverständnis zur Nachverfolgung gegeben haben:</span><span class="sxs-lookup"><span data-stu-id="9a894-240">The following image shows the app after you give consent to tracking:</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9a894-242">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="9a894-242">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="9a894-243">Drücken Sie **CMD+OPT+F5** für eine Ausführung ohne Debugger.</span><span class="sxs-lookup"><span data-stu-id="9a894-243">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="9a894-244">Visual Studio startet dann [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="9a894-244">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="9a894-245">Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="9a894-245">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="9a894-246">Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.</span><span class="sxs-lookup"><span data-stu-id="9a894-246">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="9a894-248">Die folgende Abbildung zeigt die App, nachdem Sie das Einverständnis zur Nachverfolgung gegeben haben:</span><span class="sxs-lookup"><span data-stu-id="9a894-248">The following image shows the app after you give consent to tracking:</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="9a894-250">Überprüfen der Projektdateien</span><span class="sxs-lookup"><span data-stu-id="9a894-250">Examine the project files</span></span>

<span data-ttu-id="9a894-251">Es folgt eine Übersicht über die Hauptprojektordner und -dateien, mit denen Sie in späteren Tutorials arbeiten werden.</span><span class="sxs-lookup"><span data-stu-id="9a894-251">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="9a894-252">Ordner „Seiten“</span><span class="sxs-lookup"><span data-stu-id="9a894-252">Pages folder</span></span>

<span data-ttu-id="9a894-253">Enthält Razor-Seiten und unterstützende Dateien.</span><span class="sxs-lookup"><span data-stu-id="9a894-253">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="9a894-254">Jede Razor-Seite besteht aus einem Dateienpaar:</span><span class="sxs-lookup"><span data-stu-id="9a894-254">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="9a894-255">Eine *.cshtml*-Datei, die HTML-Markup mit C#-Code in Razor-Syntax enthält</span><span class="sxs-lookup"><span data-stu-id="9a894-255">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="9a894-256">Eine *.cshtml.cs*-Datei mit C# Code, in dem Seitenereignisse verarbeitet werden</span><span class="sxs-lookup"><span data-stu-id="9a894-256">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="9a894-257">Unterstützende Dateien haben Namen, die mit einem Unterstrich beginnen.</span><span class="sxs-lookup"><span data-stu-id="9a894-257">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="9a894-258">Zum Beispiel sind in der Datei *_Layout.cshtml* Benutzeroberflächenelemente konfiguriert, die für alle Seiten gelten.</span><span class="sxs-lookup"><span data-stu-id="9a894-258">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="9a894-259">Mit dieser Datei werden das Navigationsmenü oben auf der Seite und der Urheberrechtshinweis unten auf der Seite eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="9a894-259">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="9a894-260">Weitere Informationen finden Sie unter <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="9a894-260">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="9a894-261">Ordner „wwwroot“</span><span class="sxs-lookup"><span data-stu-id="9a894-261">wwwroot folder</span></span>

<span data-ttu-id="9a894-262">Enthält statische Dateien, z. B. HTML-Dateien, JavaScript-Dateien und CSS-Dateien.</span><span class="sxs-lookup"><span data-stu-id="9a894-262">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="9a894-263">Weitere Informationen finden Sie unter <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="9a894-263">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="9a894-264">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="9a894-264">appSettings.json</span></span>

<span data-ttu-id="9a894-265">Enthält Konfigurationsdaten, z. B. Verbindungszeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="9a894-265">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="9a894-266">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="9a894-266">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="9a894-267">Program.cs</span><span class="sxs-lookup"><span data-stu-id="9a894-267">Program.cs</span></span>

<span data-ttu-id="9a894-268">Enthält den Einstiegspunkt für das Programm.</span><span class="sxs-lookup"><span data-stu-id="9a894-268">Contains the entry point for the program.</span></span> <span data-ttu-id="9a894-269">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="9a894-269">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="9a894-270">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="9a894-270">Startup.cs</span></span>

<span data-ttu-id="9a894-271">Enthält Code, mit dem das App-Verhalten konfiguriert wird, beispielsweise, ob die App Zustimmung für Cookies erfordert.</span><span class="sxs-lookup"><span data-stu-id="9a894-271">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="9a894-272">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="9a894-272">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9a894-273">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="9a894-273">Additional resources</span></span>

* [<span data-ttu-id="9a894-274">Dieses Tutorials auf YouTube</span><span class="sxs-lookup"><span data-stu-id="9a894-274">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="9a894-275">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="9a894-275">Next steps</span></span>

<span data-ttu-id="9a894-276">Wechseln Sie zum nächsten Tutorial in der Reihe:</span><span class="sxs-lookup"><span data-stu-id="9a894-276">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="9a894-277">Hinzufügen eines Modells</span><span class="sxs-lookup"><span data-stu-id="9a894-277">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

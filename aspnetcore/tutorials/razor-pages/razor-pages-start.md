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
ms.openlocfilehash: 3b8ccf639bb91234f81c67750fffa170e52d636f
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/05/2020
ms.locfileid: "84452341"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="ba1ec-104">Tutorial: Erste Schritte mit Razor Pages in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ba1ec-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="ba1ec-105">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ba1ec-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="ba1ec-106">Dies ist das erste Tutorial einer Reihe, in der Sie Grundlegendes zur Erstellung einer ASP.NET Core-Web-App mit Razor Pages erfahren.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="ba1ec-107">Am Ende der Reihe verfügen Sie über eine App, mit der eine Filmdatenbank verwaltet werden kann.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="ba1ec-108">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="ba1ec-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ba1ec-109">Sie erstellen eine Razor Pages-Web-App.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="ba1ec-110">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-110">Run the app.</span></span>
> * <span data-ttu-id="ba1ec-111">Überprüfen Sie die Projektdateien.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-111">Examine the project files.</span></span>

<span data-ttu-id="ba1ec-112">Am Ende dieses Tutorials verfügen Sie über eine funktionsfähige Razor Pages-Web-App, auf der Sie in späteren Tutorials aufbauen werden.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="ba1ec-114">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="ba1ec-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ba1ec-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ba1ec-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ba1ec-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ba1ec-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ba1ec-117">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ba1ec-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="ba1ec-118">Erstellen einer Razor Pages-Web-App</span><span class="sxs-lookup"><span data-stu-id="ba1ec-118">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ba1ec-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ba1ec-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ba1ec-120">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="ba1ec-121">Erstellen Sie eine neue ASP.NET Core-Webanwendung, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="ba1ec-122">![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="ba1ec-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="ba1ec-123">Nennen Sie das Projekt **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="ba1ec-124">Es ist wichtig, den Namen *RazorPagesMovie* zu verwenden, damit die Namespaces übereinstimmen, wenn Sie Code kopieren und einfügen.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="ba1ec-125">![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="ba1ec-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="ba1ec-126">Klicken Sie in der Dropdownliste nacheinander auf **ASP.NET Core 3.1**, **Webanwendung** und **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-126">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="ba1ec-128">Das folgende Startprojekt wird erstellt:</span><span class="sxs-lookup"><span data-stu-id="ba1ec-128">The following starter project is created:</span></span>

  ![Projektmappen-Explorer](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="ba1ec-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ba1ec-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ba1ec-131">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="ba1ec-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="ba1ec-132">Wechseln Sie zum Verzeichnis (`cd`), das das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="ba1ec-133">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="ba1ec-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="ba1ec-134">Der Befehl `dotnet new` erstellt ein neues Razor Pages-Projekt im Ordner *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="ba1ec-135">Der Befehl `code` öffnet den Ordner *RazorPagesMovie* in der aktuellen Instanz von Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="ba1ec-136">Wenn das OmniSharp-Flammensymbol in der Statusleiste grün angezeigt wird, wird ein Dialogfeld mit folgender Meldung angezeigt: **Required assets to build and debug are missing from 'RazorPagesMovie'. (Die erforderlichen Objekte zum Erstellen und Debuggen sind in „RazorPagesMovie“ nicht vorhanden. Sollen sie hinzugefügt werden?**</span><span class="sxs-lookup"><span data-stu-id="ba1ec-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="ba1ec-137">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-137">Select **Yes**.</span></span>

  <span data-ttu-id="ba1ec-138">Ein *VSCODE*-Verzeichnis, das die Dateien *launch.json* und *tasks.json* enthält, wird dem Stammverzeichnis des Projekts hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ba1ec-139">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ba1ec-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ba1ec-140">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-140">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="ba1ec-142">Navigieren Sie in Visual Studio für Mac-Versionen vor Version 8.6 zu **.NET Core** > **App** > **Webanwendung** > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-142">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="ba1ec-143">Bei Version 8.6 oder höher klicken Sie auf **Web and Console** > **App** > **Webanwendung** > **Weiter** (Web und Konsole).</span><span class="sxs-lookup"><span data-stu-id="ba1ec-143">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

  ![Auswählen der macOS-Web-App-Vorlage](razor-pages-start/_static/web_app_template_vsmac.png)

* <span data-ttu-id="ba1ec-145">Bestätigen Sie die folgenden Konfigurationen:</span><span class="sxs-lookup"><span data-stu-id="ba1ec-145">Confirm the following configurations:</span></span>

  * <span data-ttu-id="ba1ec-146">**Zielframework** auf **.NET Core 3.1** festgelegt</span><span class="sxs-lookup"><span data-stu-id="ba1ec-146">**Target Framework** set to **.NET Core 3.1**.</span></span>
  * <span data-ttu-id="ba1ec-147">**Authentifizierung** auf **Keine Authentifizierung** festgelegt</span><span class="sxs-lookup"><span data-stu-id="ba1ec-147">**Authentication** set to **No Authentication**.</span></span>
   
  <span data-ttu-id="ba1ec-148">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-148">Select **Next**.</span></span>

  ![Auswahl für .NET Core 3.1 in macOS](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="ba1ec-150">Nennen Sie das Projekt **RazorPagesMovie**, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-150">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![Benennen des macOS-Projekts](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="ba1ec-152">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="ba1ec-152">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="ba1ec-153">Überprüfen der Projektdateien</span><span class="sxs-lookup"><span data-stu-id="ba1ec-153">Examine the project files</span></span>

<span data-ttu-id="ba1ec-154">Es folgt eine Übersicht über die Hauptprojektordner und -dateien, mit denen Sie in späteren Tutorials arbeiten werden.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-154">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="ba1ec-155">Ordner „Seiten“</span><span class="sxs-lookup"><span data-stu-id="ba1ec-155">Pages folder</span></span>

<span data-ttu-id="ba1ec-156">Enthält Razor-Seiten und unterstützende Dateien.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-156">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="ba1ec-157">Jede Razor-Seite besteht aus einem Dateienpaar:</span><span class="sxs-lookup"><span data-stu-id="ba1ec-157">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="ba1ec-158">Eine *.cshtml*-Datei, die HTML-Markup mit C#-Code in Razor-Syntax enthält</span><span class="sxs-lookup"><span data-stu-id="ba1ec-158">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="ba1ec-159">Eine *.cshtml.cs*-Datei mit C# Code, in dem Seitenereignisse verarbeitet werden</span><span class="sxs-lookup"><span data-stu-id="ba1ec-159">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="ba1ec-160">Unterstützende Dateien haben Namen, die mit einem Unterstrich beginnen.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-160">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="ba1ec-161">Zum Beispiel sind in der Datei *_Layout.cshtml* Benutzeroberflächenelemente konfiguriert, die für alle Seiten gelten.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-161">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="ba1ec-162">Mit dieser Datei werden das Navigationsmenü oben auf der Seite und der Urheberrechtshinweis unten auf der Seite eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-162">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="ba1ec-163">Weitere Informationen finden Sie unter <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-163">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="ba1ec-164">Ordner „wwwroot“</span><span class="sxs-lookup"><span data-stu-id="ba1ec-164">wwwroot folder</span></span>

<span data-ttu-id="ba1ec-165">Enthält statische Dateien, z. B. HTML-Dateien, JavaScript-Dateien und CSS-Dateien.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-165">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="ba1ec-166">Weitere Informationen finden Sie unter <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-166">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="ba1ec-167">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="ba1ec-167">appSettings.json</span></span>

<span data-ttu-id="ba1ec-168">Enthält Konfigurationsdaten, z. B. Verbindungszeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-168">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="ba1ec-169">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-169">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="ba1ec-170">Program.cs</span><span class="sxs-lookup"><span data-stu-id="ba1ec-170">Program.cs</span></span>

<span data-ttu-id="ba1ec-171">Enthält den Einstiegspunkt für das Programm.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-171">Contains the entry point for the program.</span></span> <span data-ttu-id="ba1ec-172">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-172">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="ba1ec-173">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="ba1ec-173">Startup.cs</span></span>

<span data-ttu-id="ba1ec-174">Diese Datei enthält Code, mit dem das App-Verhalten konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-174">Contains code that configures app behavior.</span></span> <span data-ttu-id="ba1ec-175">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-175">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="ba1ec-176">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="ba1ec-176">Next steps</span></span>

<span data-ttu-id="ba1ec-177">Wechseln Sie zum nächsten Tutorial in der Reihe:</span><span class="sxs-lookup"><span data-stu-id="ba1ec-177">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="ba1ec-178">Hinzufügen eines Modells</span><span class="sxs-lookup"><span data-stu-id="ba1ec-178">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ba1ec-179">Dies ist das erste Tutorial aus einer Reihe.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-179">This is the first tutorial of a series.</span></span> <span data-ttu-id="ba1ec-180">[In der Reihe](xref:tutorials/razor-pages/index) lernen Sie die Grundlagen zum Erstellen einer Razor Pages-Web-App mit ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-180">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="ba1ec-181">Am Ende der Reihe verfügen Sie über eine App, mit der eine Filmdatenbank verwaltet werden kann.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-181">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="ba1ec-182">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="ba1ec-182">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ba1ec-183">Sie erstellen eine Razor Pages-Web-App.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-183">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="ba1ec-184">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-184">Run the app.</span></span>
> * <span data-ttu-id="ba1ec-185">Überprüfen Sie die Projektdateien.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-185">Examine the project files.</span></span>

<span data-ttu-id="ba1ec-186">Am Ende dieses Tutorials verfügen Sie über eine funktionsfähige Razor Pages-Web-App, auf der Sie in späteren Tutorials aufbauen werden.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-186">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="ba1ec-188">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="ba1ec-188">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ba1ec-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ba1ec-189">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ba1ec-190">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ba1ec-190">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ba1ec-191">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ba1ec-191">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="ba1ec-192">Erstellen einer Razor Pages-Web-App</span><span class="sxs-lookup"><span data-stu-id="ba1ec-192">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ba1ec-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ba1ec-193">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ba1ec-194">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-194">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="ba1ec-195">Erstellen Sie eine neue ASP.NET Core-Webanwendung, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-195">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="ba1ec-197">Nennen Sie das Projekt **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-197">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="ba1ec-198">Es ist wichtig, den Namen *RazorPagesMovie* zu verwenden, damit die Namespaces übereinstimmen, wenn Sie Code kopieren und einfügen.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-198">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/config.png)

* <span data-ttu-id="ba1ec-200">Klicken Sie in der Dropdownliste auf **ASP.NET Core 2.2**, auf **Webanwendung** und anschließend auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-200">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="ba1ec-202">Das folgende Startprojekt wird erstellt:</span><span class="sxs-lookup"><span data-stu-id="ba1ec-202">The following starter project is created:</span></span>

  ![Projektmappen-Explorer](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="ba1ec-204">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ba1ec-204">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ba1ec-205">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="ba1ec-205">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="ba1ec-206">Wechseln Sie zum Verzeichnis (`cd`), das das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-206">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="ba1ec-207">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="ba1ec-207">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="ba1ec-208">Der Befehl `dotnet new` erstellt ein neues Razor Pages-Projekt im Ordner *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-208">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="ba1ec-209">Der Befehl `code` öffnet den Ordner *RazorPagesMovie* in der aktuellen Instanz von Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-209">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="ba1ec-210">Wenn das OmniSharp-Flammensymbol in der Statusleiste grün angezeigt wird, wird ein Dialogfeld mit folgender Meldung angezeigt: **Required assets to build and debug are missing from 'RazorPagesMovie'. (Die erforderlichen Objekte zum Erstellen und Debuggen sind in „RazorPagesMovie“ nicht vorhanden. Sollen sie hinzugefügt werden?**</span><span class="sxs-lookup"><span data-stu-id="ba1ec-210">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="ba1ec-211">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-211">Select **Yes**.</span></span>

  <span data-ttu-id="ba1ec-212">Ein *VSCODE*-Verzeichnis, das die Dateien *launch.json* und *tasks.json* enthält, wird dem Stammverzeichnis des Projekts hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-212">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ba1ec-213">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ba1ec-213">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ba1ec-214">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-214">Select **File** > **New Solution**.</span></span>

![Neue Projektmappe in macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="ba1ec-216">Navigieren Sie in Visual Studio für Mac-Versionen vor Version 8.6 zu **.NET Core** > **App** > **Webanwendung** > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-216">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="ba1ec-217">Bei Version 8.6 oder höher klicken Sie auf **Web and Console** > **App** > **Webanwendung** > **Weiter** (Web und Konsole).</span><span class="sxs-lookup"><span data-stu-id="ba1ec-217">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

* <span data-ttu-id="ba1ec-218">Legen Sie im Dialogfeld **Neue ASP.NET Core-Web-API konfigurieren** das **Zielframework** auf **.NET Core 3.1** fest.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-218">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** to **.NET Core 3.1**.</span></span>

  ![Auswahl für .NET Core 3.0 in macOS](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="ba1ec-220">Nennen Sie das Projekt **RazorPagesMovie**, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-220">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="ba1ec-222">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="ba1ec-222">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ba1ec-223">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ba1ec-223">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ba1ec-224">Drücken Sie STRG+F5, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-224">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="ba1ec-225">Visual Studio startet [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) und führt die App aus.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-225">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="ba1ec-226">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-226">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="ba1ec-227">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-227">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="ba1ec-228">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-228">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="ba1ec-229">Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-229">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="ba1ec-230">Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-230">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="ba1ec-231">Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-231">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="ba1ec-233">Die folgende Abbildung zeigt die App, nachdem Sie das Einverständnis zur Nachverfolgung gegeben haben:</span><span class="sxs-lookup"><span data-stu-id="ba1ec-233">The following image shows the app after you give consent to tracking:</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="ba1ec-235">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ba1ec-235">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="ba1ec-236">Drücken Sie **STRG+F5**, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-236">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="ba1ec-237">Visual Studio Code startet [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-237">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="ba1ec-238">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-238">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="ba1ec-239">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-239">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="ba1ec-240">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-240">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="ba1ec-241">Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-241">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="ba1ec-242">Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-242">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="ba1ec-244">Die folgende Abbildung zeigt die App, nachdem Sie das Einverständnis zur Nachverfolgung gegeben haben:</span><span class="sxs-lookup"><span data-stu-id="ba1ec-244">The following image shows the app after you give consent to tracking:</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ba1ec-246">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ba1ec-246">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="ba1ec-247">Drücken Sie **CMD+OPT+F5** für eine Ausführung ohne Debugger.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-247">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="ba1ec-248">Visual Studio startet dann [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-248">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="ba1ec-249">Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-249">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="ba1ec-250">Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-250">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="ba1ec-252">Die folgende Abbildung zeigt die App, nachdem Sie das Einverständnis zur Nachverfolgung gegeben haben:</span><span class="sxs-lookup"><span data-stu-id="ba1ec-252">The following image shows the app after you give consent to tracking:</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="ba1ec-254">Überprüfen der Projektdateien</span><span class="sxs-lookup"><span data-stu-id="ba1ec-254">Examine the project files</span></span>

<span data-ttu-id="ba1ec-255">Es folgt eine Übersicht über die Hauptprojektordner und -dateien, mit denen Sie in späteren Tutorials arbeiten werden.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-255">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="ba1ec-256">Ordner „Seiten“</span><span class="sxs-lookup"><span data-stu-id="ba1ec-256">Pages folder</span></span>

<span data-ttu-id="ba1ec-257">Enthält Razor-Seiten und unterstützende Dateien.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-257">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="ba1ec-258">Jede Razor-Seite besteht aus einem Dateienpaar:</span><span class="sxs-lookup"><span data-stu-id="ba1ec-258">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="ba1ec-259">Eine *.cshtml*-Datei, die HTML-Markup mit C#-Code in Razor-Syntax enthält</span><span class="sxs-lookup"><span data-stu-id="ba1ec-259">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="ba1ec-260">Eine *.cshtml.cs*-Datei mit C# Code, in dem Seitenereignisse verarbeitet werden</span><span class="sxs-lookup"><span data-stu-id="ba1ec-260">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="ba1ec-261">Unterstützende Dateien haben Namen, die mit einem Unterstrich beginnen.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-261">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="ba1ec-262">Zum Beispiel sind in der Datei *_Layout.cshtml* Benutzeroberflächenelemente konfiguriert, die für alle Seiten gelten.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-262">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="ba1ec-263">Mit dieser Datei werden das Navigationsmenü oben auf der Seite und der Urheberrechtshinweis unten auf der Seite eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-263">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="ba1ec-264">Weitere Informationen finden Sie unter <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-264">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="ba1ec-265">Ordner „wwwroot“</span><span class="sxs-lookup"><span data-stu-id="ba1ec-265">wwwroot folder</span></span>

<span data-ttu-id="ba1ec-266">Enthält statische Dateien, z. B. HTML-Dateien, JavaScript-Dateien und CSS-Dateien.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-266">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="ba1ec-267">Weitere Informationen finden Sie unter <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-267">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="ba1ec-268">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="ba1ec-268">appSettings.json</span></span>

<span data-ttu-id="ba1ec-269">Enthält Konfigurationsdaten, z. B. Verbindungszeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-269">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="ba1ec-270">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-270">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="ba1ec-271">Program.cs</span><span class="sxs-lookup"><span data-stu-id="ba1ec-271">Program.cs</span></span>

<span data-ttu-id="ba1ec-272">Enthält den Einstiegspunkt für das Programm.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-272">Contains the entry point for the program.</span></span> <span data-ttu-id="ba1ec-273">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-273">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="ba1ec-274">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="ba1ec-274">Startup.cs</span></span>

<span data-ttu-id="ba1ec-275">Enthält Code, mit dem das App-Verhalten konfiguriert wird, beispielsweise, ob die App Zustimmung für Cookies erfordert.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-275">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="ba1ec-276">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="ba1ec-276">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ba1ec-277">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="ba1ec-277">Additional resources</span></span>

* [<span data-ttu-id="ba1ec-278">Dieses Tutorials auf YouTube</span><span class="sxs-lookup"><span data-stu-id="ba1ec-278">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="ba1ec-279">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="ba1ec-279">Next steps</span></span>

<span data-ttu-id="ba1ec-280">Wechseln Sie zum nächsten Tutorial in der Reihe:</span><span class="sxs-lookup"><span data-stu-id="ba1ec-280">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="ba1ec-281">Hinzufügen eines Modells</span><span class="sxs-lookup"><span data-stu-id="ba1ec-281">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

---
title: 'Tutorial: Erste Schritte mit Razor Pages in ASP.NET Core'
author: rick-anderson
description: Diese Reihe von Tutorials zeigt, wie Sie Razor Pages in ASP.NET Core verwenden können. Erfahren Sie, wie Sie ein Modell erstellen, Code für Razor-Seiten generieren, Entity Framework Core und SQL Server für den Datenzugriff verwenden, Suchfunktionen hinzufügen, Eingabeüberprüfung hinzufügen und Migrationen verwenden, um das Modell zu aktualisieren.
ms.author: riande
ms.date: 07/25/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 0cc00cb85b6054752417b82c783cfd4c306aeda5
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082577"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="c738d-104">Tutorial: Erste Schritte mit Razor Pages in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c738d-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="c738d-105">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c738d-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="c738d-106">Dies ist das erste Tutorial einer Reihe, in der Sie Grundlegendes zur Erstellung einer ASP.NET Core-Webapp mit Razor Pages erfahren.</span><span class="sxs-lookup"><span data-stu-id="c738d-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="c738d-107">Am Ende der Reihe verfügen Sie über eine App, mit der eine Filmdatenbank verwaltet werden kann.</span><span class="sxs-lookup"><span data-stu-id="c738d-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="c738d-108">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="c738d-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c738d-109">Erstellen einer Razor Pages-Web-App</span><span class="sxs-lookup"><span data-stu-id="c738d-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="c738d-110">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="c738d-110">Run the app.</span></span>
> * <span data-ttu-id="c738d-111">Überprüfen Sie die Projektdateien.</span><span class="sxs-lookup"><span data-stu-id="c738d-111">Examine the project files.</span></span>

<span data-ttu-id="c738d-112">Am Ende dieses Tutorials verfügen Sie über eine funktionsfähige Razor Pages-Web-App, auf der Sie in späteren Tutorials aufbauen werden.</span><span class="sxs-lookup"><span data-stu-id="c738d-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="c738d-114">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="c738d-114">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c738d-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c738d-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c738d-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c738d-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c738d-117">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="c738d-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="c738d-118">Erstellen einer Razor Pages-Web-App</span><span class="sxs-lookup"><span data-stu-id="c738d-118">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c738d-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c738d-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c738d-120">Wählen Sie in Visual Studio im Menü **Datei** die Option **Neu** > **Projekt** aus.</span><span class="sxs-lookup"><span data-stu-id="c738d-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="c738d-121">Erstellen Sie eine neue ASP.NET Core-Webanwendung, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="c738d-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="c738d-122">![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="c738d-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="c738d-123">Nennen Sie das Projekt **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="c738d-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="c738d-124">Es ist wichtig, den Namen *RazorPagesMovie* zu verwenden, damit die Namespaces übereinstimmen, wenn Sie Code kopieren und einfügen.</span><span class="sxs-lookup"><span data-stu-id="c738d-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="c738d-125">![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="c738d-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="c738d-126">Klicken Sie in der Dropdownliste auf **ASP.NET Core 3.0**, auf **Webanwendung** und anschließend auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="c738d-126">Select **ASP.NET Core 3.0** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="c738d-128">Das folgende Startprojekt wird erstellt:</span><span class="sxs-lookup"><span data-stu-id="c738d-128">The following starter project is created:</span></span>

  ![Projektmappen-Explorer](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c738d-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c738d-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c738d-131">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="c738d-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="c738d-132">Wechseln Sie zum Verzeichnis (`cd`), das das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="c738d-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="c738d-133">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="c738d-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="c738d-134">Der Befehl `dotnet new` erstellt ein neues Razor Pages-Projekt im Ordner *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="c738d-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="c738d-135">Der Befehl `code` öffnet den Ordner *RazorPagesMovie* in der aktuellen Instanz von Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="c738d-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="c738d-136">Wenn das OmniSharp-Flammensymbol in der Statusleiste grün angezeigt wird, wird ein Dialogfeld mit folgender Meldung angezeigt: **Required assets to build and debug are missing from 'RazorPagesMovie'. (Die erforderlichen Objekte zum Erstellen und Debuggen sind in „RazorPagesMovie“ nicht vorhanden. Sollen sie hinzugefügt werden?**</span><span class="sxs-lookup"><span data-stu-id="c738d-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="c738d-137">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="c738d-137">Select **Yes**.</span></span>

  <span data-ttu-id="c738d-138">Ein *VSCODE*-Verzeichnis, das die Dateien *launch.json* und *tasks.json* enthält, wird dem Stammverzeichnis des Projekts hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="c738d-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c738d-139">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="c738d-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c738d-140">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="c738d-140">Select **File** > **New Solution**.</span></span>

![Neue Projektmappe in macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="c738d-142">Wählen Sie **.NET Core** > **App** > **Web-Anwendung** > **Weiter** aus.</span><span class="sxs-lookup"><span data-stu-id="c738d-142">Select **.NET Core** > **App** > **Web Application** > **Next**.</span></span>

  ![Dialogfeld „Neue Projektmappe“ in macOS](razor-pages-start/_static/webapp.png)

* <span data-ttu-id="c738d-144">Wählen Sie im Dialogfeld **Configure your new ASP.NET Core Web API** (Neue ASP.NET Core-Web-API konfigurieren) für das **Zielframework** den Eintrag **.NET Core 3.0** aus.</span><span class="sxs-lookup"><span data-stu-id="c738d-144">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** to **.NET Core 3.0**.</span></span>

  ![Auswahl für .NET Core 3.0 in macOS](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="c738d-146">Nennen Sie das Projekt **RazorPagesMovie**, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="c738d-146">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)


## <a name="open-the-project"></a><span data-ttu-id="c738d-148">Öffnen des Projekts</span><span class="sxs-lookup"><span data-stu-id="c738d-148">Open the project</span></span>

<span data-ttu-id="c738d-149">Klicken Sie in Visual Studio auf **Datei > Öffnen**, und wählen Sie dann die Datei *RazorPagesMovie.csproj* aus.</span><span class="sxs-lookup"><span data-stu-id="c738d-149">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="c738d-150">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="c738d-150">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c738d-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c738d-151">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c738d-152">Drücken Sie STRG+F5, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="c738d-152">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="c738d-153">Visual Studio startet [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) und führt die App aus.</span><span class="sxs-lookup"><span data-stu-id="c738d-153">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="c738d-154">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="c738d-154">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="c738d-155">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="c738d-155">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="c738d-156">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="c738d-156">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="c738d-157">Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.</span><span class="sxs-lookup"><span data-stu-id="c738d-157">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
 
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c738d-158">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c738d-158">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="c738d-159">Drücken Sie **STRG+F5**, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="c738d-159">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="c738d-160">Visual Studio Code startet [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="c738d-160">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="c738d-161">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="c738d-161">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="c738d-162">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="c738d-162">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="c738d-163">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="c738d-163">Localhost only serves web requests from the local computer.</span></span>

  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c738d-164">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="c738d-164">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="c738d-165">Drücken Sie **ALT+CMD+EINGABETASTE**, um die Ausführung zu starten, ohne dabei den Debugger zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="c738d-165">Press **Alt-Cmd-Enter** to run without the debugger.</span></span> <span data-ttu-id="c738d-166">Alternativ können Sie auch in der Menüleiste auf „Ausführen“ und anschließend auf „Ohne Debugger starten“ klicken.</span><span class="sxs-lookup"><span data-stu-id="c738d-166">Alternatively, navigate to the menu bar and go to Run>Start Without Debugging.</span></span>

  <span data-ttu-id="c738d-167">Visual Studio startet dann [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="c738d-167">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="c738d-168">Überprüfen der Projektdateien</span><span class="sxs-lookup"><span data-stu-id="c738d-168">Examine the project files</span></span>

<span data-ttu-id="c738d-169">Es folgt eine Übersicht über die Hauptprojektordner und -dateien, mit denen Sie in späteren Tutorials arbeiten werden.</span><span class="sxs-lookup"><span data-stu-id="c738d-169">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="c738d-170">Ordner „Seiten“</span><span class="sxs-lookup"><span data-stu-id="c738d-170">Pages folder</span></span>

<span data-ttu-id="c738d-171">Enthält Razor-Seiten und unterstützende Dateien.</span><span class="sxs-lookup"><span data-stu-id="c738d-171">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="c738d-172">Jede Razor-Seite besteht aus einem Dateienpaar:</span><span class="sxs-lookup"><span data-stu-id="c738d-172">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="c738d-173">Eine *.cshtml*-Datei, die HTML-Markup mit C#-Code in Razor-Syntax enthält</span><span class="sxs-lookup"><span data-stu-id="c738d-173">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="c738d-174">Eine *.cshtml.cs*-Datei mit C# Code, in dem Seitenereignisse verarbeitet werden</span><span class="sxs-lookup"><span data-stu-id="c738d-174">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="c738d-175">Unterstützende Dateien haben Namen, die mit einem Unterstrich beginnen.</span><span class="sxs-lookup"><span data-stu-id="c738d-175">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="c738d-176">Zum Beispiel sind in der Datei *_Layout.cshtml* Benutzeroberflächenelemente konfiguriert, die für alle Seiten gelten.</span><span class="sxs-lookup"><span data-stu-id="c738d-176">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="c738d-177">Mit dieser Datei werden das Navigationsmenü oben auf der Seite und der Urheberrechtshinweis unten auf der Seite eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="c738d-177">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="c738d-178">Weitere Informationen finden Sie unter <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="c738d-178">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="c738d-179">Ordner „wwwroot“</span><span class="sxs-lookup"><span data-stu-id="c738d-179">wwwroot folder</span></span>

<span data-ttu-id="c738d-180">Enthält statische Dateien, z. B. HTML-Dateien, JavaScript-Dateien und CSS-Dateien.</span><span class="sxs-lookup"><span data-stu-id="c738d-180">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="c738d-181">Weitere Informationen finden Sie unter <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="c738d-181">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="c738d-182">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="c738d-182">appSettings.json</span></span>

<span data-ttu-id="c738d-183">Enthält Konfigurationsdaten, z. B. Verbindungszeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="c738d-183">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="c738d-184">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="c738d-184">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="c738d-185">Program.cs</span><span class="sxs-lookup"><span data-stu-id="c738d-185">Program.cs</span></span>

<span data-ttu-id="c738d-186">Enthält den Einstiegspunkt für das Programm.</span><span class="sxs-lookup"><span data-stu-id="c738d-186">Contains the entry point for the program.</span></span> <span data-ttu-id="c738d-187">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="c738d-187">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="c738d-188">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="c738d-188">Startup.cs</span></span>

<span data-ttu-id="c738d-189">Diese Datei enthält Code, mit dem das App-Verhalten konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="c738d-189">Contains code that configures app behavior.</span></span> <span data-ttu-id="c738d-190">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="c738d-190">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="c738d-191">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="c738d-191">Next steps</span></span>

<span data-ttu-id="c738d-192">Wechseln Sie zum nächsten Tutorial in der Reihe:</span><span class="sxs-lookup"><span data-stu-id="c738d-192">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="c738d-193">Hinzufügen eines Modells</span><span class="sxs-lookup"><span data-stu-id="c738d-193">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c738d-194">Dies ist das erste Tutorial aus einer Reihe.</span><span class="sxs-lookup"><span data-stu-id="c738d-194">This is the first tutorial of a series.</span></span> <span data-ttu-id="c738d-195">[In der Reihe](xref:tutorials/razor-pages/index) lernen Sie die Grundlagen zum Erstellen einer Razor Pages-Web-App mit ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c738d-195">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="c738d-196">Am Ende der Reihe verfügen Sie über eine App, mit der eine Filmdatenbank verwaltet werden kann.</span><span class="sxs-lookup"><span data-stu-id="c738d-196">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="c738d-197">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="c738d-197">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c738d-198">Erstellen einer Razor Pages-Web-App</span><span class="sxs-lookup"><span data-stu-id="c738d-198">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="c738d-199">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="c738d-199">Run the app.</span></span>
> * <span data-ttu-id="c738d-200">Überprüfen Sie die Projektdateien.</span><span class="sxs-lookup"><span data-stu-id="c738d-200">Examine the project files.</span></span>

<span data-ttu-id="c738d-201">Am Ende dieses Tutorials verfügen Sie über eine funktionsfähige Razor Pages-Web-App, auf der Sie in späteren Tutorials aufbauen werden.</span><span class="sxs-lookup"><span data-stu-id="c738d-201">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="c738d-203">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="c738d-203">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c738d-204">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c738d-204">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c738d-205">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c738d-205">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c738d-206">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="c738d-206">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="c738d-207">Erstellen einer Razor Pages-Web-App</span><span class="sxs-lookup"><span data-stu-id="c738d-207">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c738d-208">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c738d-208">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c738d-209">Wählen Sie in Visual Studio im Menü **Datei** die Option **Neu** > **Projekt** aus.</span><span class="sxs-lookup"><span data-stu-id="c738d-209">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="c738d-210">Erstellen Sie eine neue ASP.NET Core-Webanwendung, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="c738d-210">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="c738d-212">Nennen Sie das Projekt **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="c738d-212">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="c738d-213">Es ist wichtig, den Namen *RazorPagesMovie* zu verwenden, damit die Namespaces übereinstimmen, wenn Sie Code kopieren und einfügen.</span><span class="sxs-lookup"><span data-stu-id="c738d-213">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/config.png)

* <span data-ttu-id="c738d-215">Klicken Sie in der Dropdownliste auf **ASP.NET Core 2.2**, auf **Webanwendung** und anschließend auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="c738d-215">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="c738d-217">Das folgende Startprojekt wird erstellt:</span><span class="sxs-lookup"><span data-stu-id="c738d-217">The following starter project is created:</span></span>

  ![Projektmappen-Explorer](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c738d-219">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c738d-219">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c738d-220">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="c738d-220">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="c738d-221">Wechseln Sie zum Verzeichnis (`cd`), das das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="c738d-221">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="c738d-222">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="c738d-222">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="c738d-223">Der Befehl `dotnet new` erstellt ein neues Razor Pages-Projekt im Ordner *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="c738d-223">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="c738d-224">Der Befehl `code` öffnet den Ordner *RazorPagesMovie* in der aktuellen Instanz von Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="c738d-224">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="c738d-225">Wenn das OmniSharp-Flammensymbol in der Statusleiste grün angezeigt wird, wird ein Dialogfeld mit folgender Meldung angezeigt: **Required assets to build and debug are missing from 'RazorPagesMovie'. (Die erforderlichen Objekte zum Erstellen und Debuggen sind in „RazorPagesMovie“ nicht vorhanden. Sollen sie hinzugefügt werden?**</span><span class="sxs-lookup"><span data-stu-id="c738d-225">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="c738d-226">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="c738d-226">Select **Yes**.</span></span>

  <span data-ttu-id="c738d-227">Ein *VSCODE*-Verzeichnis, das die Dateien *launch.json* und *tasks.json* enthält, wird dem Stammverzeichnis des Projekts hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="c738d-227">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c738d-228">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="c738d-228">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="c738d-229">Führen Sie über ein Terminal den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="c738d-229">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```dotnetcli
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="c738d-230">Diese Befehle verwenden die [.NET Core-CLI](/dotnet/core/tools/dotnet), um ein Razor Pages-Projekt zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="c738d-230">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="c738d-231">Öffnen des Projekts</span><span class="sxs-lookup"><span data-stu-id="c738d-231">Open the project</span></span>

<span data-ttu-id="c738d-232">Klicken Sie in Visual Studio auf **Datei > Öffnen**, und wählen Sie dann die Datei *RazorPagesMovie.csproj* aus.</span><span class="sxs-lookup"><span data-stu-id="c738d-232">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="c738d-233">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="c738d-233">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c738d-234">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c738d-234">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c738d-235">Drücken Sie STRG+F5, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="c738d-235">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="c738d-236">Visual Studio startet [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) und führt die App aus.</span><span class="sxs-lookup"><span data-stu-id="c738d-236">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="c738d-237">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="c738d-237">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="c738d-238">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="c738d-238">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="c738d-239">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="c738d-239">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="c738d-240">Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.</span><span class="sxs-lookup"><span data-stu-id="c738d-240">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="c738d-241">Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="c738d-241">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="c738d-242">Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.</span><span class="sxs-lookup"><span data-stu-id="c738d-242">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="c738d-244">Die folgende Abbildung zeigt die App, nachdem Sie das Einverständnis zur Nachverfolgung gegeben haben:</span><span class="sxs-lookup"><span data-stu-id="c738d-244">The following image shows the app after you give consent to tracking:</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c738d-246">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c738d-246">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="c738d-247">Drücken Sie **STRG+F5**, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="c738d-247">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="c738d-248">Visual Studio Code startet [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="c738d-248">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="c738d-249">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="c738d-249">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="c738d-250">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="c738d-250">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="c738d-251">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="c738d-251">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="c738d-252">Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="c738d-252">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="c738d-253">Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.</span><span class="sxs-lookup"><span data-stu-id="c738d-253">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="c738d-255">Die folgende Abbildung zeigt die App, nachdem Sie das Einverständnis zur Nachverfolgung gegeben haben:</span><span class="sxs-lookup"><span data-stu-id="c738d-255">The following image shows the app after you give consent to tracking:</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c738d-257">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="c738d-257">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="c738d-258">Drücken Sie **CMD+OPT+F5** für eine Ausführung ohne Debugger.</span><span class="sxs-lookup"><span data-stu-id="c738d-258">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="c738d-259">Visual Studio startet dann [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="c738d-259">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="c738d-260">Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="c738d-260">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="c738d-261">Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.</span><span class="sxs-lookup"><span data-stu-id="c738d-261">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="c738d-263">Die folgende Abbildung zeigt die App, nachdem Sie das Einverständnis zur Nachverfolgung gegeben haben:</span><span class="sxs-lookup"><span data-stu-id="c738d-263">The following image shows the app after you give consent to tracking:</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="c738d-265">Überprüfen der Projektdateien</span><span class="sxs-lookup"><span data-stu-id="c738d-265">Examine the project files</span></span>

<span data-ttu-id="c738d-266">Es folgt eine Übersicht über die Hauptprojektordner und -dateien, mit denen Sie in späteren Tutorials arbeiten werden.</span><span class="sxs-lookup"><span data-stu-id="c738d-266">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="c738d-267">Ordner „Seiten“</span><span class="sxs-lookup"><span data-stu-id="c738d-267">Pages folder</span></span>

<span data-ttu-id="c738d-268">Enthält Razor-Seiten und unterstützende Dateien.</span><span class="sxs-lookup"><span data-stu-id="c738d-268">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="c738d-269">Jede Razor-Seite besteht aus einem Dateienpaar:</span><span class="sxs-lookup"><span data-stu-id="c738d-269">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="c738d-270">Eine *.cshtml*-Datei, die HTML-Markup mit C#-Code in Razor-Syntax enthält</span><span class="sxs-lookup"><span data-stu-id="c738d-270">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="c738d-271">Eine *.cshtml.cs*-Datei mit C# Code, in dem Seitenereignisse verarbeitet werden</span><span class="sxs-lookup"><span data-stu-id="c738d-271">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="c738d-272">Unterstützende Dateien haben Namen, die mit einem Unterstrich beginnen.</span><span class="sxs-lookup"><span data-stu-id="c738d-272">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="c738d-273">Zum Beispiel sind in der Datei *_Layout.cshtml* Benutzeroberflächenelemente konfiguriert, die für alle Seiten gelten.</span><span class="sxs-lookup"><span data-stu-id="c738d-273">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="c738d-274">Mit dieser Datei werden das Navigationsmenü oben auf der Seite und der Urheberrechtshinweis unten auf der Seite eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="c738d-274">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="c738d-275">Weitere Informationen finden Sie unter <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="c738d-275">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="c738d-276">Ordner „wwwroot“</span><span class="sxs-lookup"><span data-stu-id="c738d-276">wwwroot folder</span></span>

<span data-ttu-id="c738d-277">Enthält statische Dateien, z. B. HTML-Dateien, JavaScript-Dateien und CSS-Dateien.</span><span class="sxs-lookup"><span data-stu-id="c738d-277">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="c738d-278">Weitere Informationen finden Sie unter <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="c738d-278">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="c738d-279">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="c738d-279">appSettings.json</span></span>

<span data-ttu-id="c738d-280">Enthält Konfigurationsdaten, z. B. Verbindungszeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="c738d-280">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="c738d-281">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="c738d-281">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="c738d-282">Program.cs</span><span class="sxs-lookup"><span data-stu-id="c738d-282">Program.cs</span></span>

<span data-ttu-id="c738d-283">Enthält den Einstiegspunkt für das Programm.</span><span class="sxs-lookup"><span data-stu-id="c738d-283">Contains the entry point for the program.</span></span> <span data-ttu-id="c738d-284">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="c738d-284">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="c738d-285">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="c738d-285">Startup.cs</span></span>

<span data-ttu-id="c738d-286">Enthält Code, mit dem das App-Verhalten konfiguriert wird, beispielsweise, ob die App Zustimmung für Cookies erfordert.</span><span class="sxs-lookup"><span data-stu-id="c738d-286">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="c738d-287">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="c738d-287">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c738d-288">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="c738d-288">Additional resources</span></span>

* [<span data-ttu-id="c738d-289">Dieses Tutorials auf YouTube</span><span class="sxs-lookup"><span data-stu-id="c738d-289">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="c738d-290">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="c738d-290">Next steps</span></span>

<span data-ttu-id="c738d-291">Wechseln Sie zum nächsten Tutorial in der Reihe:</span><span class="sxs-lookup"><span data-stu-id="c738d-291">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="c738d-292">Hinzufügen eines Modells</span><span class="sxs-lookup"><span data-stu-id="c738d-292">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

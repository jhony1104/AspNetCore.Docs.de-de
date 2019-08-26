---
title: 'Tutorial: Erste Schritte mit Razor Pages in ASP.NET Core'
author: rick-anderson
description: Diese Reihe von Tutorials zeigt, wie Sie Razor Pages in ASP.NET Core verwenden können. Erfahren Sie, wie Sie ein Modell erstellen, Code für Razor-Seiten generieren, Entity Framework Core und SQL Server für den Datenzugriff verwenden, Suchfunktionen hinzufügen, Eingabeüberprüfung hinzufügen und Migrationen verwenden, um das Modell zu aktualisieren.
ms.author: riande
ms.date: 07/25/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 67a5fcee0a37861fd39a018443edbc0b9e513213
ms.sourcegitcommit: 7a46973998623aead757ad386fe33602b1658793
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69487652"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="145e7-104">Tutorial: Erste Schritte mit Razor Pages in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="145e7-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="145e7-105">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="145e7-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="145e7-106">Dies ist das erste Tutorial einer Reihe, in der Sie Grundlegendes zur Erstellung einer ASP.NET Core-Webapp mit Razor Pages erfahren.</span><span class="sxs-lookup"><span data-stu-id="145e7-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="145e7-107">Am Ende der Reihe verfügen Sie über eine App, mit der eine Filmdatenbank verwaltet werden kann.</span><span class="sxs-lookup"><span data-stu-id="145e7-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="145e7-108">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="145e7-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="145e7-109">Erstellen einer Razor Pages-Web-App</span><span class="sxs-lookup"><span data-stu-id="145e7-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="145e7-110">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="145e7-110">Run the app.</span></span>
> * <span data-ttu-id="145e7-111">Überprüfen Sie die Projektdateien.</span><span class="sxs-lookup"><span data-stu-id="145e7-111">Examine the project files.</span></span>

<span data-ttu-id="145e7-112">Am Ende dieses Tutorials verfügen Sie über eine funktionsfähige Razor Pages-Web-App, auf der Sie in späteren Tutorials aufbauen werden.</span><span class="sxs-lookup"><span data-stu-id="145e7-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="145e7-114">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="145e7-114">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="145e7-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="145e7-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="145e7-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="145e7-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="145e7-117">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="145e7-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="145e7-118">Erstellen einer Razor Pages-Web-App</span><span class="sxs-lookup"><span data-stu-id="145e7-118">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="145e7-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="145e7-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="145e7-120">Wählen Sie in Visual Studio im Menü **Datei** die Option **Neu** > **Projekt** aus.</span><span class="sxs-lookup"><span data-stu-id="145e7-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="145e7-121">Erstellen Sie eine neue ASP.NET Core-Webanwendung, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="145e7-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="145e7-122">![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="145e7-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="145e7-123">Nennen Sie das Projekt **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="145e7-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="145e7-124">Es ist wichtig, den Namen *RazorPagesMovie* zu verwenden, damit die Namespaces übereinstimmen, wenn Sie Code kopieren und einfügen.</span><span class="sxs-lookup"><span data-stu-id="145e7-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="145e7-125">![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="145e7-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="145e7-126">Klicken Sie in der Dropdownliste auf **ASP.NET Core 3.0**, auf **Webanwendung** und anschließend auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="145e7-126">Select **ASP.NET Core 3.0** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="145e7-128">Das folgende Startprojekt wird erstellt:</span><span class="sxs-lookup"><span data-stu-id="145e7-128">The following starter project is created:</span></span>

  ![Projektmappen-Explorer](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="145e7-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="145e7-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="145e7-131">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="145e7-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="145e7-132">Wechseln Sie zum Verzeichnis (`cd`), das das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="145e7-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="145e7-133">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="145e7-133">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="145e7-134">Der Befehl `dotnet new` erstellt ein neues Razor Pages-Projekt im Ordner *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="145e7-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="145e7-135">Der Befehl `code` öffnet den Ordner *RazorPagesMovie* in der aktuellen Instanz von Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="145e7-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="145e7-136">Wenn das OmniSharp-Flammensymbol in der Statusleiste grün angezeigt wird, wird ein Dialogfeld mit folgender Meldung angezeigt: **Required assets to build and debug are missing from 'RazorPagesMovie'. (Die erforderlichen Objekte zum Erstellen und Debuggen sind in „RazorPagesMovie“ nicht vorhanden. Sollen sie hinzugefügt werden?**</span><span class="sxs-lookup"><span data-stu-id="145e7-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="145e7-137">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="145e7-137">Select **Yes**.</span></span>

  <span data-ttu-id="145e7-138">Ein *VSCODE*-Verzeichnis, das die Dateien *launch.json* und *tasks.json* enthält, wird dem Stammverzeichnis des Projekts hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="145e7-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="145e7-139">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="145e7-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="145e7-140">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="145e7-140">Select **File** > **New Solution**.</span></span>

![Neue Projektmappe in macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="145e7-142">Wählen Sie **.NET Core** > **App** > **Web-Anwendung** > **Weiter** aus.</span><span class="sxs-lookup"><span data-stu-id="145e7-142">Select **.NET Core** > **App** > **Web Application** > **Next**.</span></span>

  ![Dialogfeld „Neue Projektmappe“ in macOS](razor-pages-start/_static/webapp.png)

* <span data-ttu-id="145e7-144">Wählen Sie im Dialogfeld **Configure your new ASP.NET Core Web API** (Neue ASP.NET Core-Web-API konfigurieren) für das **Zielframework** den Eintrag **.NET Core 3.0** aus.</span><span class="sxs-lookup"><span data-stu-id="145e7-144">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** to **.NET Core 3.0**.</span></span>

  ![Auswahl für .NET Core 3.0 in macOS](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="145e7-146">Nennen Sie das Projekt **RazorPagesMovie**, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="145e7-146">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)


## <a name="open-the-project"></a><span data-ttu-id="145e7-148">Öffnen des Projekts</span><span class="sxs-lookup"><span data-stu-id="145e7-148">Open the project</span></span>

<span data-ttu-id="145e7-149">Klicken Sie in Visual Studio auf **Datei > Öffnen**, und wählen Sie dann die Datei *RazorPagesMovie.csproj* aus.</span><span class="sxs-lookup"><span data-stu-id="145e7-149">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="145e7-150">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="145e7-150">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="145e7-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="145e7-151">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="145e7-152">Drücken Sie STRG+F5, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="145e7-152">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="145e7-153">Visual Studio startet [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) und führt die App aus.</span><span class="sxs-lookup"><span data-stu-id="145e7-153">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="145e7-154">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="145e7-154">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="145e7-155">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="145e7-155">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="145e7-156">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="145e7-156">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="145e7-157">Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.</span><span class="sxs-lookup"><span data-stu-id="145e7-157">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
 
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="145e7-158">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="145e7-158">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="145e7-159">Drücken Sie **STRG+F5**, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="145e7-159">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="145e7-160">Visual Studio Code startet [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="145e7-160">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="145e7-161">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="145e7-161">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="145e7-162">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="145e7-162">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="145e7-163">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="145e7-163">Localhost only serves web requests from the local computer.</span></span>

  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="145e7-164">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="145e7-164">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="145e7-165">Drücken Sie **ALT+CMD+EINGABETASTE**, um die Ausführung zu starten, ohne dabei den Debugger zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="145e7-165">Press **Alt-Cmd-Enter** to run without the debugger.</span></span> <span data-ttu-id="145e7-166">Alternativ können Sie auch in der Menüleiste auf „Ausführen“ und anschließend auf „Ohne Debugger starten“ klicken.</span><span class="sxs-lookup"><span data-stu-id="145e7-166">Alternatively, navigate to the menu bar and go to Run>Start Without Debugging.</span></span>

  <span data-ttu-id="145e7-167">Visual Studio startet dann [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="145e7-167">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="145e7-168">Überprüfen der Projektdateien</span><span class="sxs-lookup"><span data-stu-id="145e7-168">Examine the project files</span></span>

<span data-ttu-id="145e7-169">Es folgt eine Übersicht über die Hauptprojektordner und -dateien, mit denen Sie in späteren Tutorials arbeiten werden.</span><span class="sxs-lookup"><span data-stu-id="145e7-169">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="145e7-170">Ordner „Seiten“</span><span class="sxs-lookup"><span data-stu-id="145e7-170">Pages folder</span></span>

<span data-ttu-id="145e7-171">Enthält Razor-Seiten und unterstützende Dateien.</span><span class="sxs-lookup"><span data-stu-id="145e7-171">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="145e7-172">Jede Razor-Seite besteht aus einem Dateienpaar:</span><span class="sxs-lookup"><span data-stu-id="145e7-172">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="145e7-173">Eine *.cshtml*-Datei, die HTML-Markup mit C#-Code in Razor-Syntax enthält</span><span class="sxs-lookup"><span data-stu-id="145e7-173">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="145e7-174">Eine *.cshtml.cs*-Datei mit C# Code, in dem Seitenereignisse verarbeitet werden</span><span class="sxs-lookup"><span data-stu-id="145e7-174">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="145e7-175">Unterstützende Dateien haben Namen, die mit einem Unterstrich beginnen.</span><span class="sxs-lookup"><span data-stu-id="145e7-175">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="145e7-176">Zum Beispiel sind in der Datei *_Layout.cshtml* Benutzeroberflächenelemente konfiguriert, die für alle Seiten gelten.</span><span class="sxs-lookup"><span data-stu-id="145e7-176">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="145e7-177">Mit dieser Datei werden das Navigationsmenü oben auf der Seite und der Urheberrechtshinweis unten auf der Seite eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="145e7-177">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="145e7-178">Weitere Informationen finden Sie unter <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="145e7-178">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="145e7-179">Ordner „wwwroot“</span><span class="sxs-lookup"><span data-stu-id="145e7-179">wwwroot folder</span></span>

<span data-ttu-id="145e7-180">Enthält statische Dateien, z. B. HTML-Dateien, JavaScript-Dateien und CSS-Dateien.</span><span class="sxs-lookup"><span data-stu-id="145e7-180">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="145e7-181">Weitere Informationen finden Sie unter <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="145e7-181">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="145e7-182">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="145e7-182">appSettings.json</span></span>

<span data-ttu-id="145e7-183">Enthält Konfigurationsdaten, z. B. Verbindungszeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="145e7-183">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="145e7-184">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="145e7-184">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="145e7-185">Program.cs</span><span class="sxs-lookup"><span data-stu-id="145e7-185">Program.cs</span></span>

<span data-ttu-id="145e7-186">Enthält den Einstiegspunkt für das Programm.</span><span class="sxs-lookup"><span data-stu-id="145e7-186">Contains the entry point for the program.</span></span> <span data-ttu-id="145e7-187">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="145e7-187">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="145e7-188">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="145e7-188">Startup.cs</span></span>

<span data-ttu-id="145e7-189">Diese Datei enthält Code, mit dem das App-Verhalten konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="145e7-189">Contains code that configures app behavior.</span></span> <span data-ttu-id="145e7-190">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="145e7-190">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="145e7-191">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="145e7-191">Next steps</span></span>

<span data-ttu-id="145e7-192">Wechseln Sie zum nächsten Tutorial in der Reihe:</span><span class="sxs-lookup"><span data-stu-id="145e7-192">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="145e7-193">Hinzufügen eines Modells</span><span class="sxs-lookup"><span data-stu-id="145e7-193">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="145e7-194">Dies ist das erste Tutorial aus einer Reihe.</span><span class="sxs-lookup"><span data-stu-id="145e7-194">This is the first tutorial of a series.</span></span> <span data-ttu-id="145e7-195">[In der Reihe](xref:tutorials/razor-pages/index) lernen Sie die Grundlagen zum Erstellen einer Razor Pages-Web-App mit ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="145e7-195">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="145e7-196">Am Ende der Reihe verfügen Sie über eine App, mit der eine Filmdatenbank verwaltet werden kann.</span><span class="sxs-lookup"><span data-stu-id="145e7-196">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="145e7-197">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="145e7-197">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="145e7-198">Erstellen einer Razor Pages-Web-App</span><span class="sxs-lookup"><span data-stu-id="145e7-198">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="145e7-199">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="145e7-199">Run the app.</span></span>
> * <span data-ttu-id="145e7-200">Überprüfen Sie die Projektdateien.</span><span class="sxs-lookup"><span data-stu-id="145e7-200">Examine the project files.</span></span>

<span data-ttu-id="145e7-201">Am Ende dieses Tutorials verfügen Sie über eine funktionsfähige Razor Pages-Web-App, auf der Sie in späteren Tutorials aufbauen werden.</span><span class="sxs-lookup"><span data-stu-id="145e7-201">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="145e7-203">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="145e7-203">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="145e7-204">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="145e7-204">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="145e7-205">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="145e7-205">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="145e7-206">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="145e7-206">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="145e7-207">Erstellen einer Razor Pages-Web-App</span><span class="sxs-lookup"><span data-stu-id="145e7-207">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="145e7-208">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="145e7-208">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="145e7-209">Wählen Sie in Visual Studio im Menü **Datei** die Option **Neu** > **Projekt** aus.</span><span class="sxs-lookup"><span data-stu-id="145e7-209">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="145e7-210">Erstellen Sie eine neue ASP.NET Core-Webanwendung, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="145e7-210">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="145e7-212">Nennen Sie das Projekt **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="145e7-212">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="145e7-213">Es ist wichtig, den Namen *RazorPagesMovie* zu verwenden, damit die Namespaces übereinstimmen, wenn Sie Code kopieren und einfügen.</span><span class="sxs-lookup"><span data-stu-id="145e7-213">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/config.png)

* <span data-ttu-id="145e7-215">Klicken Sie in der Dropdownliste auf **ASP.NET Core 2.2**, auf **Webanwendung** und anschließend auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="145e7-215">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="145e7-217">Das folgende Startprojekt wird erstellt:</span><span class="sxs-lookup"><span data-stu-id="145e7-217">The following starter project is created:</span></span>

  ![Projektmappen-Explorer](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="145e7-219">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="145e7-219">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="145e7-220">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="145e7-220">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="145e7-221">Wechseln Sie zum Verzeichnis (`cd`), das das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="145e7-221">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="145e7-222">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="145e7-222">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="145e7-223">Der Befehl `dotnet new` erstellt ein neues Razor Pages-Projekt im Ordner *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="145e7-223">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="145e7-224">Der Befehl `code` öffnet den Ordner *RazorPagesMovie* in der aktuellen Instanz von Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="145e7-224">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="145e7-225">Wenn das OmniSharp-Flammensymbol in der Statusleiste grün angezeigt wird, wird ein Dialogfeld mit folgender Meldung angezeigt: **Required assets to build and debug are missing from 'RazorPagesMovie'. (Die erforderlichen Objekte zum Erstellen und Debuggen sind in „RazorPagesMovie“ nicht vorhanden. Sollen sie hinzugefügt werden?**</span><span class="sxs-lookup"><span data-stu-id="145e7-225">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="145e7-226">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="145e7-226">Select **Yes**.</span></span>

  <span data-ttu-id="145e7-227">Ein *VSCODE*-Verzeichnis, das die Dateien *launch.json* und *tasks.json* enthält, wird dem Stammverzeichnis des Projekts hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="145e7-227">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="145e7-228">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="145e7-228">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="145e7-229">Führen Sie über ein Terminal den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="145e7-229">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="145e7-230">Diese Befehle verwenden die [.NET Core-CLI](/dotnet/core/tools/dotnet), um ein Razor Pages-Projekt zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="145e7-230">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="145e7-231">Öffnen des Projekts</span><span class="sxs-lookup"><span data-stu-id="145e7-231">Open the project</span></span>

<span data-ttu-id="145e7-232">Klicken Sie in Visual Studio auf **Datei > Öffnen**, und wählen Sie dann die Datei *RazorPagesMovie.csproj* aus.</span><span class="sxs-lookup"><span data-stu-id="145e7-232">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="145e7-233">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="145e7-233">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="145e7-234">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="145e7-234">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="145e7-235">Drücken Sie STRG+F5, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="145e7-235">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="145e7-236">Visual Studio startet [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) und führt die App aus.</span><span class="sxs-lookup"><span data-stu-id="145e7-236">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="145e7-237">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="145e7-237">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="145e7-238">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="145e7-238">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="145e7-239">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="145e7-239">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="145e7-240">Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.</span><span class="sxs-lookup"><span data-stu-id="145e7-240">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="145e7-241">Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="145e7-241">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="145e7-242">Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.</span><span class="sxs-lookup"><span data-stu-id="145e7-242">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="145e7-244">Die folgende Abbildung zeigt die App, nachdem Sie das Einverständnis zur Nachverfolgung gegeben haben:</span><span class="sxs-lookup"><span data-stu-id="145e7-244">The following image shows the app after you give consent to tracking:</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="145e7-246">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="145e7-246">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="145e7-247">Drücken Sie **STRG+F5**, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="145e7-247">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="145e7-248">Visual Studio Code startet [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="145e7-248">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="145e7-249">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="145e7-249">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="145e7-250">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="145e7-250">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="145e7-251">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="145e7-251">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="145e7-252">Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="145e7-252">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="145e7-253">Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.</span><span class="sxs-lookup"><span data-stu-id="145e7-253">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="145e7-255">Die folgende Abbildung zeigt die App, nachdem Sie das Einverständnis zur Nachverfolgung gegeben haben:</span><span class="sxs-lookup"><span data-stu-id="145e7-255">The following image shows the app after you give consent to tracking:</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="145e7-257">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="145e7-257">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="145e7-258">Drücken Sie **CMD+OPT+F5** für eine Ausführung ohne Debugger.</span><span class="sxs-lookup"><span data-stu-id="145e7-258">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="145e7-259">Visual Studio startet dann [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="145e7-259">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="145e7-260">Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="145e7-260">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="145e7-261">Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.</span><span class="sxs-lookup"><span data-stu-id="145e7-261">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="145e7-263">Die folgende Abbildung zeigt die App, nachdem Sie das Einverständnis zur Nachverfolgung gegeben haben:</span><span class="sxs-lookup"><span data-stu-id="145e7-263">The following image shows the app after you give consent to tracking:</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="145e7-265">Überprüfen der Projektdateien</span><span class="sxs-lookup"><span data-stu-id="145e7-265">Examine the project files</span></span>

<span data-ttu-id="145e7-266">Es folgt eine Übersicht über die Hauptprojektordner und -dateien, mit denen Sie in späteren Tutorials arbeiten werden.</span><span class="sxs-lookup"><span data-stu-id="145e7-266">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="145e7-267">Ordner „Seiten“</span><span class="sxs-lookup"><span data-stu-id="145e7-267">Pages folder</span></span>

<span data-ttu-id="145e7-268">Enthält Razor-Seiten und unterstützende Dateien.</span><span class="sxs-lookup"><span data-stu-id="145e7-268">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="145e7-269">Jede Razor-Seite besteht aus einem Dateienpaar:</span><span class="sxs-lookup"><span data-stu-id="145e7-269">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="145e7-270">Eine *.cshtml*-Datei, die HTML-Markup mit C#-Code in Razor-Syntax enthält</span><span class="sxs-lookup"><span data-stu-id="145e7-270">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="145e7-271">Eine *.cshtml.cs*-Datei mit C# Code, in dem Seitenereignisse verarbeitet werden</span><span class="sxs-lookup"><span data-stu-id="145e7-271">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="145e7-272">Unterstützende Dateien haben Namen, die mit einem Unterstrich beginnen.</span><span class="sxs-lookup"><span data-stu-id="145e7-272">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="145e7-273">Zum Beispiel sind in der Datei *_Layout.cshtml* Benutzeroberflächenelemente konfiguriert, die für alle Seiten gelten.</span><span class="sxs-lookup"><span data-stu-id="145e7-273">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="145e7-274">Mit dieser Datei werden das Navigationsmenü oben auf der Seite und der Urheberrechtshinweis unten auf der Seite eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="145e7-274">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="145e7-275">Weitere Informationen finden Sie unter <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="145e7-275">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="145e7-276">Ordner „wwwroot“</span><span class="sxs-lookup"><span data-stu-id="145e7-276">wwwroot folder</span></span>

<span data-ttu-id="145e7-277">Enthält statische Dateien, z. B. HTML-Dateien, JavaScript-Dateien und CSS-Dateien.</span><span class="sxs-lookup"><span data-stu-id="145e7-277">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="145e7-278">Weitere Informationen finden Sie unter <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="145e7-278">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="145e7-279">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="145e7-279">appSettings.json</span></span>

<span data-ttu-id="145e7-280">Enthält Konfigurationsdaten, z. B. Verbindungszeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="145e7-280">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="145e7-281">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="145e7-281">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="145e7-282">Program.cs</span><span class="sxs-lookup"><span data-stu-id="145e7-282">Program.cs</span></span>

<span data-ttu-id="145e7-283">Enthält den Einstiegspunkt für das Programm.</span><span class="sxs-lookup"><span data-stu-id="145e7-283">Contains the entry point for the program.</span></span> <span data-ttu-id="145e7-284">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="145e7-284">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="145e7-285">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="145e7-285">Startup.cs</span></span>

<span data-ttu-id="145e7-286">Enthält Code, mit dem das App-Verhalten konfiguriert wird, beispielsweise, ob die App Zustimmung für Cookies erfordert.</span><span class="sxs-lookup"><span data-stu-id="145e7-286">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="145e7-287">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="145e7-287">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="145e7-288">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="145e7-288">Additional resources</span></span>

* [<span data-ttu-id="145e7-289">Dieses Tutorials auf YouTube</span><span class="sxs-lookup"><span data-stu-id="145e7-289">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="145e7-290">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="145e7-290">Next steps</span></span>

<span data-ttu-id="145e7-291">Wechseln Sie zum nächsten Tutorial in der Reihe:</span><span class="sxs-lookup"><span data-stu-id="145e7-291">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="145e7-292">Hinzufügen eines Modells</span><span class="sxs-lookup"><span data-stu-id="145e7-292">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

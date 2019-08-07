---
title: 'Tutorial: Erste Schritte mit Razor Pages in ASP.NET Core'
author: rick-anderson
description: Diese Reihe von Tutorials zeigt, wie Sie Razor Pages in ASP.NET Core verwenden können. Erfahren Sie, wie Sie ein Modell erstellen, Code für Razor-Seiten generieren, Entity Framework Core und SQL Server für den Datenzugriff verwenden, Suchfunktionen hinzufügen, Eingabeüberprüfung hinzufügen und Migrationen verwenden, um das Modell zu aktualisieren.
ms.author: riande
ms.date: 07/25/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 57a10895c718c539ece280afcb27cb4033c7fb45
ms.sourcegitcommit: 979dbfc5e9ce09b9470789989cddfcfb57079d94
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/31/2019
ms.locfileid: "68682794"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="94cc8-104">Tutorial: Erste Schritte mit Razor Pages in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="94cc8-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="94cc8-105">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="94cc8-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="94cc8-106">Dies ist das erste Tutorial einer Reihe, in der Sie Grundlegendes zur Erstellung einer ASP.NET Core-Webapp mit Razor Pages erfahren.</span><span class="sxs-lookup"><span data-stu-id="94cc8-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="94cc8-107">Am Ende der Reihe verfügen Sie über eine App, mit der eine Filmdatenbank verwaltet werden kann.</span><span class="sxs-lookup"><span data-stu-id="94cc8-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="94cc8-108">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="94cc8-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="94cc8-109">Erstellen einer Razor Pages-Web-App</span><span class="sxs-lookup"><span data-stu-id="94cc8-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="94cc8-110">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="94cc8-110">Run the app.</span></span>
> * <span data-ttu-id="94cc8-111">Überprüfen Sie die Projektdateien.</span><span class="sxs-lookup"><span data-stu-id="94cc8-111">Examine the project files.</span></span>

<span data-ttu-id="94cc8-112">Am Ende dieses Tutorials verfügen Sie über eine funktionsfähige Razor Pages-Web-App, auf der Sie in späteren Tutorials aufbauen werden.</span><span class="sxs-lookup"><span data-stu-id="94cc8-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="94cc8-114">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="94cc8-114">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="94cc8-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94cc8-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="94cc8-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94cc8-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="94cc8-117">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="94cc8-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="94cc8-118">Erstellen einer Razor Pages-Web-App</span><span class="sxs-lookup"><span data-stu-id="94cc8-118">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="94cc8-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94cc8-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="94cc8-120">Wählen Sie in Visual Studio im Menü **Datei** die Option **Neu** > **Projekt** aus.</span><span class="sxs-lookup"><span data-stu-id="94cc8-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="94cc8-121">Erstellen Sie eine neue ASP.NET Core-Webanwendung, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="94cc8-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="94cc8-122">![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="94cc8-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="94cc8-123">Nennen Sie das Projekt **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="94cc8-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="94cc8-124">Es ist wichtig, den Namen *RazorPagesMovie* zu verwenden, damit die Namespaces übereinstimmen, wenn Sie Code kopieren und einfügen.</span><span class="sxs-lookup"><span data-stu-id="94cc8-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="94cc8-125">![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="94cc8-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="94cc8-126">Klicken Sie in der Dropdownliste auf **ASP.NET Core 3.0**, auf **Webanwendung** und anschließend auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="94cc8-126">Select **ASP.NET Core 3.0** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="94cc8-128">Das folgende Startprojekt wird erstellt:</span><span class="sxs-lookup"><span data-stu-id="94cc8-128">The following starter project is created:</span></span>

  ![Projektmappen-Explorer](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="94cc8-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94cc8-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="94cc8-131">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="94cc8-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="94cc8-132">Wechseln Sie zum Verzeichnis (`cd`), das das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="94cc8-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="94cc8-133">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="94cc8-133">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="94cc8-134">Der Befehl `dotnet new` erstellt ein neues Razor Pages-Projekt im Ordner *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="94cc8-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="94cc8-135">Der Befehl `code` öffnet den Ordner *RazorPagesMovie* in der aktuellen Instanz von Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="94cc8-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="94cc8-136">Wenn das OmniSharp-Flammensymbol in der Statusleiste grün angezeigt wird, wird ein Dialogfeld mit folgender Meldung angezeigt: **Required assets to build and debug are missing from 'RazorPagesMovie'. (Die erforderlichen Objekte zum Erstellen und Debuggen sind in „RazorPagesMovie“ nicht vorhanden. Sollen sie hinzugefügt werden?**</span><span class="sxs-lookup"><span data-stu-id="94cc8-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="94cc8-137">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="94cc8-137">Select **Yes**.</span></span>

  <span data-ttu-id="94cc8-138">Ein *VSCODE*-Verzeichnis, das die Dateien *launch.json* und *tasks.json* enthält, wird dem Stammverzeichnis des Projekts hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="94cc8-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="94cc8-139">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="94cc8-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="94cc8-140">Führen Sie über ein Terminal den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="94cc8-140">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="94cc8-141">Diese Befehle verwenden die [.NET Core-CLI](/dotnet/core/tools/dotnet), um ein Razor Pages-Projekt zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="94cc8-141">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="94cc8-142">Öffnen des Projekts</span><span class="sxs-lookup"><span data-stu-id="94cc8-142">Open the project</span></span>

<span data-ttu-id="94cc8-143">Klicken Sie in Visual Studio auf **Datei > Öffnen**, und wählen Sie dann die Datei *RazorPagesMovie.csproj* aus.</span><span class="sxs-lookup"><span data-stu-id="94cc8-143">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="94cc8-144">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="94cc8-144">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="94cc8-145">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94cc8-145">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="94cc8-146">Drücken Sie STRG+F5, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="94cc8-146">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="94cc8-147">Visual Studio startet [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) und führt die App aus.</span><span class="sxs-lookup"><span data-stu-id="94cc8-147">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="94cc8-148">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="94cc8-148">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="94cc8-149">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="94cc8-149">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="94cc8-150">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="94cc8-150">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="94cc8-151">Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.</span><span class="sxs-lookup"><span data-stu-id="94cc8-151">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
 
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="94cc8-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94cc8-152">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="94cc8-153">Drücken Sie **STRG+F5**, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="94cc8-153">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="94cc8-154">Visual Studio Code startet [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="94cc8-154">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="94cc8-155">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="94cc8-155">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="94cc8-156">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="94cc8-156">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="94cc8-157">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="94cc8-157">Localhost only serves web requests from the local computer.</span></span>

  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="94cc8-158">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="94cc8-158">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="94cc8-159">Drücken Sie **ALT+CMD+EINGABETASTE**, um die Ausführung zu starten, ohne dabei den Debugger zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="94cc8-159">Press **Alt-Cmd-Enter** to run without the debugger.</span></span> <span data-ttu-id="94cc8-160">Alternativ können Sie auch in der Menüleiste auf „Ausführen“ und anschließend auf „Ohne Debugger starten“ klicken.</span><span class="sxs-lookup"><span data-stu-id="94cc8-160">Alternatively, navigate to the menu bar and go to Run>Start Without Debugging.</span></span>

  <span data-ttu-id="94cc8-161">Visual Studio startet dann [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="94cc8-161">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="94cc8-162">Überprüfen der Projektdateien</span><span class="sxs-lookup"><span data-stu-id="94cc8-162">Examine the project files</span></span>

<span data-ttu-id="94cc8-163">Es folgt eine Übersicht über die Hauptprojektordner und -dateien, mit denen Sie in späteren Tutorials arbeiten werden.</span><span class="sxs-lookup"><span data-stu-id="94cc8-163">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="94cc8-164">Ordner „Seiten“</span><span class="sxs-lookup"><span data-stu-id="94cc8-164">Pages folder</span></span>

<span data-ttu-id="94cc8-165">Enthält Razor-Seiten und unterstützende Dateien.</span><span class="sxs-lookup"><span data-stu-id="94cc8-165">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="94cc8-166">Jede Razor-Seite besteht aus einem Dateienpaar:</span><span class="sxs-lookup"><span data-stu-id="94cc8-166">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="94cc8-167">Eine *.cshtml*-Datei, die HTML-Markup mit C#-Code in Razor-Syntax enthält</span><span class="sxs-lookup"><span data-stu-id="94cc8-167">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="94cc8-168">Eine *.cshtml.cs*-Datei mit C# Code, in dem Seitenereignisse verarbeitet werden</span><span class="sxs-lookup"><span data-stu-id="94cc8-168">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="94cc8-169">Unterstützende Dateien haben Namen, die mit einem Unterstrich beginnen.</span><span class="sxs-lookup"><span data-stu-id="94cc8-169">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="94cc8-170">Zum Beispiel sind in der Datei *_Layout.cshtml* Benutzeroberflächenelemente konfiguriert, die für alle Seiten gelten.</span><span class="sxs-lookup"><span data-stu-id="94cc8-170">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="94cc8-171">Mit dieser Datei werden das Navigationsmenü oben auf der Seite und der Urheberrechtshinweis unten auf der Seite eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="94cc8-171">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="94cc8-172">Weitere Informationen finden Sie unter <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="94cc8-172">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="94cc8-173">Ordner „wwwroot“</span><span class="sxs-lookup"><span data-stu-id="94cc8-173">wwwroot folder</span></span>

<span data-ttu-id="94cc8-174">Enthält statische Dateien, z. B. HTML-Dateien, JavaScript-Dateien und CSS-Dateien.</span><span class="sxs-lookup"><span data-stu-id="94cc8-174">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="94cc8-175">Weitere Informationen finden Sie unter <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="94cc8-175">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="94cc8-176">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="94cc8-176">appSettings.json</span></span>

<span data-ttu-id="94cc8-177">Enthält Konfigurationsdaten, z. B. Verbindungszeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="94cc8-177">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="94cc8-178">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="94cc8-178">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="94cc8-179">Program.cs</span><span class="sxs-lookup"><span data-stu-id="94cc8-179">Program.cs</span></span>

<span data-ttu-id="94cc8-180">Enthält den Einstiegspunkt für das Programm.</span><span class="sxs-lookup"><span data-stu-id="94cc8-180">Contains the entry point for the program.</span></span> <span data-ttu-id="94cc8-181">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="94cc8-181">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="94cc8-182">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="94cc8-182">Startup.cs</span></span>

<span data-ttu-id="94cc8-183">Diese Datei enthält Code, mit dem das App-Verhalten konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="94cc8-183">Contains code that configures app behavior.</span></span> <span data-ttu-id="94cc8-184">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="94cc8-184">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="94cc8-185">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="94cc8-185">Next steps</span></span>

<span data-ttu-id="94cc8-186">Wechseln Sie zum nächsten Tutorial in der Reihe:</span><span class="sxs-lookup"><span data-stu-id="94cc8-186">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="94cc8-187">Hinzufügen eines Modells</span><span class="sxs-lookup"><span data-stu-id="94cc8-187">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="94cc8-188">Dies ist das erste Tutorial aus einer Reihe.</span><span class="sxs-lookup"><span data-stu-id="94cc8-188">This is the first tutorial of a series.</span></span> <span data-ttu-id="94cc8-189">[In der Reihe](xref:tutorials/razor-pages/index) lernen Sie die Grundlagen zum Erstellen einer Razor Pages-Web-App mit ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="94cc8-189">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="94cc8-190">Am Ende der Reihe verfügen Sie über eine App, mit der eine Filmdatenbank verwaltet werden kann.</span><span class="sxs-lookup"><span data-stu-id="94cc8-190">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="94cc8-191">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="94cc8-191">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="94cc8-192">Erstellen einer Razor Pages-Web-App</span><span class="sxs-lookup"><span data-stu-id="94cc8-192">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="94cc8-193">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="94cc8-193">Run the app.</span></span>
> * <span data-ttu-id="94cc8-194">Überprüfen Sie die Projektdateien.</span><span class="sxs-lookup"><span data-stu-id="94cc8-194">Examine the project files.</span></span>

<span data-ttu-id="94cc8-195">Am Ende dieses Tutorials verfügen Sie über eine funktionsfähige Razor Pages-Web-App, auf der Sie in späteren Tutorials aufbauen werden.</span><span class="sxs-lookup"><span data-stu-id="94cc8-195">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="94cc8-197">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="94cc8-197">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="94cc8-198">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94cc8-198">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="94cc8-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94cc8-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="94cc8-200">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="94cc8-200">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="94cc8-201">Erstellen einer Razor Pages-Web-App</span><span class="sxs-lookup"><span data-stu-id="94cc8-201">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="94cc8-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94cc8-202">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="94cc8-203">Wählen Sie in Visual Studio im Menü **Datei** die Option **Neu** > **Projekt** aus.</span><span class="sxs-lookup"><span data-stu-id="94cc8-203">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="94cc8-204">Erstellen Sie eine neue ASP.NET Core-Webanwendung, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="94cc8-204">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="94cc8-206">Nennen Sie das Projekt **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="94cc8-206">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="94cc8-207">Es ist wichtig, den Namen *RazorPagesMovie* zu verwenden, damit die Namespaces übereinstimmen, wenn Sie Code kopieren und einfügen.</span><span class="sxs-lookup"><span data-stu-id="94cc8-207">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/config.png)

* <span data-ttu-id="94cc8-209">Klicken Sie in der Dropdownliste auf **ASP.NET Core 2.2**, auf **Webanwendung** und anschließend auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="94cc8-209">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="94cc8-211">Das folgende Startprojekt wird erstellt:</span><span class="sxs-lookup"><span data-stu-id="94cc8-211">The following starter project is created:</span></span>

  ![Projektmappen-Explorer](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="94cc8-213">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94cc8-213">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="94cc8-214">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="94cc8-214">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="94cc8-215">Wechseln Sie zum Verzeichnis (`cd`), das das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="94cc8-215">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="94cc8-216">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="94cc8-216">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="94cc8-217">Der Befehl `dotnet new` erstellt ein neues Razor Pages-Projekt im Ordner *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="94cc8-217">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="94cc8-218">Der Befehl `code` öffnet den Ordner *RazorPagesMovie* in der aktuellen Instanz von Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="94cc8-218">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="94cc8-219">Wenn das OmniSharp-Flammensymbol in der Statusleiste grün angezeigt wird, wird ein Dialogfeld mit folgender Meldung angezeigt: **Required assets to build and debug are missing from 'RazorPagesMovie'. (Die erforderlichen Objekte zum Erstellen und Debuggen sind in „RazorPagesMovie“ nicht vorhanden. Sollen sie hinzugefügt werden?**</span><span class="sxs-lookup"><span data-stu-id="94cc8-219">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="94cc8-220">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="94cc8-220">Select **Yes**.</span></span>

  <span data-ttu-id="94cc8-221">Ein *VSCODE*-Verzeichnis, das die Dateien *launch.json* und *tasks.json* enthält, wird dem Stammverzeichnis des Projekts hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="94cc8-221">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="94cc8-222">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="94cc8-222">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="94cc8-223">Führen Sie über ein Terminal den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="94cc8-223">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="94cc8-224">Diese Befehle verwenden die [.NET Core-CLI](/dotnet/core/tools/dotnet), um ein Razor Pages-Projekt zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="94cc8-224">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="94cc8-225">Öffnen des Projekts</span><span class="sxs-lookup"><span data-stu-id="94cc8-225">Open the project</span></span>

<span data-ttu-id="94cc8-226">Klicken Sie in Visual Studio auf **Datei > Öffnen**, und wählen Sie dann die Datei *RazorPagesMovie.csproj* aus.</span><span class="sxs-lookup"><span data-stu-id="94cc8-226">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="94cc8-227">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="94cc8-227">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="94cc8-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94cc8-228">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="94cc8-229">Drücken Sie STRG+F5, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="94cc8-229">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="94cc8-230">Visual Studio startet [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) und führt die App aus.</span><span class="sxs-lookup"><span data-stu-id="94cc8-230">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="94cc8-231">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="94cc8-231">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="94cc8-232">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="94cc8-232">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="94cc8-233">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="94cc8-233">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="94cc8-234">Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.</span><span class="sxs-lookup"><span data-stu-id="94cc8-234">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="94cc8-235">Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="94cc8-235">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="94cc8-236">Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.</span><span class="sxs-lookup"><span data-stu-id="94cc8-236">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="94cc8-238">Die folgende Abbildung zeigt die App, nachdem Sie das Einverständnis zur Nachverfolgung gegeben haben:</span><span class="sxs-lookup"><span data-stu-id="94cc8-238">The following image shows the app after you give consent to tracking:</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="94cc8-240">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94cc8-240">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="94cc8-241">Drücken Sie **STRG+F5**, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="94cc8-241">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="94cc8-242">Visual Studio Code startet [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="94cc8-242">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="94cc8-243">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="94cc8-243">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="94cc8-244">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="94cc8-244">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="94cc8-245">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="94cc8-245">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="94cc8-246">Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="94cc8-246">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="94cc8-247">Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.</span><span class="sxs-lookup"><span data-stu-id="94cc8-247">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="94cc8-249">Die folgende Abbildung zeigt die App, nachdem Sie das Einverständnis zur Nachverfolgung gegeben haben:</span><span class="sxs-lookup"><span data-stu-id="94cc8-249">The following image shows the app after you give consent to tracking:</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="94cc8-251">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="94cc8-251">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="94cc8-252">Drücken Sie **CMD+OPT+F5** für eine Ausführung ohne Debugger.</span><span class="sxs-lookup"><span data-stu-id="94cc8-252">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="94cc8-253">Visual Studio startet dann [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="94cc8-253">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="94cc8-254">Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="94cc8-254">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="94cc8-255">Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.</span><span class="sxs-lookup"><span data-stu-id="94cc8-255">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="94cc8-257">Die folgende Abbildung zeigt die App, nachdem Sie das Einverständnis zur Nachverfolgung gegeben haben:</span><span class="sxs-lookup"><span data-stu-id="94cc8-257">The following image shows the app after you give consent to tracking:</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="94cc8-259">Überprüfen der Projektdateien</span><span class="sxs-lookup"><span data-stu-id="94cc8-259">Examine the project files</span></span>

<span data-ttu-id="94cc8-260">Es folgt eine Übersicht über die Hauptprojektordner und -dateien, mit denen Sie in späteren Tutorials arbeiten werden.</span><span class="sxs-lookup"><span data-stu-id="94cc8-260">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="94cc8-261">Ordner „Seiten“</span><span class="sxs-lookup"><span data-stu-id="94cc8-261">Pages folder</span></span>

<span data-ttu-id="94cc8-262">Enthält Razor-Seiten und unterstützende Dateien.</span><span class="sxs-lookup"><span data-stu-id="94cc8-262">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="94cc8-263">Jede Razor-Seite besteht aus einem Dateienpaar:</span><span class="sxs-lookup"><span data-stu-id="94cc8-263">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="94cc8-264">Eine *.cshtml*-Datei, die HTML-Markup mit C#-Code in Razor-Syntax enthält</span><span class="sxs-lookup"><span data-stu-id="94cc8-264">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="94cc8-265">Eine *.cshtml.cs*-Datei mit C# Code, in dem Seitenereignisse verarbeitet werden</span><span class="sxs-lookup"><span data-stu-id="94cc8-265">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="94cc8-266">Unterstützende Dateien haben Namen, die mit einem Unterstrich beginnen.</span><span class="sxs-lookup"><span data-stu-id="94cc8-266">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="94cc8-267">Zum Beispiel sind in der Datei *_Layout.cshtml* Benutzeroberflächenelemente konfiguriert, die für alle Seiten gelten.</span><span class="sxs-lookup"><span data-stu-id="94cc8-267">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="94cc8-268">Mit dieser Datei werden das Navigationsmenü oben auf der Seite und der Urheberrechtshinweis unten auf der Seite eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="94cc8-268">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="94cc8-269">Weitere Informationen finden Sie unter <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="94cc8-269">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="94cc8-270">Ordner „wwwroot“</span><span class="sxs-lookup"><span data-stu-id="94cc8-270">wwwroot folder</span></span>

<span data-ttu-id="94cc8-271">Enthält statische Dateien, z. B. HTML-Dateien, JavaScript-Dateien und CSS-Dateien.</span><span class="sxs-lookup"><span data-stu-id="94cc8-271">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="94cc8-272">Weitere Informationen finden Sie unter <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="94cc8-272">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="94cc8-273">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="94cc8-273">appSettings.json</span></span>

<span data-ttu-id="94cc8-274">Enthält Konfigurationsdaten, z. B. Verbindungszeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="94cc8-274">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="94cc8-275">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="94cc8-275">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="94cc8-276">Program.cs</span><span class="sxs-lookup"><span data-stu-id="94cc8-276">Program.cs</span></span>

<span data-ttu-id="94cc8-277">Enthält den Einstiegspunkt für das Programm.</span><span class="sxs-lookup"><span data-stu-id="94cc8-277">Contains the entry point for the program.</span></span> <span data-ttu-id="94cc8-278">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="94cc8-278">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="94cc8-279">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="94cc8-279">Startup.cs</span></span>

<span data-ttu-id="94cc8-280">Enthält Code, mit dem das App-Verhalten konfiguriert wird, beispielsweise, ob die App Zustimmung für Cookies erfordert.</span><span class="sxs-lookup"><span data-stu-id="94cc8-280">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="94cc8-281">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="94cc8-281">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="94cc8-282">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="94cc8-282">Additional resources</span></span>

* [<span data-ttu-id="94cc8-283">Dieses Tutorials auf YouTube</span><span class="sxs-lookup"><span data-stu-id="94cc8-283">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="94cc8-284">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="94cc8-284">Next steps</span></span>

<span data-ttu-id="94cc8-285">Wechseln Sie zum nächsten Tutorial in der Reihe:</span><span class="sxs-lookup"><span data-stu-id="94cc8-285">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="94cc8-286">Hinzufügen eines Modells</span><span class="sxs-lookup"><span data-stu-id="94cc8-286">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

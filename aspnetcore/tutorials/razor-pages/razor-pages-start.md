---
title: 'Tutorial: Erste Schritte mit Razor Pages in ASP.NET Core'
author: rick-anderson
description: Diese Reihe von Tutorials zeigt, wie Sie Razor Pages in ASP.NET Core verwenden können. Erfahren Sie, wie Sie ein Modell erstellen, Code für Razor-Seiten generieren, Entity Framework Core und SQL Server für den Datenzugriff verwenden, Suchfunktionen hinzufügen, Eingabeüberprüfung hinzufügen und Migrationen verwenden, um das Modell zu aktualisieren.
ms.author: riande
ms.date: 07/25/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 1605197188d97f27a884739a72400da2d5818b1a
ms.sourcegitcommit: 849af69ee3c94cdb9fd8fa1f1bb8f5a5dda7b9eb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2019
ms.locfileid: "68371999"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="36e44-104">Tutorial: Erste Schritte mit Razor Pages in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="36e44-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="36e44-105">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="36e44-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="36e44-106">Dies ist das erste Tutorial einer Reihe, in der Sie Grundlegendes zur Erstellung einer ASP.NET Core-Webapp mit Razor Pages erfahren.</span><span class="sxs-lookup"><span data-stu-id="36e44-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="36e44-107">Am Ende der Reihe verfügen Sie über eine App, mit der eine Filmdatenbank verwaltet werden kann.</span><span class="sxs-lookup"><span data-stu-id="36e44-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="36e44-108">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="36e44-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="36e44-109">Erstellen einer Razor Pages-Web-App</span><span class="sxs-lookup"><span data-stu-id="36e44-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="36e44-110">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="36e44-110">Run the app.</span></span>
> * <span data-ttu-id="36e44-111">Überprüfen Sie die Projektdateien.</span><span class="sxs-lookup"><span data-stu-id="36e44-111">Examine the project files.</span></span>

<span data-ttu-id="36e44-112">Am Ende dieses Tutorials verfügen Sie über eine funktionsfähige Razor Pages-Web-App, auf der Sie in späteren Tutorials aufbauen werden.</span><span class="sxs-lookup"><span data-stu-id="36e44-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="36e44-114">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="36e44-114">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="36e44-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="36e44-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="36e44-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="36e44-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="36e44-117">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="36e44-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="36e44-118">Erstellen einer Razor Pages-Web-App</span><span class="sxs-lookup"><span data-stu-id="36e44-118">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="36e44-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="36e44-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="36e44-120">Wählen Sie in Visual Studio im Menü **Datei** die Option **Neu** > **Projekt** aus.</span><span class="sxs-lookup"><span data-stu-id="36e44-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="36e44-121">Erstellen Sie eine neue ASP.NET Core-Webanwendung, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="36e44-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="36e44-122">![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="36e44-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="36e44-123">Nennen Sie das Projekt **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="36e44-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="36e44-124">Es ist wichtig, den Namen *RazorPagesMovie* zu verwenden, damit die Namespaces übereinstimmen, wenn Sie Code kopieren und einfügen.</span><span class="sxs-lookup"><span data-stu-id="36e44-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="36e44-125">![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="36e44-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="36e44-126">Klicken Sie in der Dropdownliste auf **ASP.NET Core 3.0**, auf **Webanwendung** und anschließend auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="36e44-126">Select **ASP.NET Core 3.0** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="36e44-128">Das folgende Startprojekt wird erstellt:</span><span class="sxs-lookup"><span data-stu-id="36e44-128">The following starter project is created:</span></span>

  ![Projektmappen-Explorer](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="36e44-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="36e44-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="36e44-131">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="36e44-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="36e44-132">Wechseln Sie zum Verzeichnis (`cd`), das das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="36e44-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="36e44-133">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="36e44-133">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="36e44-134">Der Befehl `dotnet new` erstellt ein neues Razor Pages-Projekt im Ordner *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="36e44-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="36e44-135">Der Befehl `code` öffnet den Ordner *RazorPagesMovie* in der aktuellen Instanz von Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="36e44-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="36e44-136">Wenn das OmniSharp-Flammensymbol in der Statusleiste grün angezeigt wird, wird ein Dialogfeld mit folgender Meldung angezeigt: **Required assets to build and debug are missing from 'RazorPagesMovie'. (Die erforderlichen Objekte zum Erstellen und Debuggen sind in „RazorPagesMovie“ nicht vorhanden. Sollen sie hinzugefügt werden?**</span><span class="sxs-lookup"><span data-stu-id="36e44-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="36e44-137">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="36e44-137">Select **Yes**.</span></span>

  <span data-ttu-id="36e44-138">Ein *VSCODE*-Verzeichnis, das die Dateien *launch.json* und *tasks.json* enthält, wird dem Stammverzeichnis des Projekts hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="36e44-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="36e44-139">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="36e44-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="36e44-140">Führen Sie über ein Terminal den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="36e44-140">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="36e44-141">Diese Befehle verwenden die [.NET Core-CLI](/dotnet/core/tools/dotnet), um ein Razor Pages-Projekt zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="36e44-141">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="36e44-142">Öffnen des Projekts</span><span class="sxs-lookup"><span data-stu-id="36e44-142">Open the project</span></span>

<span data-ttu-id="36e44-143">Klicken Sie in Visual Studio auf **Datei > Öffnen**, und wählen Sie dann die Datei *RazorPagesMovie.csproj* aus.</span><span class="sxs-lookup"><span data-stu-id="36e44-143">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="36e44-144">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="36e44-144">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="36e44-145">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="36e44-145">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="36e44-146">Drücken Sie STRG+F5, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="36e44-146">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="36e44-147">Visual Studio startet [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) und führt die App aus.</span><span class="sxs-lookup"><span data-stu-id="36e44-147">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="36e44-148">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="36e44-148">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="36e44-149">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="36e44-149">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="36e44-150">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="36e44-150">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="36e44-151">Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.</span><span class="sxs-lookup"><span data-stu-id="36e44-151">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
 
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="36e44-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="36e44-152">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="36e44-153">Drücken Sie **STRG+F5**, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="36e44-153">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="36e44-154">Visual Studio Code startet [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="36e44-154">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="36e44-155">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="36e44-155">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="36e44-156">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="36e44-156">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="36e44-157">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="36e44-157">Localhost only serves web requests from the local computer.</span></span>

  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="36e44-158">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="36e44-158">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="36e44-159">Drücken Sie **CMD+OPT+F5** für eine Ausführung ohne Debugger.</span><span class="sxs-lookup"><span data-stu-id="36e44-159">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="36e44-160">Visual Studio startet dann [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="36e44-160">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="36e44-161">Überprüfen der Projektdateien</span><span class="sxs-lookup"><span data-stu-id="36e44-161">Examine the project files</span></span>

<span data-ttu-id="36e44-162">Es folgt eine Übersicht über die Hauptprojektordner und -dateien, mit denen Sie in späteren Tutorials arbeiten werden.</span><span class="sxs-lookup"><span data-stu-id="36e44-162">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="36e44-163">Ordner „Seiten“</span><span class="sxs-lookup"><span data-stu-id="36e44-163">Pages folder</span></span>

<span data-ttu-id="36e44-164">Enthält Razor-Seiten und unterstützende Dateien.</span><span class="sxs-lookup"><span data-stu-id="36e44-164">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="36e44-165">Jede Razor-Seite besteht aus einem Dateienpaar:</span><span class="sxs-lookup"><span data-stu-id="36e44-165">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="36e44-166">Eine *.cshtml*-Datei, die HTML-Markup mit C#-Code in Razor-Syntax enthält</span><span class="sxs-lookup"><span data-stu-id="36e44-166">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="36e44-167">Eine *.cshtml.cs*-Datei mit C# Code, in dem Seitenereignisse verarbeitet werden</span><span class="sxs-lookup"><span data-stu-id="36e44-167">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="36e44-168">Unterstützende Dateien haben Namen, die mit einem Unterstrich beginnen.</span><span class="sxs-lookup"><span data-stu-id="36e44-168">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="36e44-169">Zum Beispiel sind in der Datei *_Layout.cshtml* Benutzeroberflächenelemente konfiguriert, die für alle Seiten gelten.</span><span class="sxs-lookup"><span data-stu-id="36e44-169">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="36e44-170">Mit dieser Datei werden das Navigationsmenü oben auf der Seite und der Urheberrechtshinweis unten auf der Seite eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="36e44-170">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="36e44-171">Weitere Informationen finden Sie unter <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="36e44-171">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="36e44-172">Ordner „wwwroot“</span><span class="sxs-lookup"><span data-stu-id="36e44-172">wwwroot folder</span></span>

<span data-ttu-id="36e44-173">Enthält statische Dateien, z. B. HTML-Dateien, JavaScript-Dateien und CSS-Dateien.</span><span class="sxs-lookup"><span data-stu-id="36e44-173">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="36e44-174">Weitere Informationen finden Sie unter <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="36e44-174">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="36e44-175">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="36e44-175">appSettings.json</span></span>

<span data-ttu-id="36e44-176">Enthält Konfigurationsdaten, z. B. Verbindungszeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="36e44-176">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="36e44-177">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="36e44-177">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="36e44-178">Program.cs</span><span class="sxs-lookup"><span data-stu-id="36e44-178">Program.cs</span></span>

<span data-ttu-id="36e44-179">Enthält den Einstiegspunkt für das Programm.</span><span class="sxs-lookup"><span data-stu-id="36e44-179">Contains the entry point for the program.</span></span> <span data-ttu-id="36e44-180">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="36e44-180">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="36e44-181">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="36e44-181">Startup.cs</span></span>

<span data-ttu-id="36e44-182">Diese Datei enthält Code, mit dem das App-Verhalten konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="36e44-182">Contains code that configures app behavior.</span></span> <span data-ttu-id="36e44-183">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="36e44-183">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="36e44-184">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="36e44-184">Next steps</span></span>

<span data-ttu-id="36e44-185">Wechseln Sie zum nächsten Tutorial in der Reihe:</span><span class="sxs-lookup"><span data-stu-id="36e44-185">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="36e44-186">Hinzufügen eines Modells</span><span class="sxs-lookup"><span data-stu-id="36e44-186">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="36e44-187">Dies ist das erste Tutorial aus einer Reihe.</span><span class="sxs-lookup"><span data-stu-id="36e44-187">This is the first tutorial of a series.</span></span> <span data-ttu-id="36e44-188">[In der Reihe](xref:tutorials/razor-pages/index) lernen Sie die Grundlagen zum Erstellen einer Razor Pages-Web-App mit ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="36e44-188">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="36e44-189">Am Ende der Reihe verfügen Sie über eine App, mit der eine Filmdatenbank verwaltet werden kann.</span><span class="sxs-lookup"><span data-stu-id="36e44-189">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="36e44-190">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="36e44-190">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="36e44-191">Erstellen einer Razor Pages-Web-App</span><span class="sxs-lookup"><span data-stu-id="36e44-191">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="36e44-192">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="36e44-192">Run the app.</span></span>
> * <span data-ttu-id="36e44-193">Überprüfen Sie die Projektdateien.</span><span class="sxs-lookup"><span data-stu-id="36e44-193">Examine the project files.</span></span>

<span data-ttu-id="36e44-194">Am Ende dieses Tutorials verfügen Sie über eine funktionsfähige Razor Pages-Web-App, auf der Sie in späteren Tutorials aufbauen werden.</span><span class="sxs-lookup"><span data-stu-id="36e44-194">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="36e44-196">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="36e44-196">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="36e44-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="36e44-197">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="36e44-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="36e44-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="36e44-199">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="36e44-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="36e44-200">Erstellen einer Razor Pages-Web-App</span><span class="sxs-lookup"><span data-stu-id="36e44-200">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="36e44-201">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="36e44-201">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="36e44-202">Wählen Sie in Visual Studio im Menü **Datei** die Option **Neu** > **Projekt** aus.</span><span class="sxs-lookup"><span data-stu-id="36e44-202">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="36e44-203">Erstellen Sie eine neue ASP.NET Core-Webanwendung, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="36e44-203">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="36e44-205">Nennen Sie das Projekt **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="36e44-205">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="36e44-206">Es ist wichtig, den Namen *RazorPagesMovie* zu verwenden, damit die Namespaces übereinstimmen, wenn Sie Code kopieren und einfügen.</span><span class="sxs-lookup"><span data-stu-id="36e44-206">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/config.png)

* <span data-ttu-id="36e44-208">Klicken Sie in der Dropdownliste auf **ASP.NET Core 2.2**, auf **Webanwendung** und anschließend auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="36e44-208">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="36e44-210">Das folgende Startprojekt wird erstellt:</span><span class="sxs-lookup"><span data-stu-id="36e44-210">The following starter project is created:</span></span>

  ![Projektmappen-Explorer](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="36e44-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="36e44-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="36e44-213">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="36e44-213">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="36e44-214">Wechseln Sie zum Verzeichnis (`cd`), das das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="36e44-214">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="36e44-215">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="36e44-215">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="36e44-216">Der Befehl `dotnet new` erstellt ein neues Razor Pages-Projekt im Ordner *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="36e44-216">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="36e44-217">Der Befehl `code` öffnet den Ordner *RazorPagesMovie* in der aktuellen Instanz von Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="36e44-217">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="36e44-218">Wenn das OmniSharp-Flammensymbol in der Statusleiste grün angezeigt wird, wird ein Dialogfeld mit folgender Meldung angezeigt: **Required assets to build and debug are missing from 'RazorPagesMovie'. (Die erforderlichen Objekte zum Erstellen und Debuggen sind in „RazorPagesMovie“ nicht vorhanden. Sollen sie hinzugefügt werden?**</span><span class="sxs-lookup"><span data-stu-id="36e44-218">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="36e44-219">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="36e44-219">Select **Yes**.</span></span>

  <span data-ttu-id="36e44-220">Ein *VSCODE*-Verzeichnis, das die Dateien *launch.json* und *tasks.json* enthält, wird dem Stammverzeichnis des Projekts hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="36e44-220">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="36e44-221">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="36e44-221">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="36e44-222">Führen Sie über ein Terminal den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="36e44-222">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="36e44-223">Diese Befehle verwenden die [.NET Core-CLI](/dotnet/core/tools/dotnet), um ein Razor Pages-Projekt zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="36e44-223">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="36e44-224">Öffnen des Projekts</span><span class="sxs-lookup"><span data-stu-id="36e44-224">Open the project</span></span>

<span data-ttu-id="36e44-225">Klicken Sie in Visual Studio auf **Datei > Öffnen**, und wählen Sie dann die Datei *RazorPagesMovie.csproj* aus.</span><span class="sxs-lookup"><span data-stu-id="36e44-225">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="36e44-226">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="36e44-226">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="36e44-227">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="36e44-227">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="36e44-228">Drücken Sie STRG+F5, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="36e44-228">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="36e44-229">Visual Studio startet [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) und führt die App aus.</span><span class="sxs-lookup"><span data-stu-id="36e44-229">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="36e44-230">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="36e44-230">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="36e44-231">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="36e44-231">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="36e44-232">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="36e44-232">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="36e44-233">Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.</span><span class="sxs-lookup"><span data-stu-id="36e44-233">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="36e44-234">Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="36e44-234">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="36e44-235">Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.</span><span class="sxs-lookup"><span data-stu-id="36e44-235">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="36e44-237">Die folgende Abbildung zeigt die App, nachdem Sie das Einverständnis zur Nachverfolgung gegeben haben:</span><span class="sxs-lookup"><span data-stu-id="36e44-237">The following image shows the app after you give consent to tracking:</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="36e44-239">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="36e44-239">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="36e44-240">Drücken Sie **STRG+F5**, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="36e44-240">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="36e44-241">Visual Studio Code startet [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="36e44-241">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="36e44-242">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="36e44-242">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="36e44-243">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="36e44-243">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="36e44-244">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="36e44-244">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="36e44-245">Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="36e44-245">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="36e44-246">Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.</span><span class="sxs-lookup"><span data-stu-id="36e44-246">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="36e44-248">Die folgende Abbildung zeigt die App, nachdem Sie das Einverständnis zur Nachverfolgung gegeben haben:</span><span class="sxs-lookup"><span data-stu-id="36e44-248">The following image shows the app after you give consent to tracking:</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="36e44-250">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="36e44-250">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="36e44-251">Drücken Sie **CMD+OPT+F5** für eine Ausführung ohne Debugger.</span><span class="sxs-lookup"><span data-stu-id="36e44-251">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="36e44-252">Visual Studio startet dann [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="36e44-252">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="36e44-253">Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="36e44-253">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="36e44-254">Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.</span><span class="sxs-lookup"><span data-stu-id="36e44-254">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="36e44-256">Die folgende Abbildung zeigt die App, nachdem Sie das Einverständnis zur Nachverfolgung gegeben haben:</span><span class="sxs-lookup"><span data-stu-id="36e44-256">The following image shows the app after you give consent to tracking:</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="36e44-258">Überprüfen der Projektdateien</span><span class="sxs-lookup"><span data-stu-id="36e44-258">Examine the project files</span></span>

<span data-ttu-id="36e44-259">Es folgt eine Übersicht über die Hauptprojektordner und -dateien, mit denen Sie in späteren Tutorials arbeiten werden.</span><span class="sxs-lookup"><span data-stu-id="36e44-259">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="36e44-260">Ordner „Seiten“</span><span class="sxs-lookup"><span data-stu-id="36e44-260">Pages folder</span></span>

<span data-ttu-id="36e44-261">Enthält Razor-Seiten und unterstützende Dateien.</span><span class="sxs-lookup"><span data-stu-id="36e44-261">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="36e44-262">Jede Razor-Seite besteht aus einem Dateienpaar:</span><span class="sxs-lookup"><span data-stu-id="36e44-262">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="36e44-263">Eine *.cshtml*-Datei, die HTML-Markup mit C#-Code in Razor-Syntax enthält</span><span class="sxs-lookup"><span data-stu-id="36e44-263">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="36e44-264">Eine *.cshtml.cs*-Datei mit C# Code, in dem Seitenereignisse verarbeitet werden</span><span class="sxs-lookup"><span data-stu-id="36e44-264">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="36e44-265">Unterstützende Dateien haben Namen, die mit einem Unterstrich beginnen.</span><span class="sxs-lookup"><span data-stu-id="36e44-265">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="36e44-266">Zum Beispiel sind in der Datei *_Layout.cshtml* Benutzeroberflächenelemente konfiguriert, die für alle Seiten gelten.</span><span class="sxs-lookup"><span data-stu-id="36e44-266">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="36e44-267">Mit dieser Datei werden das Navigationsmenü oben auf der Seite und der Urheberrechtshinweis unten auf der Seite eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="36e44-267">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="36e44-268">Weitere Informationen finden Sie unter <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="36e44-268">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="36e44-269">Ordner „wwwroot“</span><span class="sxs-lookup"><span data-stu-id="36e44-269">wwwroot folder</span></span>

<span data-ttu-id="36e44-270">Enthält statische Dateien, z. B. HTML-Dateien, JavaScript-Dateien und CSS-Dateien.</span><span class="sxs-lookup"><span data-stu-id="36e44-270">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="36e44-271">Weitere Informationen finden Sie unter <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="36e44-271">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="36e44-272">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="36e44-272">appSettings.json</span></span>

<span data-ttu-id="36e44-273">Enthält Konfigurationsdaten, z. B. Verbindungszeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="36e44-273">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="36e44-274">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="36e44-274">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="36e44-275">Program.cs</span><span class="sxs-lookup"><span data-stu-id="36e44-275">Program.cs</span></span>

<span data-ttu-id="36e44-276">Enthält den Einstiegspunkt für das Programm.</span><span class="sxs-lookup"><span data-stu-id="36e44-276">Contains the entry point for the program.</span></span> <span data-ttu-id="36e44-277">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="36e44-277">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="36e44-278">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="36e44-278">Startup.cs</span></span>

<span data-ttu-id="36e44-279">Enthält Code, mit dem das App-Verhalten konfiguriert wird, beispielsweise, ob die App Zustimmung für Cookies erfordert.</span><span class="sxs-lookup"><span data-stu-id="36e44-279">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="36e44-280">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="36e44-280">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="36e44-281">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="36e44-281">Additional resources</span></span>

* [<span data-ttu-id="36e44-282">Dieses Tutorials auf YouTube</span><span class="sxs-lookup"><span data-stu-id="36e44-282">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="36e44-283">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="36e44-283">Next steps</span></span>

<span data-ttu-id="36e44-284">Wechseln Sie zum nächsten Tutorial in der Reihe:</span><span class="sxs-lookup"><span data-stu-id="36e44-284">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="36e44-285">Hinzufügen eines Modells</span><span class="sxs-lookup"><span data-stu-id="36e44-285">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
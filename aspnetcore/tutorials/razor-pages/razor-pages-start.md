---
title: 'Tutorial: Erste Schritte mit Razor Pages in ASP.NET Core'
author: rick-anderson
description: Diese Reihe von Tutorials zeigt, wie Sie Razor Pages in ASP.NET Core verwenden können. Erfahren Sie, wie Sie ein Modell erstellen, Code für Razor-Seiten generieren, Entity Framework Core und SQL Server für den Datenzugriff verwenden, Suchfunktionen hinzufügen, Eingabeüberprüfung hinzufügen und Migrationen verwenden, um das Modell zu aktualisieren.
ms.author: riande
ms.date: 12/5/2018
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 81a2a76fc1cecc78b69226fe714d7c9272b04bf7
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/27/2019
ms.locfileid: "56899190"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="c583d-104">Tutorial: Erste Schritte mit Razor Pages in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c583d-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="c583d-105">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c583d-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c583d-106">Dies ist das erste Tutorial aus einer Reihe.</span><span class="sxs-lookup"><span data-stu-id="c583d-106">This is the first tutorial of a series.</span></span> <span data-ttu-id="c583d-107">[In der Reihe](xref:tutorials/razor-pages/index) lernen Sie die Grundlagen zum Erstellen einer Razor Pages-Web-App mit ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c583d-107">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="c583d-108">Am Ende der Reihe verfügen Sie über eine App, mit der eine Filmdatenbank verwaltet werden kann.</span><span class="sxs-lookup"><span data-stu-id="c583d-108">At the end of the series you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="c583d-109">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="c583d-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c583d-110">Erstellen einer Razor Pages-Web-App</span><span class="sxs-lookup"><span data-stu-id="c583d-110">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="c583d-111">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="c583d-111">Run the app.</span></span>
> * <span data-ttu-id="c583d-112">Überprüfen Sie die Projektdateien.</span><span class="sxs-lookup"><span data-stu-id="c583d-112">Examine the project files.</span></span>

<span data-ttu-id="c583d-113">Am Ende dieses Tutorials verfügen Sie über eine funktionsfähige Razor Pages-Web-App, auf der Sie in späteren Tutorials aufbauen werden.</span><span class="sxs-lookup"><span data-stu-id="c583d-113">At the end of this tutorial you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)

[!INCLUDE[](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="c583d-115">Erstellen einer Razor Pages-Web-App</span><span class="sxs-lookup"><span data-stu-id="c583d-115">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c583d-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c583d-116">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c583d-117">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="c583d-117">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="c583d-118">Erstellen Sie eine neue ASP.NET Core-Webanwendung.</span><span class="sxs-lookup"><span data-stu-id="c583d-118">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="c583d-119">Nennen Sie das Projekt **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="c583d-119">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="c583d-120">Es ist wichtig, den Namen *RazorPagesMovie* zu verwenden, damit die Namespaces übereinstimmen, wenn Sie Code kopieren und einfügen.</span><span class="sxs-lookup"><span data-stu-id="c583d-120">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="c583d-122">Wählen Sie in der Dropdownliste **ASP.NET Core 2.2** und anschließend **Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="c583d-122">Select **ASP.NET Core 2.2** in the dropdown, and then select **Web Application**.</span></span>

  ![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="c583d-124">Das folgende Startprojekt wird erstellt:</span><span class="sxs-lookup"><span data-stu-id="c583d-124">The following starter project is created:</span></span>

  ![Projektmappen-Explorer](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c583d-126">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c583d-126">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c583d-127">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="c583d-127">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="c583d-128">Wechseln Sie mit `cd` zu einem Ordner, der das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="c583d-128">Change directories (`cd`) to a folder which will contain the project.</span></span>

* <span data-ttu-id="c583d-129">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="c583d-129">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="c583d-130">Der Befehl `dotnet new` erstellt ein neues Razor Pages-Projekt im Ordner *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="c583d-130">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="c583d-131">Der Befehl `code` öffnet den Ordner *RazorPagesMovie* in einer neuen Instanz von Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="c583d-131">The `code` command opens the *RazorPagesMovie* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="c583d-132">Es wird ein Dialogfeld mit folgender Meldung angezeigt: **Die erforderlichen Objekte zum Erstellen und Debuggen sind in "RazorPagesMovie" nicht vorhanden. Sollen sie hinzugefügt werden?**</span><span class="sxs-lookup"><span data-stu-id="c583d-132">A dialog box appears with **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span>

* <span data-ttu-id="c583d-133">Wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="c583d-133">Select **Yes**</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c583d-134">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="c583d-134">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="c583d-135">Führen Sie über ein Terminal die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="c583d-135">From a terminal, run the following commands:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
cd RazorPagesMovie
```

<span data-ttu-id="c583d-136">Diese Befehle verwenden die [.NET Core-CLI](/dotnet/core/tools/dotnet), um ein Razor Pages-Projekt zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="c583d-136">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="c583d-137">Öffnen des Projekts</span><span class="sxs-lookup"><span data-stu-id="c583d-137">Open the project</span></span>

<span data-ttu-id="c583d-138">Klicken Sie in Visual Studio auf **Datei > Öffnen**, und wählen Sie dann die Datei *RazorPagesMovie.csproj* aus.</span><span class="sxs-lookup"><span data-stu-id="c583d-138">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="c583d-139">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="c583d-139">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c583d-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c583d-140">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c583d-141">Drücken Sie STRG+F5, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="c583d-141">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="c583d-142">Visual Studio startet [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) und führt die App aus.</span><span class="sxs-lookup"><span data-stu-id="c583d-142">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="c583d-143">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="c583d-143">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="c583d-144">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="c583d-144">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="c583d-145">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="c583d-145">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="c583d-146">Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.</span><span class="sxs-lookup"><span data-stu-id="c583d-146">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c583d-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c583d-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c583d-148">Drücken Sie **STRG+F5**, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="c583d-148">Press **Ctrl-F5** to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="c583d-149">Visual Studio Code startet [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="c583d-149">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="c583d-150">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="c583d-150">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="c583d-151">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="c583d-151">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="c583d-152">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="c583d-152">Localhost only serves web requests from the local computer.</span></span>
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c583d-153">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="c583d-153">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="c583d-154">Klicken Sie in Visual Studio auf **Ausführen > Ohne Debuggen starten**, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="c583d-154">Select **Run > Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="c583d-155">Visual Studio startet dann [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="c583d-155">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

<!-- End of VS tabs -->

---

* <span data-ttu-id="c583d-156">Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="c583d-156">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="c583d-157">Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.</span><span class="sxs-lookup"><span data-stu-id="c583d-157">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="c583d-159">Die folgende Abbildung zeigt die App, nachdem Sie das Einverständnis zur Nachverfolgung gegeben haben:</span><span class="sxs-lookup"><span data-stu-id="c583d-159">The following image shows the app after you give consent to tracking:</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)

## <a name="examine-the-project-files"></a><span data-ttu-id="c583d-161">Überprüfen der Projektdateien</span><span class="sxs-lookup"><span data-stu-id="c583d-161">Examine the project files</span></span>

<span data-ttu-id="c583d-162">Es folgt eine Übersicht über die Hauptprojektordner und -dateien, mit denen Sie in späteren Tutorials arbeiten werden.</span><span class="sxs-lookup"><span data-stu-id="c583d-162">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="c583d-163">Ordner „Seiten“</span><span class="sxs-lookup"><span data-stu-id="c583d-163">Pages folder</span></span>

<span data-ttu-id="c583d-164">Enthält Razor-Seiten und unterstützende Dateien.</span><span class="sxs-lookup"><span data-stu-id="c583d-164">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="c583d-165">Jede Razor-Seite besteht aus einem Dateienpaar:</span><span class="sxs-lookup"><span data-stu-id="c583d-165">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="c583d-166">Eine *.cshtml*-Datei, die HTML-Markup mit C#-Code in Razor-Syntax enthält</span><span class="sxs-lookup"><span data-stu-id="c583d-166">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="c583d-167">Eine *.cshtml.cs*-Datei mit C# Code, in dem Seitenereignisse verarbeitet werden</span><span class="sxs-lookup"><span data-stu-id="c583d-167">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="c583d-168">Unterstützende Dateien haben Namen, die mit einem Unterstrich beginnen.</span><span class="sxs-lookup"><span data-stu-id="c583d-168">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="c583d-169">Zum Beispiel sind in der Datei *_Layout.cshtml* Benutzeroberflächenelemente konfiguriert, die für alle Seiten gelten.</span><span class="sxs-lookup"><span data-stu-id="c583d-169">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="c583d-170">Mit dieser Datei werden das Navigationsmenü oben auf der Seite und der Urheberrechtshinweis unten auf der Seite eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="c583d-170">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="c583d-171">Weitere Informationen finden Sie unter <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="c583d-171">For more information, see <xref:mvc/views/layout>.</span></span>


### <a name="wwwroot-folder"></a><span data-ttu-id="c583d-172">Ordner „wwwroot“</span><span class="sxs-lookup"><span data-stu-id="c583d-172">wwwroot folder</span></span>

<span data-ttu-id="c583d-173">Enthält statische Dateien, z. B. HTML-Dateien, JavaScript-Dateien und CSS-Dateien.</span><span class="sxs-lookup"><span data-stu-id="c583d-173">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="c583d-174">Weitere Informationen finden Sie unter <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="c583d-174">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="c583d-175">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="c583d-175">appSettings.json</span></span>

<span data-ttu-id="c583d-176">Enthält Konfigurationsdaten, z. B. Verbindungszeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="c583d-176">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="c583d-177">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="c583d-177">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="c583d-178">Program.cs</span><span class="sxs-lookup"><span data-stu-id="c583d-178">Program.cs</span></span>

<span data-ttu-id="c583d-179">Enthält den Einstiegspunkt für das Programm.</span><span class="sxs-lookup"><span data-stu-id="c583d-179">Contains the entry point for the program.</span></span> <span data-ttu-id="c583d-180">Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="c583d-180">For more information, see <xref:fundamentals/host/web-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="c583d-181">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="c583d-181">Startup.cs</span></span>

<span data-ttu-id="c583d-182">Enthält Code, mit dem das App-Verhalten konfiguriert wird, beispielsweise, ob die App Zustimmung für Cookies erfordert.</span><span class="sxs-lookup"><span data-stu-id="c583d-182">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="c583d-183">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="c583d-183">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="c583d-184">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="c583d-184">Next steps</span></span>

<span data-ttu-id="c583d-185">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="c583d-185">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c583d-186">Haben Sie eine Razor Pages-Web-App erstellt.</span><span class="sxs-lookup"><span data-stu-id="c583d-186">Created a Razor Pages web app.</span></span>
> * <span data-ttu-id="c583d-187">Haben Sie die App ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="c583d-187">Ran the app.</span></span>
> * <span data-ttu-id="c583d-188">Haben Sie die Projektdateien überprüft.</span><span class="sxs-lookup"><span data-stu-id="c583d-188">Examined the project files.</span></span>

<span data-ttu-id="c583d-189">Wechseln Sie zum nächsten Tutorial in der Reihe:</span><span class="sxs-lookup"><span data-stu-id="c583d-189">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="c583d-190">Hinzufügen eines Modells</span><span class="sxs-lookup"><span data-stu-id="c583d-190">Add a model</span></span>](xref:tutorials/razor-pages/model)

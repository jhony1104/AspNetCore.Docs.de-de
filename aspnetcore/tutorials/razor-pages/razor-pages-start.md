---
title: 'Tutorial: Erste Schritte mit Razor Pages in ASP.NET Core'
author: rick-anderson
description: Diese Reihe von Tutorials zeigt, wie Sie Razor Pages in ASP.NET Core verwenden können. Erfahren Sie, wie Sie ein Modell erstellen, Code für Razor-Seiten generieren, Entity Framework Core und SQL Server für den Datenzugriff verwenden, Suchfunktionen hinzufügen, Eingabeüberprüfung hinzufügen und Migrationen verwenden, um das Modell zu aktualisieren.
ms.author: riande
ms.date: 12/5/2018
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: acebe52719e1876dc6808441fcff6fe849e36983
ms.sourcegitcommit: d75d8eb26c2cce19876c8d5b65ac8a4b21f625ef
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/19/2019
ms.locfileid: "56410413"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="4cd5a-104">Tutorial: Erste Schritte mit Razor Pages in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4cd5a-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="4cd5a-105">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4cd5a-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4cd5a-106">Dies ist das erste Tutorial aus einer Reihe.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-106">This is the first tutorial of a series.</span></span> <span data-ttu-id="4cd5a-107">[In der Reihe](xref:tutorials/razor-pages/index) lernen Sie die Grundlagen zum Erstellen einer Razor Pages-Web-App mit ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-107">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="4cd5a-108">Am Ende der Reihe verfügen Sie über eine App, mit der eine Filmdatenbank verwaltet werden kann.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-108">At the end of the series you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="4cd5a-109">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="4cd5a-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4cd5a-110">Erstellen einer Razor Pages-Web-App</span><span class="sxs-lookup"><span data-stu-id="4cd5a-110">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="4cd5a-111">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-111">Run the app.</span></span>
> * <span data-ttu-id="4cd5a-112">Überprüfen Sie die Projektdateien.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-112">Examine the project files.</span></span>

<span data-ttu-id="4cd5a-113">Am Ende dieses Tutorials verfügen Sie über eine funktionsfähige Razor Pages-Web-App, auf der Sie in späteren Tutorials aufbauen werden.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-113">At the end of this tutorial you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)

[!INCLUDE[](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="4cd5a-115">Erstellen einer Razor Pages-Web-App</span><span class="sxs-lookup"><span data-stu-id="4cd5a-115">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4cd5a-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4cd5a-116">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4cd5a-117">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-117">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="4cd5a-118">Erstellen Sie eine neue ASP.NET Core-Webanwendung.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-118">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="4cd5a-119">Nennen Sie das Projekt **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-119">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="4cd5a-120">Es ist wichtig, den Namen *RazorPagesMovie* zu verwenden, damit die Namespaces übereinstimmen, wenn Sie Code kopieren und einfügen.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-120">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="4cd5a-122">Wählen Sie in der Dropdownliste **ASP.NET Core 2.2** und anschließend **Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-122">Select **ASP.NET Core 2.2** in the dropdown, and then select **Web Application**.</span></span>

  ![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="4cd5a-124">Das folgende Startprojekt wird erstellt:</span><span class="sxs-lookup"><span data-stu-id="4cd5a-124">The following starter project is created:</span></span>

  ![Projektmappen-Explorer](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4cd5a-126">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4cd5a-126">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4cd5a-127">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="4cd5a-127">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="4cd5a-128">Wechseln Sie mit `cd` zu einem Ordner, der das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-128">Change directories (`cd`) to a folder which will contain the project.</span></span>

* <span data-ttu-id="4cd5a-129">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="4cd5a-129">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="4cd5a-130">Der Befehl `dotnet new` erstellt ein neues Razor Pages-Projekt im Ordner *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-130">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="4cd5a-131">Der Befehl `code` öffnet den Ordner *RazorPagesMovie* in einer neuen Instanz von Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-131">The `code` command opens the *RazorPagesMovie* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="4cd5a-132">Es wird ein Dialogfeld mit folgender Meldung angezeigt: **Die erforderlichen Objekte zum Erstellen und Debuggen sind in "RazorPagesMovie" nicht vorhanden. Sollen sie hinzugefügt werden?**</span><span class="sxs-lookup"><span data-stu-id="4cd5a-132">A dialog box appears with **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span>

* <span data-ttu-id="4cd5a-133">Wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-133">Select **Yes**</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4cd5a-134">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4cd5a-134">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4cd5a-135">Führen Sie über ein Terminal die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="4cd5a-135">From a terminal, run the following commands:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
cd RazorPagesMovie
```

<span data-ttu-id="4cd5a-136">Diese Befehle verwenden die [.NET Core-CLI](/dotnet/core/tools/dotnet), um ein Razor Pages-Projekt zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-136">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="4cd5a-137">Öffnen des Projekts</span><span class="sxs-lookup"><span data-stu-id="4cd5a-137">Open the project</span></span>

<span data-ttu-id="4cd5a-138">Klicken Sie in Visual Studio auf **Datei > Öffnen**, und wählen Sie dann die Datei *RazorPagesMovie.csproj* aus.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-138">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-web-app"></a><span data-ttu-id="4cd5a-139">Webanwendung ausführen</span><span class="sxs-lookup"><span data-stu-id="4cd5a-139">Run the web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4cd5a-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4cd5a-140">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4cd5a-141">Drücken Sie STRG+F5, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-141">Press Ctrl+F5 to run without the debugger.</span></span>

  <span data-ttu-id="4cd5a-142">Visual Studio startet [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) und führt die App aus.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-142">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="4cd5a-143">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-143">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="4cd5a-144">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-144">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="4cd5a-145">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-145">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="4cd5a-146">Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-146">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="4cd5a-147">In der vorherigen Abbildung ist die Portnummer 5001.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-147">In the preceding image, the port number is 5001.</span></span> <span data-ttu-id="4cd5a-148">Wenn Sie die App ausführen, wird eine andere Portnummer angezeigt.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-148">When you run the app, you'll see a different port number.</span></span>
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4cd5a-149">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4cd5a-149">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4cd5a-150">Drücken Sie **STRG+F5**, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-150">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="4cd5a-151">Visual Studio Code startet [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-151">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="4cd5a-152">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-152">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="4cd5a-153">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-153">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="4cd5a-154">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-154">Localhost only serves web requests from the local computer.</span></span>
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4cd5a-155">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4cd5a-155">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4cd5a-156">Klicken Sie in Visual Studio auf **Ausführen > Ohne Debuggen starten**, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-156">Select **Run > Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="4cd5a-157">Visual Studio startet dann [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-157">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

<!-- End of VS tabs -->

---

* <span data-ttu-id="4cd5a-158">Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-158">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="4cd5a-159">Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-159">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="4cd5a-161">Die folgende Abbildung zeigt die App, nachdem Sie das Einverständnis zur Nachverfolgung gegeben haben:</span><span class="sxs-lookup"><span data-stu-id="4cd5a-161">The following image shows the app after you give consent to tracking:</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)

## <a name="examine-the-project-files"></a><span data-ttu-id="4cd5a-163">Überprüfen der Projektdateien</span><span class="sxs-lookup"><span data-stu-id="4cd5a-163">Examine the project files</span></span>

<span data-ttu-id="4cd5a-164">Es folgt eine Übersicht über die Hauptprojektordner und -dateien, mit denen Sie in späteren Tutorials arbeiten werden.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-164">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="4cd5a-165">Ordner „Seiten“</span><span class="sxs-lookup"><span data-stu-id="4cd5a-165">Pages folder</span></span>

<span data-ttu-id="4cd5a-166">Enthält Razor-Seiten und unterstützende Dateien.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-166">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="4cd5a-167">Jede Razor-Seite besteht aus einem Dateienpaar:</span><span class="sxs-lookup"><span data-stu-id="4cd5a-167">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="4cd5a-168">Eine *.cshtml*-Datei, die HTML-Markup mit C#-Code in Razor-Syntax enthält</span><span class="sxs-lookup"><span data-stu-id="4cd5a-168">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="4cd5a-169">Eine *.cshtml.cs*-Datei mit C# Code, in dem Seitenereignisse verarbeitet werden</span><span class="sxs-lookup"><span data-stu-id="4cd5a-169">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="4cd5a-170">Unterstützende Dateien haben Namen, die mit einem Unterstrich beginnen.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-170">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="4cd5a-171">Zum Beispiel sind in der Datei *_Layout.cshtml* Benutzeroberflächenelemente konfiguriert, die für alle Seiten gelten.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-171">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="4cd5a-172">Mit dieser Datei werden das Navigationsmenü oben auf der Seite und der Urheberrechtshinweis unten auf der Seite eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-172">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="4cd5a-173">Weitere Informationen finden Sie unter <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-173">For more information, see <xref:mvc/views/layout>.</span></span>


### <a name="wwwroot-folder"></a><span data-ttu-id="4cd5a-174">Ordner „wwwroot“</span><span class="sxs-lookup"><span data-stu-id="4cd5a-174">wwwroot folder</span></span>

<span data-ttu-id="4cd5a-175">Enthält statische Dateien, z. B. HTML-Dateien, JavaScript-Dateien und CSS-Dateien.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-175">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="4cd5a-176">Weitere Informationen finden Sie unter <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-176">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="4cd5a-177">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="4cd5a-177">appSettings.json</span></span>

<span data-ttu-id="4cd5a-178">Enthält Konfigurationsdaten, z. B. Verbindungszeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-178">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="4cd5a-179">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-179">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="4cd5a-180">Program.cs</span><span class="sxs-lookup"><span data-stu-id="4cd5a-180">Program.cs</span></span>

<span data-ttu-id="4cd5a-181">Enthält den Einstiegspunkt für das Programm.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-181">Contains the entry point for the program.</span></span> <span data-ttu-id="4cd5a-182">Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-182">For more information, see <xref:fundamentals/host/web-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="4cd5a-183">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="4cd5a-183">Startup.cs</span></span>

<span data-ttu-id="4cd5a-184">Enthält Code, mit dem das App-Verhalten konfiguriert wird, beispielsweise, ob die App Zustimmung für Cookies erfordert.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-184">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="4cd5a-185">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-185">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="4cd5a-186">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="4cd5a-186">Next steps</span></span>

<span data-ttu-id="4cd5a-187">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="4cd5a-187">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4cd5a-188">Haben Sie eine Razor Pages-Web-App erstellt.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-188">Created a Razor Pages web app.</span></span>
> * <span data-ttu-id="4cd5a-189">Haben Sie die App ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-189">Ran the app.</span></span>
> * <span data-ttu-id="4cd5a-190">Haben Sie die Projektdateien überprüft.</span><span class="sxs-lookup"><span data-stu-id="4cd5a-190">Examined the project files.</span></span>

<span data-ttu-id="4cd5a-191">Wechseln Sie zum nächsten Tutorial in der Reihe:</span><span class="sxs-lookup"><span data-stu-id="4cd5a-191">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="4cd5a-192">Hinzufügen eines Modells</span><span class="sxs-lookup"><span data-stu-id="4cd5a-192">Add a model</span></span>](xref:tutorials/razor-pages/model)

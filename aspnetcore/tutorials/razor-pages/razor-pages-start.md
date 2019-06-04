---
title: 'Tutorial: Erste Schritte mit Razor Pages in ASP.NET Core'
author: rick-anderson
description: Diese Reihe von Tutorials zeigt, wie Sie Razor Pages in ASP.NET Core verwenden können. Erfahren Sie, wie Sie ein Modell erstellen, Code für Razor-Seiten generieren, Entity Framework Core und SQL Server für den Datenzugriff verwenden, Suchfunktionen hinzufügen, Eingabeüberprüfung hinzufügen und Migrationen verwenden, um das Modell zu aktualisieren.
ms.author: riande
ms.date: 05/30/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: e9f11f68aa138ab74a0ffbbd0e32067bc984606d
ms.sourcegitcommit: 9ae1fd11f39b0a72b2ae42f0b450345e6e306bc0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66415661"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="92431-104">Tutorial: Erste Schritte mit Razor Pages in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="92431-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="92431-105">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="92431-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="92431-106">Dies ist das erste Tutorial aus einer Reihe.</span><span class="sxs-lookup"><span data-stu-id="92431-106">This is the first tutorial of a series.</span></span> <span data-ttu-id="92431-107">[In der Reihe](xref:tutorials/razor-pages/index) lernen Sie die Grundlagen zum Erstellen einer Razor Pages-Web-App mit ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="92431-107">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="92431-108">Am Ende der Reihe verfügen Sie über eine App, mit der eine Filmdatenbank verwaltet werden kann.</span><span class="sxs-lookup"><span data-stu-id="92431-108">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="92431-109">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="92431-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="92431-110">Erstellen einer Razor Pages-Web-App</span><span class="sxs-lookup"><span data-stu-id="92431-110">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="92431-111">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="92431-111">Run the app.</span></span>
> * <span data-ttu-id="92431-112">Überprüfen Sie die Projektdateien.</span><span class="sxs-lookup"><span data-stu-id="92431-112">Examine the project files.</span></span>

<span data-ttu-id="92431-113">Am Ende dieses Tutorials verfügen Sie über eine funktionsfähige Razor Pages-Web-App, auf der Sie in späteren Tutorials aufbauen werden.</span><span class="sxs-lookup"><span data-stu-id="92431-113">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)

[!INCLUDE[](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="92431-115">Erstellen einer Razor Pages-Web-App</span><span class="sxs-lookup"><span data-stu-id="92431-115">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="92431-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92431-116">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="92431-117">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="92431-117">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="92431-118">Erstellen Sie eine neue ASP.NET Core-Webanwendung.</span><span class="sxs-lookup"><span data-stu-id="92431-118">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="92431-119">Nennen Sie das Projekt **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="92431-119">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="92431-120">Es ist wichtig, den Namen *RazorPagesMovie* zu verwenden, damit die Namespaces übereinstimmen, wenn Sie Code kopieren und einfügen.</span><span class="sxs-lookup"><span data-stu-id="92431-120">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="92431-122">Wählen Sie in der Dropdownliste **ASP.NET Core 2.2** und anschließend **Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="92431-122">Select **ASP.NET Core 2.2** in the dropdown, and then select **Web Application**.</span></span>

  ![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="92431-124">Das folgende Startprojekt wird erstellt:</span><span class="sxs-lookup"><span data-stu-id="92431-124">The following starter project is created:</span></span>

  ![Projektmappen-Explorer](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="92431-126">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="92431-126">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="92431-127">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="92431-127">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="92431-128">Wechseln Sie zum Verzeichnis (`cd`), das das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="92431-128">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="92431-129">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="92431-129">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="92431-130">Der Befehl `dotnet new` erstellt ein neues Razor Pages-Projekt im Ordner *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="92431-130">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="92431-131">Der Befehl `code` öffnet den Ordner *RazorPagesMovie* in der aktuellen Instanz von Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="92431-131">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="92431-132">Wenn das OmniSharp-Flammensymbol in der Statusleiste grün angezeigt wird, wird ein Dialogfeld mit folgender Meldung angezeigt: **Required assets to build and debug are missing from 'RazorPagesMovie'. (Die erforderlichen Objekte zum Erstellen und Debuggen sind in „RazorPagesMovie“ nicht vorhanden. Sollen sie hinzugefügt werden?**</span><span class="sxs-lookup"><span data-stu-id="92431-132">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="92431-133">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="92431-133">Select **Yes**.</span></span>

  <span data-ttu-id="92431-134">Ein *VSCODE*-Verzeichnis, das die Dateien *launch.json* und *tasks.json* enthält, wird dem Stammverzeichnis des Projekts hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="92431-134">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="92431-135">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="92431-135">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="92431-136">Führen Sie über ein Terminal den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="92431-136">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="92431-137">Diese Befehle verwenden die [.NET Core-CLI](/dotnet/core/tools/dotnet), um ein Razor Pages-Projekt zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="92431-137">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="92431-138">Öffnen des Projekts</span><span class="sxs-lookup"><span data-stu-id="92431-138">Open the project</span></span>

<span data-ttu-id="92431-139">Klicken Sie in Visual Studio auf **Datei > Öffnen**, und wählen Sie dann die Datei *RazorPagesMovie.csproj* aus.</span><span class="sxs-lookup"><span data-stu-id="92431-139">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="92431-140">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="92431-140">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="92431-141">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92431-141">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="92431-142">Drücken Sie STRG+F5, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="92431-142">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="92431-143">Visual Studio startet [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) und führt die App aus.</span><span class="sxs-lookup"><span data-stu-id="92431-143">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="92431-144">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="92431-144">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="92431-145">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="92431-145">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="92431-146">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="92431-146">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="92431-147">Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.</span><span class="sxs-lookup"><span data-stu-id="92431-147">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="92431-148">Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="92431-148">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="92431-149">Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.</span><span class="sxs-lookup"><span data-stu-id="92431-149">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="92431-151">Die folgende Abbildung zeigt die App, nachdem Sie das Einverständnis zur Nachverfolgung gegeben haben:</span><span class="sxs-lookup"><span data-stu-id="92431-151">The following image shows the app after you give consent to tracking:</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="92431-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="92431-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="92431-154">Drücken Sie **STRG+F5**, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="92431-154">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="92431-155">Visual Studio Code startet [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="92431-155">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="92431-156">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="92431-156">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="92431-157">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="92431-157">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="92431-158">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="92431-158">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="92431-159">Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="92431-159">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="92431-160">Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.</span><span class="sxs-lookup"><span data-stu-id="92431-160">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="92431-162">Die folgende Abbildung zeigt die App, nachdem Sie das Einverständnis zur Nachverfolgung gegeben haben:</span><span class="sxs-lookup"><span data-stu-id="92431-162">The following image shows the app after you give consent to tracking:</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="92431-164">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="92431-164">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="92431-165">Drücken Sie **CMD+OPT+F5** für eine Ausführung ohne Debugger.</span><span class="sxs-lookup"><span data-stu-id="92431-165">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="92431-166">Visual Studio startet dann [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="92431-166">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="92431-167">Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="92431-167">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="92431-168">Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.</span><span class="sxs-lookup"><span data-stu-id="92431-168">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="92431-170">Die folgende Abbildung zeigt die App, nachdem Sie das Einverständnis zur Nachverfolgung gegeben haben:</span><span class="sxs-lookup"><span data-stu-id="92431-170">The following image shows the app after you give consent to tracking:</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="92431-172">Überprüfen der Projektdateien</span><span class="sxs-lookup"><span data-stu-id="92431-172">Examine the project files</span></span>

<span data-ttu-id="92431-173">Es folgt eine Übersicht über die Hauptprojektordner und -dateien, mit denen Sie in späteren Tutorials arbeiten werden.</span><span class="sxs-lookup"><span data-stu-id="92431-173">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="92431-174">Ordner „Seiten“</span><span class="sxs-lookup"><span data-stu-id="92431-174">Pages folder</span></span>

<span data-ttu-id="92431-175">Enthält Razor-Seiten und unterstützende Dateien.</span><span class="sxs-lookup"><span data-stu-id="92431-175">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="92431-176">Jede Razor-Seite besteht aus einem Dateienpaar:</span><span class="sxs-lookup"><span data-stu-id="92431-176">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="92431-177">Eine *.cshtml*-Datei, die HTML-Markup mit C#-Code in Razor-Syntax enthält</span><span class="sxs-lookup"><span data-stu-id="92431-177">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="92431-178">Eine *.cshtml.cs*-Datei mit C# Code, in dem Seitenereignisse verarbeitet werden</span><span class="sxs-lookup"><span data-stu-id="92431-178">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="92431-179">Unterstützende Dateien haben Namen, die mit einem Unterstrich beginnen.</span><span class="sxs-lookup"><span data-stu-id="92431-179">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="92431-180">Zum Beispiel sind in der Datei *_Layout.cshtml* Benutzeroberflächenelemente konfiguriert, die für alle Seiten gelten.</span><span class="sxs-lookup"><span data-stu-id="92431-180">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="92431-181">Mit dieser Datei werden das Navigationsmenü oben auf der Seite und der Urheberrechtshinweis unten auf der Seite eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="92431-181">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="92431-182">Weitere Informationen finden Sie unter <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="92431-182">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="92431-183">Ordner „wwwroot“</span><span class="sxs-lookup"><span data-stu-id="92431-183">wwwroot folder</span></span>

<span data-ttu-id="92431-184">Enthält statische Dateien, z. B. HTML-Dateien, JavaScript-Dateien und CSS-Dateien.</span><span class="sxs-lookup"><span data-stu-id="92431-184">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="92431-185">Weitere Informationen finden Sie unter <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="92431-185">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="92431-186">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="92431-186">appSettings.json</span></span>

<span data-ttu-id="92431-187">Enthält Konfigurationsdaten, z. B. Verbindungszeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="92431-187">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="92431-188">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="92431-188">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="92431-189">Program.cs</span><span class="sxs-lookup"><span data-stu-id="92431-189">Program.cs</span></span>

<span data-ttu-id="92431-190">Enthält den Einstiegspunkt für das Programm.</span><span class="sxs-lookup"><span data-stu-id="92431-190">Contains the entry point for the program.</span></span> <span data-ttu-id="92431-191">Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="92431-191">For more information, see <xref:fundamentals/host/web-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="92431-192">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="92431-192">Startup.cs</span></span>

<span data-ttu-id="92431-193">Enthält Code, mit dem das App-Verhalten konfiguriert wird, beispielsweise, ob die App Zustimmung für Cookies erfordert.</span><span class="sxs-lookup"><span data-stu-id="92431-193">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="92431-194">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="92431-194">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="92431-195">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="92431-195">Additional resources</span></span>

* [<span data-ttu-id="92431-196">Dieses Tutorials auf YouTube</span><span class="sxs-lookup"><span data-stu-id="92431-196">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="92431-197">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="92431-197">Next steps</span></span>

<span data-ttu-id="92431-198">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="92431-198">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="92431-199">Haben Sie eine Razor Pages-Web-App erstellt.</span><span class="sxs-lookup"><span data-stu-id="92431-199">Created a Razor Pages web app.</span></span>
> * <span data-ttu-id="92431-200">Haben Sie die App ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="92431-200">Ran the app.</span></span>
> * <span data-ttu-id="92431-201">Haben Sie die Projektdateien überprüft.</span><span class="sxs-lookup"><span data-stu-id="92431-201">Examined the project files.</span></span>

<span data-ttu-id="92431-202">Wechseln Sie zum nächsten Tutorial in der Reihe:</span><span class="sxs-lookup"><span data-stu-id="92431-202">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="92431-203">Hinzufügen eines Modells</span><span class="sxs-lookup"><span data-stu-id="92431-203">Add a model</span></span>](xref:tutorials/razor-pages/model)

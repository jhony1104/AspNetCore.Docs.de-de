---
title: 'Tutorial: Erste Schritte mit Razor Pages in ASP.NET Core'
author: rick-anderson
description: Diese Reihe von Tutorials zeigt, wie Sie Razor Pages in ASP.NET Core verwenden können. Erfahren Sie, wie Sie ein Modell erstellen, Code für Razor-Seiten generieren, Entity Framework Core und SQL Server für den Datenzugriff verwenden, Suchfunktionen hinzufügen, Eingabeüberprüfung hinzufügen und Migrationen verwenden, um das Modell zu aktualisieren.
ms.author: riande
ms.date: 6/3/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: d843e47ccb5180fab34b4c4c4a4b5cbda21289bf
ms.sourcegitcommit: a1364109d11d414121a6337b611bee61d6e489e9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66491206"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="83a95-104">Tutorial: Erste Schritte mit Razor Pages in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="83a95-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="83a95-105">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="83a95-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="83a95-106">Dies ist das erste Tutorial aus einer Reihe.</span><span class="sxs-lookup"><span data-stu-id="83a95-106">This is the first tutorial of a series.</span></span> <span data-ttu-id="83a95-107">[In der Reihe](xref:tutorials/razor-pages/index) lernen Sie die Grundlagen zum Erstellen einer Razor Pages-Web-App mit ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="83a95-107">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="83a95-108">Am Ende der Reihe verfügen Sie über eine App, mit der eine Filmdatenbank verwaltet werden kann.</span><span class="sxs-lookup"><span data-stu-id="83a95-108">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="83a95-109">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="83a95-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="83a95-110">Erstellen einer Razor Pages-Web-App</span><span class="sxs-lookup"><span data-stu-id="83a95-110">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="83a95-111">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="83a95-111">Run the app.</span></span>
> * <span data-ttu-id="83a95-112">Überprüfen Sie die Projektdateien.</span><span class="sxs-lookup"><span data-stu-id="83a95-112">Examine the project files.</span></span>

<span data-ttu-id="83a95-113">Am Ende dieses Tutorials verfügen Sie über eine funktionsfähige Razor Pages-Web-App, auf der Sie in späteren Tutorials aufbauen werden.</span><span class="sxs-lookup"><span data-stu-id="83a95-113">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)

[!INCLUDE[](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="83a95-115">Erstellen einer Razor Pages-Web-App</span><span class="sxs-lookup"><span data-stu-id="83a95-115">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="83a95-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83a95-116">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="83a95-117">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="83a95-117">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="83a95-118">Erstellen Sie eine neue ASP.NET Core-Webanwendung, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="83a95-118">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="83a95-120">Nennen Sie das Projekt **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="83a95-120">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="83a95-121">Es ist wichtig, dem Projekt den Namen *RazorPagesMovie* zu geben, damit die Namespaces übereinstimmen, wenn Sie Code kopieren und einfügen.</span><span class="sxs-lookup"><span data-stu-id="83a95-121">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code code.</span></span>

  ![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/config.png)

* <span data-ttu-id="83a95-123">Klicken Sie in der Dropdownliste auf **ASP.NET Core 2.2**, auf **Webanwendung** und anschließend auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="83a95-123">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="83a95-125">Das folgende Startprojekt wird erstellt:</span><span class="sxs-lookup"><span data-stu-id="83a95-125">The following starter project is created:</span></span>

  ![Projektmappen-Explorer](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="83a95-127">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="83a95-127">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="83a95-128">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="83a95-128">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="83a95-129">Wechseln Sie zum Verzeichnis (`cd`), das das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="83a95-129">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="83a95-130">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="83a95-130">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="83a95-131">Der Befehl `dotnet new` erstellt ein neues Razor Pages-Projekt im Ordner *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="83a95-131">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="83a95-132">Der Befehl `code` öffnet den Ordner *RazorPagesMovie* in der aktuellen Instanz von Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="83a95-132">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="83a95-133">Wenn das OmniSharp-Flammensymbol in der Statusleiste grün angezeigt wird, wird ein Dialogfeld mit folgender Meldung angezeigt: **Required assets to build and debug are missing from 'RazorPagesMovie'. (Die erforderlichen Objekte zum Erstellen und Debuggen sind in „RazorPagesMovie“ nicht vorhanden. Sollen sie hinzugefügt werden?**</span><span class="sxs-lookup"><span data-stu-id="83a95-133">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="83a95-134">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="83a95-134">Select **Yes**.</span></span>

  <span data-ttu-id="83a95-135">Ein *VSCODE*-Verzeichnis, das die Dateien *launch.json* und *tasks.json* enthält, wird dem Stammverzeichnis des Projekts hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="83a95-135">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="83a95-136">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="83a95-136">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="83a95-137">Führen Sie über ein Terminal den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="83a95-137">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="83a95-138">Diese Befehle verwenden die [.NET Core-CLI](/dotnet/core/tools/dotnet), um ein Razor Pages-Projekt zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="83a95-138">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="83a95-139">Öffnen des Projekts</span><span class="sxs-lookup"><span data-stu-id="83a95-139">Open the project</span></span>

<span data-ttu-id="83a95-140">Klicken Sie in Visual Studio auf **Datei > Öffnen**, und wählen Sie dann die Datei *RazorPagesMovie.csproj* aus.</span><span class="sxs-lookup"><span data-stu-id="83a95-140">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="83a95-141">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="83a95-141">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="83a95-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83a95-142">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="83a95-143">Drücken Sie STRG+F5, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="83a95-143">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="83a95-144">Visual Studio startet [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) und führt die App aus.</span><span class="sxs-lookup"><span data-stu-id="83a95-144">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="83a95-145">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="83a95-145">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="83a95-146">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="83a95-146">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="83a95-147">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="83a95-147">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="83a95-148">Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.</span><span class="sxs-lookup"><span data-stu-id="83a95-148">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="83a95-149">Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="83a95-149">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="83a95-150">Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.</span><span class="sxs-lookup"><span data-stu-id="83a95-150">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="83a95-152">Die folgende Abbildung zeigt die App, nachdem Sie das Einverständnis zur Nachverfolgung gegeben haben:</span><span class="sxs-lookup"><span data-stu-id="83a95-152">The following image shows the app after you give consent to tracking:</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="83a95-154">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="83a95-154">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="83a95-155">Drücken Sie **STRG+F5**, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="83a95-155">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="83a95-156">Visual Studio Code startet [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="83a95-156">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="83a95-157">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="83a95-157">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="83a95-158">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="83a95-158">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="83a95-159">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="83a95-159">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="83a95-160">Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="83a95-160">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="83a95-161">Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.</span><span class="sxs-lookup"><span data-stu-id="83a95-161">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="83a95-163">Die folgende Abbildung zeigt die App, nachdem Sie das Einverständnis zur Nachverfolgung gegeben haben:</span><span class="sxs-lookup"><span data-stu-id="83a95-163">The following image shows the app after you give consent to tracking:</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="83a95-165">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="83a95-165">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="83a95-166">Drücken Sie **CMD+OPT+F5** für eine Ausführung ohne Debugger.</span><span class="sxs-lookup"><span data-stu-id="83a95-166">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="83a95-167">Visual Studio startet dann [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="83a95-167">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="83a95-168">Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="83a95-168">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="83a95-169">Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.</span><span class="sxs-lookup"><span data-stu-id="83a95-169">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="83a95-171">Die folgende Abbildung zeigt die App, nachdem Sie das Einverständnis zur Nachverfolgung gegeben haben:</span><span class="sxs-lookup"><span data-stu-id="83a95-171">The following image shows the app after you give consent to tracking:</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="83a95-173">Überprüfen der Projektdateien</span><span class="sxs-lookup"><span data-stu-id="83a95-173">Examine the project files</span></span>

<span data-ttu-id="83a95-174">Es folgt eine Übersicht über die Hauptprojektordner und -dateien, mit denen Sie in späteren Tutorials arbeiten werden.</span><span class="sxs-lookup"><span data-stu-id="83a95-174">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="83a95-175">Ordner „Seiten“</span><span class="sxs-lookup"><span data-stu-id="83a95-175">Pages folder</span></span>

<span data-ttu-id="83a95-176">Enthält Razor-Seiten und unterstützende Dateien.</span><span class="sxs-lookup"><span data-stu-id="83a95-176">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="83a95-177">Jede Razor-Seite besteht aus einem Dateienpaar:</span><span class="sxs-lookup"><span data-stu-id="83a95-177">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="83a95-178">Eine *.cshtml*-Datei, die HTML-Markup mit C#-Code in Razor-Syntax enthält</span><span class="sxs-lookup"><span data-stu-id="83a95-178">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="83a95-179">Eine *.cshtml.cs*-Datei mit C# Code, in dem Seitenereignisse verarbeitet werden</span><span class="sxs-lookup"><span data-stu-id="83a95-179">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="83a95-180">Unterstützende Dateien haben Namen, die mit einem Unterstrich beginnen.</span><span class="sxs-lookup"><span data-stu-id="83a95-180">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="83a95-181">Zum Beispiel sind in der Datei *_Layout.cshtml* Benutzeroberflächenelemente konfiguriert, die für alle Seiten gelten.</span><span class="sxs-lookup"><span data-stu-id="83a95-181">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="83a95-182">Mit dieser Datei werden das Navigationsmenü oben auf der Seite und der Urheberrechtshinweis unten auf der Seite eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="83a95-182">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="83a95-183">Weitere Informationen finden Sie unter <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="83a95-183">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="83a95-184">Ordner „wwwroot“</span><span class="sxs-lookup"><span data-stu-id="83a95-184">wwwroot folder</span></span>

<span data-ttu-id="83a95-185">Enthält statische Dateien, z. B. HTML-Dateien, JavaScript-Dateien und CSS-Dateien.</span><span class="sxs-lookup"><span data-stu-id="83a95-185">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="83a95-186">Weitere Informationen finden Sie unter <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="83a95-186">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="83a95-187">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="83a95-187">appSettings.json</span></span>

<span data-ttu-id="83a95-188">Enthält Konfigurationsdaten, z. B. Verbindungszeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="83a95-188">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="83a95-189">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="83a95-189">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="83a95-190">Program.cs</span><span class="sxs-lookup"><span data-stu-id="83a95-190">Program.cs</span></span>

<span data-ttu-id="83a95-191">Enthält den Einstiegspunkt für das Programm.</span><span class="sxs-lookup"><span data-stu-id="83a95-191">Contains the entry point for the program.</span></span> <span data-ttu-id="83a95-192">Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="83a95-192">For more information, see <xref:fundamentals/host/web-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="83a95-193">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="83a95-193">Startup.cs</span></span>

<span data-ttu-id="83a95-194">Enthält Code, mit dem das App-Verhalten konfiguriert wird, beispielsweise, ob die App Zustimmung für Cookies erfordert.</span><span class="sxs-lookup"><span data-stu-id="83a95-194">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="83a95-195">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="83a95-195">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="83a95-196">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="83a95-196">Additional resources</span></span>

* [<span data-ttu-id="83a95-197">Dieses Tutorials auf YouTube</span><span class="sxs-lookup"><span data-stu-id="83a95-197">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="83a95-198">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="83a95-198">Next steps</span></span>

<span data-ttu-id="83a95-199">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="83a95-199">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="83a95-200">Haben Sie eine Razor Pages-Web-App erstellt.</span><span class="sxs-lookup"><span data-stu-id="83a95-200">Created a Razor Pages web app.</span></span>
> * <span data-ttu-id="83a95-201">Haben Sie die App ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="83a95-201">Ran the app.</span></span>
> * <span data-ttu-id="83a95-202">Haben Sie die Projektdateien überprüft.</span><span class="sxs-lookup"><span data-stu-id="83a95-202">Examined the project files.</span></span>

<span data-ttu-id="83a95-203">Wechseln Sie zum nächsten Tutorial in der Reihe:</span><span class="sxs-lookup"><span data-stu-id="83a95-203">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="83a95-204">Hinzufügen eines Modells</span><span class="sxs-lookup"><span data-stu-id="83a95-204">Add a model</span></span>](xref:tutorials/razor-pages/model)

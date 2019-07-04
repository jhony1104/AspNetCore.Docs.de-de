---
title: 'Tutorial: Erste Schritte mit Razor Pages in ASP.NET Core'
author: rick-anderson
description: Diese Reihe von Tutorials zeigt, wie Sie Razor Pages in ASP.NET Core verwenden können. Erfahren Sie, wie Sie ein Modell erstellen, Code für Razor-Seiten generieren, Entity Framework Core und SQL Server für den Datenzugriff verwenden, Suchfunktionen hinzufügen, Eingabeüberprüfung hinzufügen und Migrationen verwenden, um das Modell zu aktualisieren.
ms.author: riande
ms.date: 6/3/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 207f5a5ab5e21d8cd8f6cf2f63641b94d8077ae5
ms.sourcegitcommit: d6e51c60439f03a8992bda70cc982ddb15d3f100
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555797"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="312b5-104">Tutorial: Erste Schritte mit Razor Pages in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="312b5-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="312b5-105">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="312b5-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="312b5-106">Dies ist das erste Tutorial aus einer Reihe.</span><span class="sxs-lookup"><span data-stu-id="312b5-106">This is the first tutorial of a series.</span></span> <span data-ttu-id="312b5-107">[In der Reihe](xref:tutorials/razor-pages/index) lernen Sie die Grundlagen zum Erstellen einer Razor Pages-Web-App mit ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="312b5-107">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="312b5-108">Am Ende der Reihe verfügen Sie über eine App, mit der eine Filmdatenbank verwaltet werden kann.</span><span class="sxs-lookup"><span data-stu-id="312b5-108">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="312b5-109">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="312b5-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="312b5-110">Erstellen einer Razor Pages-Web-App</span><span class="sxs-lookup"><span data-stu-id="312b5-110">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="312b5-111">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="312b5-111">Run the app.</span></span>
> * <span data-ttu-id="312b5-112">Überprüfen Sie die Projektdateien.</span><span class="sxs-lookup"><span data-stu-id="312b5-112">Examine the project files.</span></span>

<span data-ttu-id="312b5-113">Am Ende dieses Tutorials verfügen Sie über eine funktionsfähige Razor Pages-Web-App, auf der Sie in späteren Tutorials aufbauen werden.</span><span class="sxs-lookup"><span data-stu-id="312b5-113">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="312b5-115">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="312b5-115">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="312b5-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="312b5-116">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="312b5-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="312b5-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="312b5-118">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="312b5-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="312b5-119">Erstellen einer Razor Pages-Web-App</span><span class="sxs-lookup"><span data-stu-id="312b5-119">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="312b5-120">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="312b5-120">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="312b5-121">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="312b5-121">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="312b5-122">Erstellen Sie eine neue ASP.NET Core-Webanwendung, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="312b5-122">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="312b5-124">Nennen Sie das Projekt **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="312b5-124">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="312b5-125">Es ist wichtig, den Namen *RazorPagesMovie* zu verwenden, damit die Namespaces übereinstimmen, wenn Sie Code kopieren und einfügen.</span><span class="sxs-lookup"><span data-stu-id="312b5-125">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/config.png)

* <span data-ttu-id="312b5-127">Klicken Sie in der Dropdownliste auf **ASP.NET Core 2.2**, auf **Webanwendung** und anschließend auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="312b5-127">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="312b5-129">Das folgende Startprojekt wird erstellt:</span><span class="sxs-lookup"><span data-stu-id="312b5-129">The following starter project is created:</span></span>

  ![Projektmappen-Explorer](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="312b5-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="312b5-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="312b5-132">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="312b5-132">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="312b5-133">Wechseln Sie zum Verzeichnis (`cd`), das das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="312b5-133">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="312b5-134">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="312b5-134">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="312b5-135">Der Befehl `dotnet new` erstellt ein neues Razor Pages-Projekt im Ordner *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="312b5-135">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="312b5-136">Der Befehl `code` öffnet den Ordner *RazorPagesMovie* in der aktuellen Instanz von Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="312b5-136">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="312b5-137">Wenn das OmniSharp-Flammensymbol in der Statusleiste grün angezeigt wird, wird ein Dialogfeld mit folgender Meldung angezeigt: **Required assets to build and debug are missing from 'RazorPagesMovie'. (Die erforderlichen Objekte zum Erstellen und Debuggen sind in „RazorPagesMovie“ nicht vorhanden. Sollen sie hinzugefügt werden?**</span><span class="sxs-lookup"><span data-stu-id="312b5-137">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="312b5-138">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="312b5-138">Select **Yes**.</span></span>

  <span data-ttu-id="312b5-139">Ein *VSCODE*-Verzeichnis, das die Dateien *launch.json* und *tasks.json* enthält, wird dem Stammverzeichnis des Projekts hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="312b5-139">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="312b5-140">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="312b5-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="312b5-141">Führen Sie über ein Terminal den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="312b5-141">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="312b5-142">Diese Befehle verwenden die [.NET Core-CLI](/dotnet/core/tools/dotnet), um ein Razor Pages-Projekt zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="312b5-142">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="312b5-143">Öffnen des Projekts</span><span class="sxs-lookup"><span data-stu-id="312b5-143">Open the project</span></span>

<span data-ttu-id="312b5-144">Klicken Sie in Visual Studio auf **Datei > Öffnen**, und wählen Sie dann die Datei *RazorPagesMovie.csproj* aus.</span><span class="sxs-lookup"><span data-stu-id="312b5-144">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="312b5-145">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="312b5-145">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="312b5-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="312b5-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="312b5-147">Drücken Sie STRG+F5, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="312b5-147">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="312b5-148">Visual Studio startet [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) und führt die App aus.</span><span class="sxs-lookup"><span data-stu-id="312b5-148">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="312b5-149">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="312b5-149">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="312b5-150">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="312b5-150">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="312b5-151">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="312b5-151">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="312b5-152">Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.</span><span class="sxs-lookup"><span data-stu-id="312b5-152">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="312b5-153">Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="312b5-153">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="312b5-154">Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.</span><span class="sxs-lookup"><span data-stu-id="312b5-154">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="312b5-156">Die folgende Abbildung zeigt die App, nachdem Sie das Einverständnis zur Nachverfolgung gegeben haben:</span><span class="sxs-lookup"><span data-stu-id="312b5-156">The following image shows the app after you give consent to tracking:</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="312b5-158">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="312b5-158">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="312b5-159">Drücken Sie **STRG+F5**, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="312b5-159">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="312b5-160">Visual Studio Code startet [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="312b5-160">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="312b5-161">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="312b5-161">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="312b5-162">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="312b5-162">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="312b5-163">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="312b5-163">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="312b5-164">Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="312b5-164">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="312b5-165">Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.</span><span class="sxs-lookup"><span data-stu-id="312b5-165">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="312b5-167">Die folgende Abbildung zeigt die App, nachdem Sie das Einverständnis zur Nachverfolgung gegeben haben:</span><span class="sxs-lookup"><span data-stu-id="312b5-167">The following image shows the app after you give consent to tracking:</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="312b5-169">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="312b5-169">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="312b5-170">Drücken Sie **CMD+OPT+F5** für eine Ausführung ohne Debugger.</span><span class="sxs-lookup"><span data-stu-id="312b5-170">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="312b5-171">Visual Studio startet dann [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="312b5-171">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="312b5-172">Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="312b5-172">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="312b5-173">Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.</span><span class="sxs-lookup"><span data-stu-id="312b5-173">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="312b5-175">Die folgende Abbildung zeigt die App, nachdem Sie das Einverständnis zur Nachverfolgung gegeben haben:</span><span class="sxs-lookup"><span data-stu-id="312b5-175">The following image shows the app after you give consent to tracking:</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="312b5-177">Überprüfen der Projektdateien</span><span class="sxs-lookup"><span data-stu-id="312b5-177">Examine the project files</span></span>

<span data-ttu-id="312b5-178">Es folgt eine Übersicht über die Hauptprojektordner und -dateien, mit denen Sie in späteren Tutorials arbeiten werden.</span><span class="sxs-lookup"><span data-stu-id="312b5-178">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="312b5-179">Ordner „Seiten“</span><span class="sxs-lookup"><span data-stu-id="312b5-179">Pages folder</span></span>

<span data-ttu-id="312b5-180">Enthält Razor-Seiten und unterstützende Dateien.</span><span class="sxs-lookup"><span data-stu-id="312b5-180">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="312b5-181">Jede Razor-Seite besteht aus einem Dateienpaar:</span><span class="sxs-lookup"><span data-stu-id="312b5-181">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="312b5-182">Eine *.cshtml*-Datei, die HTML-Markup mit C#-Code in Razor-Syntax enthält</span><span class="sxs-lookup"><span data-stu-id="312b5-182">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="312b5-183">Eine *.cshtml.cs*-Datei mit C# Code, in dem Seitenereignisse verarbeitet werden</span><span class="sxs-lookup"><span data-stu-id="312b5-183">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="312b5-184">Unterstützende Dateien haben Namen, die mit einem Unterstrich beginnen.</span><span class="sxs-lookup"><span data-stu-id="312b5-184">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="312b5-185">Zum Beispiel sind in der Datei *_Layout.cshtml* Benutzeroberflächenelemente konfiguriert, die für alle Seiten gelten.</span><span class="sxs-lookup"><span data-stu-id="312b5-185">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="312b5-186">Mit dieser Datei werden das Navigationsmenü oben auf der Seite und der Urheberrechtshinweis unten auf der Seite eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="312b5-186">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="312b5-187">Weitere Informationen finden Sie unter <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="312b5-187">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="312b5-188">Ordner „wwwroot“</span><span class="sxs-lookup"><span data-stu-id="312b5-188">wwwroot folder</span></span>

<span data-ttu-id="312b5-189">Enthält statische Dateien, z. B. HTML-Dateien, JavaScript-Dateien und CSS-Dateien.</span><span class="sxs-lookup"><span data-stu-id="312b5-189">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="312b5-190">Weitere Informationen finden Sie unter <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="312b5-190">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="312b5-191">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="312b5-191">appSettings.json</span></span>

<span data-ttu-id="312b5-192">Enthält Konfigurationsdaten, z. B. Verbindungszeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="312b5-192">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="312b5-193">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="312b5-193">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="312b5-194">Program.cs</span><span class="sxs-lookup"><span data-stu-id="312b5-194">Program.cs</span></span>

<span data-ttu-id="312b5-195">Enthält den Einstiegspunkt für das Programm.</span><span class="sxs-lookup"><span data-stu-id="312b5-195">Contains the entry point for the program.</span></span> <span data-ttu-id="312b5-196">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="312b5-196">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="312b5-197">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="312b5-197">Startup.cs</span></span>

<span data-ttu-id="312b5-198">Enthält Code, mit dem das App-Verhalten konfiguriert wird, beispielsweise, ob die App Zustimmung für Cookies erfordert.</span><span class="sxs-lookup"><span data-stu-id="312b5-198">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="312b5-199">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="312b5-199">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="312b5-200">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="312b5-200">Additional resources</span></span>

* [<span data-ttu-id="312b5-201">Dieses Tutorials auf YouTube</span><span class="sxs-lookup"><span data-stu-id="312b5-201">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="312b5-202">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="312b5-202">Next steps</span></span>

<span data-ttu-id="312b5-203">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="312b5-203">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="312b5-204">Haben Sie eine Razor Pages-Web-App erstellt.</span><span class="sxs-lookup"><span data-stu-id="312b5-204">Created a Razor Pages web app.</span></span>
> * <span data-ttu-id="312b5-205">Haben Sie die App ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="312b5-205">Ran the app.</span></span>
> * <span data-ttu-id="312b5-206">Haben Sie die Projektdateien überprüft.</span><span class="sxs-lookup"><span data-stu-id="312b5-206">Examined the project files.</span></span>

<span data-ttu-id="312b5-207">Wechseln Sie zum nächsten Tutorial in der Reihe:</span><span class="sxs-lookup"><span data-stu-id="312b5-207">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="312b5-208">Hinzufügen eines Modells</span><span class="sxs-lookup"><span data-stu-id="312b5-208">Add a model</span></span>](xref:tutorials/razor-pages/model)

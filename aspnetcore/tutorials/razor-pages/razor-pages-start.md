---
title: 'Tutorial: Erste Schritte mit Razor Pages in ASP.NET Core'
author: rick-anderson
description: Diese Reihe von Tutorials zeigt, wie Sie Razor Pages in ASP.NET Core verwenden können. Erfahren Sie, wie Sie ein Modell erstellen, Code für Razor-Seiten generieren, Entity Framework Core und SQL Server für den Datenzugriff verwenden, Suchfunktionen hinzufügen, Eingabeüberprüfung hinzufügen und Migrationen verwenden, um das Modell zu aktualisieren.
ms.author: riande
ms.date: 06/03/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 7e228c99b4d55c14cea9c915cf06a7fbbbd5af44
ms.sourcegitcommit: 7a40c56bf6a6aaa63a7ee83a2cac9b3a1d77555e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/12/2019
ms.locfileid: "67855734"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="56d05-104">Tutorial: Erste Schritte mit Razor Pages in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="56d05-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="56d05-105">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="56d05-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="56d05-106">Dies ist das erste Tutorial aus einer Reihe.</span><span class="sxs-lookup"><span data-stu-id="56d05-106">This is the first tutorial of a series.</span></span> <span data-ttu-id="56d05-107">[In der Reihe](xref:tutorials/razor-pages/index) lernen Sie die Grundlagen zum Erstellen einer Razor Pages-Web-App mit ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="56d05-107">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="56d05-108">Am Ende der Reihe verfügen Sie über eine App, mit der eine Filmdatenbank verwaltet werden kann.</span><span class="sxs-lookup"><span data-stu-id="56d05-108">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="56d05-109">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="56d05-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="56d05-110">Erstellen einer Razor Pages-Web-App</span><span class="sxs-lookup"><span data-stu-id="56d05-110">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="56d05-111">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="56d05-111">Run the app.</span></span>
> * <span data-ttu-id="56d05-112">Überprüfen Sie die Projektdateien.</span><span class="sxs-lookup"><span data-stu-id="56d05-112">Examine the project files.</span></span>

<span data-ttu-id="56d05-113">Am Ende dieses Tutorials verfügen Sie über eine funktionsfähige Razor Pages-Web-App, auf der Sie in späteren Tutorials aufbauen werden.</span><span class="sxs-lookup"><span data-stu-id="56d05-113">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="56d05-115">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="56d05-115">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="56d05-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56d05-116">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="56d05-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="56d05-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="56d05-118">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="56d05-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="56d05-119">Erstellen einer Razor Pages-Web-App</span><span class="sxs-lookup"><span data-stu-id="56d05-119">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="56d05-120">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56d05-120">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="56d05-121">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="56d05-121">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="56d05-122">Erstellen Sie eine neue ASP.NET Core-Webanwendung, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="56d05-122">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="56d05-124">Nennen Sie das Projekt **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="56d05-124">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="56d05-125">Es ist wichtig, den Namen *RazorPagesMovie* zu verwenden, damit die Namespaces übereinstimmen, wenn Sie Code kopieren und einfügen.</span><span class="sxs-lookup"><span data-stu-id="56d05-125">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/config.png)

* <span data-ttu-id="56d05-127">Klicken Sie in der Dropdownliste auf **ASP.NET Core 2.2**, auf **Webanwendung** und anschließend auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="56d05-127">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="56d05-129">Das folgende Startprojekt wird erstellt:</span><span class="sxs-lookup"><span data-stu-id="56d05-129">The following starter project is created:</span></span>

  ![Projektmappen-Explorer](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="56d05-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="56d05-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="56d05-132">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="56d05-132">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="56d05-133">Wechseln Sie zum Verzeichnis (`cd`), das das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="56d05-133">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="56d05-134">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="56d05-134">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="56d05-135">Der Befehl `dotnet new` erstellt ein neues Razor Pages-Projekt im Ordner *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="56d05-135">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="56d05-136">Der Befehl `code` öffnet den Ordner *RazorPagesMovie* in der aktuellen Instanz von Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="56d05-136">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="56d05-137">Wenn das OmniSharp-Flammensymbol in der Statusleiste grün angezeigt wird, wird ein Dialogfeld mit folgender Meldung angezeigt: **Required assets to build and debug are missing from 'RazorPagesMovie'. (Die erforderlichen Objekte zum Erstellen und Debuggen sind in „RazorPagesMovie“ nicht vorhanden. Sollen sie hinzugefügt werden?**</span><span class="sxs-lookup"><span data-stu-id="56d05-137">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="56d05-138">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="56d05-138">Select **Yes**.</span></span>

  <span data-ttu-id="56d05-139">Ein *VSCODE*-Verzeichnis, das die Dateien *launch.json* und *tasks.json* enthält, wird dem Stammverzeichnis des Projekts hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="56d05-139">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="56d05-140">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="56d05-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="56d05-141">Führen Sie über ein Terminal den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="56d05-141">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="56d05-142">Diese Befehle verwenden die [.NET Core-CLI](/dotnet/core/tools/dotnet), um ein Razor Pages-Projekt zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="56d05-142">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="56d05-143">Öffnen des Projekts</span><span class="sxs-lookup"><span data-stu-id="56d05-143">Open the project</span></span>

<span data-ttu-id="56d05-144">Klicken Sie in Visual Studio auf **Datei > Öffnen**, und wählen Sie dann die Datei *RazorPagesMovie.csproj* aus.</span><span class="sxs-lookup"><span data-stu-id="56d05-144">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="56d05-145">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="56d05-145">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="56d05-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56d05-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="56d05-147">Drücken Sie STRG+F5, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="56d05-147">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="56d05-148">Visual Studio startet [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) und führt die App aus.</span><span class="sxs-lookup"><span data-stu-id="56d05-148">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="56d05-149">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="56d05-149">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="56d05-150">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="56d05-150">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="56d05-151">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="56d05-151">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="56d05-152">Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.</span><span class="sxs-lookup"><span data-stu-id="56d05-152">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="56d05-153">Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="56d05-153">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="56d05-154">Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.</span><span class="sxs-lookup"><span data-stu-id="56d05-154">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="56d05-156">Die folgende Abbildung zeigt die App, nachdem Sie das Einverständnis zur Nachverfolgung gegeben haben:</span><span class="sxs-lookup"><span data-stu-id="56d05-156">The following image shows the app after you give consent to tracking:</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="56d05-158">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="56d05-158">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="56d05-159">Drücken Sie **STRG+F5**, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="56d05-159">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="56d05-160">Visual Studio Code startet [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="56d05-160">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="56d05-161">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="56d05-161">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="56d05-162">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="56d05-162">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="56d05-163">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="56d05-163">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="56d05-164">Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="56d05-164">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="56d05-165">Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.</span><span class="sxs-lookup"><span data-stu-id="56d05-165">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="56d05-167">Die folgende Abbildung zeigt die App, nachdem Sie das Einverständnis zur Nachverfolgung gegeben haben:</span><span class="sxs-lookup"><span data-stu-id="56d05-167">The following image shows the app after you give consent to tracking:</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="56d05-169">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="56d05-169">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="56d05-170">Drücken Sie **CMD+OPT+F5** für eine Ausführung ohne Debugger.</span><span class="sxs-lookup"><span data-stu-id="56d05-170">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="56d05-171">Visual Studio startet dann [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="56d05-171">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="56d05-172">Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="56d05-172">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="56d05-173">Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.</span><span class="sxs-lookup"><span data-stu-id="56d05-173">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="56d05-175">Die folgende Abbildung zeigt die App, nachdem Sie das Einverständnis zur Nachverfolgung gegeben haben:</span><span class="sxs-lookup"><span data-stu-id="56d05-175">The following image shows the app after you give consent to tracking:</span></span>

  ![Start- oder Indexseite](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="56d05-177">Überprüfen der Projektdateien</span><span class="sxs-lookup"><span data-stu-id="56d05-177">Examine the project files</span></span>

<span data-ttu-id="56d05-178">Es folgt eine Übersicht über die Hauptprojektordner und -dateien, mit denen Sie in späteren Tutorials arbeiten werden.</span><span class="sxs-lookup"><span data-stu-id="56d05-178">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="56d05-179">Ordner „Seiten“</span><span class="sxs-lookup"><span data-stu-id="56d05-179">Pages folder</span></span>

<span data-ttu-id="56d05-180">Enthält Razor-Seiten und unterstützende Dateien.</span><span class="sxs-lookup"><span data-stu-id="56d05-180">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="56d05-181">Jede Razor-Seite besteht aus einem Dateienpaar:</span><span class="sxs-lookup"><span data-stu-id="56d05-181">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="56d05-182">Eine *.cshtml*-Datei, die HTML-Markup mit C#-Code in Razor-Syntax enthält</span><span class="sxs-lookup"><span data-stu-id="56d05-182">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="56d05-183">Eine *.cshtml.cs*-Datei mit C# Code, in dem Seitenereignisse verarbeitet werden</span><span class="sxs-lookup"><span data-stu-id="56d05-183">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="56d05-184">Unterstützende Dateien haben Namen, die mit einem Unterstrich beginnen.</span><span class="sxs-lookup"><span data-stu-id="56d05-184">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="56d05-185">Zum Beispiel sind in der Datei *_Layout.cshtml* Benutzeroberflächenelemente konfiguriert, die für alle Seiten gelten.</span><span class="sxs-lookup"><span data-stu-id="56d05-185">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="56d05-186">Mit dieser Datei werden das Navigationsmenü oben auf der Seite und der Urheberrechtshinweis unten auf der Seite eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="56d05-186">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="56d05-187">Weitere Informationen finden Sie unter <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="56d05-187">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="56d05-188">Ordner „wwwroot“</span><span class="sxs-lookup"><span data-stu-id="56d05-188">wwwroot folder</span></span>

<span data-ttu-id="56d05-189">Enthält statische Dateien, z. B. HTML-Dateien, JavaScript-Dateien und CSS-Dateien.</span><span class="sxs-lookup"><span data-stu-id="56d05-189">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="56d05-190">Weitere Informationen finden Sie unter <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="56d05-190">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="56d05-191">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="56d05-191">appSettings.json</span></span>

<span data-ttu-id="56d05-192">Enthält Konfigurationsdaten, z. B. Verbindungszeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="56d05-192">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="56d05-193">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="56d05-193">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="56d05-194">Program.cs</span><span class="sxs-lookup"><span data-stu-id="56d05-194">Program.cs</span></span>

<span data-ttu-id="56d05-195">Enthält den Einstiegspunkt für das Programm.</span><span class="sxs-lookup"><span data-stu-id="56d05-195">Contains the entry point for the program.</span></span> <span data-ttu-id="56d05-196">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="56d05-196">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="56d05-197">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="56d05-197">Startup.cs</span></span>

<span data-ttu-id="56d05-198">Enthält Code, mit dem das App-Verhalten konfiguriert wird, beispielsweise, ob die App Zustimmung für Cookies erfordert.</span><span class="sxs-lookup"><span data-stu-id="56d05-198">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="56d05-199">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="56d05-199">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="56d05-200">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="56d05-200">Additional resources</span></span>

* [<span data-ttu-id="56d05-201">Dieses Tutorials auf YouTube</span><span class="sxs-lookup"><span data-stu-id="56d05-201">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="56d05-202">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="56d05-202">Next steps</span></span>

<span data-ttu-id="56d05-203">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="56d05-203">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="56d05-204">Haben Sie eine Razor Pages-Web-App erstellt.</span><span class="sxs-lookup"><span data-stu-id="56d05-204">Created a Razor Pages web app.</span></span>
> * <span data-ttu-id="56d05-205">Haben Sie die App ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="56d05-205">Ran the app.</span></span>
> * <span data-ttu-id="56d05-206">Haben Sie die Projektdateien überprüft.</span><span class="sxs-lookup"><span data-stu-id="56d05-206">Examined the project files.</span></span>

<span data-ttu-id="56d05-207">Wechseln Sie zum nächsten Tutorial in der Reihe:</span><span class="sxs-lookup"><span data-stu-id="56d05-207">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="56d05-208">Hinzufügen eines Modells</span><span class="sxs-lookup"><span data-stu-id="56d05-208">Add a model</span></span>](xref:tutorials/razor-pages/model)

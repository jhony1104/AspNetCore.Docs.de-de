---
title: Erste Schritte mit ASP.NET Core MVC
author: rick-anderson
description: Hier finden Sie Informationen zum Einstieg in ASP.NET Core MVC.
ms.author: riande
ms.date: 12/12/2018
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: f0c2351de017de7f4c62021b8f9478603055e9bc
ms.sourcegitcommit: d75d8eb26c2cce19876c8d5b65ac8a4b21f625ef
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/19/2019
ms.locfileid: "56410546"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="3bc83-103">Erste Schritte mit ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="3bc83-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="3bc83-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3bc83-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [consider RP](~/includes/razor.md)]

https://docs.microsoft.com/en-us/visualstudio/ide/visual-studio-ide?view=vs-2017

<span data-ttu-id="3bc83-105">In diesem Tutorial lernen Sie Grundlegendes zur Erstellung einer ASP.NET Core-MVC-Web-App.</span><span class="sxs-lookup"><span data-stu-id="3bc83-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="3bc83-106">Die App verwaltet eine Datenbank mit Filmtiteln.</span><span class="sxs-lookup"><span data-stu-id="3bc83-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="3bc83-107">Sie lernen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="3bc83-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3bc83-108">Erstellen einer Web-App</span><span class="sxs-lookup"><span data-stu-id="3bc83-108">Create a web app.</span></span>
> * <span data-ttu-id="3bc83-109">Hinzufügen eines Modells und Erstellen eines Gerüsts für das Modell</span><span class="sxs-lookup"><span data-stu-id="3bc83-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="3bc83-110">Arbeiten mit einer Datenbank</span><span class="sxs-lookup"><span data-stu-id="3bc83-110">Work with a database.</span></span>
> * <span data-ttu-id="3bc83-111">Hinzufügen von Such- und Überprüfungsfunktionen</span><span class="sxs-lookup"><span data-stu-id="3bc83-111">Add search and validation.</span></span>

<span data-ttu-id="3bc83-112">Am Ende verfügen Sie über eine App, die Filmdaten verwalten und anzeigen kann.</span><span class="sxs-lookup"><span data-stu-id="3bc83-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

> [!NOTE]
> <span data-ttu-id="3bc83-113">Wir testen gerade eine vorgeschlagene neue Struktur für das ASP.NET Core-Inhaltsverzeichnis.</span><span class="sxs-lookup"><span data-stu-id="3bc83-113">We’re testing the usability of a proposed new structure for the ASP.NET Core table of contents.</span></span>  <span data-ttu-id="3bc83-114">Falls Sie einige Minuten Zeit haben, um einen Test durchzuführen, in dem Sie sieben unterschiedliche Artikel im aktuellen und vorgeschlagene Inhaltsverzeichnis finden sollen, [klicken Sie hier, um daran teilzunehmen](https://dpk4xbh5.optimalworkshop.com/treejack/aa11wn82).</span><span class="sxs-lookup"><span data-stu-id="3bc83-114">If you have a few minutes to try an exercise of finding 7 different topics in the current or proposed table of contents, please [click here to participate in the study](https://dpk4xbh5.optimalworkshop.com/treejack/aa11wn82).</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-web-app"></a><span data-ttu-id="3bc83-115">Erstellen einer Web-App</span><span class="sxs-lookup"><span data-stu-id="3bc83-115">Create a web app</span></span>

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3bc83-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3bc83-116">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3bc83-117">Klicken Sie in Visual Studio auf **Datei > Neu > Projekt**.</span><span class="sxs-lookup"><span data-stu-id="3bc83-117">From Visual Studio, select  **File > New > Project**.</span></span>

![Datei > Neu > Projekt](start-mvc/_static/alt_new_project.png)

<span data-ttu-id="3bc83-119">Schließen Sie das Dialogfeld **Neues Projekt**ab:</span><span class="sxs-lookup"><span data-stu-id="3bc83-119">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="3bc83-120">Wählen Sie im linken Bereich die Option **.NET Core** aus.</span><span class="sxs-lookup"><span data-stu-id="3bc83-120">In the left pane, select **.NET Core**</span></span>
* <span data-ttu-id="3bc83-121">Wählen Sie im mittleren Bereich die Option **ASP.NET Core-Webanwendung (.NET Core)** aus.</span><span class="sxs-lookup"><span data-stu-id="3bc83-121">In the center pane, select **ASP.NET Core Web Application (.NET Core)**</span></span>
* <span data-ttu-id="3bc83-122">Geben Sie dem Projekt den Namen „MvcMovie“. Es ist wichtig, dem Projekt diesen Namen zu geben, sodass beim Kopieren von Code der Namespace übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="3bc83-122">Name the project "MvcMovie" (It's important to name the project "MvcMovie" so when you copy code, the namespace will match.)</span></span>
* <span data-ttu-id="3bc83-123">Wählen Sie **OK** aus.</span><span class="sxs-lookup"><span data-stu-id="3bc83-123">select **OK**</span></span>

![<span data-ttu-id="3bc83-124">Dialogfeld „Neues Projekt“, .NET Core im linken Bereich, ASP.NET Core-Web</span><span class="sxs-lookup"><span data-stu-id="3bc83-124">New project dialog, .Net core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project2-21.png)

<span data-ttu-id="3bc83-125">Schließen Sie das Dialogfeld **ASP.NET Core-Webanwendung (.NET Core) – MvcMovie** ab:</span><span class="sxs-lookup"><span data-stu-id="3bc83-125">Complete the **New ASP.NET Core Web Application (.NET Core) - MvcMovie** dialog:</span></span>

* <span data-ttu-id="3bc83-126">Klicken Sie im Dropdownfeld der Versionsauswahl auf **ASP.NET Core 2.2**.</span><span class="sxs-lookup"><span data-stu-id="3bc83-126">In the version selector drop-down box select **ASP.NET Core 2.2**</span></span>
* <span data-ttu-id="3bc83-127">Wählen Sie **Webanwendung (Model-View-Controller)** aus.</span><span class="sxs-lookup"><span data-stu-id="3bc83-127">Select **Web Application (Model-View-Controller)**</span></span>
* <span data-ttu-id="3bc83-128">Wählen Sie **OK** aus.</span><span class="sxs-lookup"><span data-stu-id="3bc83-128">select **OK**.</span></span>

![<span data-ttu-id="3bc83-129">Dialogfeld „Neues Projekt“, .NET Core im linken Bereich, ASP.NET Core-Web</span><span class="sxs-lookup"><span data-stu-id="3bc83-129">New project dialog, .Net core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="3bc83-130">Visual Studio verwendet eine Standardvorlage für das MVC-Projekt, das Sie gerade erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="3bc83-130">Visual Studio used a default template for the MVC project you just created.</span></span> <span data-ttu-id="3bc83-131">Wenn Sie einen Projektnamen eingeben und einige Optionen festlegen, funktioniert Ihre App bereits.</span><span class="sxs-lookup"><span data-stu-id="3bc83-131">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="3bc83-132">Dies ist ein grundlegendes Startprojekt und ein guter Einstieg.</span><span class="sxs-lookup"><span data-stu-id="3bc83-132">This is a basic starter project, and it's a good place to start.</span></span>

<span data-ttu-id="3bc83-133">Drücken Sie **STRG+F5**, um die App im Nicht-Debugmodus auszuführen.</span><span class="sxs-lookup"><span data-stu-id="3bc83-133">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

* <span data-ttu-id="3bc83-134">Visual Studio startet [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) und führt Ihre App aus.</span><span class="sxs-lookup"><span data-stu-id="3bc83-134">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs your app.</span></span> <span data-ttu-id="3bc83-135">Beachten Sie, dass die Adressleiste `localhost:port#` und nicht etwas wie `example.com` anzeigt.</span><span class="sxs-lookup"><span data-stu-id="3bc83-135">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="3bc83-136">Das liegt daran, dass es sich bei `localhost` um den Standard-Hostnamen für Ihren lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="3bc83-136">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="3bc83-137">Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.</span><span class="sxs-lookup"><span data-stu-id="3bc83-137">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="3bc83-138">In der obigen Abbildung ist die Portnummer 5000.</span><span class="sxs-lookup"><span data-stu-id="3bc83-138">In the image above, the port number is 5000.</span></span> <span data-ttu-id="3bc83-139">Die URL im Browser zeigt `localhost:5000` an.</span><span class="sxs-lookup"><span data-stu-id="3bc83-139">The URL in the browser shows `localhost:5000`.</span></span> <span data-ttu-id="3bc83-140">Wenn Sie die App ausführen, wird eine andere Portnummer angezeigt.</span><span class="sxs-lookup"><span data-stu-id="3bc83-140">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="3bc83-141">Das Starten der App mit **STRG+F5** (Nicht-Debugmodus) ermöglicht die Änderung des Codes, das Speichern der Datei, das Aktualisieren des Browsers und das Anzeigen von Codeänderungen.</span><span class="sxs-lookup"><span data-stu-id="3bc83-141">Launching the app with **Ctrl+F5** (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="3bc83-142">Viele Entwickler bevorzugen den Nicht-Debugmodus, um die App schnell zu starten und Änderungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="3bc83-142">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="3bc83-143">Sie können die App über das Menüelement **Debuggen** im Debugmodus oder Nicht-Debugmodus starten:</span><span class="sxs-lookup"><span data-stu-id="3bc83-143">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

![Menü „Debuggen“](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="3bc83-145">Sie können die App debuggen, indem Sie die Schaltfläche **IIS Express** auswählen.</span><span class="sxs-lookup"><span data-stu-id="3bc83-145">You can debug the app by selecting the **IIS Express** button</span></span>

![IIS Express](start-mvc/_static/iis_express.png)

<!-- Code -------------------------->
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3bc83-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3bc83-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3bc83-148">Das Tutorial setzt voraus, dass Sie mit VS Code vertraut sind.</span><span class="sxs-lookup"><span data-stu-id="3bc83-148">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="3bc83-149">Weitere Informationen finden Sie unter [Erste Schritte mit VS Code](https://code.visualstudio.com/docs) und [Hilfe zu Visual Studio Code](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="3bc83-149">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="3bc83-150">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="3bc83-150">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="3bc83-151">Wechseln Sie mit `cd` zu einem Ordner, der das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="3bc83-151">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="3bc83-152">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="3bc83-152">Run the following command:</span></span>

   ```console
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="3bc83-153">Es wird ein Dialogfeld mit folgender Meldung angezeigt: **Die erforderlichen Objekte zum Erstellen und Debuggen sind in "MvcMovie" nicht vorhanden. Sollen sie hinzugefügt werden?**</span><span class="sxs-lookup"><span data-stu-id="3bc83-153">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="3bc83-154">Wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="3bc83-154">Select **Yes**</span></span>

  * <span data-ttu-id="3bc83-155">`dotnet new mvc -o MvcMovie`: Erstellt ein neues ASP.NET Core MVC-Projekt im Ordner *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="3bc83-155">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="3bc83-156">`code -r MvcMovie`: Lädt die Projektdatei *MvcMovie.csproj* in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="3bc83-156">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

### <a name="launch-the-app"></a><span data-ttu-id="3bc83-157">Starten der App</span><span class="sxs-lookup"><span data-stu-id="3bc83-157">Launch the app</span></span>

* <span data-ttu-id="3bc83-158">Drücken Sie **STRG+F5**, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="3bc83-158">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="3bc83-159">Visual Studio Code startet [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="3bc83-159">Visual Studio Code starts starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="3bc83-160">Die Adressleiste zeigt `localhost:port:5001` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="3bc83-160">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="3bc83-161">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="3bc83-161">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="3bc83-162">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="3bc83-162">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="3bc83-163">Das Starten der App mit **STRG+F5** (Nicht-Debugmodus) ermöglicht die Änderung des Codes, das Speichern der Datei, das Aktualisieren des Browsers und das Anzeigen von Codeänderungen.</span><span class="sxs-lookup"><span data-stu-id="3bc83-163">Launching the app with **Ctrl+F5** (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="3bc83-164">Viele Entwickler bevorzugen den Nicht-Debugmodus, um die Seite zu aktualisieren und Änderungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="3bc83-164">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

<!-- Mac -------------------------->
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3bc83-165">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="3bc83-165">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3bc83-166">Wählen Sie **Datei** > **Neue Projektmappe** aus.</span><span class="sxs-lookup"><span data-stu-id="3bc83-166">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](~/tutorials/first-web-api-mac/_static/sln.png)

* <span data-ttu-id="3bc83-168">Wählen Sie **.NET Core App** > **ASP.NET Core** > **ASP.NET Core Web App (MVC)** > **Next** aus.</span><span class="sxs-lookup"><span data-stu-id="3bc83-168">Select **.NET Core App** > **ASP.NET Core** > **ASP.NET Core Web App (MVC)** > **Next**.</span></span>

  ![Dialogfeld „Neue Projektmappe“ in macOS](~/tutorials/first-mvc-app-mac/start-mvc/1.png)

* <span data-ttu-id="3bc83-170">Übernehmen Sie im Dialogfeld **Neue ASP.NET Core-Web-API konfigurieren** die Standardeinstellung **Zielframework** von \**.NET Core 2.2*.</span><span class="sxs-lookup"><span data-stu-id="3bc83-170">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="3bc83-171">Nennen Sie das Projekt **MvcMovie**, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="3bc83-171">Name the project **MvcMovie**, and then select **Create**.</span></span>

### <a name="launch-the-app"></a><span data-ttu-id="3bc83-172">Starten der App</span><span class="sxs-lookup"><span data-stu-id="3bc83-172">Launch the app</span></span>

<span data-ttu-id="3bc83-173">Wählen Sie **Ausführen** > **Ohne Debuggen starten** aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="3bc83-173">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="3bc83-174">Visual Studio für Mac startet den [Kestrel](xref:fundamentals/servers/index#kestrel)-Server und einen Browser und navigiert zu `http://localhost:port`, wobei es sich bei *port* um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="3bc83-174">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

* <span data-ttu-id="3bc83-175">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="3bc83-175">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="3bc83-176">Das liegt daran, dass es sich bei `localhost` um den Standard-Hostnamen für Ihren lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="3bc83-176">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="3bc83-177">Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.</span><span class="sxs-lookup"><span data-stu-id="3bc83-177">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="3bc83-178">Wenn Sie die App ausführen, wird eine andere Portnummer angezeigt.</span><span class="sxs-lookup"><span data-stu-id="3bc83-178">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="3bc83-179">Sie können die App über das Menü **Ausführen** im Debugmodus oder Nicht-Debugmodus starten.</span><span class="sxs-lookup"><span data-stu-id="3bc83-179">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

---  
<!-- End of VS tabs -->

* <span data-ttu-id="3bc83-180">Wählen Sie **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="3bc83-180">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="3bc83-181">Diese App verfolgt keine personenbezogenen Informationen nach.</span><span class="sxs-lookup"><span data-stu-id="3bc83-181">This app doesn't track personal information.</span></span> <span data-ttu-id="3bc83-182">Der generierte Vorlagencode enthält Objekte, die bei der Erfüllung der [Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) als Unterstützung dienen sollen.</span><span class="sxs-lookup"><span data-stu-id="3bc83-182">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](start-mvc/_static/privacy.png)

  <span data-ttu-id="3bc83-184">Die folgende Abbildung zeigt die App, nachdem die Nachverfolgung akzeptiert wurde:</span><span class="sxs-lookup"><span data-stu-id="3bc83-184">The following image shows the app after accepting tracking:</span></span>

  ![Start- oder Indexseite](start-mvc/_static/home2.2.png)

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="3bc83-186">Im nächsten Teil dieses Tutorials erfahren Sie mehr über MVC und beginnen mit dem Schreiben von Code.</span><span class="sxs-lookup"><span data-stu-id="3bc83-186">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="3bc83-187">Nächste</span><span class="sxs-lookup"><span data-stu-id="3bc83-187">Next</span></span>](adding-controller.md)  

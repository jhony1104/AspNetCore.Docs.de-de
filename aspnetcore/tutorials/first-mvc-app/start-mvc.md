---
title: Erste Schritte mit ASP.NET Core MVC
author: rick-anderson
description: Hier finden Sie Informationen zum Einstieg in ASP.NET Core MVC.
ms.author: riande
ms.date: 12/12/2018
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: c09c06f55c4179e9e2174f0063ab7387b7e4c31b
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/27/2019
ms.locfileid: "56899228"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="1eb23-103">Erste Schritte mit ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="1eb23-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="1eb23-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1eb23-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="1eb23-105">In diesem Tutorial lernen Sie Grundlegendes zur Erstellung einer ASP.NET Core-MVC-Web-App.</span><span class="sxs-lookup"><span data-stu-id="1eb23-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="1eb23-106">Die App verwaltet eine Datenbank mit Filmtiteln.</span><span class="sxs-lookup"><span data-stu-id="1eb23-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="1eb23-107">Sie lernen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="1eb23-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="1eb23-108">Erstellen einer Web-App</span><span class="sxs-lookup"><span data-stu-id="1eb23-108">Create a web app.</span></span>
> * <span data-ttu-id="1eb23-109">Hinzufügen eines Modells und Erstellen eines Gerüsts für das Modell</span><span class="sxs-lookup"><span data-stu-id="1eb23-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="1eb23-110">Arbeiten mit einer Datenbank</span><span class="sxs-lookup"><span data-stu-id="1eb23-110">Work with a database.</span></span>
> * <span data-ttu-id="1eb23-111">Hinzufügen von Such- und Überprüfungsfunktionen</span><span class="sxs-lookup"><span data-stu-id="1eb23-111">Add search and validation.</span></span>

<span data-ttu-id="1eb23-112">Am Ende verfügen Sie über eine App, die Filmdaten verwalten und anzeigen kann.</span><span class="sxs-lookup"><span data-stu-id="1eb23-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

[!INCLUDE[](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-web-app"></a><span data-ttu-id="1eb23-113">Erstellen einer Web-App</span><span class="sxs-lookup"><span data-stu-id="1eb23-113">Create a web app</span></span>

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1eb23-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1eb23-114">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1eb23-115">Klicken Sie in Visual Studio auf **Datei > Neu > Projekt**.</span><span class="sxs-lookup"><span data-stu-id="1eb23-115">From Visual Studio, select  **File > New > Project**.</span></span>

![Datei > Neu > Projekt](start-mvc/_static/alt_new_project.png)

<span data-ttu-id="1eb23-117">Schließen Sie das Dialogfeld **Neues Projekt**ab:</span><span class="sxs-lookup"><span data-stu-id="1eb23-117">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="1eb23-118">Wählen Sie im linken Bereich die Option **.NET Core** aus.</span><span class="sxs-lookup"><span data-stu-id="1eb23-118">In the left pane, select **.NET Core**</span></span>
* <span data-ttu-id="1eb23-119">Wählen Sie im mittleren Bereich die Option **ASP.NET Core-Webanwendung (.NET Core)** aus.</span><span class="sxs-lookup"><span data-stu-id="1eb23-119">In the center pane, select **ASP.NET Core Web Application (.NET Core)**</span></span>
* <span data-ttu-id="1eb23-120">Geben Sie dem Projekt den Namen „MvcMovie“. Es ist wichtig, dem Projekt diesen Namen zu geben, sodass beim Kopieren von Code der Namespace übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="1eb23-120">Name the project "MvcMovie" (It's important to name the project "MvcMovie" so when you copy code, the namespace will match.)</span></span>
* <span data-ttu-id="1eb23-121">Wählen Sie **OK** aus.</span><span class="sxs-lookup"><span data-stu-id="1eb23-121">select **OK**</span></span>

![<span data-ttu-id="1eb23-122">Dialogfeld „Neues Projekt“, .NET Core im linken Bereich, ASP.NET Core-Web</span><span class="sxs-lookup"><span data-stu-id="1eb23-122">New project dialog, .Net core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project2-21.png)

<span data-ttu-id="1eb23-123">Schließen Sie das Dialogfeld **ASP.NET Core-Webanwendung (.NET Core) – MvcMovie** ab:</span><span class="sxs-lookup"><span data-stu-id="1eb23-123">Complete the **New ASP.NET Core Web Application (.NET Core) - MvcMovie** dialog:</span></span>

* <span data-ttu-id="1eb23-124">Klicken Sie im Dropdownfeld der Versionsauswahl auf **ASP.NET Core 2.2**.</span><span class="sxs-lookup"><span data-stu-id="1eb23-124">In the version selector drop-down box select **ASP.NET Core 2.2**</span></span>
* <span data-ttu-id="1eb23-125">Wählen Sie **Webanwendung (Model-View-Controller)** aus.</span><span class="sxs-lookup"><span data-stu-id="1eb23-125">Select **Web Application (Model-View-Controller)**</span></span>
* <span data-ttu-id="1eb23-126">Wählen Sie **OK** aus.</span><span class="sxs-lookup"><span data-stu-id="1eb23-126">select **OK**.</span></span>

![<span data-ttu-id="1eb23-127">Dialogfeld „Neues Projekt“, .NET Core im linken Bereich, ASP.NET Core-Web</span><span class="sxs-lookup"><span data-stu-id="1eb23-127">New project dialog, .Net core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="1eb23-128">Visual Studio verwendet eine Standardvorlage für das MVC-Projekt, das Sie gerade erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="1eb23-128">Visual Studio used a default template for the MVC project you just created.</span></span> <span data-ttu-id="1eb23-129">Wenn Sie einen Projektnamen eingeben und einige Optionen festlegen, funktioniert Ihre App bereits.</span><span class="sxs-lookup"><span data-stu-id="1eb23-129">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="1eb23-130">Dies ist ein grundlegendes Startprojekt und ein guter Einstieg.</span><span class="sxs-lookup"><span data-stu-id="1eb23-130">This is a basic starter project, and it's a good place to start.</span></span>

<!-- Code -------------------------->
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1eb23-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1eb23-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1eb23-132">Das Tutorial setzt voraus, dass Sie mit VS Code vertraut sind.</span><span class="sxs-lookup"><span data-stu-id="1eb23-132">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="1eb23-133">Weitere Informationen finden Sie unter [Erste Schritte mit VS Code](https://code.visualstudio.com/docs) und [Hilfe zu Visual Studio Code](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="1eb23-133">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="1eb23-134">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="1eb23-134">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="1eb23-135">Wechseln Sie mit `cd` zu einem Ordner, der das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="1eb23-135">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="1eb23-136">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="1eb23-136">Run the following command:</span></span>

   ```console
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="1eb23-137">Es wird ein Dialogfeld mit folgender Meldung angezeigt: **Die erforderlichen Objekte zum Erstellen und Debuggen sind in "MvcMovie" nicht vorhanden. Sollen sie hinzugefügt werden?**</span><span class="sxs-lookup"><span data-stu-id="1eb23-137">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="1eb23-138">Wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="1eb23-138">Select **Yes**</span></span>

  * <span data-ttu-id="1eb23-139">`dotnet new mvc -o MvcMovie`: Erstellt ein neues ASP.NET Core MVC-Projekt im Ordner *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="1eb23-139">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="1eb23-140">`code -r MvcMovie`: Lädt die Projektdatei *MvcMovie.csproj* in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="1eb23-140">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

<!-- Mac -------------------------->
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1eb23-141">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="1eb23-141">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="1eb23-142">Wählen Sie **Datei** > **Neue Projektmappe** aus.</span><span class="sxs-lookup"><span data-stu-id="1eb23-142">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](~/tutorials/first-web-api-mac/_static/sln.png)

* <span data-ttu-id="1eb23-144">Wählen Sie **.NET Core App** > **ASP.NET Core** > **ASP.NET Core Web App (MVC)** > **Next** aus.</span><span class="sxs-lookup"><span data-stu-id="1eb23-144">Select **.NET Core App** > **ASP.NET Core** > **ASP.NET Core Web App (MVC)** > **Next**.</span></span>

  ![Dialogfeld „Neue Projektmappe“ in macOS](~/tutorials/first-mvc-app-mac/start-mvc/1.png)

* <span data-ttu-id="1eb23-146">Übernehmen Sie im Dialogfeld **Neue ASP.NET Core-Web-API konfigurieren** die Standardeinstellung **Zielframework** von \**.NET Core 2.2*.</span><span class="sxs-lookup"><span data-stu-id="1eb23-146">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="1eb23-147">Nennen Sie das Projekt **MvcMovie**, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="1eb23-147">Name the project **MvcMovie**, and then select **Create**.</span></span>

---  
<!-- End of VS tabs -->

### <a name="run-the-app"></a><span data-ttu-id="1eb23-148">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="1eb23-148">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1eb23-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1eb23-149">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="1eb23-150">Drücken Sie **STRG+F5**, um die App im Nicht-Debugmodus auszuführen.</span><span class="sxs-lookup"><span data-stu-id="1eb23-150">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="1eb23-151">Visual Studio startet [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) und führt die App aus.</span><span class="sxs-lookup"><span data-stu-id="1eb23-151">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="1eb23-152">Beachten Sie, dass die Adressleiste `localhost:port#` und nicht etwas wie `example.com` anzeigt.</span><span class="sxs-lookup"><span data-stu-id="1eb23-152">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="1eb23-153">Das liegt daran, dass es sich bei `localhost` um den Standard-Hostnamen für Ihren lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="1eb23-153">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="1eb23-154">Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.</span><span class="sxs-lookup"><span data-stu-id="1eb23-154">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="1eb23-155">Das Starten der App mit STRG+F5 (Nicht-Debugmodus) ermöglicht die Änderung des Codes, das Speichern der Datei, das Aktualisieren des Browsers und das Anzeigen von Codeänderungen.</span><span class="sxs-lookup"><span data-stu-id="1eb23-155">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="1eb23-156">Viele Entwickler bevorzugen den Nicht-Debugmodus, um die App schnell zu starten und Änderungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="1eb23-156">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="1eb23-157">Sie können die App über das Menüelement **Debuggen** im Debugmodus oder Nicht-Debugmodus starten:</span><span class="sxs-lookup"><span data-stu-id="1eb23-157">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menü „Debuggen“](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="1eb23-159">Sie können die App debuggen, indem Sie die Schaltfläche **IIS Express** auswählen.</span><span class="sxs-lookup"><span data-stu-id="1eb23-159">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1eb23-161">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1eb23-161">Visual Studio Code</span></span>](#tab/visual-studio-code) 

<span data-ttu-id="1eb23-162">Drücken Sie STRG+F5, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="1eb23-162">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="1eb23-163">Visual Studio Code startet [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="1eb23-163">Visual Studio Code starts starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="1eb23-164">Die Adressleiste zeigt `localhost:port:5001` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="1eb23-164">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="1eb23-165">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="1eb23-165">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="1eb23-166">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="1eb23-166">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="1eb23-167">Das Starten der App mit STRG+F5 (Nicht-Debugmodus) ermöglicht die Änderung des Codes, das Speichern der Datei, das Aktualisieren des Browsers und das Anzeigen von Codeänderungen.</span><span class="sxs-lookup"><span data-stu-id="1eb23-167">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="1eb23-168">Viele Entwickler bevorzugen den Nicht-Debugmodus, um die Seite zu aktualisieren und Änderungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="1eb23-168">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1eb23-169">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="1eb23-169">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="1eb23-170">Wählen Sie **Ausführen** > **Ohne Debuggen starten** aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="1eb23-170">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="1eb23-171">Visual Studio für Mac startet den [Kestrel](xref:fundamentals/servers/index#kestrel)-Server und einen Browser und navigiert zu `http://localhost:port`, wobei es sich bei *port* um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="1eb23-171">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="1eb23-172">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="1eb23-172">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="1eb23-173">Das liegt daran, dass es sich bei `localhost` um den Standard-Hostnamen für Ihren lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="1eb23-173">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="1eb23-174">Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.</span><span class="sxs-lookup"><span data-stu-id="1eb23-174">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="1eb23-175">Wenn Sie die App ausführen, wird eine andere Portnummer angezeigt.</span><span class="sxs-lookup"><span data-stu-id="1eb23-175">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="1eb23-176">Sie können die App über das Menü **Ausführen** im Debugmodus oder Nicht-Debugmodus starten.</span><span class="sxs-lookup"><span data-stu-id="1eb23-176">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

------

* <span data-ttu-id="1eb23-177">Wählen Sie **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="1eb23-177">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="1eb23-178">Diese App verfolgt keine personenbezogenen Informationen nach.</span><span class="sxs-lookup"><span data-stu-id="1eb23-178">This app doesn't track personal information.</span></span> <span data-ttu-id="1eb23-179">Der generierte Vorlagencode enthält Objekte, die bei der Erfüllung der [Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) als Unterstützung dienen sollen.</span><span class="sxs-lookup"><span data-stu-id="1eb23-179">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](start-mvc/_static/privacy.png)

  <span data-ttu-id="1eb23-181">Die folgende Abbildung zeigt die App, nachdem die Nachverfolgung akzeptiert wurde:</span><span class="sxs-lookup"><span data-stu-id="1eb23-181">The following image shows the app after accepting tracking:</span></span>

  ![Start- oder Indexseite](start-mvc/_static/home2.2.png)

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="1eb23-183">Im nächsten Teil dieses Tutorials erfahren Sie mehr über MVC und beginnen mit dem Schreiben von Code.</span><span class="sxs-lookup"><span data-stu-id="1eb23-183">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="1eb23-184">Nächste</span><span class="sxs-lookup"><span data-stu-id="1eb23-184">Next</span></span>](adding-controller.md)  

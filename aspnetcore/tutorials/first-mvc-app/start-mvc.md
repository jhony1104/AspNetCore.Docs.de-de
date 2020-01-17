---
title: Erste Schritte mit ASP.NET Core MVC
author: rick-anderson
description: Hier finden Sie Informationen zum Einstieg in ASP.NET Core MVC.
ms.author: riande
ms.date: 10/16/2019
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 901257efdfbc7b36249233745175f5ed253da2c7
ms.sourcegitcommit: da2fb2d78ce70accdba903ccbfdcfffdd0112123
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/07/2020
ms.locfileid: "75722868"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="71284-103">Erste Schritte mit ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="71284-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="71284-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="71284-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="71284-105">In diesem Tutorial lernen Sie Grundlegendes zur Erstellung einer ASP.NET Core-MVC-Web-App.</span><span class="sxs-lookup"><span data-stu-id="71284-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="71284-106">Die App verwaltet eine Datenbank mit Filmtiteln.</span><span class="sxs-lookup"><span data-stu-id="71284-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="71284-107">Sie lernen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="71284-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="71284-108">Erstellen einer Web-App</span><span class="sxs-lookup"><span data-stu-id="71284-108">Create a web app.</span></span>
> * <span data-ttu-id="71284-109">Hinzufügen eines Modells und Erstellen eines Gerüsts für das Modell</span><span class="sxs-lookup"><span data-stu-id="71284-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="71284-110">Arbeiten mit einer Datenbank</span><span class="sxs-lookup"><span data-stu-id="71284-110">Work with a database.</span></span>
> * <span data-ttu-id="71284-111">Hinzufügen von Such- und Überprüfungsfunktionen</span><span class="sxs-lookup"><span data-stu-id="71284-111">Add search and validation.</span></span>

<span data-ttu-id="71284-112">Am Ende verfügen Sie über eine App, die Filmdaten verwalten und anzeigen kann.</span><span class="sxs-lookup"><span data-stu-id="71284-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="71284-113">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="71284-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="71284-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="71284-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="71284-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="71284-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="71284-116">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="71284-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="71284-117">Erstellen einer Web-App</span><span class="sxs-lookup"><span data-stu-id="71284-117">Create a web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="71284-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="71284-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="71284-119">Wählen Sie in Visual Studio die Option **Neues Projekt erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="71284-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="71284-120">Klicken Sie auf **ASP.NET Core-Webanwendung** und dann auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="71284-120">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![neue ASP.NET Core-Webanwendung](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="71284-122">Geben Sie dem Projekt den Namen **MvcMovie**, und klicken Sie dann auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="71284-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="71284-123">Es ist wichtig, dem Projekt den Namen **MvcMovie** zu geben, damit beim Kopieren von Code der Namespace übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="71284-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![neue ASP.NET Core-Webanwendung](start-mvc/_static/config.png)

* <span data-ttu-id="71284-125">Klicken Sie auf **Webanwendung (Model View Controller)** und anschließend auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="71284-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="71284-126">Dialogfeld „Neues Projekt“, .NET Core im linken Bereich, ASP.NET Core-Web-App</span><span class="sxs-lookup"><span data-stu-id="71284-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="71284-127">Visual Studio verwendet die Standardvorlage für das MVC-Projekt, das Sie gerade erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="71284-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="71284-128">Wenn Sie einen Projektnamen eingeben und einige Optionen festlegen, funktioniert Ihre App bereits.</span><span class="sxs-lookup"><span data-stu-id="71284-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="71284-129">Hierbei handelt es sich um ein grundlegendes Startprojekt.</span><span class="sxs-lookup"><span data-stu-id="71284-129">This is a basic starter project.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="71284-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="71284-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="71284-131">Das Tutorial setzt voraus, dass Sie mit VS Code vertraut sind.</span><span class="sxs-lookup"><span data-stu-id="71284-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="71284-132">Weitere Informationen finden Sie unter [Erste Schritte mit VS Code](https://code.visualstudio.com/docs) und [Hilfe zu Visual Studio Code](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="71284-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="71284-133">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="71284-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="71284-134">Wechseln Sie mit `cd` zu einem Ordner, der das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="71284-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="71284-135">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="71284-135">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="71284-136">Es wird ein Dialogfeld mit folgender Meldung angezeigt: **Die erforderlichen Objekte zum Erstellen und Debuggen sind in "MvcMovie" nicht vorhanden. Sollen sie hinzugefügt werden?**</span><span class="sxs-lookup"><span data-stu-id="71284-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="71284-137">Wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="71284-137">Select **Yes**</span></span>

  * <span data-ttu-id="71284-138">`dotnet new mvc -o MvcMovie`: Erstellt ein neues ASP.NET Core MVC-Projekt im Ordner *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="71284-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="71284-139">`code -r MvcMovie`: Lädt die Projektdatei *MvcMovie.csproj* in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="71284-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="71284-140">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="71284-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="71284-141">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="71284-141">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="71284-143">Wählen Sie **.NET Core** > **App** > **Webanwendung (Model-View-Controller)** > **Weiter** aus.</span><span class="sxs-lookup"><span data-stu-id="71284-143">Select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![Dialogfeld „Neue Projektmappe“ in macOS](./start-mvc/_static/new_project_mvc_vsmac.png)

* <span data-ttu-id="71284-145">Wählen Sie im Dialogfeld **Configure your new ASP.NET Core Web API** (Neue ASP.NET Core-Web-API konfigurieren) für das **Zielframework** den Eintrag **.NET Core 3.1** aus.</span><span class="sxs-lookup"><span data-stu-id="71284-145">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** of **.NET Core 3.1**.</span></span>

  ![Auswahl für .NET Core 3.1 in macOS](./start-mvc/_static/new_project_31_vsmac.png)

* <span data-ttu-id="71284-147">Nennen Sie das Projekt **MvcMovie**, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="71284-147">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="71284-148">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="71284-148">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="71284-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="71284-149">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="71284-150">Drücken Sie **STRG+F5**, um die App im Nicht-Debugmodus auszuführen.</span><span class="sxs-lookup"><span data-stu-id="71284-150">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="71284-151">Visual Studio startet [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) und führt die App aus.</span><span class="sxs-lookup"><span data-stu-id="71284-151">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="71284-152">Beachten Sie, dass die Adressleiste `localhost:port#` und nicht etwas wie `example.com` anzeigt.</span><span class="sxs-lookup"><span data-stu-id="71284-152">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="71284-153">Das liegt daran, dass es sich bei `localhost` um den Standard-Hostnamen für Ihren lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="71284-153">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="71284-154">Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.</span><span class="sxs-lookup"><span data-stu-id="71284-154">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="71284-155">Das Starten der App mit STRG+F5 (Nicht-Debugmodus) ermöglicht die Änderung des Codes, das Speichern der Datei, das Aktualisieren des Browsers und das Anzeigen von Codeänderungen.</span><span class="sxs-lookup"><span data-stu-id="71284-155">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="71284-156">Viele Entwickler bevorzugen den Nicht-Debugmodus, um die App schnell zu starten und Änderungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="71284-156">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="71284-157">Sie können die App über das Menüelement **Debuggen** im Debugmodus oder Nicht-Debugmodus starten:</span><span class="sxs-lookup"><span data-stu-id="71284-157">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menü „Debuggen“](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="71284-159">Sie können die App debuggen, indem Sie die Schaltfläche **IIS Express** auswählen.</span><span class="sxs-lookup"><span data-stu-id="71284-159">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="71284-161">Auf der folgenden Abbildung sehen Sie die App:</span><span class="sxs-lookup"><span data-stu-id="71284-161">The following image shows the app:</span></span>

  ![Start- oder Indexseite](start-mvc/_static/home2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="71284-163">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="71284-163">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="71284-164">Drücken Sie STRG+F5, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="71284-164">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="71284-165">Visual Studio Code startet [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="71284-165">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="71284-166">Die Adressleiste zeigt `localhost:port:5001` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="71284-166">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="71284-167">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="71284-167">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="71284-168">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="71284-168">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="71284-169">Das Starten der App mit STRG+F5 (Nicht-Debugmodus) ermöglicht die Änderung des Codes, das Speichern der Datei, das Aktualisieren des Browsers und das Anzeigen von Codeänderungen.</span><span class="sxs-lookup"><span data-stu-id="71284-169">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="71284-170">Viele Entwickler bevorzugen den Nicht-Debugmodus, um die Seite zu aktualisieren und Änderungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="71284-170">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Start- oder Indexseite](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="71284-172">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="71284-172">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="71284-173">Wählen Sie **Ausführen** > **Ohne Debuggen starten** aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="71284-173">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="71284-174">Visual Studio für Mac startet den [Kestrel](xref:fundamentals/servers/index#kestrel)-Server und einen Browser und navigiert zu `http://localhost:port`, wobei es sich bei *port* um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="71284-174">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="71284-175">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="71284-175">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="71284-176">Das liegt daran, dass es sich bei `localhost` um den Standard-Hostnamen für Ihren lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="71284-176">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="71284-177">Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.</span><span class="sxs-lookup"><span data-stu-id="71284-177">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="71284-178">Wenn Sie die App ausführen, wird eine andere Portnummer angezeigt.</span><span class="sxs-lookup"><span data-stu-id="71284-178">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="71284-179">Sie können die App über das Menü **Ausführen** im Debugmodus oder Nicht-Debugmodus starten.</span><span class="sxs-lookup"><span data-stu-id="71284-179">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="71284-180">Auf der folgenden Abbildung sehen Sie die App:</span><span class="sxs-lookup"><span data-stu-id="71284-180">The following image shows the app:</span></span>

  ![Start- oder Indexseite](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="71284-182">Im nächsten Teil dieses Tutorials erfahren Sie mehr über MVC und beginnen mit dem Schreiben von Code.</span><span class="sxs-lookup"><span data-stu-id="71284-182">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="71284-183">Nächste</span><span class="sxs-lookup"><span data-stu-id="71284-183">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="71284-184">In diesem Tutorial lernen Sie Grundlegendes zur Erstellung einer ASP.NET Core-MVC-Web-App.</span><span class="sxs-lookup"><span data-stu-id="71284-184">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="71284-185">Die App verwaltet eine Datenbank mit Filmtiteln.</span><span class="sxs-lookup"><span data-stu-id="71284-185">The app manages a database of movie titles.</span></span> <span data-ttu-id="71284-186">Sie lernen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="71284-186">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="71284-187">Erstellen einer Web-App</span><span class="sxs-lookup"><span data-stu-id="71284-187">Create a web app.</span></span>
> * <span data-ttu-id="71284-188">Hinzufügen eines Modells und Erstellen eines Gerüsts für das Modell</span><span class="sxs-lookup"><span data-stu-id="71284-188">Add and scaffold a model.</span></span>
> * <span data-ttu-id="71284-189">Arbeiten mit einer Datenbank</span><span class="sxs-lookup"><span data-stu-id="71284-189">Work with a database.</span></span>
> * <span data-ttu-id="71284-190">Hinzufügen von Such- und Überprüfungsfunktionen</span><span class="sxs-lookup"><span data-stu-id="71284-190">Add search and validation.</span></span>

<span data-ttu-id="71284-191">Am Ende verfügen Sie über eine App, die Filmdaten verwalten und anzeigen kann.</span><span class="sxs-lookup"><span data-stu-id="71284-191">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="71284-192">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="71284-192">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="71284-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="71284-193">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="71284-194">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="71284-194">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="71284-195">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="71284-195">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="71284-196">Erstellen einer Web-App</span><span class="sxs-lookup"><span data-stu-id="71284-196">Create a web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="71284-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="71284-197">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="71284-198">Wählen Sie in Visual Studio die Option **Neues Projekt erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="71284-198">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="71284-199">Klicken Sie auf **ASP.NET Core-Webanwendung** und dann auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="71284-199">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![neue ASP.NET Core-Webanwendung](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="71284-201">Geben Sie dem Projekt den Namen **MvcMovie**, und klicken Sie dann auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="71284-201">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="71284-202">Es ist wichtig, dem Projekt den Namen **MvcMovie** zu geben, damit beim Kopieren von Code der Namespace übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="71284-202">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![neue ASP.NET Core-Webanwendung](start-mvc/_static/config.png)


* <span data-ttu-id="71284-204">Klicken Sie auf **Webanwendung (Model View Controller)** und anschließend auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="71284-204">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="71284-205">Dialogfeld „Neues Projekt“, .NET Core im linken Bereich, ASP.NET Core-Web-App</span><span class="sxs-lookup"><span data-stu-id="71284-205">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="71284-206">Visual Studio verwendet die Standardvorlage für das MVC-Projekt, das Sie gerade erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="71284-206">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="71284-207">Wenn Sie einen Projektnamen eingeben und einige Optionen festlegen, funktioniert Ihre App bereits.</span><span class="sxs-lookup"><span data-stu-id="71284-207">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="71284-208">Dies ist ein grundlegendes Startprojekt und ein guter Einstieg.</span><span class="sxs-lookup"><span data-stu-id="71284-208">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="71284-209">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="71284-209">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="71284-210">Das Tutorial setzt voraus, dass Sie mit VS Code vertraut sind.</span><span class="sxs-lookup"><span data-stu-id="71284-210">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="71284-211">Weitere Informationen finden Sie unter [Erste Schritte mit VS Code](https://code.visualstudio.com/docs) und [Hilfe zu Visual Studio Code](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="71284-211">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="71284-212">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="71284-212">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="71284-213">Wechseln Sie mit `cd` zu einem Ordner, der das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="71284-213">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="71284-214">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="71284-214">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="71284-215">Es wird ein Dialogfeld mit folgender Meldung angezeigt: **Die erforderlichen Objekte zum Erstellen und Debuggen sind in "MvcMovie" nicht vorhanden. Sollen sie hinzugefügt werden?**</span><span class="sxs-lookup"><span data-stu-id="71284-215">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="71284-216">Wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="71284-216">Select **Yes**</span></span>

  * <span data-ttu-id="71284-217">`dotnet new mvc -o MvcMovie`: Erstellt ein neues ASP.NET Core MVC-Projekt im Ordner *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="71284-217">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="71284-218">`code -r MvcMovie`: Lädt die Projektdatei *MvcMovie.csproj* in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="71284-218">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="71284-219">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="71284-219">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="71284-220">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="71284-220">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="71284-222">Wählen Sie **.NET Core** > **App** > **Webanwendung (Model-View-Controller)** > **Weiter** aus.</span><span class="sxs-lookup"><span data-stu-id="71284-222">Select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![Dialogfeld „Neue Projektmappe“ in macOS](./start-mvc/_static/new_project_mvc_vsmac.png)

* <span data-ttu-id="71284-224">Übernehmen Sie im Dialogfeld **Neue ASP.NET Core-Web-API konfigurieren** die Standardeinstellung für **Zielframework** von **.NET Core 2.2**.</span><span class="sxs-lookup"><span data-stu-id="71284-224">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of **.NET Core 2.2**.</span></span>

  ![Auswahl für .NET Core 2.2 in macOS](./start-mvc/_static/new_project_22_vsmac.png)

* <span data-ttu-id="71284-226">Nennen Sie das Projekt **MvcMovie**, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="71284-226">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="71284-227">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="71284-227">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="71284-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="71284-228">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="71284-229">Drücken Sie **STRG+F5**, um die App im Nicht-Debugmodus auszuführen.</span><span class="sxs-lookup"><span data-stu-id="71284-229">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="71284-230">Visual Studio startet [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) und führt die App aus.</span><span class="sxs-lookup"><span data-stu-id="71284-230">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="71284-231">Beachten Sie, dass die Adressleiste `localhost:port#` und nicht etwas wie `example.com` anzeigt.</span><span class="sxs-lookup"><span data-stu-id="71284-231">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="71284-232">Das liegt daran, dass es sich bei `localhost` um den Standard-Hostnamen für Ihren lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="71284-232">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="71284-233">Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.</span><span class="sxs-lookup"><span data-stu-id="71284-233">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="71284-234">Das Starten der App mit STRG+F5 (Nicht-Debugmodus) ermöglicht die Änderung des Codes, das Speichern der Datei, das Aktualisieren des Browsers und das Anzeigen von Codeänderungen.</span><span class="sxs-lookup"><span data-stu-id="71284-234">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="71284-235">Viele Entwickler bevorzugen den Nicht-Debugmodus, um die App schnell zu starten und Änderungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="71284-235">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="71284-236">Sie können die App über das Menüelement **Debuggen** im Debugmodus oder Nicht-Debugmodus starten:</span><span class="sxs-lookup"><span data-stu-id="71284-236">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menü „Debuggen“](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="71284-238">Sie können die App debuggen, indem Sie die Schaltfläche **IIS Express** auswählen.</span><span class="sxs-lookup"><span data-stu-id="71284-238">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="71284-240">Wählen Sie **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="71284-240">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="71284-241">Diese App verfolgt keine personenbezogenen Informationen nach.</span><span class="sxs-lookup"><span data-stu-id="71284-241">This app doesn't track personal information.</span></span> <span data-ttu-id="71284-242">Der generierte Vorlagencode enthält Objekte, die bei der Erfüllung der [Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) als Unterstützung dienen sollen.</span><span class="sxs-lookup"><span data-stu-id="71284-242">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](start-mvc/_static/privacy.png)

  <span data-ttu-id="71284-244">Die folgende Abbildung zeigt die App, nachdem die Nachverfolgung akzeptiert wurde:</span><span class="sxs-lookup"><span data-stu-id="71284-244">The following image shows the app after accepting tracking:</span></span>

  ![Start- oder Indexseite](start-mvc/_static/home2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="71284-246">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="71284-246">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="71284-247">Drücken Sie STRG+F5, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="71284-247">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="71284-248">Visual Studio Code startet [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="71284-248">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="71284-249">Die Adressleiste zeigt `localhost:port:5001` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="71284-249">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="71284-250">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="71284-250">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="71284-251">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="71284-251">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="71284-252">Das Starten der App mit STRG+F5 (Nicht-Debugmodus) ermöglicht die Änderung des Codes, das Speichern der Datei, das Aktualisieren des Browsers und das Anzeigen von Codeänderungen.</span><span class="sxs-lookup"><span data-stu-id="71284-252">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="71284-253">Viele Entwickler bevorzugen den Nicht-Debugmodus, um die Seite zu aktualisieren und Änderungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="71284-253">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="71284-254">Wählen Sie **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="71284-254">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="71284-255">Diese App verfolgt keine personenbezogenen Informationen nach.</span><span class="sxs-lookup"><span data-stu-id="71284-255">This app doesn't track personal information.</span></span> <span data-ttu-id="71284-256">Der generierte Vorlagencode enthält Objekte, die bei der Erfüllung der [Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) als Unterstützung dienen sollen.</span><span class="sxs-lookup"><span data-stu-id="71284-256">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](start-mvc/_static/privacy.png)

  <span data-ttu-id="71284-258">Die folgende Abbildung zeigt die App, nachdem die Nachverfolgung akzeptiert wurde:</span><span class="sxs-lookup"><span data-stu-id="71284-258">The following image shows the app after accepting tracking:</span></span>

  ![Start- oder Indexseite](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="71284-260">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="71284-260">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="71284-261">Wählen Sie **Ausführen** > **Ohne Debuggen starten** aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="71284-261">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="71284-262">Visual Studio für Mac startet den [Kestrel](xref:fundamentals/servers/index#kestrel)-Server und einen Browser und navigiert zu `http://localhost:port`, wobei es sich bei *port* um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="71284-262">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="71284-263">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="71284-263">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="71284-264">Das liegt daran, dass es sich bei `localhost` um den Standard-Hostnamen für Ihren lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="71284-264">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="71284-265">Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.</span><span class="sxs-lookup"><span data-stu-id="71284-265">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="71284-266">Wenn Sie die App ausführen, wird eine andere Portnummer angezeigt.</span><span class="sxs-lookup"><span data-stu-id="71284-266">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="71284-267">Sie können die App über das Menü **Ausführen** im Debugmodus oder Nicht-Debugmodus starten.</span><span class="sxs-lookup"><span data-stu-id="71284-267">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="71284-268">Wählen Sie **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="71284-268">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="71284-269">Diese App verfolgt keine personenbezogenen Informationen nach.</span><span class="sxs-lookup"><span data-stu-id="71284-269">This app doesn't track personal information.</span></span> <span data-ttu-id="71284-270">Der generierte Vorlagencode enthält Objekte, die bei der Erfüllung der [Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) als Unterstützung dienen sollen.</span><span class="sxs-lookup"><span data-stu-id="71284-270">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="71284-272">Die folgende Abbildung zeigt die App, nachdem die Nachverfolgung akzeptiert wurde:</span><span class="sxs-lookup"><span data-stu-id="71284-272">The following image shows the app after accepting tracking:</span></span>

  ![Start- oder Indexseite](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="71284-274">Im nächsten Teil dieses Tutorials erfahren Sie mehr über MVC und beginnen mit dem Schreiben von Code.</span><span class="sxs-lookup"><span data-stu-id="71284-274">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="71284-275">Nächste</span><span class="sxs-lookup"><span data-stu-id="71284-275">Next</span></span>](adding-controller.md)

::: moniker-end

---
title: Erste Schritte mit ASP.NET Core MVC
author: rick-anderson
description: Hier finden Sie Informationen zum Einstieg in ASP.NET Core MVC.
ms.author: riande
ms.date: 04/24/2019
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 91564bac02df77632a3b56b6dbddeb3b622f6438
ms.sourcegitcommit: d6e51c60439f03a8992bda70cc982ddb15d3f100
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555846"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="0a9cf-103">Erste Schritte mit ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="0a9cf-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="0a9cf-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0a9cf-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="0a9cf-105">In diesem Tutorial lernen Sie Grundlegendes zur Erstellung einer ASP.NET Core-MVC-Web-App.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="0a9cf-106">Die App verwaltet eine Datenbank mit Filmtiteln.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="0a9cf-107">Sie lernen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="0a9cf-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0a9cf-108">Erstellen einer Web-App</span><span class="sxs-lookup"><span data-stu-id="0a9cf-108">Create a web app.</span></span>
> * <span data-ttu-id="0a9cf-109">Hinzufügen eines Modells und Erstellen eines Gerüsts für das Modell</span><span class="sxs-lookup"><span data-stu-id="0a9cf-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="0a9cf-110">Arbeiten mit einer Datenbank</span><span class="sxs-lookup"><span data-stu-id="0a9cf-110">Work with a database.</span></span>
> * <span data-ttu-id="0a9cf-111">Hinzufügen von Such- und Überprüfungsfunktionen</span><span class="sxs-lookup"><span data-stu-id="0a9cf-111">Add search and validation.</span></span>

<span data-ttu-id="0a9cf-112">Am Ende verfügen Sie über eine App, die Filmdaten verwalten und anzeigen kann.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="0a9cf-113">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="0a9cf-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0a9cf-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a9cf-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0a9cf-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a9cf-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="0a9cf-116">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="0a9cf-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="0a9cf-117">Erstellen einer Web-App</span><span class="sxs-lookup"><span data-stu-id="0a9cf-117">Create a web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0a9cf-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a9cf-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0a9cf-119">Wählen Sie in Visual Studio die Option **Neues Projekt erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="0a9cf-120">Klicken Sie auf **ASP.NET Core-Webanwendung** und dann auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-120">Selecct **ASP.NET Core Web Application** and then select **Next**.</span></span>

![neue ASP.NET Core-Webanwendung](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="0a9cf-122">Geben Sie dem Projekt den Namen **MvcMovie**, und klicken Sie dann auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="0a9cf-123">Es ist wichtig, dem Projekt den Namen **MvcMovie** zu geben, damit beim Kopieren von Code der Namespace übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![neue ASP.NET Core-Webanwendung](start-mvc/_static/config.png)


* <span data-ttu-id="0a9cf-125">Klicken Sie auf **Webanwendung (Model View Controller)** und anschließend auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="0a9cf-126">Dialogfeld „Neues Projekt“, .NET Core im linken Bereich, ASP.NET Core-Web-App</span><span class="sxs-lookup"><span data-stu-id="0a9cf-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="0a9cf-127">Visual Studio verwendet die Standardvorlage für das MVC-Projekt, das Sie gerade erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="0a9cf-128">Wenn Sie einen Projektnamen eingeben und einige Optionen festlegen, funktioniert Ihre App bereits.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="0a9cf-129">Dies ist ein grundlegendes Startprojekt und ein guter Einstieg.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-129">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0a9cf-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a9cf-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0a9cf-131">Das Tutorial setzt voraus, dass Sie mit VS Code vertraut sind.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="0a9cf-132">Weitere Informationen finden Sie unter [Erste Schritte mit VS Code](https://code.visualstudio.com/docs) und [Hilfe zu Visual Studio Code](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="0a9cf-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="0a9cf-133">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="0a9cf-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="0a9cf-134">Wechseln Sie mit `cd` zu einem Ordner, der das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="0a9cf-135">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="0a9cf-135">Run the following command:</span></span>

   ```console
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="0a9cf-136">Es wird ein Dialogfeld mit folgender Meldung angezeigt: **Die erforderlichen Objekte zum Erstellen und Debuggen sind in "MvcMovie" nicht vorhanden. Sollen sie hinzugefügt werden?**</span><span class="sxs-lookup"><span data-stu-id="0a9cf-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="0a9cf-137">Wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-137">Select **Yes**</span></span>

  * <span data-ttu-id="0a9cf-138">`dotnet new mvc -o MvcMovie`: Erstellt ein neues ASP.NET Core MVC-Projekt im Ordner *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="0a9cf-139">`code -r MvcMovie`: Lädt die Projektdatei *MvcMovie.csproj* in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="0a9cf-140">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="0a9cf-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0a9cf-141">Wählen Sie **Datei** > **Neue Projektmappe** aus.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-141">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="0a9cf-143">Klicken Sie auf **.NET Core** > **App** > **Webanwendung (Model View Controller)**  > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-143">Select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![Dialogfeld „Neue Projektmappe“ in macOS](./start-mvc/_static/new_project_mvc_vsmac.png)

* <span data-ttu-id="0a9cf-145">Übernehmen Sie im Dialogfeld **Neue ASP.NET Core-Web-API konfigurieren** die Standardeinstellung für **Zielframework** von **.NET Core 2.2**.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-145">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of **.NET Core 2.2**.</span></span>

  ![Auswahl für .NET Core 2.2 in macOS](./start-mvc/_static/new_project_22_vsmac.png)

* <span data-ttu-id="0a9cf-147">Nennen Sie das Projekt **MvcMovie**, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-147">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="0a9cf-148">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="0a9cf-148">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0a9cf-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a9cf-149">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0a9cf-150">Drücken Sie **STRG+F5**, um die App im Nicht-Debugmodus auszuführen.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-150">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="0a9cf-151">Visual Studio startet [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) und führt die App aus.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-151">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="0a9cf-152">Beachten Sie, dass die Adressleiste `localhost:port#` und nicht etwas wie `example.com` anzeigt.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-152">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="0a9cf-153">Das liegt daran, dass es sich bei `localhost` um den Standard-Hostnamen für Ihren lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-153">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="0a9cf-154">Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-154">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="0a9cf-155">Das Starten der App mit STRG+F5 (Nicht-Debugmodus) ermöglicht die Änderung des Codes, das Speichern der Datei, das Aktualisieren des Browsers und das Anzeigen von Codeänderungen.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-155">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="0a9cf-156">Viele Entwickler bevorzugen den Nicht-Debugmodus, um die App schnell zu starten und Änderungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-156">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="0a9cf-157">Sie können die App über das Menüelement **Debuggen** im Debugmodus oder Nicht-Debugmodus starten:</span><span class="sxs-lookup"><span data-stu-id="0a9cf-157">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menü „Debuggen“](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="0a9cf-159">Sie können die App debuggen, indem Sie die Schaltfläche **IIS Express** auswählen.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-159">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="0a9cf-161">Wählen Sie **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-161">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="0a9cf-162">Diese App verfolgt keine personenbezogenen Informationen nach.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-162">This app doesn't track personal information.</span></span> <span data-ttu-id="0a9cf-163">Der generierte Vorlagencode enthält Objekte, die bei der Erfüllung der [Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) als Unterstützung dienen sollen.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-163">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](start-mvc/_static/privacy.png)

  <span data-ttu-id="0a9cf-165">Die folgende Abbildung zeigt die App, nachdem die Nachverfolgung akzeptiert wurde:</span><span class="sxs-lookup"><span data-stu-id="0a9cf-165">The following image shows the app after accepting tracking:</span></span>

  ![Start- oder Indexseite](start-mvc/_static/home2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0a9cf-167">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a9cf-167">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0a9cf-168">Drücken Sie STRG+F5, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-168">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="0a9cf-169">Visual Studio Code startet [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-169">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="0a9cf-170">Die Adressleiste zeigt `localhost:port:5001` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-170">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="0a9cf-171">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-171">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="0a9cf-172">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-172">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="0a9cf-173">Das Starten der App mit STRG+F5 (Nicht-Debugmodus) ermöglicht die Änderung des Codes, das Speichern der Datei, das Aktualisieren des Browsers und das Anzeigen von Codeänderungen.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-173">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="0a9cf-174">Viele Entwickler bevorzugen den Nicht-Debugmodus, um die Seite zu aktualisieren und Änderungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-174">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="0a9cf-175">Wählen Sie **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-175">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="0a9cf-176">Diese App verfolgt keine personenbezogenen Informationen nach.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-176">This app doesn't track personal information.</span></span> <span data-ttu-id="0a9cf-177">Der generierte Vorlagencode enthält Objekte, die bei der Erfüllung der [Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) als Unterstützung dienen sollen.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-177">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](start-mvc/_static/privacy.png)

  <span data-ttu-id="0a9cf-179">Die folgende Abbildung zeigt die App, nachdem die Nachverfolgung akzeptiert wurde:</span><span class="sxs-lookup"><span data-stu-id="0a9cf-179">The following image shows the app after accepting tracking:</span></span>

  ![Start- oder Indexseite](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="0a9cf-181">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="0a9cf-181">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0a9cf-182">Wählen Sie **Ausführen** > **Ohne Debuggen starten** aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-182">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="0a9cf-183">Visual Studio für Mac startet den [Kestrel](xref:fundamentals/servers/index#kestrel)-Server und einen Browser und navigiert zu `http://localhost:port`, wobei es sich bei *port* um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-183">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="0a9cf-184">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-184">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="0a9cf-185">Das liegt daran, dass es sich bei `localhost` um den Standard-Hostnamen für Ihren lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-185">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="0a9cf-186">Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-186">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="0a9cf-187">Wenn Sie die App ausführen, wird eine andere Portnummer angezeigt.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-187">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="0a9cf-188">Sie können die App über das Menü **Ausführen** im Debugmodus oder Nicht-Debugmodus starten.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-188">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="0a9cf-189">Wählen Sie **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-189">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="0a9cf-190">Diese App verfolgt keine personenbezogenen Informationen nach.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-190">This app doesn't track personal information.</span></span> <span data-ttu-id="0a9cf-191">Der generierte Vorlagencode enthält Objekte, die bei der Erfüllung der [Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) als Unterstützung dienen sollen.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-191">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Start- oder Indexseite](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="0a9cf-193">Die folgende Abbildung zeigt die App, nachdem die Nachverfolgung akzeptiert wurde:</span><span class="sxs-lookup"><span data-stu-id="0a9cf-193">The following image shows the app after accepting tracking:</span></span>

  ![Start- oder Indexseite](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="0a9cf-195">Im nächsten Teil dieses Tutorials erfahren Sie mehr über MVC und beginnen mit dem Schreiben von Code.</span><span class="sxs-lookup"><span data-stu-id="0a9cf-195">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="0a9cf-196">Nächste</span><span class="sxs-lookup"><span data-stu-id="0a9cf-196">Next</span></span>](adding-controller.md)

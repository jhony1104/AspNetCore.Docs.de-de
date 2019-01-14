---
title: 'Tutorial: Erstellen einer Web-API mit ASP.NET Core MVC'
author: rick-anderson
description: Erstellen einer Web-API mit ASP.NET Core MVC
ms.author: riande
ms.custom: mvc
ms.date: 12/10/2018
uid: tutorials/first-web-api
ms.openlocfilehash: 03936ee74836c7b214cb3dc4023a6e3c252f2a26
ms.sourcegitcommit: cec77d5ad8a0cedb1ecbec32834111492afd0cd2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54207446"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core-mvc"></a><span data-ttu-id="85c85-103">Tutorial: Erstellen einer Web-API mit ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="85c85-103">Tutorial: Create a web API with ASP.NET Core MVC</span></span>

<span data-ttu-id="85c85-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="85c85-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="85c85-105">In diesem Tutorial lernen Sie die Grundlagen der Erstellung einer Web-API mit ASP.NET Core kennen.</span><span class="sxs-lookup"><span data-stu-id="85c85-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

<span data-ttu-id="85c85-106">In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="85c85-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="85c85-107">Erstellen eines Web-API-Projekts</span><span class="sxs-lookup"><span data-stu-id="85c85-107">Create a web API project.</span></span>
> * <span data-ttu-id="85c85-108">Hinzufügen einer Modellklasse</span><span class="sxs-lookup"><span data-stu-id="85c85-108">Add a model class.</span></span>
> * <span data-ttu-id="85c85-109">Erstellen des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="85c85-109">Create the database context.</span></span>
> * <span data-ttu-id="85c85-110">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="85c85-110">Register the database context.</span></span>
> * <span data-ttu-id="85c85-111">Hinzufügen eines Controllers</span><span class="sxs-lookup"><span data-stu-id="85c85-111">Add a controller.</span></span>
> * <span data-ttu-id="85c85-112">Hinzufügen von CRUD-Methoden</span><span class="sxs-lookup"><span data-stu-id="85c85-112">Add CRUD methods.</span></span>
> * <span data-ttu-id="85c85-113">Konfigurieren von Routing und URL-Pfaden</span><span class="sxs-lookup"><span data-stu-id="85c85-113">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="85c85-114">Angeben von Rückgabewerten</span><span class="sxs-lookup"><span data-stu-id="85c85-114">Specify return values.</span></span>
> * <span data-ttu-id="85c85-115">Aufrufen der Web-API mit Postman</span><span class="sxs-lookup"><span data-stu-id="85c85-115">Call the web API with Postman.</span></span>
> * <span data-ttu-id="85c85-116">Aufrufen der Web-API mit jQuery</span><span class="sxs-lookup"><span data-stu-id="85c85-116">Call the web API with jQuery.</span></span>

<span data-ttu-id="85c85-117">Am Ende haben Sie eine Web-API, die in einer relationalen Datenbank gespeicherte „Aufgaben“ verwalten kann.</span><span class="sxs-lookup"><span data-stu-id="85c85-117">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="85c85-118">Übersicht</span><span class="sxs-lookup"><span data-stu-id="85c85-118">Overview</span></span>

<span data-ttu-id="85c85-119">In diesem Tutorial wird die folgende API erstellt:</span><span class="sxs-lookup"><span data-stu-id="85c85-119">This tutorial creates the following API:</span></span>

|<span data-ttu-id="85c85-120">API</span><span class="sxs-lookup"><span data-stu-id="85c85-120">API</span></span> | <span data-ttu-id="85c85-121">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="85c85-121">Description</span></span> | <span data-ttu-id="85c85-122">Anforderungstext</span><span class="sxs-lookup"><span data-stu-id="85c85-122">Request body</span></span> | <span data-ttu-id="85c85-123">Antworttext</span><span class="sxs-lookup"><span data-stu-id="85c85-123">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="85c85-124">GET /api/todo</span><span class="sxs-lookup"><span data-stu-id="85c85-124">GET /api/todo</span></span> | <span data-ttu-id="85c85-125">Alle To-do-Elemente abrufen</span><span class="sxs-lookup"><span data-stu-id="85c85-125">Get all to-do items</span></span> | <span data-ttu-id="85c85-126">Keiner</span><span class="sxs-lookup"><span data-stu-id="85c85-126">None</span></span> | <span data-ttu-id="85c85-127">Array von To-do-Elementen</span><span class="sxs-lookup"><span data-stu-id="85c85-127">Array of to-do items</span></span>|
|<span data-ttu-id="85c85-128">GET /api/todo/{id}</span><span class="sxs-lookup"><span data-stu-id="85c85-128">GET /api/todo/{id}</span></span> | <span data-ttu-id="85c85-129">Ein Element nach ID abrufen</span><span class="sxs-lookup"><span data-stu-id="85c85-129">Get an item by ID</span></span> | <span data-ttu-id="85c85-130">Keiner</span><span class="sxs-lookup"><span data-stu-id="85c85-130">None</span></span> | <span data-ttu-id="85c85-131">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="85c85-131">To-do item</span></span>|
|<span data-ttu-id="85c85-132">POST /api/todo</span><span class="sxs-lookup"><span data-stu-id="85c85-132">POST /api/todo</span></span> | <span data-ttu-id="85c85-133">Neues Element hinzufügen</span><span class="sxs-lookup"><span data-stu-id="85c85-133">Add a new item</span></span> | <span data-ttu-id="85c85-134">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="85c85-134">To-do item</span></span> | <span data-ttu-id="85c85-135">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="85c85-135">To-do item</span></span> |
|<span data-ttu-id="85c85-136">PUT /api/todo/{id}</span><span class="sxs-lookup"><span data-stu-id="85c85-136">PUT /api/todo/{id}</span></span> | <span data-ttu-id="85c85-137">Vorhandenes Element aktualisieren &nbsp;</span><span class="sxs-lookup"><span data-stu-id="85c85-137">Update an existing item &nbsp;</span></span> | <span data-ttu-id="85c85-138">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="85c85-138">To-do item</span></span> | <span data-ttu-id="85c85-139">Keiner</span><span class="sxs-lookup"><span data-stu-id="85c85-139">None</span></span> |
|<span data-ttu-id="85c85-140">DELETE /api/todo/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="85c85-140">DELETE /api/todo/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="85c85-141">Löschen eines Elements &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="85c85-141">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="85c85-142">Keiner</span><span class="sxs-lookup"><span data-stu-id="85c85-142">None</span></span> | <span data-ttu-id="85c85-143">Keiner</span><span class="sxs-lookup"><span data-stu-id="85c85-143">None</span></span>|

<span data-ttu-id="85c85-144">Das folgende Diagramm zeigt den Entwurf der App.</span><span class="sxs-lookup"><span data-stu-id="85c85-144">The following diagram shows the design of the app.</span></span>

![Der Client wird von einem Feld auf der linken Seite dargestellt. Er sendet eine Anforderung und erhält von der Anwendung (Feld auf der rechten Seite) eine Antwort.](first-web-api/_static/architecture.png)

[!INCLUDE[](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-web-project"></a><span data-ttu-id="85c85-149">Erstellen eines Webprojekts</span><span class="sxs-lookup"><span data-stu-id="85c85-149">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="85c85-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="85c85-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="85c85-151">Wählen Sie im Menü **Datei** den Befehl **Neu** > **Projekt** aus.</span><span class="sxs-lookup"><span data-stu-id="85c85-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="85c85-152">Wählen Sie die Vorlage **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="85c85-152">Select the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="85c85-153">Geben Sie dem Projekt den Namen *TodoApi*, und klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="85c85-153">Name the project *TodoApi* and click **OK**.</span></span>
* <span data-ttu-id="85c85-154">Wählen Sie im Dialogfeld **ASP.NET Core-Webanwendung – TodoAPI** die ASP.NET Core-Version aus.</span><span class="sxs-lookup"><span data-stu-id="85c85-154">In the **New ASP.NET Core Web Application - TodoApi** dialog, choose the ASP.NET Core version.</span></span> <span data-ttu-id="85c85-155">Wählen Sie die Vorlage **API** aus, und klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="85c85-155">Select the **API** template and click **OK**.</span></span> <span data-ttu-id="85c85-156">Wählen Sie **nicht** **Enable Docker Support** (Docker-Unterstützung aktivieren) aus.</span><span class="sxs-lookup"><span data-stu-id="85c85-156">Do **not** select **Enable Docker Support**.</span></span>

![VS-Dialogfeld „Neues Projekt“](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="85c85-158">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="85c85-158">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="85c85-159">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="85c85-159">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="85c85-160">Wechseln Sie mit `cd` zu dem Ordner, der den Projektordner enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="85c85-160">Change directories (`cd`) to the folder which will contain the project folder.</span></span>
* <span data-ttu-id="85c85-161">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="85c85-161">Run the following commands:</span></span>

   ```console
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="85c85-162">Diese Befehle erstellen ein neues Web-API-Projekt und öffnen eine neue Visual Studio Code-Instanz im neuen Projektordner.</span><span class="sxs-lookup"><span data-stu-id="85c85-162">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="85c85-163">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem Projekt die erforderlichen Elemente hinzufügen möchten, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="85c85-163">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="85c85-164">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="85c85-164">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="85c85-165">Wählen Sie **Datei** > **Neue Projektmappe** aus.</span><span class="sxs-lookup"><span data-stu-id="85c85-165">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="85c85-167">Klicken Sie auf **.NET Core-App** > **ASP.NET Core-Web-API** > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="85c85-167">Select **.NET Core App** > **ASP.NET Core Web API** > **Next**.</span></span>

  ![Dialogfeld „Neue Projektmappe“ in macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="85c85-169">Übernehmen Sie im Dialogfeld **Neue ASP.NET Core-Web-API konfigurieren** die Standardeinstellung **Zielframework** von \**.NET Core 2.2*.</span><span class="sxs-lookup"><span data-stu-id="85c85-169">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="85c85-170">Geben Sie für **Projektname** *TodoApi* ein, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="85c85-170">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogfeld „Konfiguration“](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="85c85-172">Testen der API</span><span class="sxs-lookup"><span data-stu-id="85c85-172">Test the API</span></span>

<span data-ttu-id="85c85-173">Die Projektvorlage erstellt eine `values`-API.</span><span class="sxs-lookup"><span data-stu-id="85c85-173">The project template creates a `values` API.</span></span> <span data-ttu-id="85c85-174">Rufen Sie in einem Browser die `Get`-Methode zum Testen der App auf.</span><span class="sxs-lookup"><span data-stu-id="85c85-174">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="85c85-175">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="85c85-175">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="85c85-176">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="85c85-176">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="85c85-177">Visual Studio startet einen Browser und navigiert zu `https://localhost:<port>/api/values`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="85c85-177">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="85c85-178">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem IIS Express-Zertifikat vertrauen, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="85c85-178">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="85c85-179">Wählen Sie im folgenden Dialogfeld **Sicherheitswarnung** die Option **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="85c85-179">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="85c85-180">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="85c85-180">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="85c85-181">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="85c85-181">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="85c85-182">Rufen Sie in einem Browser die folgende URL auf: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span><span class="sxs-lookup"><span data-stu-id="85c85-182">In a browser, go to following URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="85c85-183">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="85c85-183">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="85c85-184">Wählen Sie **Ausführen** > **Mit Debugging starten** aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="85c85-184">Select **Run** > **Start With Debugging** to launch the app.</span></span> <span data-ttu-id="85c85-185">Visual Studio für Mac startet einen Browser und navigiert zu `https://localhost:<port>`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="85c85-185">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="85c85-186">Der Fehler „HTTP 404: Nicht gefunden“ wird zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="85c85-186">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="85c85-187">Fügen Sie der URL `/api/values` an, d.h. ändern Sie die URL in `https://localhost:<port>/api/values`.</span><span class="sxs-lookup"><span data-stu-id="85c85-187">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="85c85-188">Die folgende JSON-Datei wird zurückgegeben:</span><span class="sxs-lookup"><span data-stu-id="85c85-188">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="85c85-189">Hinzufügen einer Modellklasse</span><span class="sxs-lookup"><span data-stu-id="85c85-189">Add a model class</span></span>

<span data-ttu-id="85c85-190">Ein *Modell* ist eine Gruppe von Klassen, die die Daten darstellen, die die App verwaltet.</span><span class="sxs-lookup"><span data-stu-id="85c85-190">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="85c85-191">Das Modell für diese App ist eine einzelne `TodoItem`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="85c85-191">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="85c85-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="85c85-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="85c85-193">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="85c85-193">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="85c85-194">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="85c85-194">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="85c85-195">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="85c85-195">Name the folder *Models*.</span></span>

* <span data-ttu-id="85c85-196">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="85c85-196">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="85c85-197">Nennen Sie die Klasse *TodoItem*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="85c85-197">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="85c85-198">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="85c85-198">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="85c85-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="85c85-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="85c85-200">Fügen Sie einen Ordner namens *Models* hinzu.</span><span class="sxs-lookup"><span data-stu-id="85c85-200">Add a folder named *Models*.</span></span>

* <span data-ttu-id="85c85-201">Fügen Sie dem Ordner *Modelle* mit dem folgenden Code eine `TodoItem`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="85c85-201">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="85c85-202">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="85c85-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="85c85-203">Klicken Sie mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="85c85-203">Right-click the project.</span></span> <span data-ttu-id="85c85-204">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="85c85-204">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="85c85-205">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="85c85-205">Name the folder *Models*.</span></span>

  ![Neuer Ordner](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="85c85-207">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und klicken Sie auf **Hinzufügen** > **Neue Datei** > **Allgemein** > **Leere Klasse**.</span><span class="sxs-lookup"><span data-stu-id="85c85-207">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="85c85-208">Nennen Sie die Klasse *TodoItem*, und klicken Sie dann auf **Neu**.</span><span class="sxs-lookup"><span data-stu-id="85c85-208">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="85c85-209">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="85c85-209">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="85c85-210">Die `Id`-Eigenschaft fungiert als eindeutiger Schlüssel in einer relationalen Datenbank.</span><span class="sxs-lookup"><span data-stu-id="85c85-210">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="85c85-211">Modellklassen können überall im Projekt platziert werden, doch gemäß der Konvention wird der Ordner *Modelle* verwendet.</span><span class="sxs-lookup"><span data-stu-id="85c85-211">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="85c85-212">Hinzufügen eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="85c85-212">Add a database context</span></span>

<span data-ttu-id="85c85-213">Der *Datenbankkontext* ist die Hauptklasse, die die Entity Framework-Funktionen für ein Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="85c85-213">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="85c85-214">Diese Klasse wird durch Ableiten von der `Microsoft.EntityFrameworkCore.DbContext`-Klasse erstellt.</span><span class="sxs-lookup"><span data-stu-id="85c85-214">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="85c85-215">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="85c85-215">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="85c85-216">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="85c85-216">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="85c85-217">Nennen Sie die Klasse *TodoContext*, und klicken Sie auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="85c85-217">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="85c85-218">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="85c85-218">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="85c85-219">Fügen Sie eine `TodoContext`-Klasse zum Ordner *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="85c85-219">Add a `TodoContext` class to the *Models* folder.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="85c85-220">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="85c85-220">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="85c85-221">Fügen Sie eine `TodoContext`-Klasse zum Ordner *Models* hinzu:</span><span class="sxs-lookup"><span data-stu-id="85c85-221">Add a `TodoContext` class in the *Models* folder:</span></span>

---

* <span data-ttu-id="85c85-222">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="85c85-222">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="85c85-223">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="85c85-223">Register the database context</span></span>

<span data-ttu-id="85c85-224">In ASP.NET Core müssen Dienste wie der Datenbankkontext mit dem Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) registriert werden.</span><span class="sxs-lookup"><span data-stu-id="85c85-224">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="85c85-225">Der Container stellt den Dienst für Controller bereit.</span><span class="sxs-lookup"><span data-stu-id="85c85-225">The container provides the service to controllers.</span></span>

<span data-ttu-id="85c85-226">Aktualisieren Sie *Startup.cs* mit dem folgenden hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="85c85-226">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="85c85-227">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="85c85-227">The preceding code:</span></span>

* <span data-ttu-id="85c85-228">Entfernt nicht benötigte `using`-Deklarationen</span><span class="sxs-lookup"><span data-stu-id="85c85-228">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="85c85-229">Fügt dem Abhängigkeitscontainer den Datenbankkontext hinzu</span><span class="sxs-lookup"><span data-stu-id="85c85-229">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="85c85-230">Gibt an, dass der Datenbankkontext eine In-Memory Database verwendet</span><span class="sxs-lookup"><span data-stu-id="85c85-230">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="85c85-231">Hinzufügen eines Controllers</span><span class="sxs-lookup"><span data-stu-id="85c85-231">Add a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="85c85-232">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="85c85-232">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="85c85-233">Klicken Sie mit der rechten Maustaste auf den Ordner *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="85c85-233">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="85c85-234">Klicken Sie auf **Hinzufügen** > **Neues Element**.</span><span class="sxs-lookup"><span data-stu-id="85c85-234">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="85c85-235">Wählen Sie im Dialogfeld **Neues Element hinzufügen** die Vorlage **API-Controllerklasse** aus.</span><span class="sxs-lookup"><span data-stu-id="85c85-235">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="85c85-236">Benennen Sie die Klasse *TodoController*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="85c85-236">Name the class *TodoController*, and select **Add**.</span></span>

  ![Dialogfeld „Neues Element hinzufügen“ mit Controller im Suchfeld und ausgewähltem Web-API-Controller](first-web-api/_static/new_controller.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="85c85-238">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="85c85-238">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="85c85-239">Erstellen Sie im Ordner *Controllers* eine Klasse namens `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="85c85-239">In the *Controllers* folder, create a class named `TodoController`.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="85c85-240">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="85c85-240">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="85c85-241">Fügen Sie dem Ordner *Controller* die Klasse `TodoController` hinzu.</span><span class="sxs-lookup"><span data-stu-id="85c85-241">In the *Controllers* folder, add the class `TodoController`.</span></span>

---

* <span data-ttu-id="85c85-242">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="85c85-242">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="85c85-243">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="85c85-243">The preceding code:</span></span>

* <span data-ttu-id="85c85-244">Er definiert eine API-Controllerklasse ohne Methoden.</span><span class="sxs-lookup"><span data-stu-id="85c85-244">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="85c85-245">Ergänzt die Klasse mit dem Attribut [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute).</span><span class="sxs-lookup"><span data-stu-id="85c85-245">Decorates the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="85c85-246">Dieses Attribut gibt an, dass der Controller auf Web-API-Anforderungen reagiert.</span><span class="sxs-lookup"><span data-stu-id="85c85-246">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="85c85-247">Weitere Informationen zu bestimmten Verhaltensweisen, die das Attribut ermöglicht, finden Sie unter [Anmerkungen mit dem ApiController-Attribut](xref:web-api/index#annotation-with-apicontroller-attribute).</span><span class="sxs-lookup"><span data-stu-id="85c85-247">For information about specific behaviors that the attribute enables, see [Annotation with ApiController attribute](xref:web-api/index#annotation-with-apicontroller-attribute).</span></span>
* <span data-ttu-id="85c85-248">Verwendet die Abhängigkeitsinjektion, um den Datenbankkontext (`TodoContext`) dem Controller hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="85c85-248">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="85c85-249">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="85c85-249">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="85c85-250">Fügt der Datenbank, falls sie leer ist, ein Element mit dem Namen `Item1` hinzu.</span><span class="sxs-lookup"><span data-stu-id="85c85-250">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="85c85-251">Dieser Code befindet sich im Konstruktor. Er wird bei jeder neuen HTTP-Anforderung ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="85c85-251">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="85c85-252">Wenn Sie alle Elemente löschen, erstellt der Konstruktor beim nächsten Aufruf einer API-Methode erneut `Item1`.</span><span class="sxs-lookup"><span data-stu-id="85c85-252">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="85c85-253">So sieht es möglicherweise so aus, als sei die Löschung fehlgeschlagen, obwohl sie erfolgreich war.</span><span class="sxs-lookup"><span data-stu-id="85c85-253">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="85c85-254">Hinzufügen von GET-Methoden</span><span class="sxs-lookup"><span data-stu-id="85c85-254">Add Get methods</span></span>

<span data-ttu-id="85c85-255">Fügen Sie der Klasse `TodoController` die folgenden Methoden hinzu, um eine API bereitzustellen, die Aufgaben abruft:</span><span class="sxs-lookup"><span data-stu-id="85c85-255">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="85c85-256">Diese Methoden implementieren zwei GET-Endpunkte:</span><span class="sxs-lookup"><span data-stu-id="85c85-256">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="85c85-257">Testen Sie die App, indem Sie die beiden Endpunkte in einem Browser aufrufen.</span><span class="sxs-lookup"><span data-stu-id="85c85-257">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="85c85-258">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="85c85-258">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="85c85-259">Die folgende HTTP-Antwort wird durch den Aufruf von `GetTodoItems` erzeugt:</span><span class="sxs-lookup"><span data-stu-id="85c85-259">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="85c85-260">Routing und URL-Pfade</span><span class="sxs-lookup"><span data-stu-id="85c85-260">Routing and URL paths</span></span>

<span data-ttu-id="85c85-261">Das Attribut [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) gibt eine Methode an, die auf eine HTTP GET-Anforderung antwortet.</span><span class="sxs-lookup"><span data-stu-id="85c85-261">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="85c85-262">Der URL-Pfad für jede Methode wird wie folgt erstellt:</span><span class="sxs-lookup"><span data-stu-id="85c85-262">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="85c85-263">Beginnen Sie mit der Vorlagenzeichenfolge im `Route`-Attribut des Controllers:</span><span class="sxs-lookup"><span data-stu-id="85c85-263">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="85c85-264">Ersetzen Sie `[controller]` durch den Namen des Controllers, bei dem es sich standardmäßig um den Namen der Controller-Klasse ohne das Suffix „Controller“ handelt.</span><span class="sxs-lookup"><span data-stu-id="85c85-264">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="85c85-265">Bei diesem Beispiel ist der Klassenname des Controllers „**Todo**Controller“, d.h. der Controllername lautet „todo“.</span><span class="sxs-lookup"><span data-stu-id="85c85-265">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="85c85-266">Beim ASP.NET Core-[Routing](xref:mvc/controllers/routing) wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="85c85-266">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="85c85-267">Wenn das `[HttpGet]`-Attribut eine Routenvorlage (z.B. `[HttpGet("/products")]`) hat, fügen Sie diese an den Pfad an.</span><span class="sxs-lookup"><span data-stu-id="85c85-267">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("/products")]`, append that to the path.</span></span> <span data-ttu-id="85c85-268">In diesem Beispiel wird keine Vorlage verwendet.</span><span class="sxs-lookup"><span data-stu-id="85c85-268">This sample doesn't use a template.</span></span> <span data-ttu-id="85c85-269">Weitere Informationen finden Sie unter [Attributrouting mit Http[Verb]-Attributen](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="85c85-269">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="85c85-270">In der folgenden `GetTodoItem`-Methode ist `"{id}"` eine Platzhaltervariable für den eindeutigen Bezeichners des To-do-Elements.</span><span class="sxs-lookup"><span data-stu-id="85c85-270">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="85c85-271">Wenn `GetTodoItem` aufgerufen wird, wird der Wert von `"{id}"` in der URL der Methode in ihrem Parameter `id` bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="85c85-271">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

<span data-ttu-id="85c85-272">Der Parameter `Name = "GetTodo"` erstellt eine benannte Route.</span><span class="sxs-lookup"><span data-stu-id="85c85-272">The `Name = "GetTodo"` parameter creates a named route.</span></span> <span data-ttu-id="85c85-273">Sie erfahren später, wie die App mithilfe des Routennamens einen HTTP-Link erstellt.</span><span class="sxs-lookup"><span data-stu-id="85c85-273">You'll see later how the app can use the name to create an HTTP link using the route name.</span></span>

## <a name="return-values"></a><span data-ttu-id="85c85-274">Rückgabewert</span><span class="sxs-lookup"><span data-stu-id="85c85-274">Return values</span></span>

<span data-ttu-id="85c85-275">Der Rückgabetyp der Methoden `GetTodoItems` und `GetTodoItem` ist [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="85c85-275">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="85c85-276">ASP.NET Core serialisiert automatisch das Objekt in [JSON](https://www.json.org/) und schreibt den JSON-Code in den Text der Antwortnachricht.</span><span class="sxs-lookup"><span data-stu-id="85c85-276">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="85c85-277">Der Antwortcode für diesen Rückgabetyp ist 200, vorausgesetzt, es gibt keine Ausnahmefehler.</span><span class="sxs-lookup"><span data-stu-id="85c85-277">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="85c85-278">Nicht behandelte Ausnahmen werden in 5xx-Fehler übersetzt.</span><span class="sxs-lookup"><span data-stu-id="85c85-278">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="85c85-279">`ActionResult`-Rückgabetypen können eine Vielzahl von HTTP-Statuscodes darstellen.</span><span class="sxs-lookup"><span data-stu-id="85c85-279">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="85c85-280">Beispielsweise kann `GetTodoItem` zwei verschiedene Statuswerte zurückgeben:</span><span class="sxs-lookup"><span data-stu-id="85c85-280">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="85c85-281">Wenn kein Element mit der angeforderten ID übereinstimmt, gibt die Methode einen 404-Fehlercode [Nicht gefunden](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) zurück.</span><span class="sxs-lookup"><span data-stu-id="85c85-281">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="85c85-282">Andernfalls gibt die Methode 200 mit einem JSON-Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="85c85-282">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="85c85-283">Die Rückgabe von `item` löst eine HTTP 200-Antwort aus.</span><span class="sxs-lookup"><span data-stu-id="85c85-283">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="85c85-284">Testen der GetTodoItems-Methode</span><span class="sxs-lookup"><span data-stu-id="85c85-284">Test the GetTodoItems method</span></span>

<span data-ttu-id="85c85-285">Dieses Tutorial verwendet Postman zum Testen der Web-API.</span><span class="sxs-lookup"><span data-stu-id="85c85-285">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="85c85-286">Installieren Sie [Postman](https://www.getpostman.com/apps).</span><span class="sxs-lookup"><span data-stu-id="85c85-286">Install [Postman](https://www.getpostman.com/apps)</span></span>
* <span data-ttu-id="85c85-287">Starten Sie die Web-App.</span><span class="sxs-lookup"><span data-stu-id="85c85-287">Start the web app.</span></span>
* <span data-ttu-id="85c85-288">Starten Sie Postman.</span><span class="sxs-lookup"><span data-stu-id="85c85-288">Start Postman.</span></span>
* <span data-ttu-id="85c85-289">Deaktivieren Sie **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="85c85-289">Disable **SSL certificate verification**</span></span>
  
  * <span data-ttu-id="85c85-290">Deaktivieren Sie auf der Registerkarte \**General* (Allgemein) unter **File > Settings** (Datei > Einstellungen) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="85c85-290">From  **File > Settings** (\**General* tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="85c85-291">Aktivieren Sie die Verifizierung des SSL-Zertifikats wieder, nachdem Sie den Controller getestet haben.</span><span class="sxs-lookup"><span data-stu-id="85c85-291">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="85c85-292">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="85c85-292">Create a new request.</span></span>
  * <span data-ttu-id="85c85-293">Legen Sie die HTTP-Methode auf **GET** fest.</span><span class="sxs-lookup"><span data-stu-id="85c85-293">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="85c85-294">Legen Sie die Anforderungs-URL auf `https://localhost:<port>/api/todo` fest.</span><span class="sxs-lookup"><span data-stu-id="85c85-294">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="85c85-295">Beispielsweise `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="85c85-295">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="85c85-296">Wählen Sie in Postman **Two pane view** (Ansicht mit zwei Bereichen) aus.</span><span class="sxs-lookup"><span data-stu-id="85c85-296">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="85c85-297">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="85c85-297">Select **Send**.</span></span>

![Postman mit GET-Anforderung](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="85c85-299">Hinzufügen einer Methode</span><span class="sxs-lookup"><span data-stu-id="85c85-299">Add a Create method</span></span>

<span data-ttu-id="85c85-300">Fügen Sie die folgende `PostTodoItem`-Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="85c85-300">Add the following `PostTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="85c85-301">Der vorherige Code ist eine HTTP POST-Methode, die durch das Attribut [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="85c85-301">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="85c85-302">Die Methode ruft den Wert der Aufgabe aus dem Text der HTTP-Anforderung ab.</span><span class="sxs-lookup"><span data-stu-id="85c85-302">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="85c85-303">Die `CreatedAtAction`-Methode:</span><span class="sxs-lookup"><span data-stu-id="85c85-303">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="85c85-304">Gibt eine 201-Antwort zurück.</span><span class="sxs-lookup"><span data-stu-id="85c85-304">Returns a 201 response.</span></span> <span data-ttu-id="85c85-305">HTTP 201 ist die Standardantwort für eine HTTP POST-Methode, die eine neue Ressource auf dem Server erstellt.</span><span class="sxs-lookup"><span data-stu-id="85c85-305">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="85c85-306">Fügt der Antwort einen Adressheader hinzu.</span><span class="sxs-lookup"><span data-stu-id="85c85-306">Adds a Location header to the response.</span></span> <span data-ttu-id="85c85-307">Der Adressheader gibt den URI des neu erstellten To-Do-Elements zurück.</span><span class="sxs-lookup"><span data-stu-id="85c85-307">The Location header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="85c85-308">Weitere Informationen finden Sie unter [10.2.2 201 Erstellt](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="85c85-308">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="85c85-309">Verwendet die „GetTodo“ genannte Route, um die URL zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="85c85-309">Uses the "GetTodo" named route to create the URL.</span></span> <span data-ttu-id="85c85-310">Die Route mit dem Namen „GetTodo“ wird in `GetTodoItem` definiert:</span><span class="sxs-lookup"><span data-stu-id="85c85-310">The "GetTodo" named route is defined in `GetTodoItem`:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="85c85-311">Testen der PostTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="85c85-311">Test the PostTodoItem method</span></span>

* <span data-ttu-id="85c85-312">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="85c85-312">Build the project.</span></span>
* <span data-ttu-id="85c85-313">Legen Sie in Postman die HTTP-Methode auf `POST` fest.</span><span class="sxs-lookup"><span data-stu-id="85c85-313">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="85c85-314">Wählen Sie die Registerkarte **Body** (Text) aus.</span><span class="sxs-lookup"><span data-stu-id="85c85-314">Select the **Body** tab.</span></span>
* <span data-ttu-id="85c85-315">Wählen Sie das Optionsfeld **raw** (Unformatiert) aus.</span><span class="sxs-lookup"><span data-stu-id="85c85-315">Select the **raw** radio button.</span></span>
* <span data-ttu-id="85c85-316">Legen Sie den Typ auf **JSON (application/json)** fest.</span><span class="sxs-lookup"><span data-stu-id="85c85-316">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="85c85-317">Geben Sie für die Aufgabe den Anforderungstext im JSON-Format ein:</span><span class="sxs-lookup"><span data-stu-id="85c85-317">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="85c85-318">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="85c85-318">Select **Send**.</span></span>

  ![Postman mit erstellter Anforderung](first-web-api/_static/create.png)

  <span data-ttu-id="85c85-320">Wenn Sie den Fehler „405: Methode nicht zulässig“ erhalten, wurde das Projekt wahrscheinlich nicht kompiliert, nachdem Sie die Methode `PostTodoItem` hinzugefügt haben.</span><span class="sxs-lookup"><span data-stu-id="85c85-320">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="85c85-321">Testen des Adressheader-URIs</span><span class="sxs-lookup"><span data-stu-id="85c85-321">Test the location header URI</span></span>

* <span data-ttu-id="85c85-322">Wählen Sie auf der Registerkarte **Header** (Header) den Bereich **Response** (Antwort) aus.</span><span class="sxs-lookup"><span data-stu-id="85c85-322">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="85c85-323">Kopieren Sie den den Headerwert von **Location** (Speicherort):</span><span class="sxs-lookup"><span data-stu-id="85c85-323">Copy the **Location** header value:</span></span>

  ![Registerkarte „Header“ in der Postman-Konsole](first-web-api/_static/pmc2.png)

* <span data-ttu-id="85c85-325">Legen Sie die Methode auf „GET“ fest.</span><span class="sxs-lookup"><span data-stu-id="85c85-325">Set the method to GET.</span></span>
* <span data-ttu-id="85c85-326">Fügen Sie den URI (z. B. `https://localhost:5001/api/Todo/2`) ein.</span><span class="sxs-lookup"><span data-stu-id="85c85-326">Paste the URI (for example, `https://localhost:5001/api/Todo/2`)</span></span>
* <span data-ttu-id="85c85-327">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="85c85-327">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="85c85-328">Hinzufügen einer PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="85c85-328">Add a PutTodoItem method</span></span>

<span data-ttu-id="85c85-329">Fügen Sie die folgende `PutTodoItem`-Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="85c85-329">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="85c85-330">`PutTodoItem` ähnelt `PostTodoItem`, verwendet allerdings HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="85c85-330">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="85c85-331">Die Antwort ist [204 (Kein Inhalt)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="85c85-331">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="85c85-332">Gemäß der HTTP-Spezifikation erfordert eine PUT-Anforderung, dass der Client die gesamte aktualisierte Entität (nicht nur die Änderungen) sendet.</span><span class="sxs-lookup"><span data-stu-id="85c85-332">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="85c85-333">Verwenden Sie [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute), um Teilupdates zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="85c85-333">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="85c85-334">Testen der PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="85c85-334">Test the PutTodoItem method</span></span>

<span data-ttu-id="85c85-335">Aktualisieren Sie die Aufgabe mit der ID = 1, und setzen Sie ihren Namen auf „feed fish“:</span><span class="sxs-lookup"><span data-stu-id="85c85-335">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="85c85-336">Die folgende Abbildung zeigt das Postman-Update:</span><span class="sxs-lookup"><span data-stu-id="85c85-336">The following image shows the Postman update:</span></span>

![Postman-Konsole mit der Antwort „204 (Kein Inhalt)“](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="85c85-338">Hinzufügen einer DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="85c85-338">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="85c85-339">Fügen Sie die folgende `DeleteTodoItem`-Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="85c85-339">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="85c85-340">Die `DeleteTodoItem`-Antwort lautet [204 (Kein Inhalt)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="85c85-340">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="85c85-341">Testen der DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="85c85-341">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="85c85-342">So löschen Sie mit Postman eine Aufgabe</span><span class="sxs-lookup"><span data-stu-id="85c85-342">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="85c85-343">Legen Sie die Methode auf `DELETE` fest.</span><span class="sxs-lookup"><span data-stu-id="85c85-343">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="85c85-344">Legen Sie den URI des zu löschenden Objekts fest, z. B. `https://localhost:5001/api/todo/1`.</span><span class="sxs-lookup"><span data-stu-id="85c85-344">Set the URI of the object to delete, for example `https://localhost:5001/api/todo/1`</span></span>
* <span data-ttu-id="85c85-345">Klicken Sie auf **Send**.</span><span class="sxs-lookup"><span data-stu-id="85c85-345">Select **Send**</span></span>

<span data-ttu-id="85c85-346">Sie können in der Beispiel-App alle Elemente löschen. Sobald das letzte Element gelöscht wurde, wird allerdings beim nächsten API-Aufruf vom Modellklassenkonstruktor ein neues erstellt.</span><span class="sxs-lookup"><span data-stu-id="85c85-346">The sample app allows you to delete all the items, but when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-api-with-jquery"></a><span data-ttu-id="85c85-347">Aufrufen der API mit jQuery</span><span class="sxs-lookup"><span data-stu-id="85c85-347">Call the API with jQuery</span></span>

<span data-ttu-id="85c85-348">In diesem Abschnitt wird eine HTML-Seite hinzugefügt, die mithilfe von jQuery die Web-API aufruft.</span><span class="sxs-lookup"><span data-stu-id="85c85-348">In this section, an HTML page is added that uses jQuery to call the web api.</span></span> <span data-ttu-id="85c85-349">jQuery initiiert die Anforderung und aktualisiert die Seite mit den Informationen aus der Antwort der API.</span><span class="sxs-lookup"><span data-stu-id="85c85-349">jQuery initiates the request and updates the page with the details from the API's response.</span></span>

<span data-ttu-id="85c85-350">Konfigurieren Sie die App so, dass [statische Dateien unterstützt](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) und [Standarddateizuordnung aktiviert](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) werden.</span><span class="sxs-lookup"><span data-stu-id="85c85-350">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_):</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

::: moniker range=">= aspnetcore-2.2"
<span data-ttu-id="85c85-351">Erstellen Sie im Projektverzeichnis den Ordner *wwwwroot*.</span><span class="sxs-lookup"><span data-stu-id="85c85-351">Create a *wwwroot* folder in the project directory.</span></span>
::: moniker-end

<span data-ttu-id="85c85-352">Fügen Sie dem Verzeichnis *wwwroot* eine HTML-Datei namens *index.html* hinzu.</span><span class="sxs-lookup"><span data-stu-id="85c85-352">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="85c85-353">Ersetzen Sie den Inhalt durch folgendes Markup:</span><span class="sxs-lookup"><span data-stu-id="85c85-353">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="85c85-354">Fügen Sie dem Verzeichnis *wwwroot* eine JavaScript-Datei namens *site.js* hinzu.</span><span class="sxs-lookup"><span data-stu-id="85c85-354">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="85c85-355">Ersetzen Sie den Inhalt durch folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="85c85-355">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="85c85-356">Möglicherweise ist eine Änderung an den Starteinstellungen des ASP.NET Core-Projekts erforderlich, um die HTML-Seite lokal zu testen:</span><span class="sxs-lookup"><span data-stu-id="85c85-356">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="85c85-357">Öffnen Sie *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="85c85-357">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="85c85-358">Entfernen Sie die `launchUrl`-Eigenschaft, um zu erzwingen, dass die App mit *index.html* als Startseite geöffnet wird. Dies ist die Standarddatei des Projekts.</span><span class="sxs-lookup"><span data-stu-id="85c85-358">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="85c85-359">Es gibt mehrere Möglichkeiten, um jQuery herunterzuladen.</span><span class="sxs-lookup"><span data-stu-id="85c85-359">There are several ways to get jQuery.</span></span> <span data-ttu-id="85c85-360">Im vorherigen Codeausschnitt wurde die Bibliothek aus einem Content Delivery Network (CDN) geladen.</span><span class="sxs-lookup"><span data-stu-id="85c85-360">In the preceding snippet, the library is loaded from a CDN.</span></span>

<span data-ttu-id="85c85-361">Dieses Beispiel ruft alle CRUD-Methoden der API auf.</span><span class="sxs-lookup"><span data-stu-id="85c85-361">This sample calls all of the CRUD methods of the API.</span></span> <span data-ttu-id="85c85-362">Im Folgenden werden die API-Aufrufe erläutert.</span><span class="sxs-lookup"><span data-stu-id="85c85-362">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="85c85-363">Abrufen einer Liste von To-Do-Elementen</span><span class="sxs-lookup"><span data-stu-id="85c85-363">Get a list of to-do items</span></span>

<span data-ttu-id="85c85-364">Die jQuery-Funktion [ajax](https://api.jquery.com/jquery.ajax/) sendet eine `GET`-Anforderung an die API, die JSON-Code zurückgibt, der ein Aufgabenarray darstellt.</span><span class="sxs-lookup"><span data-stu-id="85c85-364">The jQuery [ajax](https://api.jquery.com/jquery.ajax/) function sends a `GET` request to the API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="85c85-365">Die Rückruffunktion `success` wird aufgerufen, wenn die Anforderung erfolgreich ist.</span><span class="sxs-lookup"><span data-stu-id="85c85-365">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="85c85-366">Im Rückruf wird DOM mit den To-Do-Informationen aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="85c85-366">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="85c85-367">Hinzufügen eines To-Do-Elements</span><span class="sxs-lookup"><span data-stu-id="85c85-367">Add a to-do item</span></span>

<span data-ttu-id="85c85-368">Die Funktion [ajax](https://api.jquery.com/jquery.ajax/) sendet eine `POST`-Anforderung mit der Aufgabe im Anforderungstext.</span><span class="sxs-lookup"><span data-stu-id="85c85-368">The [ajax](https://api.jquery.com/jquery.ajax/) function sends a `POST` request with the to-do item in the request body.</span></span> <span data-ttu-id="85c85-369">Die Optionen `accepts` und `contentType` werden auf `application/json` festgelegt, um den gesendeten und empfangenen Medientyp anzugeben.</span><span class="sxs-lookup"><span data-stu-id="85c85-369">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="85c85-370">Die Aufgabe wird mit [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) in JSON konvertiert.</span><span class="sxs-lookup"><span data-stu-id="85c85-370">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="85c85-371">Wenn die API den Statuscode „Erfolgreich“ zurückgibt, wird die Funktion `getData` aufgerufen, um die HTML-Tabelle zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="85c85-371">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="85c85-372">Aktualisieren eines To-Do-Elements</span><span class="sxs-lookup"><span data-stu-id="85c85-372">Update a to-do item</span></span>

<span data-ttu-id="85c85-373">Das Aktualisieren einer Aufgabe ist vergleichbar mit dem Hinzufügen einer Aufgabe.</span><span class="sxs-lookup"><span data-stu-id="85c85-373">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="85c85-374">Die `url` ändert sich, um den eindeutigen Bezeichner des Elements hinzuzufügen. Der `type` lautet `PUT`.</span><span class="sxs-lookup"><span data-stu-id="85c85-374">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="85c85-375">Löschen eines To-Do-Elements</span><span class="sxs-lookup"><span data-stu-id="85c85-375">Delete a to-do item</span></span>

<span data-ttu-id="85c85-376">Eine Aufgabe wird folgendermaßen gelöscht: im AJAX-Aufruf wird `type` auf `DELETE` festgelegt, und der eindeutige Bezeichner des Elements wird in der URL angegeben.</span><span class="sxs-lookup"><span data-stu-id="85c85-376">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

## <a name="additional-resources"></a><span data-ttu-id="85c85-377">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="85c85-377">Additional resources</span></span>

<span data-ttu-id="85c85-378">[Sie können den Beispielcode für dieses Tutorial anzeigen oder herunterladen.](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)</span><span class="sxs-lookup"><span data-stu-id="85c85-378">[View or download sample code for this tutorial](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="85c85-379">Informationen [zum Herunterladen](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="85c85-379">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="85c85-380">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="85c85-380">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/index>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>

## <a name="next-steps"></a><span data-ttu-id="85c85-381">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="85c85-381">Next steps</span></span>

<span data-ttu-id="85c85-382">In diesem Tutorial haben Sie gelernt, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="85c85-382">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="85c85-383">Erstellen eines Web-API-Projekts</span><span class="sxs-lookup"><span data-stu-id="85c85-383">Create a web api project.</span></span>
> * <span data-ttu-id="85c85-384">Hinzufügen einer Modellklasse</span><span class="sxs-lookup"><span data-stu-id="85c85-384">Add a model class.</span></span>
> * <span data-ttu-id="85c85-385">Erstellen des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="85c85-385">Create the database context.</span></span>
> * <span data-ttu-id="85c85-386">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="85c85-386">Register the database context.</span></span>
> * <span data-ttu-id="85c85-387">Hinzufügen eines Controllers</span><span class="sxs-lookup"><span data-stu-id="85c85-387">Add a controller.</span></span>
> * <span data-ttu-id="85c85-388">Hinzufügen von CRUD-Methoden</span><span class="sxs-lookup"><span data-stu-id="85c85-388">Add CRUD methods.</span></span>
> * <span data-ttu-id="85c85-389">Konfigurieren von Routing und URL-Pfaden</span><span class="sxs-lookup"><span data-stu-id="85c85-389">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="85c85-390">Angeben von Rückgabewerten</span><span class="sxs-lookup"><span data-stu-id="85c85-390">Specify return values.</span></span>
> * <span data-ttu-id="85c85-391">Aufrufen der Web-API mit Postman</span><span class="sxs-lookup"><span data-stu-id="85c85-391">Call the web API with Postman.</span></span>
> * <span data-ttu-id="85c85-392">Aufrufen der Web-API mit jQuery</span><span class="sxs-lookup"><span data-stu-id="85c85-392">Call the web api with jQuery.</span></span>

<span data-ttu-id="85c85-393">Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie API-Hilfeseiten generieren:</span><span class="sxs-lookup"><span data-stu-id="85c85-393">Advance to the next tutorial to learn how to generate API help pages:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>

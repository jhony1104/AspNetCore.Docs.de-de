---
title: 'Tutorial: Erstellen einer Web-API mit ASP.NET Core MVC'
author: rick-anderson
description: Erstellen einer Web-API mit ASP.NET Core MVC
ms.author: riande
ms.custom: mvc
ms.date: 01/24/2019
uid: tutorials/first-web-api
ms.openlocfilehash: 65af70be2cec68d30dd712b80312ebcd40ea0624
ms.sourcegitcommit: c6db8b14521814f1f7e528d7aa06e474e4c04a1f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/25/2019
ms.locfileid: "55065047"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core-mvc"></a><span data-ttu-id="f6f0d-103">Tutorial: Erstellen einer Web-API mit ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="f6f0d-103">Tutorial: Create a web API with ASP.NET Core MVC</span></span>

<span data-ttu-id="f6f0d-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="f6f0d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="f6f0d-105">In diesem Tutorial lernen Sie die Grundlagen der Erstellung einer Web-API mit ASP.NET Core kennen.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

<span data-ttu-id="f6f0d-106">In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="f6f0d-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f6f0d-107">Erstellen eines Web-API-Projekts</span><span class="sxs-lookup"><span data-stu-id="f6f0d-107">Create a web API project.</span></span>
> * <span data-ttu-id="f6f0d-108">Hinzufügen einer Modellklasse</span><span class="sxs-lookup"><span data-stu-id="f6f0d-108">Add a model class.</span></span>
> * <span data-ttu-id="f6f0d-109">Erstellen des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="f6f0d-109">Create the database context.</span></span>
> * <span data-ttu-id="f6f0d-110">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="f6f0d-110">Register the database context.</span></span>
> * <span data-ttu-id="f6f0d-111">Hinzufügen eines Controllers</span><span class="sxs-lookup"><span data-stu-id="f6f0d-111">Add a controller.</span></span>
> * <span data-ttu-id="f6f0d-112">Hinzufügen von CRUD-Methoden</span><span class="sxs-lookup"><span data-stu-id="f6f0d-112">Add CRUD methods.</span></span>
> * <span data-ttu-id="f6f0d-113">Konfigurieren von Routing und URL-Pfaden</span><span class="sxs-lookup"><span data-stu-id="f6f0d-113">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="f6f0d-114">Angeben von Rückgabewerten</span><span class="sxs-lookup"><span data-stu-id="f6f0d-114">Specify return values.</span></span>
> * <span data-ttu-id="f6f0d-115">Aufrufen der Web-API mit Postman</span><span class="sxs-lookup"><span data-stu-id="f6f0d-115">Call the web API with Postman.</span></span>
> * <span data-ttu-id="f6f0d-116">Aufrufen der Web-API mit jQuery</span><span class="sxs-lookup"><span data-stu-id="f6f0d-116">Call the web API with jQuery.</span></span>

<span data-ttu-id="f6f0d-117">Am Ende haben Sie eine Web-API, die in einer relationalen Datenbank gespeicherte „Aufgaben“ verwalten kann.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-117">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="f6f0d-118">Übersicht</span><span class="sxs-lookup"><span data-stu-id="f6f0d-118">Overview</span></span>

<span data-ttu-id="f6f0d-119">In diesem Tutorial wird die folgende API erstellt:</span><span class="sxs-lookup"><span data-stu-id="f6f0d-119">This tutorial creates the following API:</span></span>

|<span data-ttu-id="f6f0d-120">API</span><span class="sxs-lookup"><span data-stu-id="f6f0d-120">API</span></span> | <span data-ttu-id="f6f0d-121">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f6f0d-121">Description</span></span> | <span data-ttu-id="f6f0d-122">Anforderungstext</span><span class="sxs-lookup"><span data-stu-id="f6f0d-122">Request body</span></span> | <span data-ttu-id="f6f0d-123">Antworttext</span><span class="sxs-lookup"><span data-stu-id="f6f0d-123">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="f6f0d-124">GET /api/todo</span><span class="sxs-lookup"><span data-stu-id="f6f0d-124">GET /api/todo</span></span> | <span data-ttu-id="f6f0d-125">Alle To-do-Elemente abrufen</span><span class="sxs-lookup"><span data-stu-id="f6f0d-125">Get all to-do items</span></span> | <span data-ttu-id="f6f0d-126">Keiner</span><span class="sxs-lookup"><span data-stu-id="f6f0d-126">None</span></span> | <span data-ttu-id="f6f0d-127">Array von To-do-Elementen</span><span class="sxs-lookup"><span data-stu-id="f6f0d-127">Array of to-do items</span></span>|
|<span data-ttu-id="f6f0d-128">GET /api/todo/{id}</span><span class="sxs-lookup"><span data-stu-id="f6f0d-128">GET /api/todo/{id}</span></span> | <span data-ttu-id="f6f0d-129">Ein Element nach ID abrufen</span><span class="sxs-lookup"><span data-stu-id="f6f0d-129">Get an item by ID</span></span> | <span data-ttu-id="f6f0d-130">Keiner</span><span class="sxs-lookup"><span data-stu-id="f6f0d-130">None</span></span> | <span data-ttu-id="f6f0d-131">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="f6f0d-131">To-do item</span></span>|
|<span data-ttu-id="f6f0d-132">POST /api/todo</span><span class="sxs-lookup"><span data-stu-id="f6f0d-132">POST /api/todo</span></span> | <span data-ttu-id="f6f0d-133">Neues Element hinzufügen</span><span class="sxs-lookup"><span data-stu-id="f6f0d-133">Add a new item</span></span> | <span data-ttu-id="f6f0d-134">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="f6f0d-134">To-do item</span></span> | <span data-ttu-id="f6f0d-135">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="f6f0d-135">To-do item</span></span> |
|<span data-ttu-id="f6f0d-136">PUT /api/todo/{id}</span><span class="sxs-lookup"><span data-stu-id="f6f0d-136">PUT /api/todo/{id}</span></span> | <span data-ttu-id="f6f0d-137">Vorhandenes Element aktualisieren &nbsp;</span><span class="sxs-lookup"><span data-stu-id="f6f0d-137">Update an existing item &nbsp;</span></span> | <span data-ttu-id="f6f0d-138">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="f6f0d-138">To-do item</span></span> | <span data-ttu-id="f6f0d-139">Keiner</span><span class="sxs-lookup"><span data-stu-id="f6f0d-139">None</span></span> |
|<span data-ttu-id="f6f0d-140">DELETE /api/todo/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="f6f0d-140">DELETE /api/todo/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="f6f0d-141">Löschen eines Elements &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="f6f0d-141">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="f6f0d-142">Keiner</span><span class="sxs-lookup"><span data-stu-id="f6f0d-142">None</span></span> | <span data-ttu-id="f6f0d-143">Keiner</span><span class="sxs-lookup"><span data-stu-id="f6f0d-143">None</span></span>|

<span data-ttu-id="f6f0d-144">Das folgende Diagramm zeigt den Entwurf der App.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-144">The following diagram shows the design of the app.</span></span>

![Der Client wird von einem Feld auf der linken Seite dargestellt. Er sendet eine Anforderung und erhält von der Anwendung (Feld auf der rechten Seite) eine Antwort.](first-web-api/_static/architecture.png)

[!INCLUDE[](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-web-project"></a><span data-ttu-id="f6f0d-149">Erstellen eines Webprojekts</span><span class="sxs-lookup"><span data-stu-id="f6f0d-149">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f6f0d-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6f0d-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f6f0d-151">Wählen Sie im Menü **Datei** den Befehl **Neu** > **Projekt** aus.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="f6f0d-152">Wählen Sie die Vorlage **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-152">Select the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="f6f0d-153">Geben Sie dem Projekt den Namen *TodoApi*, und klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-153">Name the project *TodoApi* and click **OK**.</span></span>
* <span data-ttu-id="f6f0d-154">Wählen Sie im Dialogfeld **ASP.NET Core-Webanwendung – TodoAPI** die ASP.NET Core-Version aus.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-154">In the **New ASP.NET Core Web Application - TodoApi** dialog, choose the ASP.NET Core version.</span></span> <span data-ttu-id="f6f0d-155">Wählen Sie die Vorlage **API** aus, und klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-155">Select the **API** template and click **OK**.</span></span> <span data-ttu-id="f6f0d-156">Wählen Sie **nicht** **Enable Docker Support** (Docker-Unterstützung aktivieren) aus.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-156">Do **not** select **Enable Docker Support**.</span></span>

![VS-Dialogfeld „Neues Projekt“](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f6f0d-158">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f6f0d-158">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f6f0d-159">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="f6f0d-159">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="f6f0d-160">Wechseln Sie mit `cd` zu dem Ordner, der den Projektordner enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-160">Change directories (`cd`) to the folder which will contain the project folder.</span></span>
* <span data-ttu-id="f6f0d-161">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="f6f0d-161">Run the following commands:</span></span>

   ```console
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="f6f0d-162">Diese Befehle erstellen ein neues Web-API-Projekt und öffnen eine neue Visual Studio Code-Instanz im neuen Projektordner.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-162">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="f6f0d-163">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem Projekt die erforderlichen Elemente hinzufügen möchten, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-163">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f6f0d-164">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="f6f0d-164">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f6f0d-165">Wählen Sie **Datei** > **Neue Projektmappe** aus.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-165">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="f6f0d-167">Klicken Sie auf **.NET Core-App** > **ASP.NET Core-Web-API** > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-167">Select **.NET Core App** > **ASP.NET Core Web API** > **Next**.</span></span>

  ![Dialogfeld „Neue Projektmappe“ in macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="f6f0d-169">Übernehmen Sie im Dialogfeld **Neue ASP.NET Core-Web-API konfigurieren** die Standardeinstellung **Zielframework** von \**.NET Core 2.2*.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-169">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="f6f0d-170">Geben Sie für **Projektname** *TodoApi* ein, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-170">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogfeld „Konfiguration“](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="f6f0d-172">Testen der API</span><span class="sxs-lookup"><span data-stu-id="f6f0d-172">Test the API</span></span>

<span data-ttu-id="f6f0d-173">Die Projektvorlage erstellt eine `values`-API.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-173">The project template creates a `values` API.</span></span> <span data-ttu-id="f6f0d-174">Rufen Sie in einem Browser die `Get`-Methode zum Testen der App auf.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-174">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f6f0d-175">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6f0d-175">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f6f0d-176">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-176">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="f6f0d-177">Visual Studio startet einen Browser und navigiert zu `https://localhost:<port>/api/values`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-177">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="f6f0d-178">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem IIS Express-Zertifikat vertrauen, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-178">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="f6f0d-179">Wählen Sie im folgenden Dialogfeld **Sicherheitswarnung** die Option **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-179">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f6f0d-180">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f6f0d-180">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f6f0d-181">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-181">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="f6f0d-182">Rufen Sie in einem Browser die folgende URL auf: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span><span class="sxs-lookup"><span data-stu-id="f6f0d-182">In a browser, go to following URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f6f0d-183">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="f6f0d-183">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f6f0d-184">Wählen Sie **Ausführen** > **Mit Debugging starten** aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-184">Select **Run** > **Start With Debugging** to launch the app.</span></span> <span data-ttu-id="f6f0d-185">Visual Studio für Mac startet einen Browser und navigiert zu `https://localhost:<port>`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-185">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="f6f0d-186">Der Fehler „HTTP 404: Nicht gefunden“ wird zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-186">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="f6f0d-187">Fügen Sie der URL `/api/values` an, d.h. ändern Sie die URL in `https://localhost:<port>/api/values`.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-187">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="f6f0d-188">Die folgende JSON-Datei wird zurückgegeben:</span><span class="sxs-lookup"><span data-stu-id="f6f0d-188">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="f6f0d-189">Hinzufügen einer Modellklasse</span><span class="sxs-lookup"><span data-stu-id="f6f0d-189">Add a model class</span></span>

<span data-ttu-id="f6f0d-190">Ein *Modell* ist eine Gruppe von Klassen, die die Daten darstellen, die die App verwaltet.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-190">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="f6f0d-191">Das Modell für diese App ist eine einzelne `TodoItem`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-191">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f6f0d-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6f0d-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f6f0d-193">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-193">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="f6f0d-194">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-194">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="f6f0d-195">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-195">Name the folder *Models*.</span></span>

* <span data-ttu-id="f6f0d-196">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-196">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="f6f0d-197">Nennen Sie die Klasse *TodoItem*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-197">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="f6f0d-198">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="f6f0d-198">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f6f0d-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f6f0d-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f6f0d-200">Fügen Sie einen Ordner namens *Models* hinzu.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-200">Add a folder named *Models*.</span></span>

* <span data-ttu-id="f6f0d-201">Fügen Sie dem Ordner *Modelle* mit dem folgenden Code eine `TodoItem`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="f6f0d-201">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f6f0d-202">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="f6f0d-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f6f0d-203">Klicken Sie mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-203">Right-click the project.</span></span> <span data-ttu-id="f6f0d-204">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-204">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="f6f0d-205">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-205">Name the folder *Models*.</span></span>

  ![Neuer Ordner](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="f6f0d-207">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und klicken Sie auf **Hinzufügen** > **Neue Datei** > **Allgemein** > **Leere Klasse**.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-207">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="f6f0d-208">Nennen Sie die Klasse *TodoItem*, und klicken Sie dann auf **Neu**.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-208">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="f6f0d-209">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="f6f0d-209">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="f6f0d-210">Die `Id`-Eigenschaft fungiert als eindeutiger Schlüssel in einer relationalen Datenbank.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-210">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="f6f0d-211">Modellklassen können überall im Projekt platziert werden, doch gemäß der Konvention wird der Ordner *Modelle* verwendet.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-211">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="f6f0d-212">Hinzufügen eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="f6f0d-212">Add a database context</span></span>

<span data-ttu-id="f6f0d-213">Der *Datenbankkontext* ist die Hauptklasse, die die Entity Framework-Funktionen für ein Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-213">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="f6f0d-214">Diese Klasse wird durch Ableiten von der `Microsoft.EntityFrameworkCore.DbContext`-Klasse erstellt.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-214">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f6f0d-215">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6f0d-215">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f6f0d-216">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-216">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="f6f0d-217">Nennen Sie die Klasse *TodoContext*, und klicken Sie auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-217">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="f6f0d-218">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="f6f0d-218">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="f6f0d-219">Fügen Sie eine `TodoContext`-Klasse zum Ordner *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-219">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="f6f0d-220">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="f6f0d-220">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="f6f0d-221">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="f6f0d-221">Register the database context</span></span>

<span data-ttu-id="f6f0d-222">In ASP.NET Core müssen Dienste wie der Datenbankkontext mit dem Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) registriert werden.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-222">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="f6f0d-223">Der Container stellt den Dienst für Controller bereit.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-223">The container provides the service to controllers.</span></span>

<span data-ttu-id="f6f0d-224">Aktualisieren Sie *Startup.cs* mit dem folgenden hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="f6f0d-224">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="f6f0d-225">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="f6f0d-225">The preceding code:</span></span>

* <span data-ttu-id="f6f0d-226">Entfernt nicht benötigte `using`-Deklarationen</span><span class="sxs-lookup"><span data-stu-id="f6f0d-226">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="f6f0d-227">Fügt dem Abhängigkeitscontainer den Datenbankkontext hinzu</span><span class="sxs-lookup"><span data-stu-id="f6f0d-227">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="f6f0d-228">Gibt an, dass der Datenbankkontext eine In-Memory Database verwendet</span><span class="sxs-lookup"><span data-stu-id="f6f0d-228">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="f6f0d-229">Hinzufügen eines Controllers</span><span class="sxs-lookup"><span data-stu-id="f6f0d-229">Add a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f6f0d-230">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6f0d-230">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f6f0d-231">Klicken Sie mit der rechten Maustaste auf den Ordner *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-231">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="f6f0d-232">Klicken Sie auf **Hinzufügen** > **Neues Element**.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-232">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="f6f0d-233">Wählen Sie im Dialogfeld **Neues Element hinzufügen** die Vorlage **API-Controllerklasse** aus.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-233">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="f6f0d-234">Benennen Sie die Klasse *TodoController*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-234">Name the class *TodoController*, and select **Add**.</span></span>

  ![Dialogfeld „Neues Element hinzufügen“ mit Controller im Suchfeld und ausgewähltem Web-API-Controller](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="f6f0d-236">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="f6f0d-236">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="f6f0d-237">Erstellen Sie im Ordner *Controllers* eine Klasse namens `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-237">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="f6f0d-238">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="f6f0d-238">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="f6f0d-239">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="f6f0d-239">The preceding code:</span></span>

* <span data-ttu-id="f6f0d-240">Er definiert eine API-Controllerklasse ohne Methoden.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-240">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="f6f0d-241">Ergänzt die Klasse mit dem Attribut [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute).</span><span class="sxs-lookup"><span data-stu-id="f6f0d-241">Decorates the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="f6f0d-242">Dieses Attribut gibt an, dass der Controller auf Web-API-Anforderungen reagiert.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-242">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="f6f0d-243">Weitere Informationen zu bestimmten Verhaltensweisen, die das Attribut ermöglicht, finden Sie unter [Anmerkungen mit dem ApiController-Attribut](xref:web-api/index#annotation-with-apicontroller-attribute).</span><span class="sxs-lookup"><span data-stu-id="f6f0d-243">For information about specific behaviors that the attribute enables, see [Annotation with ApiController attribute](xref:web-api/index#annotation-with-apicontroller-attribute).</span></span>
* <span data-ttu-id="f6f0d-244">Verwendet die Abhängigkeitsinjektion, um den Datenbankkontext (`TodoContext`) dem Controller hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-244">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="f6f0d-245">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-245">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="f6f0d-246">Fügt der Datenbank, falls sie leer ist, ein Element mit dem Namen `Item1` hinzu.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-246">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="f6f0d-247">Dieser Code befindet sich im Konstruktor. Er wird bei jeder neuen HTTP-Anforderung ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-247">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="f6f0d-248">Wenn Sie alle Elemente löschen, erstellt der Konstruktor beim nächsten Aufruf einer API-Methode erneut `Item1`.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-248">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="f6f0d-249">So sieht es möglicherweise so aus, als sei die Löschung fehlgeschlagen, obwohl sie erfolgreich war.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-249">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="f6f0d-250">Hinzufügen von GET-Methoden</span><span class="sxs-lookup"><span data-stu-id="f6f0d-250">Add Get methods</span></span>

<span data-ttu-id="f6f0d-251">Fügen Sie der Klasse `TodoController` die folgenden Methoden hinzu, um eine API bereitzustellen, die Aufgaben abruft:</span><span class="sxs-lookup"><span data-stu-id="f6f0d-251">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="f6f0d-252">Diese Methoden implementieren zwei GET-Endpunkte:</span><span class="sxs-lookup"><span data-stu-id="f6f0d-252">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="f6f0d-253">Testen Sie die App, indem Sie die beiden Endpunkte in einem Browser aufrufen.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-253">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="f6f0d-254">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f6f0d-254">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="f6f0d-255">Die folgende HTTP-Antwort wird durch den Aufruf von `GetTodoItems` erzeugt:</span><span class="sxs-lookup"><span data-stu-id="f6f0d-255">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="f6f0d-256">Routing und URL-Pfade</span><span class="sxs-lookup"><span data-stu-id="f6f0d-256">Routing and URL paths</span></span>

<span data-ttu-id="f6f0d-257">Das Attribut [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) gibt eine Methode an, die auf eine HTTP GET-Anforderung antwortet.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-257">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="f6f0d-258">Der URL-Pfad für jede Methode wird wie folgt erstellt:</span><span class="sxs-lookup"><span data-stu-id="f6f0d-258">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="f6f0d-259">Beginnen Sie mit der Vorlagenzeichenfolge im `Route`-Attribut des Controllers:</span><span class="sxs-lookup"><span data-stu-id="f6f0d-259">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="f6f0d-260">Ersetzen Sie `[controller]` durch den Namen des Controllers, bei dem es sich standardmäßig um den Namen der Controller-Klasse ohne das Suffix „Controller“ handelt.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-260">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="f6f0d-261">Bei diesem Beispiel ist der Klassenname des Controllers „**Todo**Controller“, d.h. der Controllername lautet „todo“.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-261">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="f6f0d-262">Beim ASP.NET Core-[Routing](xref:mvc/controllers/routing) wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-262">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="f6f0d-263">Wenn das `[HttpGet]`-Attribut eine Routenvorlage (z.B. `[HttpGet("products")]`) hat, fügen Sie diese an den Pfad an.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-263">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="f6f0d-264">In diesem Beispiel wird keine Vorlage verwendet.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-264">This sample doesn't use a template.</span></span> <span data-ttu-id="f6f0d-265">Weitere Informationen finden Sie unter [Attributrouting mit Http[Verb]-Attributen](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="f6f0d-265">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="f6f0d-266">In der folgenden `GetTodoItem`-Methode ist `"{id}"` eine Platzhaltervariable für den eindeutigen Bezeichners des To-do-Elements.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-266">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="f6f0d-267">Wenn `GetTodoItem` aufgerufen wird, wird der Wert von `"{id}"` in der URL der Methode in ihrem Parameter `id` bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-267">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="f6f0d-268">Rückgabewert</span><span class="sxs-lookup"><span data-stu-id="f6f0d-268">Return values</span></span>

<span data-ttu-id="f6f0d-269">Der Rückgabetyp der Methoden `GetTodoItems` und `GetTodoItem` ist [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="f6f0d-269">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="f6f0d-270">ASP.NET Core serialisiert automatisch das Objekt in [JSON](https://www.json.org/) und schreibt den JSON-Code in den Text der Antwortnachricht.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-270">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="f6f0d-271">Der Antwortcode für diesen Rückgabetyp ist 200, vorausgesetzt, es gibt keine Ausnahmefehler.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-271">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="f6f0d-272">Nicht behandelte Ausnahmen werden in 5xx-Fehler übersetzt.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-272">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="f6f0d-273">`ActionResult`-Rückgabetypen können eine Vielzahl von HTTP-Statuscodes darstellen.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-273">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="f6f0d-274">Beispielsweise kann `GetTodoItem` zwei verschiedene Statuswerte zurückgeben:</span><span class="sxs-lookup"><span data-stu-id="f6f0d-274">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="f6f0d-275">Wenn kein Element mit der angeforderten ID übereinstimmt, gibt die Methode einen 404-Fehlercode [Nicht gefunden](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) zurück.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-275">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="f6f0d-276">Andernfalls gibt die Methode 200 mit einem JSON-Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-276">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="f6f0d-277">Die Rückgabe von `item` löst eine HTTP 200-Antwort aus.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-277">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="f6f0d-278">Testen der GetTodoItems-Methode</span><span class="sxs-lookup"><span data-stu-id="f6f0d-278">Test the GetTodoItems method</span></span>

<span data-ttu-id="f6f0d-279">Dieses Tutorial verwendet Postman zum Testen der Web-API.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-279">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="f6f0d-280">Installieren Sie [Postman](https://www.getpostman.com/apps).</span><span class="sxs-lookup"><span data-stu-id="f6f0d-280">Install [Postman](https://www.getpostman.com/apps)</span></span>
* <span data-ttu-id="f6f0d-281">Starten Sie die Web-App.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-281">Start the web app.</span></span>
* <span data-ttu-id="f6f0d-282">Starten Sie Postman.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-282">Start Postman.</span></span>
* <span data-ttu-id="f6f0d-283">Deaktivieren Sie **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="f6f0d-283">Disable **SSL certificate verification**</span></span>
  
  * <span data-ttu-id="f6f0d-284">Deaktivieren Sie auf der Registerkarte \**General* (Allgemein) unter **File > Settings** (Datei > Einstellungen) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="f6f0d-284">From  **File > Settings** (\**General* tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="f6f0d-285">Aktivieren Sie die Verifizierung des SSL-Zertifikats wieder, nachdem Sie den Controller getestet haben.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-285">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="f6f0d-286">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-286">Create a new request.</span></span>
  * <span data-ttu-id="f6f0d-287">Legen Sie die HTTP-Methode auf **GET** fest.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-287">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="f6f0d-288">Legen Sie die Anforderungs-URL auf `https://localhost:<port>/api/todo` fest.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-288">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="f6f0d-289">Beispielsweise `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-289">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="f6f0d-290">Wählen Sie in Postman **Two pane view** (Ansicht mit zwei Bereichen) aus.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-290">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="f6f0d-291">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-291">Select **Send**.</span></span>

![Postman mit GET-Anforderung](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="f6f0d-293">Hinzufügen einer Methode</span><span class="sxs-lookup"><span data-stu-id="f6f0d-293">Add a Create method</span></span>

<span data-ttu-id="f6f0d-294">Fügen Sie die folgende `PostTodoItem`-Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="f6f0d-294">Add the following `PostTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="f6f0d-295">Der vorherige Code ist eine HTTP POST-Methode, die durch das Attribut [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-295">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="f6f0d-296">Die Methode ruft den Wert der Aufgabe aus dem Text der HTTP-Anforderung ab.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-296">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="f6f0d-297">Die `CreatedAtAction`-Methode:</span><span class="sxs-lookup"><span data-stu-id="f6f0d-297">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="f6f0d-298">Gibt bei einer erfolgreichen Anforderung den Statuscode „HTTP 201“ zurück.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-298">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="f6f0d-299">HTTP 201 ist die Standardantwort für eine HTTP POST-Methode, die eine neue Ressource auf dem Server erstellt.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-299">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="f6f0d-300">Fügt der Antwort einen `Location`-Header hinzu.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-300">Adds a `Location` header to the response.</span></span> <span data-ttu-id="f6f0d-301">Der `Location`-Header gibt den URI des neu erstellten To-Do-Elements zurück.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-301">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="f6f0d-302">Weitere Informationen finden Sie unter [10.2.2 201 Erstellt](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="f6f0d-302">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="f6f0d-303">Verweist auf die `GetTodoItem`-Aktion zum Erstellen des URIs des `Location`-Headers.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-303">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="f6f0d-304">Das `nameof`-Schlüsselwort von C# wird verwendet, um eine Hartcodierung des Aktionsnamens im `CreatedAtAction`-Aufruf zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-304">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="f6f0d-305">Testen der PostTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="f6f0d-305">Test the PostTodoItem method</span></span>

* <span data-ttu-id="f6f0d-306">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-306">Build the project.</span></span>
* <span data-ttu-id="f6f0d-307">Legen Sie in Postman die HTTP-Methode auf `POST` fest.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-307">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="f6f0d-308">Wählen Sie die Registerkarte **Body** (Text) aus.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-308">Select the **Body** tab.</span></span>
* <span data-ttu-id="f6f0d-309">Wählen Sie das Optionsfeld **raw** (Unformatiert) aus.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-309">Select the **raw** radio button.</span></span>
* <span data-ttu-id="f6f0d-310">Legen Sie den Typ auf **JSON (application/json)** fest.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-310">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="f6f0d-311">Geben Sie für die Aufgabe den Anforderungstext im JSON-Format ein:</span><span class="sxs-lookup"><span data-stu-id="f6f0d-311">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="f6f0d-312">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-312">Select **Send**.</span></span>

  ![Postman mit erstellter Anforderung](first-web-api/_static/create.png)

  <span data-ttu-id="f6f0d-314">Wenn Sie die Fehlermeldung „405: Methode nicht zulässig“ erhalten, wurde das Projekt wahrscheinlich nicht kompiliert, nachdem die Methode `PostTodoItem` hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-314">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="f6f0d-315">Testen des Adressheader-URIs</span><span class="sxs-lookup"><span data-stu-id="f6f0d-315">Test the location header URI</span></span>

* <span data-ttu-id="f6f0d-316">Wählen Sie auf der Registerkarte **Header** (Header) den Bereich **Response** (Antwort) aus.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-316">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="f6f0d-317">Kopieren Sie den den Headerwert von **Location** (Speicherort):</span><span class="sxs-lookup"><span data-stu-id="f6f0d-317">Copy the **Location** header value:</span></span>

  ![Registerkarte „Header“ in der Postman-Konsole](first-web-api/_static/pmc2.png)

* <span data-ttu-id="f6f0d-319">Legen Sie die Methode auf „GET“ fest.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-319">Set the method to GET.</span></span>
* <span data-ttu-id="f6f0d-320">Fügen Sie den URI (z. B. `https://localhost:5001/api/Todo/2`) ein.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-320">Paste the URI (for example, `https://localhost:5001/api/Todo/2`)</span></span>
* <span data-ttu-id="f6f0d-321">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-321">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="f6f0d-322">Hinzufügen einer PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="f6f0d-322">Add a PutTodoItem method</span></span>

<span data-ttu-id="f6f0d-323">Fügen Sie die folgende `PutTodoItem`-Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="f6f0d-323">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="f6f0d-324">`PutTodoItem` ähnelt `PostTodoItem`, verwendet allerdings HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-324">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="f6f0d-325">Die Antwort ist [204 (Kein Inhalt)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="f6f0d-325">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="f6f0d-326">Gemäß der HTTP-Spezifikation erfordert eine PUT-Anforderung, dass der Client die gesamte aktualisierte Entität (nicht nur die Änderungen) sendet.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-326">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="f6f0d-327">Verwenden Sie [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute), um Teilupdates zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-327">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="f6f0d-328">Testen der PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="f6f0d-328">Test the PutTodoItem method</span></span>

<span data-ttu-id="f6f0d-329">Aktualisieren Sie die Aufgabe mit der ID = 1, und setzen Sie ihren Namen auf „feed fish“:</span><span class="sxs-lookup"><span data-stu-id="f6f0d-329">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="f6f0d-330">Die folgende Abbildung zeigt das Postman-Update:</span><span class="sxs-lookup"><span data-stu-id="f6f0d-330">The following image shows the Postman update:</span></span>

![Postman-Konsole mit der Antwort „204 (Kein Inhalt)“](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="f6f0d-332">Hinzufügen einer DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="f6f0d-332">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="f6f0d-333">Fügen Sie die folgende `DeleteTodoItem`-Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="f6f0d-333">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="f6f0d-334">Die `DeleteTodoItem`-Antwort lautet [204 (Kein Inhalt)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="f6f0d-334">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="f6f0d-335">Testen der DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="f6f0d-335">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="f6f0d-336">So löschen Sie mit Postman eine Aufgabe</span><span class="sxs-lookup"><span data-stu-id="f6f0d-336">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="f6f0d-337">Legen Sie die Methode auf `DELETE` fest.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-337">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="f6f0d-338">Legen Sie den URI des zu löschenden Objekts fest, z. B. `https://localhost:5001/api/todo/1`.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-338">Set the URI of the object to delete, for example `https://localhost:5001/api/todo/1`</span></span>
* <span data-ttu-id="f6f0d-339">Klicken Sie auf **Send**.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-339">Select **Send**</span></span>

<span data-ttu-id="f6f0d-340">Sie können in der Beispiel-App alle Elemente löschen. Sobald das letzte Element gelöscht wurde, wird allerdings beim nächsten API-Aufruf vom Modellklassenkonstruktor ein neues erstellt.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-340">The sample app allows you to delete all the items, but when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-api-with-jquery"></a><span data-ttu-id="f6f0d-341">Aufrufen der API mit jQuery</span><span class="sxs-lookup"><span data-stu-id="f6f0d-341">Call the API with jQuery</span></span>

<span data-ttu-id="f6f0d-342">In diesem Abschnitt wird eine HTML-Seite hinzugefügt, die mithilfe von jQuery die Web-API aufruft.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-342">In this section, an HTML page is added that uses jQuery to call the web api.</span></span> <span data-ttu-id="f6f0d-343">jQuery initiiert die Anforderung und aktualisiert die Seite mit den Informationen aus der Antwort der API.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-343">jQuery initiates the request and updates the page with the details from the API's response.</span></span>

<span data-ttu-id="f6f0d-344">Konfigurieren Sie die App so, dass [statische Dateien unterstützt](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) und [Standarddateizuordnung aktiviert](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) werden.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-344">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_):</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

::: moniker range=">= aspnetcore-2.2"
<span data-ttu-id="f6f0d-345">Erstellen Sie im Projektverzeichnis den Ordner *wwwwroot*.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-345">Create a *wwwroot* folder in the project directory.</span></span>
::: moniker-end

<span data-ttu-id="f6f0d-346">Fügen Sie dem Verzeichnis *wwwroot* eine HTML-Datei namens *index.html* hinzu.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-346">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="f6f0d-347">Ersetzen Sie den Inhalt durch folgendes Markup:</span><span class="sxs-lookup"><span data-stu-id="f6f0d-347">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="f6f0d-348">Fügen Sie dem Verzeichnis *wwwroot* eine JavaScript-Datei namens *site.js* hinzu.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-348">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="f6f0d-349">Ersetzen Sie den Inhalt durch folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="f6f0d-349">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="f6f0d-350">Möglicherweise ist eine Änderung an den Starteinstellungen des ASP.NET Core-Projekts erforderlich, um die HTML-Seite lokal zu testen:</span><span class="sxs-lookup"><span data-stu-id="f6f0d-350">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="f6f0d-351">Öffnen Sie *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-351">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="f6f0d-352">Entfernen Sie die `launchUrl`-Eigenschaft, um zu erzwingen, dass die App mit *index.html* als Startseite geöffnet wird. Dies ist die Standarddatei des Projekts.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-352">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="f6f0d-353">Es gibt mehrere Möglichkeiten, um jQuery herunterzuladen.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-353">There are several ways to get jQuery.</span></span> <span data-ttu-id="f6f0d-354">Im vorherigen Codeausschnitt wurde die Bibliothek aus einem Content Delivery Network (CDN) geladen.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-354">In the preceding snippet, the library is loaded from a CDN.</span></span>

<span data-ttu-id="f6f0d-355">Dieses Beispiel ruft alle CRUD-Methoden der API auf.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-355">This sample calls all of the CRUD methods of the API.</span></span> <span data-ttu-id="f6f0d-356">Im Folgenden werden die API-Aufrufe erläutert.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-356">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="f6f0d-357">Abrufen einer Liste von To-Do-Elementen</span><span class="sxs-lookup"><span data-stu-id="f6f0d-357">Get a list of to-do items</span></span>

<span data-ttu-id="f6f0d-358">Die jQuery-Funktion [ajax](https://api.jquery.com/jquery.ajax/) sendet eine `GET`-Anforderung an die API, die JSON-Code zurückgibt, der ein Aufgabenarray darstellt.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-358">The jQuery [ajax](https://api.jquery.com/jquery.ajax/) function sends a `GET` request to the API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="f6f0d-359">Die Rückruffunktion `success` wird aufgerufen, wenn die Anforderung erfolgreich ist.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-359">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="f6f0d-360">Im Rückruf wird DOM mit den To-Do-Informationen aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-360">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="f6f0d-361">Hinzufügen eines To-Do-Elements</span><span class="sxs-lookup"><span data-stu-id="f6f0d-361">Add a to-do item</span></span>

<span data-ttu-id="f6f0d-362">Die Funktion [ajax](https://api.jquery.com/jquery.ajax/) sendet eine `POST`-Anforderung mit der Aufgabe im Anforderungstext.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-362">The [ajax](https://api.jquery.com/jquery.ajax/) function sends a `POST` request with the to-do item in the request body.</span></span> <span data-ttu-id="f6f0d-363">Die Optionen `accepts` und `contentType` werden auf `application/json` festgelegt, um den gesendeten und empfangenen Medientyp anzugeben.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-363">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="f6f0d-364">Die Aufgabe wird mit [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) in JSON konvertiert.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-364">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="f6f0d-365">Wenn die API den Statuscode „Erfolgreich“ zurückgibt, wird die Funktion `getData` aufgerufen, um die HTML-Tabelle zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-365">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="f6f0d-366">Aktualisieren eines To-Do-Elements</span><span class="sxs-lookup"><span data-stu-id="f6f0d-366">Update a to-do item</span></span>

<span data-ttu-id="f6f0d-367">Das Aktualisieren einer Aufgabe ist vergleichbar mit dem Hinzufügen einer Aufgabe.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-367">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="f6f0d-368">Die `url` ändert sich, um den eindeutigen Bezeichner des Elements hinzuzufügen. Der `type` lautet `PUT`.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-368">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="f6f0d-369">Löschen eines To-Do-Elements</span><span class="sxs-lookup"><span data-stu-id="f6f0d-369">Delete a to-do item</span></span>

<span data-ttu-id="f6f0d-370">Eine Aufgabe wird folgendermaßen gelöscht: im AJAX-Aufruf wird `type` auf `DELETE` festgelegt, und der eindeutige Bezeichner des Elements wird in der URL angegeben.</span><span class="sxs-lookup"><span data-stu-id="f6f0d-370">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

## <a name="additional-resources"></a><span data-ttu-id="f6f0d-371">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="f6f0d-371">Additional resources</span></span>

<span data-ttu-id="f6f0d-372">[Sie können den Beispielcode für dieses Tutorial anzeigen oder herunterladen.](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)</span><span class="sxs-lookup"><span data-stu-id="f6f0d-372">[View or download sample code for this tutorial](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="f6f0d-373">Informationen [zum Herunterladen](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="f6f0d-373">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="f6f0d-374">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="f6f0d-374">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/index>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>

## <a name="next-steps"></a><span data-ttu-id="f6f0d-375">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="f6f0d-375">Next steps</span></span>

<span data-ttu-id="f6f0d-376">In diesem Tutorial haben Sie gelernt, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="f6f0d-376">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f6f0d-377">Erstellen eines Web-API-Projekts</span><span class="sxs-lookup"><span data-stu-id="f6f0d-377">Create a web api project.</span></span>
> * <span data-ttu-id="f6f0d-378">Hinzufügen einer Modellklasse</span><span class="sxs-lookup"><span data-stu-id="f6f0d-378">Add a model class.</span></span>
> * <span data-ttu-id="f6f0d-379">Erstellen des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="f6f0d-379">Create the database context.</span></span>
> * <span data-ttu-id="f6f0d-380">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="f6f0d-380">Register the database context.</span></span>
> * <span data-ttu-id="f6f0d-381">Hinzufügen eines Controllers</span><span class="sxs-lookup"><span data-stu-id="f6f0d-381">Add a controller.</span></span>
> * <span data-ttu-id="f6f0d-382">Hinzufügen von CRUD-Methoden</span><span class="sxs-lookup"><span data-stu-id="f6f0d-382">Add CRUD methods.</span></span>
> * <span data-ttu-id="f6f0d-383">Konfigurieren von Routing und URL-Pfaden</span><span class="sxs-lookup"><span data-stu-id="f6f0d-383">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="f6f0d-384">Angeben von Rückgabewerten</span><span class="sxs-lookup"><span data-stu-id="f6f0d-384">Specify return values.</span></span>
> * <span data-ttu-id="f6f0d-385">Aufrufen der Web-API mit Postman</span><span class="sxs-lookup"><span data-stu-id="f6f0d-385">Call the web API with Postman.</span></span>
> * <span data-ttu-id="f6f0d-386">Aufrufen der Web-API mit jQuery</span><span class="sxs-lookup"><span data-stu-id="f6f0d-386">Call the web api with jQuery.</span></span>

<span data-ttu-id="f6f0d-387">Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie API-Hilfeseiten generieren:</span><span class="sxs-lookup"><span data-stu-id="f6f0d-387">Advance to the next tutorial to learn how to generate API help pages:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>

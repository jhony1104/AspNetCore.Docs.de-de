---
title: 'Tutorial: Erstellen einer Web-API mit ASP.NET Core'
author: rick-anderson
description: Informationen zum Erstellen einer Web-API mit ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 08/14/2019
uid: tutorials/first-web-api
ms.openlocfilehash: 99985e9fb1134c2ba808434f8d24c4a768773268
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2019
ms.locfileid: "69022593"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="bc281-103">Tutorial: Erstellen einer Web-API mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bc281-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="bc281-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="bc281-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="bc281-105">In diesem Tutorial lernen Sie die Grundlagen der Erstellung einer Web-API mit ASP.NET Core kennen.</span><span class="sxs-lookup"><span data-stu-id="bc281-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bc281-106">In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="bc281-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="bc281-107">Erstellen eines Web-API-Projekts</span><span class="sxs-lookup"><span data-stu-id="bc281-107">Create a web API project.</span></span>
> * <span data-ttu-id="bc281-108">Hinzufügen einer Modellklasse und eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="bc281-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="bc281-109">Erstellen eines Controllergerüsts mit CRUD-Methoden</span><span class="sxs-lookup"><span data-stu-id="bc281-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="bc281-110">Konfigurieren des Routings, der URL-Pfade und der Rückgabewerte</span><span class="sxs-lookup"><span data-stu-id="bc281-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="bc281-111">Aufrufen der Web-API mit Postman</span><span class="sxs-lookup"><span data-stu-id="bc281-111">Call the web API with Postman.</span></span>

<span data-ttu-id="bc281-112">Abschließend steht Ihnen eine Web-API zur Verfügung, die in einer relationalen Datenbank gespeicherte To-do-Elemente verwalten kann.</span><span class="sxs-lookup"><span data-stu-id="bc281-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="bc281-113">Übersicht</span><span class="sxs-lookup"><span data-stu-id="bc281-113">Overview</span></span>

<span data-ttu-id="bc281-114">In diesem Tutorial wird die folgende API erstellt:</span><span class="sxs-lookup"><span data-stu-id="bc281-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="bc281-115">API</span><span class="sxs-lookup"><span data-stu-id="bc281-115">API</span></span> | <span data-ttu-id="bc281-116">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="bc281-116">Description</span></span> | <span data-ttu-id="bc281-117">Anforderungstext</span><span class="sxs-lookup"><span data-stu-id="bc281-117">Request body</span></span> | <span data-ttu-id="bc281-118">Antworttext</span><span class="sxs-lookup"><span data-stu-id="bc281-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="bc281-119">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="bc281-119">GET /api/TodoItems</span></span> | <span data-ttu-id="bc281-120">Alle To-do-Elemente abrufen</span><span class="sxs-lookup"><span data-stu-id="bc281-120">Get all to-do items</span></span> | <span data-ttu-id="bc281-121">Keine</span><span class="sxs-lookup"><span data-stu-id="bc281-121">None</span></span> | <span data-ttu-id="bc281-122">Array von To-do-Elementen</span><span class="sxs-lookup"><span data-stu-id="bc281-122">Array of to-do items</span></span>|
|<span data-ttu-id="bc281-123">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="bc281-123">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="bc281-124">Ein Element nach ID abrufen</span><span class="sxs-lookup"><span data-stu-id="bc281-124">Get an item by ID</span></span> | <span data-ttu-id="bc281-125">Keine</span><span class="sxs-lookup"><span data-stu-id="bc281-125">None</span></span> | <span data-ttu-id="bc281-126">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="bc281-126">To-do item</span></span>|
|<span data-ttu-id="bc281-127">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="bc281-127">POST /api/TodoItems</span></span> | <span data-ttu-id="bc281-128">Neues Element hinzufügen</span><span class="sxs-lookup"><span data-stu-id="bc281-128">Add a new item</span></span> | <span data-ttu-id="bc281-129">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="bc281-129">To-do item</span></span> | <span data-ttu-id="bc281-130">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="bc281-130">To-do item</span></span> |
|<span data-ttu-id="bc281-131">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="bc281-131">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="bc281-132">Vorhandenes Element aktualisieren &nbsp;</span><span class="sxs-lookup"><span data-stu-id="bc281-132">Update an existing item &nbsp;</span></span> | <span data-ttu-id="bc281-133">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="bc281-133">To-do item</span></span> | <span data-ttu-id="bc281-134">Keine</span><span class="sxs-lookup"><span data-stu-id="bc281-134">None</span></span> |
|<span data-ttu-id="bc281-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="bc281-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="bc281-136">Löschen eines Elements &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="bc281-136">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="bc281-137">Keine</span><span class="sxs-lookup"><span data-stu-id="bc281-137">None</span></span> | <span data-ttu-id="bc281-138">Keine</span><span class="sxs-lookup"><span data-stu-id="bc281-138">None</span></span>|

<span data-ttu-id="bc281-139">Das folgende Diagramm zeigt den Entwurf der App.</span><span class="sxs-lookup"><span data-stu-id="bc281-139">The following diagram shows the design of the app.</span></span>

![Der Client ist das Feld ganz links.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="bc281-145">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="bc281-145">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bc281-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bc281-146">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="bc281-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bc281-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="bc281-148">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="bc281-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="bc281-149">Erstellen eines Webprojekts</span><span class="sxs-lookup"><span data-stu-id="bc281-149">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bc281-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bc281-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="bc281-151">Klicken Sie im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="bc281-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="bc281-152">Wählen Sie die Vorlage **ASP.NET Core-Webanwendung** aus, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="bc281-152">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="bc281-153">Geben Sie dem Projekt den Namen *TodoApi*, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="bc281-153">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="bc281-154">Vergewissern Sie sich, dass im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Optionen **.NET Core** und **ASP.NET Core 3.0** ausgewählt sind.</span><span class="sxs-lookup"><span data-stu-id="bc281-154">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span> <span data-ttu-id="bc281-155">Wählen Sie die Vorlage **API** aus, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="bc281-155">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="bc281-156">Wählen Sie **nicht** **Enable Docker Support** (Docker-Unterstützung aktivieren) aus.</span><span class="sxs-lookup"><span data-stu-id="bc281-156">**Don't** select **Enable Docker Support**.</span></span>

![VS-Dialogfeld „Neues Projekt“](first-web-api/_static/vs3.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="bc281-158">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bc281-158">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="bc281-159">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="bc281-159">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="bc281-160">Wechseln Sie vom Verzeichnis (`cd`) zum Ordner, der den Projektordner enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="bc281-160">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="bc281-161">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="bc281-161">Run the following commands:</span></span>

   ```console
   dotnet new webapi -o TodoApi
   cd TodoAPI
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.0.0-*
   dotnet add package Microsoft.EntityFrameworkCore.InMemory --version 3.0.0-*
   code -r ../TodoApi
   ```

* <span data-ttu-id="bc281-162">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem Projekt die erforderlichen Elemente hinzufügen möchten, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="bc281-162">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="bc281-163">Die obenstehenden Befehle haben folgende Konsequenzen:</span><span class="sxs-lookup"><span data-stu-id="bc281-163">The preceding commands:</span></span>

  * <span data-ttu-id="bc281-164">Ein neues Web-API-Projekt wird erstellt und in Visual Studio Code geöffnet.</span><span class="sxs-lookup"><span data-stu-id="bc281-164">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="bc281-165">Die NuGet-Pakete, die im nächsten Abschnitt erforderlich sind, werden hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="bc281-165">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="bc281-166">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="bc281-166">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="bc281-167">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="bc281-167">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="bc281-169">Klicken Sie auf **.NET Core** > **App** > **API** > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="bc281-169">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![Dialogfeld „Neue Projektmappe“ in macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="bc281-171">Wählen Sie im Dialogfeld **Configure your new ASP.NET Core Web API** (Neue ASP.NET Core-Web-API konfigurieren) für das **Zielframework** den Eintrag \* *.NET Core 3.0* aus.</span><span class="sxs-lookup"><span data-stu-id="bc281-171">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.0*.</span></span>

* <span data-ttu-id="bc281-172">Geben Sie für **Projektname** *TodoApi* ein, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="bc281-172">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogfeld „Konfiguration“](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="bc281-174">Öffnen Sie im Projektordner ein Befehlsterminal, und führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="bc281-174">Open a command terminal in the project folder and run the following commands:</span></span>

   ```console
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.0.0-*
   dotnet add package Microsoft.EntityFrameworkCore.InMemory --version 3.0.0-*
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="bc281-175">Testen der API</span><span class="sxs-lookup"><span data-stu-id="bc281-175">Test the API</span></span>

<span data-ttu-id="bc281-176">Die Projektvorlage erstellt eine `WeatherForecast`-API.</span><span class="sxs-lookup"><span data-stu-id="bc281-176">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="bc281-177">Rufen Sie in einem Browser die `Get`-Methode zum Testen der App auf.</span><span class="sxs-lookup"><span data-stu-id="bc281-177">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bc281-178">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bc281-178">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bc281-179">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="bc281-179">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="bc281-180">Visual Studio startet einen Browser und navigiert zu `https://localhost:<port>/WeatherForecast`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="bc281-180">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="bc281-181">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem IIS Express-Zertifikat vertrauen, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="bc281-181">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="bc281-182">Wählen Sie im folgenden Dialogfeld **Sicherheitswarnung** die Option **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="bc281-182">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="bc281-183">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bc281-183">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="bc281-184">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="bc281-184">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="bc281-185">Rufen Sie in einem Browser die folgende URL auf: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span><span class="sxs-lookup"><span data-stu-id="bc281-185">In a browser, go to following URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="bc281-186">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="bc281-186">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="bc281-187">Wählen Sie **Run** > **Start Debugging** (Ausführen > Debugging starten) aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="bc281-187">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="bc281-188">Visual Studio für Mac startet einen Browser und navigiert zu `https://localhost:<port>`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="bc281-188">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="bc281-189">Der Fehler „HTTP 404: Nicht gefunden“ wird zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="bc281-189">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="bc281-190">Fügen Sie der URL `/WeatherForecast` an, d.h. ändern Sie die URL in `https://localhost:<port>/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="bc281-190">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="bc281-191">Der zurückgegebene JSON-Code sieht in etwa wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="bc281-191">JSON similar to the following is returned:</span></span>

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

## <a name="add-a-model-class"></a><span data-ttu-id="bc281-192">Hinzufügen einer Modellklasse</span><span class="sxs-lookup"><span data-stu-id="bc281-192">Add a model class</span></span>

<span data-ttu-id="bc281-193">Ein *Modell* ist eine Gruppe von Klassen, die die Daten darstellen, die die App verwaltet.</span><span class="sxs-lookup"><span data-stu-id="bc281-193">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="bc281-194">Das Modell für diese App ist eine einzelne `TodoItem`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="bc281-194">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bc281-195">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bc281-195">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="bc281-196">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="bc281-196">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="bc281-197">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="bc281-197">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="bc281-198">Geben Sie dem Ordner den Namen *Models*.</span><span class="sxs-lookup"><span data-stu-id="bc281-198">Name the folder *Models*.</span></span>

* <span data-ttu-id="bc281-199">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="bc281-199">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="bc281-200">Nennen Sie die Klasse *TodoItem*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="bc281-200">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="bc281-201">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="bc281-201">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="bc281-202">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bc281-202">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="bc281-203">Fügen Sie einen Ordner namens *Models* hinzu.</span><span class="sxs-lookup"><span data-stu-id="bc281-203">Add a folder named *Models*.</span></span>

* <span data-ttu-id="bc281-204">Fügen Sie dem Ordner *Modelle* mit dem folgenden Code eine `TodoItem`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="bc281-204">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="bc281-205">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="bc281-205">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="bc281-206">Klicken Sie mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="bc281-206">Right-click the project.</span></span> <span data-ttu-id="bc281-207">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="bc281-207">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="bc281-208">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="bc281-208">Name the folder *Models*.</span></span>

  ![Neuer Ordner](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="bc281-210">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und klicken Sie auf **Hinzufügen** > **Neue Datei** > **Allgemein** > **Leere Klasse**.</span><span class="sxs-lookup"><span data-stu-id="bc281-210">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="bc281-211">Nennen Sie die Klasse *TodoItem*, und klicken Sie dann auf **Neu**.</span><span class="sxs-lookup"><span data-stu-id="bc281-211">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="bc281-212">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="bc281-212">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="bc281-213">Die `Id`-Eigenschaft fungiert als eindeutiger Schlüssel in einer relationalen Datenbank.</span><span class="sxs-lookup"><span data-stu-id="bc281-213">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="bc281-214">Modellklassen können überall im Projekt platziert werden, doch gemäß der Konvention wird der Ordner *Modelle* verwendet.</span><span class="sxs-lookup"><span data-stu-id="bc281-214">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="bc281-215">Hinzufügen eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="bc281-215">Add a database context</span></span>

<span data-ttu-id="bc281-216">Der *Datenbankkontext* ist die Hauptklasse, die die Entity Framework-Funktionen für ein Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="bc281-216">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="bc281-217">Diese Klasse wird durch Ableiten von der `Microsoft.EntityFrameworkCore.DbContext`-Klasse erstellt.</span><span class="sxs-lookup"><span data-stu-id="bc281-217">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bc281-218">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bc281-218">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="bc281-219">Hinzufügen von Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="bc281-219">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="bc281-220">Klicken Sie im Menü **Extras** auf **NuGet-Paket-Manager > NuGet-Pakete für Projektmappe verwalten...** .</span><span class="sxs-lookup"><span data-stu-id="bc281-220">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="bc281-221">Aktivieren Sie das Kontrollkästchen **Vorabversion einbeziehen**.</span><span class="sxs-lookup"><span data-stu-id="bc281-221">Select the **Include prerelease** checkbox.</span></span>
* <span data-ttu-id="bc281-222">Klicken Sie auf die Registerkarte **Durchsuchen**, und geben Sie dann **Microsoft.EntityFrameworkCore.SqlServer** in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="bc281-222">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="bc281-223">Wählen Sie im linken Bereich **Microsoft.EntityFrameworkCore.SqlServer V3.0.0-preview** aus.</span><span class="sxs-lookup"><span data-stu-id="bc281-223">Select  **Microsoft.EntityFrameworkCore.SqlServer V3.0.0-preview** in the left pane.</span></span>
* <span data-ttu-id="bc281-224">Aktivieren Sie das Kontrollkästchen **Projekt** im rechten Bereich, und klicken Sie dann auf **Installieren**.</span><span class="sxs-lookup"><span data-stu-id="bc281-224">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="bc281-225">Fügen Sie mithilfe der obigen Schritte das `Microsoft.EntityFrameworkCore.InMemory`-NuGet-Paket hinzu.</span><span class="sxs-lookup"><span data-stu-id="bc281-225">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![NuGet-Paket-Manager](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="bc281-227">Hinzufügen des TodoContext-Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="bc281-227">Add the TodoContext database context</span></span>

* <span data-ttu-id="bc281-228">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="bc281-228">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="bc281-229">Nennen Sie die Klasse *TodoContext*, und klicken Sie auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="bc281-229">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="bc281-230">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="bc281-230">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="bc281-231">Fügen Sie eine `TodoContext`-Klasse zum Ordner *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="bc281-231">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="bc281-232">Geben Sie den folgenden Code ein:</span><span class="sxs-lookup"><span data-stu-id="bc281-232">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="bc281-233">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="bc281-233">Register the database context</span></span>

<span data-ttu-id="bc281-234">In ASP.NET Core müssen Dienste wie der Datenbankkontext mit dem Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) registriert werden.</span><span class="sxs-lookup"><span data-stu-id="bc281-234">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="bc281-235">Der Container stellt den Dienst für Controller bereit.</span><span class="sxs-lookup"><span data-stu-id="bc281-235">The container provides the service to controllers.</span></span>

<span data-ttu-id="bc281-236">Aktualisieren Sie *Startup.cs* mit dem folgenden hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="bc281-236">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="bc281-237">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="bc281-237">The preceding code:</span></span>

* <span data-ttu-id="bc281-238">Entfernt nicht benötigte `using`-Deklarationen</span><span class="sxs-lookup"><span data-stu-id="bc281-238">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="bc281-239">Fügt dem Abhängigkeitscontainer den Datenbankkontext hinzu</span><span class="sxs-lookup"><span data-stu-id="bc281-239">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="bc281-240">Gibt an, dass der Datenbankkontext eine In-Memory Database verwendet</span><span class="sxs-lookup"><span data-stu-id="bc281-240">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="bc281-241">Erstellen eines Controllergerüsts</span><span class="sxs-lookup"><span data-stu-id="bc281-241">Scaffold a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bc281-242">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bc281-242">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="bc281-243">Klicken Sie mit der rechten Maustaste auf den Ordner *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="bc281-243">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="bc281-244">Klicken Sie auf **Hinzufügen** > **New Scaffolded Item** (Neues Gerüstelement).</span><span class="sxs-lookup"><span data-stu-id="bc281-244">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="bc281-245">Klicken Sie auf **API-Controller mit Aktionen unter Verwendung von Entity Framework** und dann auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="bc281-245">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="bc281-246">Führen Sie im Dialogfeld **API-Controller mit Aktionen unter Verwendung von Entity Framework** folgende Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="bc281-246">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="bc281-247">Wählen Sie in der **Modellklasse** das Element **TodoItem (TodoAPI.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="bc281-247">Select **TodoItem (TodoAPI.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="bc281-248">Wählen Sie in der **Datenkontextklasse** das Element **TodoContext (TodoAPI.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="bc281-248">Select **TodoContext (TodoAPI.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="bc281-249">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="bc281-249">Select **Add**</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="bc281-250">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="bc281-250">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="bc281-251">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="bc281-251">Run the following commands:</span></span>

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.0.0-*
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext  -outDir Controllers
```

<span data-ttu-id="bc281-252">Die obenstehenden Befehle haben folgende Konsequenzen:</span><span class="sxs-lookup"><span data-stu-id="bc281-252">The preceding commands:</span></span>

* <span data-ttu-id="bc281-253">Die NuGet-Pakete für den Gerüstbau werden hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="bc281-253">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="bc281-254">Die Gerüstbau-Engine (`dotnet-aspnet-codegenerator`) wird installiert.</span><span class="sxs-lookup"><span data-stu-id="bc281-254">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="bc281-255">Das Gerüst für `TodoItemsController` wird erstellt.</span><span class="sxs-lookup"><span data-stu-id="bc281-255">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="bc281-256">Der generierte Code hat folgende Auswirkungen:</span><span class="sxs-lookup"><span data-stu-id="bc281-256">The generated code:</span></span>

* <span data-ttu-id="bc281-257">Er definiert eine API-Controllerklasse ohne Methoden.</span><span class="sxs-lookup"><span data-stu-id="bc281-257">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="bc281-258">Ergänzt die Klasse mit dem Attribut [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute).</span><span class="sxs-lookup"><span data-stu-id="bc281-258">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="bc281-259">Dieses Attribut gibt an, dass der Controller auf Web-API-Anforderungen reagiert.</span><span class="sxs-lookup"><span data-stu-id="bc281-259">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="bc281-260">Weitere Informationen zu bestimmten Verhaltensweisen, die das Attribut ermöglicht, finden Sie unter <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="bc281-260">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="bc281-261">Verwendet die Abhängigkeitsinjektion, um den Datenbankkontext (`TodoContext`) dem Controller hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="bc281-261">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="bc281-262">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="bc281-262">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="bc281-263">Überblick über die PostTodoItem-Erstellungsmethode</span><span class="sxs-lookup"><span data-stu-id="bc281-263">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="bc281-264">Ersetzen Sie die Rückgabeanweisung in `PostTodoItem` durch eine Anweisung mit dem [nameof](/dotnet/csharp/language-reference/operators/nameof)-Operator:</span><span class="sxs-lookup"><span data-stu-id="bc281-264">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="bc281-265">Der vorherige Code ist eine HTTP POST-Methode, die durch das Attribut [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="bc281-265">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="bc281-266">Die Methode ruft den Wert der Aufgabe aus dem Text der HTTP-Anforderung ab.</span><span class="sxs-lookup"><span data-stu-id="bc281-266">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="bc281-267">Die <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>-Methode:</span><span class="sxs-lookup"><span data-stu-id="bc281-267">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="bc281-268">Sie gibt den HTTP-Statuscode 201 zurück, wenn der Vorgang erfolgreich ist.</span><span class="sxs-lookup"><span data-stu-id="bc281-268">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="bc281-269">HTTP 201 ist die Standardantwort für eine HTTP POST-Methode, die eine neue Ressource auf dem Server erstellt.</span><span class="sxs-lookup"><span data-stu-id="bc281-269">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="bc281-270">Sie fügt der Antwort einen [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location)-Header hinzu.</span><span class="sxs-lookup"><span data-stu-id="bc281-270">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="bc281-271">Der `Location`-Header legt den [URI](https://developer.mozilla.org/docs/Glossary/URI) des neu erstellten To-do-Elements fest.</span><span class="sxs-lookup"><span data-stu-id="bc281-271">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="bc281-272">Weitere Informationen finden Sie unter [10.2.2 201 Erstellt](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="bc281-272">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="bc281-273">Verweist auf die `GetTodoItem`-Aktion zum Erstellen des URIs des `Location`-Headers.</span><span class="sxs-lookup"><span data-stu-id="bc281-273">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="bc281-274">Das `nameof`-Schlüsselwort von C# wird verwendet, um eine Hartcodierung des Aktionsnamens im `CreatedAtAction`-Aufruf zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="bc281-274">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="bc281-275">Installieren von Postman</span><span class="sxs-lookup"><span data-stu-id="bc281-275">Install Postman</span></span>

<span data-ttu-id="bc281-276">Dieses Tutorial verwendet Postman zum Testen der Web-API.</span><span class="sxs-lookup"><span data-stu-id="bc281-276">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="bc281-277">Installieren Sie [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="bc281-277">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="bc281-278">Starten Sie die Web-App.</span><span class="sxs-lookup"><span data-stu-id="bc281-278">Start the web app.</span></span>
* <span data-ttu-id="bc281-279">Starten Sie Postman.</span><span class="sxs-lookup"><span data-stu-id="bc281-279">Start Postman.</span></span>
* <span data-ttu-id="bc281-280">Deaktivieren Sie **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="bc281-280">Disable **SSL certificate verification**</span></span>
* <span data-ttu-id="bc281-281">Deaktivieren Sie auf der Registerkarte \**General* (Allgemein) unter **File > Settings** (Datei > Einstellungen) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="bc281-281">From  **File > Settings** (\**General* tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="bc281-282">Aktivieren Sie die Verifizierung des SSL-Zertifikats wieder, nachdem Sie den Controller getestet haben.</span><span class="sxs-lookup"><span data-stu-id="bc281-282">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="bc281-283">Testen von PostTodoItem mit Postman</span><span class="sxs-lookup"><span data-stu-id="bc281-283">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="bc281-284">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="bc281-284">Create a new request.</span></span>
* <span data-ttu-id="bc281-285">Legen Sie als HTTP-Methode `POST` fest.</span><span class="sxs-lookup"><span data-stu-id="bc281-285">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="bc281-286">Wählen Sie die Registerkarte **Body** (Text) aus.</span><span class="sxs-lookup"><span data-stu-id="bc281-286">Select the **Body** tab.</span></span>
* <span data-ttu-id="bc281-287">Wählen Sie das Optionsfeld **raw** (Unformatiert) aus.</span><span class="sxs-lookup"><span data-stu-id="bc281-287">Select the **raw** radio button.</span></span>
* <span data-ttu-id="bc281-288">Legen Sie den Typ auf **JSON (application/json)** fest.</span><span class="sxs-lookup"><span data-stu-id="bc281-288">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="bc281-289">Geben Sie für die Aufgabe den Anforderungstext im JSON-Format ein:</span><span class="sxs-lookup"><span data-stu-id="bc281-289">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="bc281-290">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="bc281-290">Select **Send**.</span></span>

  ![Postman mit erstellter Anforderung](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="bc281-292">Testen des Adressheader-URIs</span><span class="sxs-lookup"><span data-stu-id="bc281-292">Test the location header URI</span></span>

* <span data-ttu-id="bc281-293">Wählen Sie auf der Registerkarte **Header** (Header) den Bereich **Response** (Antwort) aus.</span><span class="sxs-lookup"><span data-stu-id="bc281-293">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="bc281-294">Kopieren Sie den den Headerwert von **Location** (Speicherort):</span><span class="sxs-lookup"><span data-stu-id="bc281-294">Copy the **Location** header value:</span></span>

  ![Registerkarte „Header“ in der Postman-Konsole](first-web-api/_static/3/create.png)

* <span data-ttu-id="bc281-296">Legen Sie die Methode auf „GET“ fest.</span><span class="sxs-lookup"><span data-stu-id="bc281-296">Set the method to GET.</span></span>
* <span data-ttu-id="bc281-297">Fügen Sie den URI (z. B. `https://localhost:5001/api/TodoItems/1`) ein.</span><span class="sxs-lookup"><span data-stu-id="bc281-297">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`)</span></span>
* <span data-ttu-id="bc281-298">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="bc281-298">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="bc281-299">Überblick über die GET-Methoden</span><span class="sxs-lookup"><span data-stu-id="bc281-299">Examine the GET methods</span></span>

<span data-ttu-id="bc281-300">Diese Methoden implementieren zwei GET-Endpunkte:</span><span class="sxs-lookup"><span data-stu-id="bc281-300">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="bc281-301">Testen Sie die App, indem Sie die beiden Endpunkte in einem Browser oder über Postman aufrufen.</span><span class="sxs-lookup"><span data-stu-id="bc281-301">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="bc281-302">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="bc281-302">For example:</span></span>

* [https://localhost:5001/api/TodoItems](https://localhost:5001/api/TodoItems)
* [https://localhost:5001/api/TodoItems/1](https://localhost:5001/api/TodoItems/1)

<span data-ttu-id="bc281-303">Durch einen Aufruf von `GetTodoItems` wird eine Antwort gesendet, die der folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="bc281-303">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="bc281-304">Testen von GET mit Postman</span><span class="sxs-lookup"><span data-stu-id="bc281-304">Test Get with Postman</span></span>

* <span data-ttu-id="bc281-305">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="bc281-305">Create a new request.</span></span>
* <span data-ttu-id="bc281-306">Legen Sie die HTTP-Methode auf **GET** fest.</span><span class="sxs-lookup"><span data-stu-id="bc281-306">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="bc281-307">Legen Sie die Anforderungs-URL auf `https://localhost:<port>/api/TodoItems` fest.</span><span class="sxs-lookup"><span data-stu-id="bc281-307">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="bc281-308">Beispielsweise `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="bc281-308">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="bc281-309">Wählen Sie in Postman **Two pane view** (Ansicht mit zwei Bereichen) aus.</span><span class="sxs-lookup"><span data-stu-id="bc281-309">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="bc281-310">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="bc281-310">Select **Send**.</span></span>

<span data-ttu-id="bc281-311">Diese App verwendet eine In-Memory-Datenbank.</span><span class="sxs-lookup"><span data-stu-id="bc281-311">This app uses an in-memory database.</span></span> <span data-ttu-id="bc281-312">Wenn die App angehalten und dann wieder gestartet wird, werden durch die vorherige GET-Anforderung keine Daten zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="bc281-312">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="bc281-313">Wenn keine Daten zurückgegeben werden, senden Sie mit [POST](#post) Daten an die App.</span><span class="sxs-lookup"><span data-stu-id="bc281-313">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="bc281-314">Routing und URL-Pfade</span><span class="sxs-lookup"><span data-stu-id="bc281-314">Routing and URL paths</span></span>

<span data-ttu-id="bc281-315">Das Attribut [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) gibt eine Methode an, die auf eine HTTP GET-Anforderung antwortet.</span><span class="sxs-lookup"><span data-stu-id="bc281-315">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="bc281-316">Der URL-Pfad für jede Methode wird wie folgt erstellt:</span><span class="sxs-lookup"><span data-stu-id="bc281-316">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="bc281-317">Beginnen Sie mit der Vorlagenzeichenfolge im `Route`-Attribut des Controllers:</span><span class="sxs-lookup"><span data-stu-id="bc281-317">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="bc281-318">Ersetzen Sie `[controller]` durch den Namen des Controllers, bei dem es sich standardmäßig um den Namen der Controller-Klasse ohne das Suffix „Controller“ handelt.</span><span class="sxs-lookup"><span data-stu-id="bc281-318">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="bc281-319">In diesem Beispiel ist der Klassenname des Controllers „**TodoItems**Controller“. Der Controllername lautet also „TodoItems“.</span><span class="sxs-lookup"><span data-stu-id="bc281-319">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="bc281-320">Beim ASP.NET Core-[Routing](xref:mvc/controllers/routing) wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="bc281-320">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="bc281-321">Wenn das `[HttpGet]`-Attribut eine Routenvorlage (z.B. `[HttpGet("products")]`) hat, fügen Sie diese an den Pfad an.</span><span class="sxs-lookup"><span data-stu-id="bc281-321">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="bc281-322">In diesem Beispiel wird keine Vorlage verwendet.</span><span class="sxs-lookup"><span data-stu-id="bc281-322">This sample doesn't use a template.</span></span> <span data-ttu-id="bc281-323">Weitere Informationen finden Sie unter [Attributrouting mit Http[Verb]-Attributen](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="bc281-323">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="bc281-324">In der folgenden `GetTodoItem`-Methode ist `"{id}"` eine Platzhaltervariable für den eindeutigen Bezeichners des To-do-Elements.</span><span class="sxs-lookup"><span data-stu-id="bc281-324">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="bc281-325">Wenn `GetTodoItem` aufgerufen wird, wird der Wert von `"{id}"` in der URL der Methode in ihrem Parameter `id` bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="bc281-325">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="bc281-326">Rückgabewert</span><span class="sxs-lookup"><span data-stu-id="bc281-326">Return values</span></span>

<span data-ttu-id="bc281-327">Der Rückgabetyp der Methoden `GetTodoItems` und `GetTodoItem` ist [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="bc281-327">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="bc281-328">ASP.NET Core serialisiert automatisch das Objekt in [JSON](https://www.json.org/) und schreibt den JSON-Code in den Text der Antwortnachricht.</span><span class="sxs-lookup"><span data-stu-id="bc281-328">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="bc281-329">Der Antwortcode für diesen Rückgabetyp ist 200, vorausgesetzt, es gibt keine Ausnahmefehler.</span><span class="sxs-lookup"><span data-stu-id="bc281-329">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="bc281-330">Nicht behandelte Ausnahmen werden in 5xx-Fehler übersetzt.</span><span class="sxs-lookup"><span data-stu-id="bc281-330">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="bc281-331">`ActionResult`-Rückgabetypen können eine Vielzahl von HTTP-Statuscodes darstellen.</span><span class="sxs-lookup"><span data-stu-id="bc281-331">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="bc281-332">Beispielsweise kann `GetTodoItem` zwei verschiedene Statuswerte zurückgeben:</span><span class="sxs-lookup"><span data-stu-id="bc281-332">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="bc281-333">Wenn kein Element mit der angeforderten ID übereinstimmt, gibt die Methode einen 404-Fehlercode [Nicht gefunden](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) zurück.</span><span class="sxs-lookup"><span data-stu-id="bc281-333">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="bc281-334">Andernfalls gibt die Methode 200 mit einem JSON-Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="bc281-334">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="bc281-335">Die Rückgabe von `item` löst eine HTTP 200-Antwort aus.</span><span class="sxs-lookup"><span data-stu-id="bc281-335">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="bc281-336">PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="bc281-336">The PutTodoItem method</span></span>

<span data-ttu-id="bc281-337">Untersuchen Sie die `PutTodoItem`-Methode.</span><span class="sxs-lookup"><span data-stu-id="bc281-337">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="bc281-338">`PutTodoItem` ähnelt `PostTodoItem`, verwendet allerdings HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="bc281-338">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="bc281-339">Die Antwort ist [204 (Kein Inhalt)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="bc281-339">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="bc281-340">Gemäß der HTTP-Spezifikation erfordert eine PUT-Anforderung, dass der Client die gesamte aktualisierte Entität (nicht nur die Änderungen) sendet.</span><span class="sxs-lookup"><span data-stu-id="bc281-340">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="bc281-341">Verwenden Sie [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute), um Teilupdates zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="bc281-341">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="bc281-342">Wenn beim Aufrufen von `PutTodoItem` ein Fehler zurückgegeben wird, rufen Sie `GET` auf, um sicherzustellen, dass die Datenbank ein Element enthält.</span><span class="sxs-lookup"><span data-stu-id="bc281-342">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="bc281-343">Testen der PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="bc281-343">Test the PutTodoItem method</span></span>

<span data-ttu-id="bc281-344">In diesem Beispiel wird eine In-Memory Database verwendet, die jedes Mal initialisiert werden muss, wenn die App gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="bc281-344">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="bc281-345">Es muss ein Element in der Datenbank vorhanden sein, bevor Sie einen PUT-Aufruf durchführen.</span><span class="sxs-lookup"><span data-stu-id="bc281-345">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="bc281-346">Rufen Sie vor einem PUT-Aufruf GET auf, um sicherzustellen, dass ein Element in der Datenbank vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="bc281-346">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="bc281-347">Aktualisieren Sie das To-do-Element, das über den ID-Wert 1 verfügt, und legen Sie als Namen „feed fish“ fest:</span><span class="sxs-lookup"><span data-stu-id="bc281-347">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="bc281-348">Die folgende Abbildung zeigt das Postman-Update:</span><span class="sxs-lookup"><span data-stu-id="bc281-348">The following image shows the Postman update:</span></span>

![Postman-Konsole mit der Antwort „204 (Kein Inhalt)“](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="bc281-350">DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="bc281-350">The DeleteTodoItem method</span></span>

<span data-ttu-id="bc281-351">Untersuchen Sie die `DeleteTodoItem`-Methode.</span><span class="sxs-lookup"><span data-stu-id="bc281-351">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

<span data-ttu-id="bc281-352">Die `DeleteTodoItem`-Antwort lautet [204 (Kein Inhalt)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="bc281-352">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="bc281-353">Testen der DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="bc281-353">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="bc281-354">So löschen Sie mit Postman eine Aufgabe</span><span class="sxs-lookup"><span data-stu-id="bc281-354">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="bc281-355">Legen Sie die Methode auf `DELETE` fest.</span><span class="sxs-lookup"><span data-stu-id="bc281-355">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="bc281-356">Legen Sie den URI des zu löschenden Objekts fest, z. B. `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="bc281-356">Set the URI of the object to delete, for example `https://localhost:5001/api/TodoItems/1`</span></span>
* <span data-ttu-id="bc281-357">Klicken Sie auf **Send**.</span><span class="sxs-lookup"><span data-stu-id="bc281-357">Select **Send**</span></span>

## <a name="call-the-api-from-jquery"></a><span data-ttu-id="bc281-358">Aufrufen einer API über jQuery</span><span class="sxs-lookup"><span data-stu-id="bc281-358">Call the API from jQuery</span></span>

<span data-ttu-id="bc281-359">Eine Schritt-für-Schritt-Anleitung finden Sie im [Tutorial: Aufrufen einer ASP.NET Core-Web-API mit jQuery](xref:tutorials/web-api-jquery).</span><span class="sxs-lookup"><span data-stu-id="bc281-359">See [Tutorial: Call an ASP.NET Core web API with jQuery](xref:tutorials/web-api-jquery).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bc281-360">In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="bc281-360">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="bc281-361">Erstellen eines Web-API-Projekts</span><span class="sxs-lookup"><span data-stu-id="bc281-361">Create a web API project.</span></span>
> * <span data-ttu-id="bc281-362">Hinzufügen einer Modellklasse und eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="bc281-362">Add a model class and a database context.</span></span>
> * <span data-ttu-id="bc281-363">Hinzufügen eines Controllers</span><span class="sxs-lookup"><span data-stu-id="bc281-363">Add a controller.</span></span>
> * <span data-ttu-id="bc281-364">Hinzufügen von CRUD-Methoden</span><span class="sxs-lookup"><span data-stu-id="bc281-364">Add CRUD methods.</span></span>
> * <span data-ttu-id="bc281-365">Konfigurieren von Routing und URL-Pfaden</span><span class="sxs-lookup"><span data-stu-id="bc281-365">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="bc281-366">Angeben von Rückgabewerten</span><span class="sxs-lookup"><span data-stu-id="bc281-366">Specify return values.</span></span>
> * <span data-ttu-id="bc281-367">Aufrufen der Web-API mit Postman</span><span class="sxs-lookup"><span data-stu-id="bc281-367">Call the web API with Postman.</span></span>
> * <span data-ttu-id="bc281-368">Aufrufen der Web-API mit jQuery</span><span class="sxs-lookup"><span data-stu-id="bc281-368">Call the web API with jQuery.</span></span>

<span data-ttu-id="bc281-369">Am Ende haben Sie eine Web-API, die in einer relationalen Datenbank gespeicherte „Aufgaben“ verwalten kann.</span><span class="sxs-lookup"><span data-stu-id="bc281-369">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>
## <a name="overview"></a><span data-ttu-id="bc281-370">Übersicht</span><span class="sxs-lookup"><span data-stu-id="bc281-370">Overview</span></span>

<span data-ttu-id="bc281-371">In diesem Tutorial wird die folgende API erstellt:</span><span class="sxs-lookup"><span data-stu-id="bc281-371">This tutorial creates the following API:</span></span>

|<span data-ttu-id="bc281-372">API</span><span class="sxs-lookup"><span data-stu-id="bc281-372">API</span></span> | <span data-ttu-id="bc281-373">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="bc281-373">Description</span></span> | <span data-ttu-id="bc281-374">Anforderungstext</span><span class="sxs-lookup"><span data-stu-id="bc281-374">Request body</span></span> | <span data-ttu-id="bc281-375">Antworttext</span><span class="sxs-lookup"><span data-stu-id="bc281-375">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="bc281-376">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="bc281-376">GET /api/TodoItems</span></span> | <span data-ttu-id="bc281-377">Alle To-do-Elemente abrufen</span><span class="sxs-lookup"><span data-stu-id="bc281-377">Get all to-do items</span></span> | <span data-ttu-id="bc281-378">Keine</span><span class="sxs-lookup"><span data-stu-id="bc281-378">None</span></span> | <span data-ttu-id="bc281-379">Array von To-do-Elementen</span><span class="sxs-lookup"><span data-stu-id="bc281-379">Array of to-do items</span></span>|
|<span data-ttu-id="bc281-380">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="bc281-380">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="bc281-381">Ein Element nach ID abrufen</span><span class="sxs-lookup"><span data-stu-id="bc281-381">Get an item by ID</span></span> | <span data-ttu-id="bc281-382">Keine</span><span class="sxs-lookup"><span data-stu-id="bc281-382">None</span></span> | <span data-ttu-id="bc281-383">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="bc281-383">To-do item</span></span>|
|<span data-ttu-id="bc281-384">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="bc281-384">POST /api/TodoItems</span></span> | <span data-ttu-id="bc281-385">Neues Element hinzufügen</span><span class="sxs-lookup"><span data-stu-id="bc281-385">Add a new item</span></span> | <span data-ttu-id="bc281-386">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="bc281-386">To-do item</span></span> | <span data-ttu-id="bc281-387">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="bc281-387">To-do item</span></span> |
|<span data-ttu-id="bc281-388">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="bc281-388">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="bc281-389">Vorhandenes Element aktualisieren &nbsp;</span><span class="sxs-lookup"><span data-stu-id="bc281-389">Update an existing item &nbsp;</span></span> | <span data-ttu-id="bc281-390">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="bc281-390">To-do item</span></span> | <span data-ttu-id="bc281-391">Keine</span><span class="sxs-lookup"><span data-stu-id="bc281-391">None</span></span> |
|<span data-ttu-id="bc281-392">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="bc281-392">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="bc281-393">Löschen eines Elements &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="bc281-393">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="bc281-394">Keine</span><span class="sxs-lookup"><span data-stu-id="bc281-394">None</span></span> | <span data-ttu-id="bc281-395">Keine</span><span class="sxs-lookup"><span data-stu-id="bc281-395">None</span></span>|

<span data-ttu-id="bc281-396">Das folgende Diagramm zeigt den Entwurf der App.</span><span class="sxs-lookup"><span data-stu-id="bc281-396">The following diagram shows the design of the app.</span></span>

![Der Client ist das Feld ganz links.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="bc281-402">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="bc281-402">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bc281-403">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bc281-403">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="bc281-404">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bc281-404">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="bc281-405">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="bc281-405">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="bc281-406">Erstellen eines Webprojekts</span><span class="sxs-lookup"><span data-stu-id="bc281-406">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bc281-407">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bc281-407">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="bc281-408">Klicken Sie im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="bc281-408">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="bc281-409">Wählen Sie die Vorlage **ASP.NET Core-Webanwendung** aus, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="bc281-409">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="bc281-410">Geben Sie dem Projekt den Namen *TodoApi*, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="bc281-410">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="bc281-411">Vergewissern Sie sich, dass im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Optionen **.NET Core** und **ASP.NET Core 2.2** ausgewählt sind.</span><span class="sxs-lookup"><span data-stu-id="bc281-411">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="bc281-412">Wählen Sie die Vorlage **API** aus, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="bc281-412">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="bc281-413">Wählen Sie **nicht** **Enable Docker Support** (Docker-Unterstützung aktivieren) aus.</span><span class="sxs-lookup"><span data-stu-id="bc281-413">**Don't** select **Enable Docker Support**.</span></span>

![VS-Dialogfeld „Neues Projekt“](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="bc281-415">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bc281-415">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="bc281-416">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="bc281-416">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="bc281-417">Wechseln Sie vom Verzeichnis (`cd`) zum Ordner, der den Projektordner enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="bc281-417">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="bc281-418">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="bc281-418">Run the following commands:</span></span>

   ```console
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="bc281-419">Diese Befehle erstellen ein neues Web-API-Projekt und öffnen eine neue Visual Studio Code-Instanz im neuen Projektordner.</span><span class="sxs-lookup"><span data-stu-id="bc281-419">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="bc281-420">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem Projekt die erforderlichen Elemente hinzufügen möchten, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="bc281-420">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="bc281-421">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="bc281-421">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="bc281-422">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="bc281-422">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="bc281-424">Klicken Sie auf **.NET Core** > **App** > **API** > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="bc281-424">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![Dialogfeld „Neue Projektmappe“ in macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="bc281-426">Übernehmen Sie im Dialogfeld **Neue ASP.NET Core-Web-API konfigurieren** die Standardeinstellung **Zielframework** von \* *.NET Core 2.2*.</span><span class="sxs-lookup"><span data-stu-id="bc281-426">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="bc281-427">Geben Sie für **Projektname** *TodoApi* ein, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="bc281-427">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogfeld „Konfiguration“](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="bc281-429">Testen der API</span><span class="sxs-lookup"><span data-stu-id="bc281-429">Test the API</span></span>

<span data-ttu-id="bc281-430">Die Projektvorlage erstellt eine `values`-API.</span><span class="sxs-lookup"><span data-stu-id="bc281-430">The project template creates a `values` API.</span></span> <span data-ttu-id="bc281-431">Rufen Sie in einem Browser die `Get`-Methode zum Testen der App auf.</span><span class="sxs-lookup"><span data-stu-id="bc281-431">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bc281-432">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bc281-432">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bc281-433">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="bc281-433">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="bc281-434">Visual Studio startet einen Browser und navigiert zu `https://localhost:<port>/api/values`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="bc281-434">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="bc281-435">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem IIS Express-Zertifikat vertrauen, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="bc281-435">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="bc281-436">Wählen Sie im folgenden Dialogfeld **Sicherheitswarnung** die Option **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="bc281-436">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="bc281-437">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bc281-437">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="bc281-438">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="bc281-438">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="bc281-439">Rufen Sie in einem Browser die folgende URL auf: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span><span class="sxs-lookup"><span data-stu-id="bc281-439">In a browser, go to following URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="bc281-440">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="bc281-440">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="bc281-441">Wählen Sie **Run** > **Start Debugging** (Ausführen > Debugging starten) aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="bc281-441">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="bc281-442">Visual Studio für Mac startet einen Browser und navigiert zu `https://localhost:<port>`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="bc281-442">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="bc281-443">Der Fehler „HTTP 404: Nicht gefunden“ wird zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="bc281-443">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="bc281-444">Fügen Sie der URL `/api/values` an, d.h. ändern Sie die URL in `https://localhost:<port>/api/values`.</span><span class="sxs-lookup"><span data-stu-id="bc281-444">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="bc281-445">Die folgende JSON-Datei wird zurückgegeben:</span><span class="sxs-lookup"><span data-stu-id="bc281-445">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="bc281-446">Hinzufügen einer Modellklasse</span><span class="sxs-lookup"><span data-stu-id="bc281-446">Add a model class</span></span>

<span data-ttu-id="bc281-447">Ein *Modell* ist eine Gruppe von Klassen, die die Daten darstellen, die die App verwaltet.</span><span class="sxs-lookup"><span data-stu-id="bc281-447">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="bc281-448">Das Modell für diese App ist eine einzelne `TodoItem`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="bc281-448">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bc281-449">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bc281-449">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="bc281-450">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="bc281-450">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="bc281-451">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="bc281-451">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="bc281-452">Geben Sie dem Ordner den Namen *Models*.</span><span class="sxs-lookup"><span data-stu-id="bc281-452">Name the folder *Models*.</span></span>

* <span data-ttu-id="bc281-453">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="bc281-453">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="bc281-454">Nennen Sie die Klasse *TodoItem*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="bc281-454">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="bc281-455">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="bc281-455">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="bc281-456">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bc281-456">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="bc281-457">Fügen Sie einen Ordner namens *Models* hinzu.</span><span class="sxs-lookup"><span data-stu-id="bc281-457">Add a folder named *Models*.</span></span>

* <span data-ttu-id="bc281-458">Fügen Sie dem Ordner *Modelle* mit dem folgenden Code eine `TodoItem`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="bc281-458">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="bc281-459">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="bc281-459">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="bc281-460">Klicken Sie mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="bc281-460">Right-click the project.</span></span> <span data-ttu-id="bc281-461">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="bc281-461">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="bc281-462">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="bc281-462">Name the folder *Models*.</span></span>

  ![Neuer Ordner](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="bc281-464">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und klicken Sie auf **Hinzufügen** > **Neue Datei** > **Allgemein** > **Leere Klasse**.</span><span class="sxs-lookup"><span data-stu-id="bc281-464">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="bc281-465">Nennen Sie die Klasse *TodoItem*, und klicken Sie dann auf **Neu**.</span><span class="sxs-lookup"><span data-stu-id="bc281-465">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="bc281-466">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="bc281-466">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="bc281-467">Die `Id`-Eigenschaft fungiert als eindeutiger Schlüssel in einer relationalen Datenbank.</span><span class="sxs-lookup"><span data-stu-id="bc281-467">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="bc281-468">Modellklassen können überall im Projekt platziert werden, doch gemäß der Konvention wird der Ordner *Modelle* verwendet.</span><span class="sxs-lookup"><span data-stu-id="bc281-468">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="bc281-469">Hinzufügen eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="bc281-469">Add a database context</span></span>

<span data-ttu-id="bc281-470">Der *Datenbankkontext* ist die Hauptklasse, die die Entity Framework-Funktionen für ein Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="bc281-470">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="bc281-471">Diese Klasse wird durch Ableiten von der `Microsoft.EntityFrameworkCore.DbContext`-Klasse erstellt.</span><span class="sxs-lookup"><span data-stu-id="bc281-471">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bc281-472">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bc281-472">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="bc281-473">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="bc281-473">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="bc281-474">Nennen Sie die Klasse *TodoContext*, und klicken Sie auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="bc281-474">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="bc281-475">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="bc281-475">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="bc281-476">Fügen Sie eine `TodoContext`-Klasse zum Ordner *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="bc281-476">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="bc281-477">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="bc281-477">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="bc281-478">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="bc281-478">Register the database context</span></span>

<span data-ttu-id="bc281-479">In ASP.NET Core müssen Dienste wie der Datenbankkontext mit dem Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) registriert werden.</span><span class="sxs-lookup"><span data-stu-id="bc281-479">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="bc281-480">Der Container stellt den Dienst für Controller bereit.</span><span class="sxs-lookup"><span data-stu-id="bc281-480">The container provides the service to controllers.</span></span>

<span data-ttu-id="bc281-481">Aktualisieren Sie *Startup.cs* mit dem folgenden hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="bc281-481">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="bc281-482">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="bc281-482">The preceding code:</span></span>

* <span data-ttu-id="bc281-483">Entfernt nicht benötigte `using`-Deklarationen</span><span class="sxs-lookup"><span data-stu-id="bc281-483">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="bc281-484">Fügt dem Abhängigkeitscontainer den Datenbankkontext hinzu</span><span class="sxs-lookup"><span data-stu-id="bc281-484">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="bc281-485">Gibt an, dass der Datenbankkontext eine In-Memory Database verwendet</span><span class="sxs-lookup"><span data-stu-id="bc281-485">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="bc281-486">Hinzufügen eines Controllers</span><span class="sxs-lookup"><span data-stu-id="bc281-486">Add a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bc281-487">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bc281-487">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="bc281-488">Klicken Sie mit der rechten Maustaste auf den Ordner *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="bc281-488">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="bc281-489">Klicken Sie auf **Hinzufügen** > **Neues Element**.</span><span class="sxs-lookup"><span data-stu-id="bc281-489">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="bc281-490">Wählen Sie im Dialogfeld **Neues Element hinzufügen** die Vorlage **API-Controllerklasse** aus.</span><span class="sxs-lookup"><span data-stu-id="bc281-490">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="bc281-491">Benennen Sie die Klasse *TodoController*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="bc281-491">Name the class *TodoController*, and select **Add**.</span></span>

  ![Dialogfeld „Neues Element hinzufügen“ mit Controller im Suchfeld und ausgewähltem Web-API-Controller](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="bc281-493">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="bc281-493">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="bc281-494">Erstellen Sie im Ordner *Controllers* eine Klasse namens `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="bc281-494">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="bc281-495">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="bc281-495">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="bc281-496">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="bc281-496">The preceding code:</span></span>

* <span data-ttu-id="bc281-497">Er definiert eine API-Controllerklasse ohne Methoden.</span><span class="sxs-lookup"><span data-stu-id="bc281-497">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="bc281-498">Ergänzt die Klasse mit dem Attribut [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute).</span><span class="sxs-lookup"><span data-stu-id="bc281-498">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="bc281-499">Dieses Attribut gibt an, dass der Controller auf Web-API-Anforderungen reagiert.</span><span class="sxs-lookup"><span data-stu-id="bc281-499">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="bc281-500">Weitere Informationen zu bestimmten Verhaltensweisen, die das Attribut ermöglicht, finden Sie unter <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="bc281-500">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="bc281-501">Verwendet die Abhängigkeitsinjektion, um den Datenbankkontext (`TodoContext`) dem Controller hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="bc281-501">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="bc281-502">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="bc281-502">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="bc281-503">Fügt der Datenbank, falls sie leer ist, ein Element mit dem Namen `Item1` hinzu.</span><span class="sxs-lookup"><span data-stu-id="bc281-503">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="bc281-504">Dieser Code befindet sich im Konstruktor. Er wird bei jeder neuen HTTP-Anforderung ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="bc281-504">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="bc281-505">Wenn Sie alle Elemente löschen, erstellt der Konstruktor beim nächsten Aufruf einer API-Methode erneut `Item1`.</span><span class="sxs-lookup"><span data-stu-id="bc281-505">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="bc281-506">So sieht es möglicherweise so aus, als sei die Löschung fehlgeschlagen, obwohl sie erfolgreich war.</span><span class="sxs-lookup"><span data-stu-id="bc281-506">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="bc281-507">Hinzufügen von GET-Methoden</span><span class="sxs-lookup"><span data-stu-id="bc281-507">Add Get methods</span></span>

<span data-ttu-id="bc281-508">Fügen Sie der Klasse `TodoController` die folgenden Methoden hinzu, um eine API bereitzustellen, die Aufgaben abruft:</span><span class="sxs-lookup"><span data-stu-id="bc281-508">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="bc281-509">Diese Methoden implementieren zwei GET-Endpunkte:</span><span class="sxs-lookup"><span data-stu-id="bc281-509">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="bc281-510">Halten Sie die App an, falls diese noch ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="bc281-510">Stop the app if it's still running.</span></span> <span data-ttu-id="bc281-511">Führen Sie diese dann noch mal aus, damit die letzten Änderungen übernommen werden.</span><span class="sxs-lookup"><span data-stu-id="bc281-511">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="bc281-512">Testen Sie die App, indem Sie die beiden Endpunkte in einem Browser aufrufen.</span><span class="sxs-lookup"><span data-stu-id="bc281-512">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="bc281-513">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="bc281-513">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="bc281-514">Die folgende HTTP-Antwort wird durch den Aufruf von `GetTodoItems` erzeugt:</span><span class="sxs-lookup"><span data-stu-id="bc281-514">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="bc281-515">Routing und URL-Pfade</span><span class="sxs-lookup"><span data-stu-id="bc281-515">Routing and URL paths</span></span>

<span data-ttu-id="bc281-516">Das Attribut [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) gibt eine Methode an, die auf eine HTTP GET-Anforderung antwortet.</span><span class="sxs-lookup"><span data-stu-id="bc281-516">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="bc281-517">Der URL-Pfad für jede Methode wird wie folgt erstellt:</span><span class="sxs-lookup"><span data-stu-id="bc281-517">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="bc281-518">Beginnen Sie mit der Vorlagenzeichenfolge im `Route`-Attribut des Controllers:</span><span class="sxs-lookup"><span data-stu-id="bc281-518">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="bc281-519">Ersetzen Sie `[controller]` durch den Namen des Controllers, bei dem es sich standardmäßig um den Namen der Controller-Klasse ohne das Suffix „Controller“ handelt.</span><span class="sxs-lookup"><span data-stu-id="bc281-519">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="bc281-520">Bei diesem Beispiel ist der Klassenname des Controllers „**Todo**Controller“, d.h. der Controllername lautet „todo“.</span><span class="sxs-lookup"><span data-stu-id="bc281-520">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="bc281-521">Beim ASP.NET Core-[Routing](xref:mvc/controllers/routing) wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="bc281-521">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="bc281-522">Wenn das `[HttpGet]`-Attribut eine Routenvorlage (z.B. `[HttpGet("products")]`) hat, fügen Sie diese an den Pfad an.</span><span class="sxs-lookup"><span data-stu-id="bc281-522">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="bc281-523">In diesem Beispiel wird keine Vorlage verwendet.</span><span class="sxs-lookup"><span data-stu-id="bc281-523">This sample doesn't use a template.</span></span> <span data-ttu-id="bc281-524">Weitere Informationen finden Sie unter [Attributrouting mit Http[Verb]-Attributen](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="bc281-524">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="bc281-525">In der folgenden `GetTodoItem`-Methode ist `"{id}"` eine Platzhaltervariable für den eindeutigen Bezeichners des To-do-Elements.</span><span class="sxs-lookup"><span data-stu-id="bc281-525">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="bc281-526">Wenn `GetTodoItem` aufgerufen wird, wird der Wert von `"{id}"` in der URL der Methode in ihrem Parameter `id` bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="bc281-526">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="bc281-527">Rückgabewert</span><span class="sxs-lookup"><span data-stu-id="bc281-527">Return values</span></span>

<span data-ttu-id="bc281-528">Der Rückgabetyp der Methoden `GetTodoItems` und `GetTodoItem` ist [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="bc281-528">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="bc281-529">ASP.NET Core serialisiert automatisch das Objekt in [JSON](https://www.json.org/) und schreibt den JSON-Code in den Text der Antwortnachricht.</span><span class="sxs-lookup"><span data-stu-id="bc281-529">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="bc281-530">Der Antwortcode für diesen Rückgabetyp ist 200, vorausgesetzt, es gibt keine Ausnahmefehler.</span><span class="sxs-lookup"><span data-stu-id="bc281-530">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="bc281-531">Nicht behandelte Ausnahmen werden in 5xx-Fehler übersetzt.</span><span class="sxs-lookup"><span data-stu-id="bc281-531">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="bc281-532">`ActionResult`-Rückgabetypen können eine Vielzahl von HTTP-Statuscodes darstellen.</span><span class="sxs-lookup"><span data-stu-id="bc281-532">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="bc281-533">Beispielsweise kann `GetTodoItem` zwei verschiedene Statuswerte zurückgeben:</span><span class="sxs-lookup"><span data-stu-id="bc281-533">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="bc281-534">Wenn kein Element mit der angeforderten ID übereinstimmt, gibt die Methode einen 404-Fehlercode [Nicht gefunden](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) zurück.</span><span class="sxs-lookup"><span data-stu-id="bc281-534">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="bc281-535">Andernfalls gibt die Methode 200 mit einem JSON-Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="bc281-535">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="bc281-536">Die Rückgabe von `item` löst eine HTTP 200-Antwort aus.</span><span class="sxs-lookup"><span data-stu-id="bc281-536">Returning `item` results in an HTTP 200 response.</span></span>


## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="bc281-537">Testen der GetTodoItems-Methode</span><span class="sxs-lookup"><span data-stu-id="bc281-537">Test the GetTodoItems method</span></span>

<span data-ttu-id="bc281-538">Dieses Tutorial verwendet Postman zum Testen der Web-API.</span><span class="sxs-lookup"><span data-stu-id="bc281-538">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="bc281-539">Installieren Sie [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="bc281-539">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="bc281-540">Starten Sie die Web-App.</span><span class="sxs-lookup"><span data-stu-id="bc281-540">Start the web app.</span></span>
* <span data-ttu-id="bc281-541">Starten Sie Postman.</span><span class="sxs-lookup"><span data-stu-id="bc281-541">Start Postman.</span></span>
* <span data-ttu-id="bc281-542">Deaktivieren Sie **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="bc281-542">Disable **SSL certificate verification**</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bc281-543">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bc281-543">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="bc281-544">Deaktivieren Sie auf der Registerkarte **General** (Allgemein) unter **File**>**Settings** (Datei > Einstellungen) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="bc281-544">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="bc281-545">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="bc281-545">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="bc281-546">Deaktivieren Sie auf der Registerkarte **General** (Allgemein) unter **Postman** > **Preferences** (Postman > Einstellungen) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="bc281-546">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="bc281-547">Wählen Sie alternativ den Schraubenschlüssel und **Settings** (Einstellungen) aus, und deaktivieren Sie dann die Überprüfung des SSL-Zertifikats.</span><span class="sxs-lookup"><span data-stu-id="bc281-547">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="bc281-548">Aktivieren Sie die Verifizierung des SSL-Zertifikats wieder, nachdem Sie den Controller getestet haben.</span><span class="sxs-lookup"><span data-stu-id="bc281-548">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="bc281-549">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="bc281-549">Create a new request.</span></span>
  * <span data-ttu-id="bc281-550">Legen Sie die HTTP-Methode auf **GET** fest.</span><span class="sxs-lookup"><span data-stu-id="bc281-550">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="bc281-551">Legen Sie die Anforderungs-URL auf `https://localhost:<port>/api/todo` fest.</span><span class="sxs-lookup"><span data-stu-id="bc281-551">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="bc281-552">Beispielsweise `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="bc281-552">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="bc281-553">Wählen Sie in Postman **Two pane view** (Ansicht mit zwei Bereichen) aus.</span><span class="sxs-lookup"><span data-stu-id="bc281-553">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="bc281-554">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="bc281-554">Select **Send**.</span></span>

![Postman mit GET-Anforderung](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="bc281-556">Hinzufügen einer Methode</span><span class="sxs-lookup"><span data-stu-id="bc281-556">Add a Create method</span></span>

<span data-ttu-id="bc281-557">Fügen Sie die folgende `PostTodoItem`-Methode in *Controllers/TodoController.cs* hinzu:</span><span class="sxs-lookup"><span data-stu-id="bc281-557">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="bc281-558">Der vorherige Code ist eine HTTP POST-Methode, die durch das Attribut [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="bc281-558">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="bc281-559">Die Methode ruft den Wert der Aufgabe aus dem Text der HTTP-Anforderung ab.</span><span class="sxs-lookup"><span data-stu-id="bc281-559">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="bc281-560">Die `CreatedAtAction`-Methode:</span><span class="sxs-lookup"><span data-stu-id="bc281-560">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="bc281-561">Gibt bei einer erfolgreichen Anforderung den Statuscode „HTTP 201“ zurück.</span><span class="sxs-lookup"><span data-stu-id="bc281-561">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="bc281-562">HTTP 201 ist die Standardantwort für eine HTTP POST-Methode, die eine neue Ressource auf dem Server erstellt.</span><span class="sxs-lookup"><span data-stu-id="bc281-562">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="bc281-563">Fügt der Antwort einen `Location`-Header hinzu.</span><span class="sxs-lookup"><span data-stu-id="bc281-563">Adds a `Location` header to the response.</span></span> <span data-ttu-id="bc281-564">Der `Location`-Header gibt den URI des neu erstellten To-Do-Elements zurück.</span><span class="sxs-lookup"><span data-stu-id="bc281-564">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="bc281-565">Weitere Informationen finden Sie unter [10.2.2 201 Erstellt](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="bc281-565">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="bc281-566">Verweist auf die `GetTodoItem`-Aktion zum Erstellen des URIs des `Location`-Headers.</span><span class="sxs-lookup"><span data-stu-id="bc281-566">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="bc281-567">Das `nameof`-Schlüsselwort von C# wird verwendet, um eine Hartcodierung des Aktionsnamens im `CreatedAtAction`-Aufruf zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="bc281-567">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="bc281-568">Testen der PostTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="bc281-568">Test the PostTodoItem method</span></span>

* <span data-ttu-id="bc281-569">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="bc281-569">Build the project.</span></span>
* <span data-ttu-id="bc281-570">Legen Sie in Postman die HTTP-Methode auf `POST` fest.</span><span class="sxs-lookup"><span data-stu-id="bc281-570">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="bc281-571">Wählen Sie die Registerkarte **Body** (Text) aus.</span><span class="sxs-lookup"><span data-stu-id="bc281-571">Select the **Body** tab.</span></span>
* <span data-ttu-id="bc281-572">Wählen Sie das Optionsfeld **raw** (Unformatiert) aus.</span><span class="sxs-lookup"><span data-stu-id="bc281-572">Select the **raw** radio button.</span></span>
* <span data-ttu-id="bc281-573">Legen Sie den Typ auf **JSON (application/json)** fest.</span><span class="sxs-lookup"><span data-stu-id="bc281-573">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="bc281-574">Geben Sie für die Aufgabe den Anforderungstext im JSON-Format ein:</span><span class="sxs-lookup"><span data-stu-id="bc281-574">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="bc281-575">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="bc281-575">Select **Send**.</span></span>

  ![Postman mit erstellter Anforderung](first-web-api/_static/create.png)

  <span data-ttu-id="bc281-577">Wenn Sie die Fehlermeldung „405: Methode nicht zulässig“ erhalten, wurde das Projekt wahrscheinlich nicht kompiliert, nachdem die Methode `PostTodoItem` hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="bc281-577">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="bc281-578">Testen des Adressheader-URIs</span><span class="sxs-lookup"><span data-stu-id="bc281-578">Test the location header URI</span></span>

* <span data-ttu-id="bc281-579">Wählen Sie auf der Registerkarte **Header** (Header) den Bereich **Response** (Antwort) aus.</span><span class="sxs-lookup"><span data-stu-id="bc281-579">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="bc281-580">Kopieren Sie den den Headerwert von **Location** (Speicherort):</span><span class="sxs-lookup"><span data-stu-id="bc281-580">Copy the **Location** header value:</span></span>

  ![Registerkarte „Header“ in der Postman-Konsole](first-web-api/_static/pmc2.png)

* <span data-ttu-id="bc281-582">Legen Sie die Methode auf „GET“ fest.</span><span class="sxs-lookup"><span data-stu-id="bc281-582">Set the method to GET.</span></span>
* <span data-ttu-id="bc281-583">Fügen Sie den URI (z. B. `https://localhost:5001/api/Todo/2`) ein.</span><span class="sxs-lookup"><span data-stu-id="bc281-583">Paste the URI (for example, `https://localhost:5001/api/Todo/2`)</span></span>
* <span data-ttu-id="bc281-584">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="bc281-584">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="bc281-585">Hinzufügen einer PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="bc281-585">Add a PutTodoItem method</span></span>

<span data-ttu-id="bc281-586">Fügen Sie die folgende `PutTodoItem`-Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="bc281-586">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="bc281-587">`PutTodoItem` ähnelt `PostTodoItem`, verwendet allerdings HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="bc281-587">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="bc281-588">Die Antwort ist [204 (Kein Inhalt)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="bc281-588">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="bc281-589">Gemäß der HTTP-Spezifikation erfordert eine PUT-Anforderung, dass der Client die gesamte aktualisierte Entität (nicht nur die Änderungen) sendet.</span><span class="sxs-lookup"><span data-stu-id="bc281-589">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="bc281-590">Verwenden Sie [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute), um Teilupdates zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="bc281-590">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="bc281-591">Wenn beim Aufrufen von `PutTodoItem` ein Fehler zurückgegeben wird, rufen Sie `GET` auf, um sicherzustellen, dass die Datenbank ein Element enthält.</span><span class="sxs-lookup"><span data-stu-id="bc281-591">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="bc281-592">Testen der PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="bc281-592">Test the PutTodoItem method</span></span>

<span data-ttu-id="bc281-593">In diesem Beispiel wird eine In-Memory Database verwendet, die jedes Mal initialisiert werden muss, wenn die App gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="bc281-593">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="bc281-594">Es muss ein Element in der Datenbank vorhanden sein, bevor Sie einen PUT-Aufruf durchführen.</span><span class="sxs-lookup"><span data-stu-id="bc281-594">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="bc281-595">Rufen Sie vor einem PUT-Aufruf GET auf, um sicherzustellen, dass ein Element in der Datenbank vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="bc281-595">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="bc281-596">Aktualisieren Sie die Aufgabe mit der ID = 1, und setzen Sie ihren Namen auf „feed fish“:</span><span class="sxs-lookup"><span data-stu-id="bc281-596">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="bc281-597">Die folgende Abbildung zeigt das Postman-Update:</span><span class="sxs-lookup"><span data-stu-id="bc281-597">The following image shows the Postman update:</span></span>

![Postman-Konsole mit der Antwort „204 (Kein Inhalt)“](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="bc281-599">Hinzufügen einer DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="bc281-599">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="bc281-600">Fügen Sie die folgende `DeleteTodoItem`-Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="bc281-600">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="bc281-601">Die `DeleteTodoItem`-Antwort lautet [204 (Kein Inhalt)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="bc281-601">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="bc281-602">Testen der DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="bc281-602">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="bc281-603">So löschen Sie mit Postman eine Aufgabe</span><span class="sxs-lookup"><span data-stu-id="bc281-603">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="bc281-604">Legen Sie die Methode auf `DELETE` fest.</span><span class="sxs-lookup"><span data-stu-id="bc281-604">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="bc281-605">Legen Sie den URI des zu löschenden Objekts fest, z. B. `https://localhost:5001/api/todo/1`.</span><span class="sxs-lookup"><span data-stu-id="bc281-605">Set the URI of the object to delete, for example `https://localhost:5001/api/todo/1`</span></span>
* <span data-ttu-id="bc281-606">Klicken Sie auf **Send**.</span><span class="sxs-lookup"><span data-stu-id="bc281-606">Select **Send**</span></span>

<span data-ttu-id="bc281-607">Sie können in der Beispiel-App alle Elemente löschen.</span><span class="sxs-lookup"><span data-stu-id="bc281-607">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="bc281-608">Sobald das letzte Element gelöscht wurde, wird allerdings beim nächsten API-Aufruf vom Modellklassenkonstruktor ein neues erstellt.</span><span class="sxs-lookup"><span data-stu-id="bc281-608">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-api-with-jquery"></a><span data-ttu-id="bc281-609">Aufrufen der API mit jQuery</span><span class="sxs-lookup"><span data-stu-id="bc281-609">Call the API with jQuery</span></span>

<span data-ttu-id="bc281-610">In diesem Abschnitt wird eine HTML-Seite hinzugefügt, die mithilfe von jQuery die Web-API aufruft.</span><span class="sxs-lookup"><span data-stu-id="bc281-610">In this section, an HTML page is added that uses jQuery to call the web api.</span></span> <span data-ttu-id="bc281-611">jQuery initiiert die Anforderung und aktualisiert die Seite mit den Informationen aus der Antwort der API.</span><span class="sxs-lookup"><span data-stu-id="bc281-611">jQuery initiates the request and updates the page with the details from the API's response.</span></span>

<span data-ttu-id="bc281-612">Konfigurieren Sie die App so, dass sie [statische Dateien bereitstellt](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_), und [aktivieren Sie die Standarddateizuordnung](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_), indem Sie *startup.cs* mit dem unten markierten Code aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="bc281-612">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="bc281-613">Erstellen Sie im Projektverzeichnis den Ordner *wwwwroot*.</span><span class="sxs-lookup"><span data-stu-id="bc281-613">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="bc281-614">Fügen Sie dem Verzeichnis *wwwroot* eine HTML-Datei namens *index.html* hinzu.</span><span class="sxs-lookup"><span data-stu-id="bc281-614">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="bc281-615">Ersetzen Sie den Inhalt durch folgendes Markup:</span><span class="sxs-lookup"><span data-stu-id="bc281-615">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="bc281-616">Fügen Sie dem Verzeichnis *wwwroot* eine JavaScript-Datei namens *site.js* hinzu.</span><span class="sxs-lookup"><span data-stu-id="bc281-616">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="bc281-617">Ersetzen Sie den Inhalt durch folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="bc281-617">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="bc281-618">Möglicherweise ist eine Änderung an den Starteinstellungen des ASP.NET Core-Projekts erforderlich, um die HTML-Seite lokal zu testen:</span><span class="sxs-lookup"><span data-stu-id="bc281-618">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="bc281-619">Öffnen Sie *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="bc281-619">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="bc281-620">Entfernen Sie die `launchUrl`-Eigenschaft, um zu erzwingen, dass die App mit *index.html* als Startseite geöffnet wird. Dies ist die Standarddatei des Projekts.</span><span class="sxs-lookup"><span data-stu-id="bc281-620">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="bc281-621">Es gibt mehrere Möglichkeiten, um jQuery herunterzuladen.</span><span class="sxs-lookup"><span data-stu-id="bc281-621">There are several ways to get jQuery.</span></span> <span data-ttu-id="bc281-622">Im vorherigen Codeausschnitt wurde die Bibliothek aus einem Content Delivery Network (CDN) geladen.</span><span class="sxs-lookup"><span data-stu-id="bc281-622">In the preceding snippet, the library is loaded from a CDN.</span></span>

<span data-ttu-id="bc281-623">Dieses Beispiel ruft alle CRUD-Methoden der API auf.</span><span class="sxs-lookup"><span data-stu-id="bc281-623">This sample calls all of the CRUD methods of the API.</span></span> <span data-ttu-id="bc281-624">Im Folgenden werden die API-Aufrufe erläutert.</span><span class="sxs-lookup"><span data-stu-id="bc281-624">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="bc281-625">Abrufen einer Liste von To-Do-Elementen</span><span class="sxs-lookup"><span data-stu-id="bc281-625">Get a list of to-do items</span></span>

<span data-ttu-id="bc281-626">Die jQuery-Funktion [ajax](https://api.jquery.com/jquery.ajax/) sendet eine `GET`-Anforderung an die API, die JSON-Code zurückgibt, der ein Aufgabenarray darstellt.</span><span class="sxs-lookup"><span data-stu-id="bc281-626">The jQuery [ajax](https://api.jquery.com/jquery.ajax/) function sends a `GET` request to the API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="bc281-627">Die Rückruffunktion `success` wird aufgerufen, wenn die Anforderung erfolgreich ist.</span><span class="sxs-lookup"><span data-stu-id="bc281-627">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="bc281-628">Im Rückruf wird DOM mit den To-Do-Informationen aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="bc281-628">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="bc281-629">Hinzufügen eines To-Do-Elements</span><span class="sxs-lookup"><span data-stu-id="bc281-629">Add a to-do item</span></span>

<span data-ttu-id="bc281-630">Die Funktion [ajax](https://api.jquery.com/jquery.ajax/) sendet eine `POST`-Anforderung mit der Aufgabe im Anforderungstext.</span><span class="sxs-lookup"><span data-stu-id="bc281-630">The [ajax](https://api.jquery.com/jquery.ajax/) function sends a `POST` request with the to-do item in the request body.</span></span> <span data-ttu-id="bc281-631">Die Optionen `accepts` und `contentType` werden auf `application/json` festgelegt, um den gesendeten und empfangenen Medientyp anzugeben.</span><span class="sxs-lookup"><span data-stu-id="bc281-631">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="bc281-632">Die Aufgabe wird mit [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) in JSON konvertiert.</span><span class="sxs-lookup"><span data-stu-id="bc281-632">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="bc281-633">Wenn die API den Statuscode „Erfolgreich“ zurückgibt, wird die Funktion `getData` aufgerufen, um die HTML-Tabelle zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="bc281-633">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="bc281-634">Aktualisieren eines To-Do-Elements</span><span class="sxs-lookup"><span data-stu-id="bc281-634">Update a to-do item</span></span>

<span data-ttu-id="bc281-635">Das Aktualisieren einer Aufgabe ist vergleichbar mit dem Hinzufügen einer Aufgabe.</span><span class="sxs-lookup"><span data-stu-id="bc281-635">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="bc281-636">Die `url` ändert sich, um den eindeutigen Bezeichner des Elements hinzuzufügen. Der `type` lautet `PUT`.</span><span class="sxs-lookup"><span data-stu-id="bc281-636">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="bc281-637">Löschen eines To-Do-Elements</span><span class="sxs-lookup"><span data-stu-id="bc281-637">Delete a to-do item</span></span>

<span data-ttu-id="bc281-638">Eine Aufgabe wird folgendermaßen gelöscht: im AJAX-Aufruf wird `type` auf `DELETE` festgelegt, und der eindeutige Bezeichner des Elements wird in der URL angegeben.</span><span class="sxs-lookup"><span data-stu-id="bc281-638">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="bc281-639">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="bc281-639">Additional resources</span></span>

<span data-ttu-id="bc281-640">[Sie können den Beispielcode für dieses Tutorial anzeigen oder herunterladen.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)</span><span class="sxs-lookup"><span data-stu-id="bc281-640">[View or download sample code for this tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="bc281-641">Informationen [zum Herunterladen](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="bc281-641">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="bc281-642">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="bc281-642">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="bc281-643">Dieses Tutorial auf YouTube</span><span class="sxs-lookup"><span data-stu-id="bc281-643">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)

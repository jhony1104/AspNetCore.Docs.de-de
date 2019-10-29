---
title: 'Tutorial: Erstellen einer Web-API mit ASP.NET Core'
author: rick-anderson
description: Informationen zum Erstellen einer Web-API mit ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 09/29/2019
uid: tutorials/first-web-api
ms.openlocfilehash: 6f2d62600da828261ecfc3a1df688ce914eccf33
ms.sourcegitcommit: a166291c6708f5949c417874108332856b53b6a9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2019
ms.locfileid: "72590015"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="d72d7-103">Tutorial: Erstellen einer Web-API mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d72d7-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="d72d7-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="d72d7-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="d72d7-105">In diesem Tutorial lernen Sie die Grundlagen der Erstellung einer Web-API mit ASP.NET Core kennen.</span><span class="sxs-lookup"><span data-stu-id="d72d7-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d72d7-106">In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="d72d7-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d72d7-107">Erstellen eines Web-API-Projekts</span><span class="sxs-lookup"><span data-stu-id="d72d7-107">Create a web API project.</span></span>
> * <span data-ttu-id="d72d7-108">Hinzufügen einer Modellklasse und eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="d72d7-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="d72d7-109">Erstellen eines Controllergerüsts mit CRUD-Methoden</span><span class="sxs-lookup"><span data-stu-id="d72d7-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="d72d7-110">Konfigurieren des Routings, der URL-Pfade und der Rückgabewerte</span><span class="sxs-lookup"><span data-stu-id="d72d7-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="d72d7-111">Aufrufen der Web-API mit Postman</span><span class="sxs-lookup"><span data-stu-id="d72d7-111">Call the web API with Postman.</span></span>

<span data-ttu-id="d72d7-112">Abschließend steht Ihnen eine Web-API zur Verfügung, die in einer relationalen Datenbank gespeicherte To-do-Elemente verwalten kann.</span><span class="sxs-lookup"><span data-stu-id="d72d7-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="d72d7-113">Übersicht</span><span class="sxs-lookup"><span data-stu-id="d72d7-113">Overview</span></span>

<span data-ttu-id="d72d7-114">In diesem Tutorial wird die folgende API erstellt:</span><span class="sxs-lookup"><span data-stu-id="d72d7-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="d72d7-115">API</span><span class="sxs-lookup"><span data-stu-id="d72d7-115">API</span></span> | <span data-ttu-id="d72d7-116">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="d72d7-116">Description</span></span> | <span data-ttu-id="d72d7-117">Anforderungstext</span><span class="sxs-lookup"><span data-stu-id="d72d7-117">Request body</span></span> | <span data-ttu-id="d72d7-118">Antworttext</span><span class="sxs-lookup"><span data-stu-id="d72d7-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="d72d7-119">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="d72d7-119">GET /api/TodoItems</span></span> | <span data-ttu-id="d72d7-120">Alle To-do-Elemente abrufen</span><span class="sxs-lookup"><span data-stu-id="d72d7-120">Get all to-do items</span></span> | <span data-ttu-id="d72d7-121">Keine</span><span class="sxs-lookup"><span data-stu-id="d72d7-121">None</span></span> | <span data-ttu-id="d72d7-122">Array von To-do-Elementen</span><span class="sxs-lookup"><span data-stu-id="d72d7-122">Array of to-do items</span></span>|
|<span data-ttu-id="d72d7-123">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="d72d7-123">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="d72d7-124">Ein Element nach ID abrufen</span><span class="sxs-lookup"><span data-stu-id="d72d7-124">Get an item by ID</span></span> | <span data-ttu-id="d72d7-125">Keine</span><span class="sxs-lookup"><span data-stu-id="d72d7-125">None</span></span> | <span data-ttu-id="d72d7-126">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="d72d7-126">To-do item</span></span>|
|<span data-ttu-id="d72d7-127">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="d72d7-127">POST /api/TodoItems</span></span> | <span data-ttu-id="d72d7-128">Neues Element hinzufügen</span><span class="sxs-lookup"><span data-stu-id="d72d7-128">Add a new item</span></span> | <span data-ttu-id="d72d7-129">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="d72d7-129">To-do item</span></span> | <span data-ttu-id="d72d7-130">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="d72d7-130">To-do item</span></span> |
|<span data-ttu-id="d72d7-131">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="d72d7-131">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="d72d7-132">Vorhandenes Element aktualisieren &nbsp;</span><span class="sxs-lookup"><span data-stu-id="d72d7-132">Update an existing item &nbsp;</span></span> | <span data-ttu-id="d72d7-133">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="d72d7-133">To-do item</span></span> | <span data-ttu-id="d72d7-134">Keine</span><span class="sxs-lookup"><span data-stu-id="d72d7-134">None</span></span> |
|<span data-ttu-id="d72d7-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="d72d7-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="d72d7-136">Löschen eines Elements &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="d72d7-136">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="d72d7-137">Keine</span><span class="sxs-lookup"><span data-stu-id="d72d7-137">None</span></span> | <span data-ttu-id="d72d7-138">Keine</span><span class="sxs-lookup"><span data-stu-id="d72d7-138">None</span></span>|

<span data-ttu-id="d72d7-139">Das folgende Diagramm zeigt den Entwurf der App.</span><span class="sxs-lookup"><span data-stu-id="d72d7-139">The following diagram shows the design of the app.</span></span>

![Der Client ist das Feld ganz links.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="d72d7-145">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="d72d7-145">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d72d7-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d72d7-146">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="d72d7-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d72d7-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="d72d7-148">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d72d7-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="d72d7-149">Erstellen eines Webprojekts</span><span class="sxs-lookup"><span data-stu-id="d72d7-149">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d72d7-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d72d7-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d72d7-151">Klicken Sie im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="d72d7-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="d72d7-152">Wählen Sie die Vorlage **ASP.NET Core-Webanwendung** aus, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="d72d7-152">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="d72d7-153">Geben Sie dem Projekt den Namen *TodoApi*, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="d72d7-153">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="d72d7-154">Vergewissern Sie sich, dass im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Optionen **.NET Core** und **ASP.NET Core 3.0** ausgewählt sind.</span><span class="sxs-lookup"><span data-stu-id="d72d7-154">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span> <span data-ttu-id="d72d7-155">Wählen Sie die Vorlage **API** aus, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="d72d7-155">Select the **API** template and click **Create**.</span></span>

![VS-Dialogfeld „Neues Projekt“](first-web-api/_static/vs3.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="d72d7-157">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d72d7-157">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="d72d7-158">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="d72d7-158">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="d72d7-159">Wechseln Sie mit dem Befehl `cd`' zu dem Ordner, der den Projektordner enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="d72d7-159">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="d72d7-160">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="d72d7-160">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="d72d7-161">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem Projekt die erforderlichen Elemente hinzufügen möchten, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="d72d7-161">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="d72d7-162">Die obenstehenden Befehle haben folgende Konsequenzen:</span><span class="sxs-lookup"><span data-stu-id="d72d7-162">The preceding commands:</span></span>

  * <span data-ttu-id="d72d7-163">Ein neues Web-API-Projekt wird erstellt und in Visual Studio Code geöffnet.</span><span class="sxs-lookup"><span data-stu-id="d72d7-163">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="d72d7-164">Die NuGet-Pakete, die im nächsten Abschnitt erforderlich sind, werden hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="d72d7-164">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="d72d7-165">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d72d7-165">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d72d7-166">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="d72d7-166">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="d72d7-168">Klicken Sie auf **.NET Core** > **App** > **API** > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="d72d7-168">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![Dialogfeld „Neue Projektmappe“ in macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="d72d7-170">Wählen Sie im Dialogfeld **Configure your new ASP.NET Core Web API** (Neue ASP.NET Core-Web-API konfigurieren) für das **Zielframework** den Eintrag \* *.NET Core 3.0* aus.</span><span class="sxs-lookup"><span data-stu-id="d72d7-170">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.0*.</span></span>

* <span data-ttu-id="d72d7-171">Geben Sie für **Projektname** *TodoApi* ein, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="d72d7-171">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogfeld „Konfiguration“](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="d72d7-173">Öffnen Sie im Projektordner ein Befehlsterminal, und führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="d72d7-173">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="d72d7-174">Testen der API</span><span class="sxs-lookup"><span data-stu-id="d72d7-174">Test the API</span></span>

<span data-ttu-id="d72d7-175">Die Projektvorlage erstellt eine `WeatherForecast`-API.</span><span class="sxs-lookup"><span data-stu-id="d72d7-175">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="d72d7-176">Rufen Sie in einem Browser die `Get`-Methode zum Testen der App auf.</span><span class="sxs-lookup"><span data-stu-id="d72d7-176">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d72d7-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d72d7-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d72d7-178">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="d72d7-178">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="d72d7-179">Visual Studio startet einen Browser und navigiert zu `https://localhost:<port>/WeatherForecast`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="d72d7-179">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="d72d7-180">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem IIS Express-Zertifikat vertrauen, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="d72d7-180">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="d72d7-181">Wählen Sie im folgenden Dialogfeld **Sicherheitswarnung** die Option **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="d72d7-181">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="d72d7-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d72d7-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d72d7-183">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="d72d7-183">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="d72d7-184">Rufen Sie in einem Browser die folgende URL auf: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span><span class="sxs-lookup"><span data-stu-id="d72d7-184">In a browser, go to following URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="d72d7-185">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d72d7-185">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d72d7-186">Wählen Sie **Ausführen** > **Debugging starten** aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="d72d7-186">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="d72d7-187">Visual Studio für Mac startet einen Browser und navigiert zu `https://localhost:<port>`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="d72d7-187">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="d72d7-188">Der Fehler „HTTP 404: Nicht gefunden“ wird zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="d72d7-188">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="d72d7-189">Fügen Sie `/WeatherForecast` an die URL an, d. h., ändern Sie sie in `https://localhost:<port>/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="d72d7-189">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="d72d7-190">Der zurückgegebene JSON-Code sieht in etwa wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="d72d7-190">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="d72d7-191">Hinzufügen einer Modellklasse</span><span class="sxs-lookup"><span data-stu-id="d72d7-191">Add a model class</span></span>

<span data-ttu-id="d72d7-192">Ein *Modell* ist eine Gruppe von Klassen, die die Daten darstellen, die die App verwaltet.</span><span class="sxs-lookup"><span data-stu-id="d72d7-192">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="d72d7-193">Das Modell für diese App ist eine einzelne `TodoItem`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="d72d7-193">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d72d7-194">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d72d7-194">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d72d7-195">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="d72d7-195">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="d72d7-196">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="d72d7-196">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="d72d7-197">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="d72d7-197">Name the folder *Models*.</span></span>

* <span data-ttu-id="d72d7-198">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="d72d7-198">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="d72d7-199">Nennen Sie die Klasse *TodoItem*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="d72d7-199">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="d72d7-200">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="d72d7-200">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="d72d7-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d72d7-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="d72d7-202">Fügen Sie einen Ordner namens *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="d72d7-202">Add a folder named *Models*.</span></span>

* <span data-ttu-id="d72d7-203">Fügen Sie dem Ordner *Modelle* mit dem folgenden Code eine `TodoItem`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="d72d7-203">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="d72d7-204">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d72d7-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d72d7-205">Klicken Sie mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="d72d7-205">Right-click the project.</span></span> <span data-ttu-id="d72d7-206">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="d72d7-206">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="d72d7-207">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="d72d7-207">Name the folder *Models*.</span></span>

  ![Neuer Ordner](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="d72d7-209">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und klicken Sie auf **Hinzufügen** > **Neue Datei** > **Allgemein** > **Leere Klasse**.</span><span class="sxs-lookup"><span data-stu-id="d72d7-209">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="d72d7-210">Nennen Sie die Klasse *TodoItem*, und klicken Sie dann auf **Neu**.</span><span class="sxs-lookup"><span data-stu-id="d72d7-210">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="d72d7-211">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="d72d7-211">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="d72d7-212">Die `Id`-Eigenschaft fungiert als eindeutiger Schlüssel in einer relationalen Datenbank.</span><span class="sxs-lookup"><span data-stu-id="d72d7-212">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="d72d7-213">Modellklassen können überall im Projekt platziert werden, doch gemäß der Konvention wird der Ordner *Modelle* verwendet.</span><span class="sxs-lookup"><span data-stu-id="d72d7-213">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="d72d7-214">Hinzufügen eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="d72d7-214">Add a database context</span></span>

<span data-ttu-id="d72d7-215">Der *Datenbankkontext* ist die Hauptklasse, die die Entity Framework-Funktionen für ein Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="d72d7-215">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="d72d7-216">Diese Klasse wird durch Ableiten von der `Microsoft.EntityFrameworkCore.DbContext`-Klasse erstellt.</span><span class="sxs-lookup"><span data-stu-id="d72d7-216">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d72d7-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d72d7-217">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="d72d7-218">Hinzufügen von Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="d72d7-218">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="d72d7-219">Klicken Sie im Menü **Extras** auf **NuGet-Paket-Manager > NuGet-Pakete für Projektmappe verwalten...** .</span><span class="sxs-lookup"><span data-stu-id="d72d7-219">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="d72d7-220">Klicken Sie auf die Registerkarte **Durchsuchen**, und geben Sie dann **Microsoft.EntityFrameworkCore.SqlServer** in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="d72d7-220">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="d72d7-221">Wählen Sie im linken Bereich **Microsoft.EntityFrameworkCore.SqlServer** aus.</span><span class="sxs-lookup"><span data-stu-id="d72d7-221">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="d72d7-222">Aktivieren Sie das Kontrollkästchen **Projekt** im rechten Bereich, und klicken Sie dann auf **Installieren**.</span><span class="sxs-lookup"><span data-stu-id="d72d7-222">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="d72d7-223">Fügen Sie mithilfe der obigen Schritte das `Microsoft.EntityFrameworkCore.InMemory`-NuGet-Paket hinzu.</span><span class="sxs-lookup"><span data-stu-id="d72d7-223">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![NuGet-Paket-Manager](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="d72d7-225">Hinzufügen des TodoContext-Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="d72d7-225">Add the TodoContext database context</span></span>

* <span data-ttu-id="d72d7-226">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="d72d7-226">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="d72d7-227">Nennen Sie die Klasse *TodoContext*, und klicken Sie auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="d72d7-227">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="d72d7-228">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d72d7-228">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="d72d7-229">Fügen Sie eine `TodoContext`-Klasse zum Ordner *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="d72d7-229">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="d72d7-230">Geben Sie den folgenden Code ein:</span><span class="sxs-lookup"><span data-stu-id="d72d7-230">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="d72d7-231">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="d72d7-231">Register the database context</span></span>

<span data-ttu-id="d72d7-232">In ASP.NET Core müssen Dienste wie der Datenbankkontext mit dem Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) registriert werden.</span><span class="sxs-lookup"><span data-stu-id="d72d7-232">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="d72d7-233">Der Container stellt den Dienst für Controller bereit.</span><span class="sxs-lookup"><span data-stu-id="d72d7-233">The container provides the service to controllers.</span></span>

<span data-ttu-id="d72d7-234">Aktualisieren Sie *Startup.cs* mit dem folgenden hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="d72d7-234">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="d72d7-235">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="d72d7-235">The preceding code:</span></span>

* <span data-ttu-id="d72d7-236">Entfernt nicht benötigte `using`-Deklarationen</span><span class="sxs-lookup"><span data-stu-id="d72d7-236">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="d72d7-237">Fügt dem Abhängigkeitscontainer den Datenbankkontext hinzu</span><span class="sxs-lookup"><span data-stu-id="d72d7-237">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="d72d7-238">Gibt an, dass der Datenbankkontext eine In-Memory Database verwendet</span><span class="sxs-lookup"><span data-stu-id="d72d7-238">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="d72d7-239">Erstellen eines Controllergerüsts</span><span class="sxs-lookup"><span data-stu-id="d72d7-239">Scaffold a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d72d7-240">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d72d7-240">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d72d7-241">Klicken Sie mit der rechten Maustaste auf den Ordner *Controller*.</span><span class="sxs-lookup"><span data-stu-id="d72d7-241">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="d72d7-242">Klicken Sie auf **Hinzufügen** > **Neues Gerüstelement**.</span><span class="sxs-lookup"><span data-stu-id="d72d7-242">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="d72d7-243">Klicken Sie auf **API-Controller mit Aktionen unter Verwendung von Entity Framework** und dann auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="d72d7-243">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="d72d7-244">Führen Sie im Dialogfeld **API-Controller mit Aktionen unter Verwendung von Entity Framework** folgende Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="d72d7-244">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="d72d7-245">Wählen Sie in der **Modellklasse** das Element **TodoItem (TodoApi.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="d72d7-245">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="d72d7-246">Wählen Sie in der **Datenkontextklasse** das Element **TodoContext (TodoApi.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="d72d7-246">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="d72d7-247">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="d72d7-247">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="d72d7-248">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d72d7-248">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="d72d7-249">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="d72d7-249">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="d72d7-250">Die obenstehenden Befehle haben folgende Konsequenzen:</span><span class="sxs-lookup"><span data-stu-id="d72d7-250">The preceding commands:</span></span>

* <span data-ttu-id="d72d7-251">Die NuGet-Pakete für den Gerüstbau werden hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="d72d7-251">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="d72d7-252">Die Gerüstbau-Engine (`dotnet-aspnet-codegenerator`) wird installiert.</span><span class="sxs-lookup"><span data-stu-id="d72d7-252">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="d72d7-253">Das Gerüst für `TodoItemsController` wird erstellt.</span><span class="sxs-lookup"><span data-stu-id="d72d7-253">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="d72d7-254">Der generierte Code hat folgende Auswirkungen:</span><span class="sxs-lookup"><span data-stu-id="d72d7-254">The generated code:</span></span>

* <span data-ttu-id="d72d7-255">Definiert eine API-Controllerklasse ohne Methoden.</span><span class="sxs-lookup"><span data-stu-id="d72d7-255">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="d72d7-256">Ergänzt die Klasse mit dem Attribut [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute).</span><span class="sxs-lookup"><span data-stu-id="d72d7-256">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="d72d7-257">Dieses Attribut gibt an, dass der Controller auf Web-API-Anforderungen reagiert.</span><span class="sxs-lookup"><span data-stu-id="d72d7-257">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="d72d7-258">Weitere Informationen zu bestimmten Verhaltensweisen, die das Attribut ermöglicht, finden Sie unter <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="d72d7-258">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="d72d7-259">Verwendet die Abhängigkeitsinjektion, um den Datenbankkontext (`TodoContext`) dem Controller hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="d72d7-259">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="d72d7-260">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="d72d7-260">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="d72d7-261">Überblick über die PostTodoItem-Erstellungsmethode</span><span class="sxs-lookup"><span data-stu-id="d72d7-261">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="d72d7-262">Ersetzen Sie die Rückgabeanweisung in `PostTodoItem` durch eine Anweisung mit dem [nameof](/dotnet/csharp/language-reference/operators/nameof)-Operator:</span><span class="sxs-lookup"><span data-stu-id="d72d7-262">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="d72d7-263">Der vorherige Code ist eine HTTP POST-Methode, die durch das Attribut [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="d72d7-263">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="d72d7-264">Die Methode ruft den Wert der Aufgabe aus dem Text der HTTP-Anforderung ab.</span><span class="sxs-lookup"><span data-stu-id="d72d7-264">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="d72d7-265">Die <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>-Methode:</span><span class="sxs-lookup"><span data-stu-id="d72d7-265">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="d72d7-266">Gibt den HTTP-Statuscode 201 zurück, wenn der Vorgang erfolgreich ist.</span><span class="sxs-lookup"><span data-stu-id="d72d7-266">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="d72d7-267">HTTP 201 ist die Standardantwort für eine HTTP POST-Methode, die eine neue Ressource auf dem Server erstellt.</span><span class="sxs-lookup"><span data-stu-id="d72d7-267">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="d72d7-268">Fügt der Antwort einen [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location)-Header hinzu.</span><span class="sxs-lookup"><span data-stu-id="d72d7-268">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="d72d7-269">Der `Location`-Header legt den [URI](https://developer.mozilla.org/docs/Glossary/URI) des neu erstellten To-do-Elements fest.</span><span class="sxs-lookup"><span data-stu-id="d72d7-269">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="d72d7-270">Weitere Informationen finden Sie unter [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="d72d7-270">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="d72d7-271">Verweist auf die `GetTodoItem`-Aktion zum Erstellen des URIs des `Location`-Headers.</span><span class="sxs-lookup"><span data-stu-id="d72d7-271">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="d72d7-272">Das `nameof`-Schlüsselwort von C# wird verwendet, um eine Hartcodierung des Aktionsnamens im `CreatedAtAction`-Aufruf zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="d72d7-272">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="d72d7-273">Installieren von Postman</span><span class="sxs-lookup"><span data-stu-id="d72d7-273">Install Postman</span></span>

<span data-ttu-id="d72d7-274">Dieses Tutorial verwendet Postman zum Testen der Web-API.</span><span class="sxs-lookup"><span data-stu-id="d72d7-274">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="d72d7-275">Installieren Sie [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="d72d7-275">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="d72d7-276">Starten Sie die Web-App.</span><span class="sxs-lookup"><span data-stu-id="d72d7-276">Start the web app.</span></span>
* <span data-ttu-id="d72d7-277">Starten Sie Postman.</span><span class="sxs-lookup"><span data-stu-id="d72d7-277">Start Postman.</span></span>
* <span data-ttu-id="d72d7-278">Deaktivieren Sie **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="d72d7-278">Disable **SSL certificate verification**</span></span>
* <span data-ttu-id="d72d7-279">Deaktivieren Sie auf der Registerkarte **General** (Allgemein) unter **File**>**Settings** (Datei > Einstellungen) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="d72d7-279">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="d72d7-280">Aktivieren Sie die Verifizierung des SSL-Zertifikats wieder, nachdem Sie den Controller getestet haben.</span><span class="sxs-lookup"><span data-stu-id="d72d7-280">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="d72d7-281">Testen von PostTodoItem mit Postman</span><span class="sxs-lookup"><span data-stu-id="d72d7-281">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="d72d7-282">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="d72d7-282">Create a new request.</span></span>
* <span data-ttu-id="d72d7-283">Legen Sie als HTTP-Methode `POST` fest.</span><span class="sxs-lookup"><span data-stu-id="d72d7-283">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="d72d7-284">Wählen Sie die Registerkarte **Body** (Text) aus.</span><span class="sxs-lookup"><span data-stu-id="d72d7-284">Select the **Body** tab.</span></span>
* <span data-ttu-id="d72d7-285">Wählen Sie das Optionsfeld **raw** (Unformatiert) aus.</span><span class="sxs-lookup"><span data-stu-id="d72d7-285">Select the **raw** radio button.</span></span>
* <span data-ttu-id="d72d7-286">Legen Sie den Typ auf **JSON (application/json)** fest.</span><span class="sxs-lookup"><span data-stu-id="d72d7-286">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="d72d7-287">Geben Sie für die Aufgabe den Anforderungstext im JSON-Format ein:</span><span class="sxs-lookup"><span data-stu-id="d72d7-287">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="d72d7-288">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="d72d7-288">Select **Send**.</span></span>

  ![Postman mit erstellter Anforderung](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="d72d7-290">Testen des Adressheader-URIs</span><span class="sxs-lookup"><span data-stu-id="d72d7-290">Test the location header URI</span></span>

* <span data-ttu-id="d72d7-291">Wählen Sie auf der Registerkarte **Header** (Header) den Bereich **Response** (Antwort) aus.</span><span class="sxs-lookup"><span data-stu-id="d72d7-291">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="d72d7-292">Kopieren Sie den den Headerwert von **Location** (Speicherort):</span><span class="sxs-lookup"><span data-stu-id="d72d7-292">Copy the **Location** header value:</span></span>

  ![Registerkarte „Header“ in der Postman-Konsole](first-web-api/_static/3/create.png)

* <span data-ttu-id="d72d7-294">Legen Sie die Methode auf „GET“ fest.</span><span class="sxs-lookup"><span data-stu-id="d72d7-294">Set the method to GET.</span></span>
* <span data-ttu-id="d72d7-295">Fügen Sie den URI (z. B. `https://localhost:5001/api/TodoItems/1`) ein.</span><span class="sxs-lookup"><span data-stu-id="d72d7-295">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="d72d7-296">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="d72d7-296">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="d72d7-297">Überblick über die GET-Methoden</span><span class="sxs-lookup"><span data-stu-id="d72d7-297">Examine the GET methods</span></span>

<span data-ttu-id="d72d7-298">Diese Methoden implementieren zwei GET-Endpunkte:</span><span class="sxs-lookup"><span data-stu-id="d72d7-298">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="d72d7-299">Testen Sie die App, indem Sie die beiden Endpunkte in einem Browser oder über Postman aufrufen.</span><span class="sxs-lookup"><span data-stu-id="d72d7-299">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="d72d7-300">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d72d7-300">For example:</span></span>

* [https://localhost:5001/api/TodoItems](https://localhost:5001/api/TodoItems)
* [https://localhost:5001/api/TodoItems/1](https://localhost:5001/api/TodoItems/1)

<span data-ttu-id="d72d7-301">Durch einen Aufruf von `GetTodoItems` wird eine Antwort gesendet, die der folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="d72d7-301">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="d72d7-302">Testen von GET mit Postman</span><span class="sxs-lookup"><span data-stu-id="d72d7-302">Test Get with Postman</span></span>

* <span data-ttu-id="d72d7-303">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="d72d7-303">Create a new request.</span></span>
* <span data-ttu-id="d72d7-304">Legen Sie die HTTP-Methode auf **GET** fest.</span><span class="sxs-lookup"><span data-stu-id="d72d7-304">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="d72d7-305">Legen Sie die Anforderungs-URL auf `https://localhost:<port>/api/TodoItems` fest.</span><span class="sxs-lookup"><span data-stu-id="d72d7-305">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="d72d7-306">Beispielsweise `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="d72d7-306">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="d72d7-307">Wählen Sie in Postman **Two pane view** (Ansicht mit zwei Bereichen) aus.</span><span class="sxs-lookup"><span data-stu-id="d72d7-307">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="d72d7-308">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="d72d7-308">Select **Send**.</span></span>

<span data-ttu-id="d72d7-309">Diese App verwendet eine In-Memory-Datenbank.</span><span class="sxs-lookup"><span data-stu-id="d72d7-309">This app uses an in-memory database.</span></span> <span data-ttu-id="d72d7-310">Wenn die App angehalten und dann wieder gestartet wird, werden durch die vorherige GET-Anforderung keine Daten zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="d72d7-310">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="d72d7-311">Wenn keine Daten zurückgegeben werden, senden Sie mit [POST](#post) Daten an die App.</span><span class="sxs-lookup"><span data-stu-id="d72d7-311">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="d72d7-312">Routing und URL-Pfade</span><span class="sxs-lookup"><span data-stu-id="d72d7-312">Routing and URL paths</span></span>

<span data-ttu-id="d72d7-313">Das Attribut [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) gibt eine Methode an, die auf eine HTTP GET-Anforderung antwortet.</span><span class="sxs-lookup"><span data-stu-id="d72d7-313">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="d72d7-314">Der URL-Pfad für jede Methode wird wie folgt erstellt:</span><span class="sxs-lookup"><span data-stu-id="d72d7-314">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="d72d7-315">Beginnen Sie mit der Vorlagenzeichenfolge im `Route`-Attribut des Controllers:</span><span class="sxs-lookup"><span data-stu-id="d72d7-315">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="d72d7-316">Ersetzen Sie `[controller]` durch den Namen des Controllers, bei dem es sich standardmäßig um den Namen der Controller-Klasse ohne das Suffix „Controller“ handelt.</span><span class="sxs-lookup"><span data-stu-id="d72d7-316">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="d72d7-317">In diesem Beispiel ist der Klassenname des Controllers „**TodoItems**Controller“. Der Controllername lautet also „TodoItems“.</span><span class="sxs-lookup"><span data-stu-id="d72d7-317">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="d72d7-318">Beim ASP.NET Core-[Routing](xref:mvc/controllers/routing) wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="d72d7-318">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="d72d7-319">Wenn das `[HttpGet]`-Attribut eine Routenvorlage (z. B. `[HttpGet("products")]`) hat, fügen Sie diese an den Pfad an.</span><span class="sxs-lookup"><span data-stu-id="d72d7-319">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="d72d7-320">In diesem Beispiel wird keine Vorlage verwendet.</span><span class="sxs-lookup"><span data-stu-id="d72d7-320">This sample doesn't use a template.</span></span> <span data-ttu-id="d72d7-321">Weitere Informationen finden Sie unter [Attributrouting mit Http[Verb]-Attributen](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="d72d7-321">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="d72d7-322">In der folgenden `GetTodoItem`-Methode ist `"{id}"` eine Platzhaltervariable für den eindeutigen Bezeichner des To-do-Elements.</span><span class="sxs-lookup"><span data-stu-id="d72d7-322">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="d72d7-323">Wenn `GetTodoItem` aufgerufen wird, wird der Wert von `"{id}"` in der URL der Methode in ihrem Parameter `id` bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="d72d7-323">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="d72d7-324">Rückgabewerte</span><span class="sxs-lookup"><span data-stu-id="d72d7-324">Return values</span></span>

<span data-ttu-id="d72d7-325">Der Rückgabetyp der Methoden `GetTodoItems` und `GetTodoItem` ist [ActionResult\<T>-Typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="d72d7-325">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="d72d7-326">ASP.NET Core serialisiert automatisch das Objekt in [JSON](https://www.json.org/) und schreibt den JSON-Code in den Text der Antwortnachricht.</span><span class="sxs-lookup"><span data-stu-id="d72d7-326">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="d72d7-327">Der Antwortcode für diesen Rückgabetyp ist 200, vorausgesetzt, es gibt keine Ausnahmefehler.</span><span class="sxs-lookup"><span data-stu-id="d72d7-327">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="d72d7-328">Nicht behandelte Ausnahmen werden in 5xx-Fehler übersetzt.</span><span class="sxs-lookup"><span data-stu-id="d72d7-328">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="d72d7-329">`ActionResult`-Rückgabetypen können eine Vielzahl von HTTP-Statuscodes darstellen.</span><span class="sxs-lookup"><span data-stu-id="d72d7-329">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="d72d7-330">Beispielsweise kann `GetTodoItem` zwei verschiedene Statuswerte zurückgeben:</span><span class="sxs-lookup"><span data-stu-id="d72d7-330">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="d72d7-331">Wenn kein Element mit der angeforderten ID übereinstimmt, gibt die Methode einen 404-Fehlercode [Nicht gefunden](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) zurück.</span><span class="sxs-lookup"><span data-stu-id="d72d7-331">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="d72d7-332">Andernfalls gibt die Methode 200 mit einem JSON-Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="d72d7-332">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="d72d7-333">Die Rückgabe von `item` löst eine HTTP 200-Antwort aus.</span><span class="sxs-lookup"><span data-stu-id="d72d7-333">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="d72d7-334">PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="d72d7-334">The PutTodoItem method</span></span>

<span data-ttu-id="d72d7-335">Untersuchen Sie die `PutTodoItem`-Methode.</span><span class="sxs-lookup"><span data-stu-id="d72d7-335">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="d72d7-336">`PutTodoItem` ähnelt `PostTodoItem`, verwendet allerdings HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="d72d7-336">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="d72d7-337">Die Antwort ist [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="d72d7-337">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="d72d7-338">Gemäß der HTTP-Spezifikation erfordert eine PUT-Anforderung, dass der Client die gesamte aktualisierte Entität (nicht nur die Änderungen) sendet.</span><span class="sxs-lookup"><span data-stu-id="d72d7-338">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="d72d7-339">Verwenden Sie [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute), um Teilupdates zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="d72d7-339">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="d72d7-340">Wenn beim Aufrufen von `PutTodoItem` ein Fehler zurückgegeben wird, rufen Sie `GET` auf, um sicherzustellen, dass die Datenbank ein Element enthält.</span><span class="sxs-lookup"><span data-stu-id="d72d7-340">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="d72d7-341">Testen der PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="d72d7-341">Test the PutTodoItem method</span></span>

<span data-ttu-id="d72d7-342">In diesem Beispiel wird eine In-Memory-Datenbank verwendet, die jedes Mal initialisiert werden muss, wenn die App gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="d72d7-342">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="d72d7-343">Es muss ein Element in der Datenbank vorhanden sein, bevor Sie einen PUT-Aufruf durchführen.</span><span class="sxs-lookup"><span data-stu-id="d72d7-343">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="d72d7-344">Rufen Sie vor einem PUT-Aufruf GET auf, um sicherzustellen, dass ein Element in der Datenbank vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="d72d7-344">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="d72d7-345">Aktualisieren Sie das To-do-Element, das über den ID-Wert 1 verfügt, und legen Sie als Namen „feed fish“ fest:</span><span class="sxs-lookup"><span data-stu-id="d72d7-345">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="d72d7-346">Die folgende Abbildung zeigt das Postman-Update:</span><span class="sxs-lookup"><span data-stu-id="d72d7-346">The following image shows the Postman update:</span></span>

![Postman-Konsole mit der Antwort „204 (Kein Inhalt)“](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="d72d7-348">DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="d72d7-348">The DeleteTodoItem method</span></span>

<span data-ttu-id="d72d7-349">Untersuchen Sie die `DeleteTodoItem`-Methode.</span><span class="sxs-lookup"><span data-stu-id="d72d7-349">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

<span data-ttu-id="d72d7-350">Die `DeleteTodoItem`-Antwort lautet [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="d72d7-350">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="d72d7-351">Testen der DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="d72d7-351">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="d72d7-352">So löschen Sie mit Postman eine Aufgabe</span><span class="sxs-lookup"><span data-stu-id="d72d7-352">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="d72d7-353">Legen Sie die Methode auf `DELETE` fest.</span><span class="sxs-lookup"><span data-stu-id="d72d7-353">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="d72d7-354">Legen Sie den URI des zu löschenden Objekts fest, z. B. `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="d72d7-354">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="d72d7-355">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="d72d7-355">Select **Send**.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="d72d7-356">Aufrufen der Web-API mit JavaScript</span><span class="sxs-lookup"><span data-stu-id="d72d7-356">Call the web API with JavaScript</span></span>

<span data-ttu-id="d72d7-357">Eine Schritt-für-Schritt-Anleitung finden Sie im [Tutorial: Aufrufen einer ASP.NET Core-Web-API mit JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="d72d7-357">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d72d7-358">In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="d72d7-358">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d72d7-359">Erstellen eines Web-API-Projekts</span><span class="sxs-lookup"><span data-stu-id="d72d7-359">Create a web API project.</span></span>
> * <span data-ttu-id="d72d7-360">Hinzufügen einer Modellklasse und eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="d72d7-360">Add a model class and a database context.</span></span>
> * <span data-ttu-id="d72d7-361">Hinzufügen eines Controllers</span><span class="sxs-lookup"><span data-stu-id="d72d7-361">Add a controller.</span></span>
> * <span data-ttu-id="d72d7-362">Hinzufügen von CRUD-Methoden</span><span class="sxs-lookup"><span data-stu-id="d72d7-362">Add CRUD methods.</span></span>
> * <span data-ttu-id="d72d7-363">Konfigurieren von Routing und URL-Pfaden</span><span class="sxs-lookup"><span data-stu-id="d72d7-363">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="d72d7-364">Angeben von Rückgabewerten</span><span class="sxs-lookup"><span data-stu-id="d72d7-364">Specify return values.</span></span>
> * <span data-ttu-id="d72d7-365">Aufrufen der Web-API mit Postman</span><span class="sxs-lookup"><span data-stu-id="d72d7-365">Call the web API with Postman.</span></span>
> * <span data-ttu-id="d72d7-366">Aufrufen der Web-API mit JavaScript</span><span class="sxs-lookup"><span data-stu-id="d72d7-366">Call the web API with JavaScript.</span></span>

<span data-ttu-id="d72d7-367">Am Ende haben Sie eine Web-API, die in einer relationalen Datenbank gespeicherte „Aufgaben“ verwalten kann.</span><span class="sxs-lookup"><span data-stu-id="d72d7-367">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="d72d7-368">Übersicht</span><span class="sxs-lookup"><span data-stu-id="d72d7-368">Overview</span></span>

<span data-ttu-id="d72d7-369">In diesem Tutorial wird die folgende API erstellt:</span><span class="sxs-lookup"><span data-stu-id="d72d7-369">This tutorial creates the following API:</span></span>

|<span data-ttu-id="d72d7-370">API</span><span class="sxs-lookup"><span data-stu-id="d72d7-370">API</span></span> | <span data-ttu-id="d72d7-371">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="d72d7-371">Description</span></span> | <span data-ttu-id="d72d7-372">Anforderungstext</span><span class="sxs-lookup"><span data-stu-id="d72d7-372">Request body</span></span> | <span data-ttu-id="d72d7-373">Antworttext</span><span class="sxs-lookup"><span data-stu-id="d72d7-373">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="d72d7-374">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="d72d7-374">GET /api/TodoItems</span></span> | <span data-ttu-id="d72d7-375">Alle To-do-Elemente abrufen</span><span class="sxs-lookup"><span data-stu-id="d72d7-375">Get all to-do items</span></span> | <span data-ttu-id="d72d7-376">Keine</span><span class="sxs-lookup"><span data-stu-id="d72d7-376">None</span></span> | <span data-ttu-id="d72d7-377">Array von To-do-Elementen</span><span class="sxs-lookup"><span data-stu-id="d72d7-377">Array of to-do items</span></span>|
|<span data-ttu-id="d72d7-378">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="d72d7-378">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="d72d7-379">Ein Element nach ID abrufen</span><span class="sxs-lookup"><span data-stu-id="d72d7-379">Get an item by ID</span></span> | <span data-ttu-id="d72d7-380">Keine</span><span class="sxs-lookup"><span data-stu-id="d72d7-380">None</span></span> | <span data-ttu-id="d72d7-381">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="d72d7-381">To-do item</span></span>|
|<span data-ttu-id="d72d7-382">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="d72d7-382">POST /api/TodoItems</span></span> | <span data-ttu-id="d72d7-383">Neues Element hinzufügen</span><span class="sxs-lookup"><span data-stu-id="d72d7-383">Add a new item</span></span> | <span data-ttu-id="d72d7-384">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="d72d7-384">To-do item</span></span> | <span data-ttu-id="d72d7-385">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="d72d7-385">To-do item</span></span> |
|<span data-ttu-id="d72d7-386">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="d72d7-386">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="d72d7-387">Vorhandenes Element aktualisieren &nbsp;</span><span class="sxs-lookup"><span data-stu-id="d72d7-387">Update an existing item &nbsp;</span></span> | <span data-ttu-id="d72d7-388">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="d72d7-388">To-do item</span></span> | <span data-ttu-id="d72d7-389">Keine</span><span class="sxs-lookup"><span data-stu-id="d72d7-389">None</span></span> |
|<span data-ttu-id="d72d7-390">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="d72d7-390">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="d72d7-391">Löschen eines Elements &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="d72d7-391">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="d72d7-392">Keine</span><span class="sxs-lookup"><span data-stu-id="d72d7-392">None</span></span> | <span data-ttu-id="d72d7-393">Keine</span><span class="sxs-lookup"><span data-stu-id="d72d7-393">None</span></span>|

<span data-ttu-id="d72d7-394">Das folgende Diagramm zeigt den Entwurf der App.</span><span class="sxs-lookup"><span data-stu-id="d72d7-394">The following diagram shows the design of the app.</span></span>

![Der Client ist das Feld ganz links.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="d72d7-400">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="d72d7-400">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d72d7-401">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d72d7-401">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="d72d7-402">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d72d7-402">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="d72d7-403">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d72d7-403">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="d72d7-404">Erstellen eines Webprojekts</span><span class="sxs-lookup"><span data-stu-id="d72d7-404">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d72d7-405">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d72d7-405">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d72d7-406">Klicken Sie im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="d72d7-406">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="d72d7-407">Wählen Sie die Vorlage **ASP.NET Core-Webanwendung** aus, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="d72d7-407">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="d72d7-408">Geben Sie dem Projekt den Namen *TodoApi*, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="d72d7-408">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="d72d7-409">Vergewissern Sie sich, dass im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Optionen **.NET Core** und **ASP.NET Core 2.2** ausgewählt sind.</span><span class="sxs-lookup"><span data-stu-id="d72d7-409">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="d72d7-410">Wählen Sie die Vorlage **API** aus, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="d72d7-410">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="d72d7-411">Wählen Sie **nicht** **Enable Docker Support** (Docker-Unterstützung aktivieren) aus.</span><span class="sxs-lookup"><span data-stu-id="d72d7-411">**Don't** select **Enable Docker Support**.</span></span>

![VS-Dialogfeld „Neues Projekt“](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="d72d7-413">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d72d7-413">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="d72d7-414">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="d72d7-414">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="d72d7-415">Wechseln Sie mit dem Befehl `cd`' zu dem Ordner, der den Projektordner enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="d72d7-415">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="d72d7-416">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="d72d7-416">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="d72d7-417">Diese Befehle erstellen ein neues Web-API-Projekt und öffnen eine neue Visual Studio Code-Instanz im neuen Projektordner.</span><span class="sxs-lookup"><span data-stu-id="d72d7-417">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="d72d7-418">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem Projekt die erforderlichen Elemente hinzufügen möchten, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="d72d7-418">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="d72d7-419">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d72d7-419">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d72d7-420">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="d72d7-420">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="d72d7-422">Klicken Sie auf **.NET Core** > **App** > **API** > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="d72d7-422">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![Dialogfeld „Neue Projektmappe“ in macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="d72d7-424">Übernehmen Sie im Dialogfeld **Neue ASP.NET Core-Web-API konfigurieren** die Standardeinstellung **Zielframework** von \* *.NET Core 2.2*.</span><span class="sxs-lookup"><span data-stu-id="d72d7-424">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="d72d7-425">Geben Sie für **Projektname** *TodoApi* ein, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="d72d7-425">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogfeld „Konfiguration“](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="d72d7-427">Testen der API</span><span class="sxs-lookup"><span data-stu-id="d72d7-427">Test the API</span></span>

<span data-ttu-id="d72d7-428">Die Projektvorlage erstellt eine `values`-API.</span><span class="sxs-lookup"><span data-stu-id="d72d7-428">The project template creates a `values` API.</span></span> <span data-ttu-id="d72d7-429">Rufen Sie in einem Browser die `Get`-Methode zum Testen der App auf.</span><span class="sxs-lookup"><span data-stu-id="d72d7-429">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d72d7-430">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d72d7-430">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d72d7-431">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="d72d7-431">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="d72d7-432">Visual Studio startet einen Browser und navigiert zu `https://localhost:<port>/api/values`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="d72d7-432">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="d72d7-433">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem IIS Express-Zertifikat vertrauen, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="d72d7-433">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="d72d7-434">Wählen Sie im folgenden Dialogfeld **Sicherheitswarnung** die Option **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="d72d7-434">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="d72d7-435">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d72d7-435">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d72d7-436">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="d72d7-436">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="d72d7-437">Rufen Sie in einem Browser die folgende URL auf: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span><span class="sxs-lookup"><span data-stu-id="d72d7-437">In a browser, go to following URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="d72d7-438">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d72d7-438">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d72d7-439">Wählen Sie **Ausführen** > **Debugging starten** aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="d72d7-439">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="d72d7-440">Visual Studio für Mac startet einen Browser und navigiert zu `https://localhost:<port>`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="d72d7-440">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="d72d7-441">Der Fehler „HTTP 404: Nicht gefunden“ wird zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="d72d7-441">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="d72d7-442">Fügen Sie der URL `/api/values` an, d.h. ändern Sie die URL in `https://localhost:<port>/api/values`.</span><span class="sxs-lookup"><span data-stu-id="d72d7-442">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="d72d7-443">Die folgende JSON-Datei wird zurückgegeben:</span><span class="sxs-lookup"><span data-stu-id="d72d7-443">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="d72d7-444">Hinzufügen einer Modellklasse</span><span class="sxs-lookup"><span data-stu-id="d72d7-444">Add a model class</span></span>

<span data-ttu-id="d72d7-445">Ein *Modell* ist eine Gruppe von Klassen, die die Daten darstellen, die die App verwaltet.</span><span class="sxs-lookup"><span data-stu-id="d72d7-445">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="d72d7-446">Das Modell für diese App ist eine einzelne `TodoItem`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="d72d7-446">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d72d7-447">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d72d7-447">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d72d7-448">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="d72d7-448">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="d72d7-449">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="d72d7-449">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="d72d7-450">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="d72d7-450">Name the folder *Models*.</span></span>

* <span data-ttu-id="d72d7-451">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="d72d7-451">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="d72d7-452">Nennen Sie die Klasse *TodoItem*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="d72d7-452">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="d72d7-453">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="d72d7-453">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="d72d7-454">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d72d7-454">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="d72d7-455">Fügen Sie einen Ordner namens *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="d72d7-455">Add a folder named *Models*.</span></span>

* <span data-ttu-id="d72d7-456">Fügen Sie dem Ordner *Modelle* mit dem folgenden Code eine `TodoItem`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="d72d7-456">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="d72d7-457">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d72d7-457">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d72d7-458">Klicken Sie mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="d72d7-458">Right-click the project.</span></span> <span data-ttu-id="d72d7-459">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="d72d7-459">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="d72d7-460">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="d72d7-460">Name the folder *Models*.</span></span>

  ![Neuer Ordner](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="d72d7-462">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und klicken Sie auf **Hinzufügen** > **Neue Datei** > **Allgemein** > **Leere Klasse**.</span><span class="sxs-lookup"><span data-stu-id="d72d7-462">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="d72d7-463">Nennen Sie die Klasse *TodoItem*, und klicken Sie dann auf **Neu**.</span><span class="sxs-lookup"><span data-stu-id="d72d7-463">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="d72d7-464">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="d72d7-464">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="d72d7-465">Die `Id`-Eigenschaft fungiert als eindeutiger Schlüssel in einer relationalen Datenbank.</span><span class="sxs-lookup"><span data-stu-id="d72d7-465">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="d72d7-466">Modellklassen können überall im Projekt platziert werden, doch gemäß der Konvention wird der Ordner *Modelle* verwendet.</span><span class="sxs-lookup"><span data-stu-id="d72d7-466">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="d72d7-467">Hinzufügen eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="d72d7-467">Add a database context</span></span>

<span data-ttu-id="d72d7-468">Der *Datenbankkontext* ist die Hauptklasse, die die Entity Framework-Funktionen für ein Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="d72d7-468">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="d72d7-469">Diese Klasse wird durch Ableiten von der `Microsoft.EntityFrameworkCore.DbContext`-Klasse erstellt.</span><span class="sxs-lookup"><span data-stu-id="d72d7-469">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d72d7-470">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d72d7-470">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d72d7-471">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="d72d7-471">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="d72d7-472">Nennen Sie die Klasse *TodoContext*, und klicken Sie auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="d72d7-472">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="d72d7-473">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d72d7-473">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="d72d7-474">Fügen Sie eine `TodoContext`-Klasse zum Ordner *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="d72d7-474">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="d72d7-475">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="d72d7-475">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="d72d7-476">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="d72d7-476">Register the database context</span></span>

<span data-ttu-id="d72d7-477">In ASP.NET Core müssen Dienste wie der Datenbankkontext mit dem Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) registriert werden.</span><span class="sxs-lookup"><span data-stu-id="d72d7-477">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="d72d7-478">Der Container stellt den Dienst für Controller bereit.</span><span class="sxs-lookup"><span data-stu-id="d72d7-478">The container provides the service to controllers.</span></span>

<span data-ttu-id="d72d7-479">Aktualisieren Sie *Startup.cs* mit dem folgenden hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="d72d7-479">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="d72d7-480">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="d72d7-480">The preceding code:</span></span>

* <span data-ttu-id="d72d7-481">Entfernt nicht benötigte `using`-Deklarationen</span><span class="sxs-lookup"><span data-stu-id="d72d7-481">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="d72d7-482">Fügt dem Abhängigkeitscontainer den Datenbankkontext hinzu</span><span class="sxs-lookup"><span data-stu-id="d72d7-482">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="d72d7-483">Gibt an, dass der Datenbankkontext eine In-Memory Database verwendet</span><span class="sxs-lookup"><span data-stu-id="d72d7-483">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="d72d7-484">Hinzufügen eines Controllers</span><span class="sxs-lookup"><span data-stu-id="d72d7-484">Add a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d72d7-485">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d72d7-485">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d72d7-486">Klicken Sie mit der rechten Maustaste auf den Ordner *Controller*.</span><span class="sxs-lookup"><span data-stu-id="d72d7-486">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="d72d7-487">Klicken Sie auf **Hinzufügen** > **Neues Element**.</span><span class="sxs-lookup"><span data-stu-id="d72d7-487">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="d72d7-488">Wählen Sie im Dialogfeld **Neues Element hinzufügen** die Vorlage **API-Controllerklasse** aus.</span><span class="sxs-lookup"><span data-stu-id="d72d7-488">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="d72d7-489">Benennen Sie die Klasse *TodoController*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="d72d7-489">Name the class *TodoController*, and select **Add**.</span></span>

  ![Dialogfeld „Neues Element hinzufügen“ mit Controller im Suchfeld und ausgewähltem Web-API-Controller](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="d72d7-491">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d72d7-491">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="d72d7-492">Erstellen Sie im Ordner *Controllers* eine Klasse namens `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="d72d7-492">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="d72d7-493">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="d72d7-493">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="d72d7-494">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="d72d7-494">The preceding code:</span></span>

* <span data-ttu-id="d72d7-495">Definiert eine API-Controllerklasse ohne Methoden.</span><span class="sxs-lookup"><span data-stu-id="d72d7-495">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="d72d7-496">Ergänzt die Klasse mit dem Attribut [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute).</span><span class="sxs-lookup"><span data-stu-id="d72d7-496">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="d72d7-497">Dieses Attribut gibt an, dass der Controller auf Web-API-Anforderungen reagiert.</span><span class="sxs-lookup"><span data-stu-id="d72d7-497">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="d72d7-498">Weitere Informationen zu bestimmten Verhaltensweisen, die das Attribut ermöglicht, finden Sie unter <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="d72d7-498">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="d72d7-499">Verwendet die Abhängigkeitsinjektion, um den Datenbankkontext (`TodoContext`) dem Controller hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="d72d7-499">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="d72d7-500">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="d72d7-500">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="d72d7-501">Fügt der Datenbank, falls sie leer ist, ein Element mit dem Namen `Item1` hinzu.</span><span class="sxs-lookup"><span data-stu-id="d72d7-501">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="d72d7-502">Dieser Code befindet sich im Konstruktor. Er wird bei jeder neuen HTTP-Anforderung ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="d72d7-502">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="d72d7-503">Wenn Sie alle Elemente löschen, erstellt der Konstruktor beim nächsten Aufruf einer API-Methode erneut `Item1`.</span><span class="sxs-lookup"><span data-stu-id="d72d7-503">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="d72d7-504">So sieht es möglicherweise so aus, als sei die Löschung fehlgeschlagen, obwohl sie erfolgreich war.</span><span class="sxs-lookup"><span data-stu-id="d72d7-504">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="d72d7-505">Hinzufügen von GET-Methoden</span><span class="sxs-lookup"><span data-stu-id="d72d7-505">Add Get methods</span></span>

<span data-ttu-id="d72d7-506">Fügen Sie der Klasse `TodoController` die folgenden Methoden hinzu, um eine API bereitzustellen, die Aufgaben abruft:</span><span class="sxs-lookup"><span data-stu-id="d72d7-506">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="d72d7-507">Diese Methoden implementieren zwei GET-Endpunkte:</span><span class="sxs-lookup"><span data-stu-id="d72d7-507">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="d72d7-508">Halten Sie die App an, falls diese noch ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="d72d7-508">Stop the app if it's still running.</span></span> <span data-ttu-id="d72d7-509">Führen Sie diese dann noch mal aus, damit die letzten Änderungen übernommen werden.</span><span class="sxs-lookup"><span data-stu-id="d72d7-509">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="d72d7-510">Testen Sie die App, indem Sie die beiden Endpunkte in einem Browser aufrufen.</span><span class="sxs-lookup"><span data-stu-id="d72d7-510">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="d72d7-511">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d72d7-511">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="d72d7-512">Die folgende HTTP-Antwort wird durch den Aufruf von `GetTodoItems` erzeugt:</span><span class="sxs-lookup"><span data-stu-id="d72d7-512">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="d72d7-513">Routing und URL-Pfade</span><span class="sxs-lookup"><span data-stu-id="d72d7-513">Routing and URL paths</span></span>

<span data-ttu-id="d72d7-514">Das Attribut [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) gibt eine Methode an, die auf eine HTTP GET-Anforderung antwortet.</span><span class="sxs-lookup"><span data-stu-id="d72d7-514">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="d72d7-515">Der URL-Pfad für jede Methode wird wie folgt erstellt:</span><span class="sxs-lookup"><span data-stu-id="d72d7-515">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="d72d7-516">Beginnen Sie mit der Vorlagenzeichenfolge im `Route`-Attribut des Controllers:</span><span class="sxs-lookup"><span data-stu-id="d72d7-516">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="d72d7-517">Ersetzen Sie `[controller]` durch den Namen des Controllers, bei dem es sich standardmäßig um den Namen der Controller-Klasse ohne das Suffix „Controller“ handelt.</span><span class="sxs-lookup"><span data-stu-id="d72d7-517">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="d72d7-518">Bei diesem Beispiel ist der Klassenname des Controllers „**Todo**Controller“, d.h. der Controllername lautet „todo“.</span><span class="sxs-lookup"><span data-stu-id="d72d7-518">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="d72d7-519">Beim ASP.NET Core-[Routing](xref:mvc/controllers/routing) wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="d72d7-519">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="d72d7-520">Wenn das `[HttpGet]`-Attribut eine Routenvorlage (z. B. `[HttpGet("products")]`) hat, fügen Sie diese an den Pfad an.</span><span class="sxs-lookup"><span data-stu-id="d72d7-520">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="d72d7-521">In diesem Beispiel wird keine Vorlage verwendet.</span><span class="sxs-lookup"><span data-stu-id="d72d7-521">This sample doesn't use a template.</span></span> <span data-ttu-id="d72d7-522">Weitere Informationen finden Sie unter [Attributrouting mit Http[Verb]-Attributen](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="d72d7-522">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="d72d7-523">In der folgenden `GetTodoItem`-Methode ist `"{id}"` eine Platzhaltervariable für den eindeutigen Bezeichner des To-do-Elements.</span><span class="sxs-lookup"><span data-stu-id="d72d7-523">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="d72d7-524">Wenn `GetTodoItem` aufgerufen wird, wird der Wert von `"{id}"` in der URL der Methode in ihrem Parameter `id` bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="d72d7-524">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="d72d7-525">Rückgabewerte</span><span class="sxs-lookup"><span data-stu-id="d72d7-525">Return values</span></span>

<span data-ttu-id="d72d7-526">Der Rückgabetyp der Methoden `GetTodoItems` und `GetTodoItem` ist [ActionResult\<T>-Typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="d72d7-526">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="d72d7-527">ASP.NET Core serialisiert automatisch das Objekt in [JSON](https://www.json.org/) und schreibt den JSON-Code in den Text der Antwortnachricht.</span><span class="sxs-lookup"><span data-stu-id="d72d7-527">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="d72d7-528">Der Antwortcode für diesen Rückgabetyp ist 200, vorausgesetzt, es gibt keine Ausnahmefehler.</span><span class="sxs-lookup"><span data-stu-id="d72d7-528">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="d72d7-529">Nicht behandelte Ausnahmen werden in 5xx-Fehler übersetzt.</span><span class="sxs-lookup"><span data-stu-id="d72d7-529">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="d72d7-530">`ActionResult`-Rückgabetypen können eine Vielzahl von HTTP-Statuscodes darstellen.</span><span class="sxs-lookup"><span data-stu-id="d72d7-530">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="d72d7-531">Beispielsweise kann `GetTodoItem` zwei verschiedene Statuswerte zurückgeben:</span><span class="sxs-lookup"><span data-stu-id="d72d7-531">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="d72d7-532">Wenn kein Element mit der angeforderten ID übereinstimmt, gibt die Methode einen 404-Fehlercode [Nicht gefunden](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) zurück.</span><span class="sxs-lookup"><span data-stu-id="d72d7-532">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="d72d7-533">Andernfalls gibt die Methode 200 mit einem JSON-Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="d72d7-533">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="d72d7-534">Die Rückgabe von `item` löst eine HTTP 200-Antwort aus.</span><span class="sxs-lookup"><span data-stu-id="d72d7-534">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="d72d7-535">Testen der GetTodoItems-Methode</span><span class="sxs-lookup"><span data-stu-id="d72d7-535">Test the GetTodoItems method</span></span>

<span data-ttu-id="d72d7-536">Dieses Tutorial verwendet Postman zum Testen der Web-API.</span><span class="sxs-lookup"><span data-stu-id="d72d7-536">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="d72d7-537">Installieren Sie [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="d72d7-537">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="d72d7-538">Starten Sie die Web-App.</span><span class="sxs-lookup"><span data-stu-id="d72d7-538">Start the web app.</span></span>
* <span data-ttu-id="d72d7-539">Starten Sie Postman.</span><span class="sxs-lookup"><span data-stu-id="d72d7-539">Start Postman.</span></span>
* <span data-ttu-id="d72d7-540">Deaktivieren Sie **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="d72d7-540">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d72d7-541">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d72d7-541">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d72d7-542">Deaktivieren Sie auf der Registerkarte **General** (Allgemein) unter **File**>**Settings** (Datei > Einstellungen) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="d72d7-542">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="d72d7-543">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d72d7-543">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="d72d7-544">Deaktivieren Sie auf der Registerkarte **General** (Allgemein) unter **Postman** > **Preferences** (Postman > Einstellungen) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="d72d7-544">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="d72d7-545">Wählen Sie alternativ den Schraubenschlüssel und **Settings** (Einstellungen) aus, und deaktivieren Sie dann die Überprüfung des SSL-Zertifikats.</span><span class="sxs-lookup"><span data-stu-id="d72d7-545">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="d72d7-546">Aktivieren Sie die Verifizierung des SSL-Zertifikats wieder, nachdem Sie den Controller getestet haben.</span><span class="sxs-lookup"><span data-stu-id="d72d7-546">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="d72d7-547">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="d72d7-547">Create a new request.</span></span>
  * <span data-ttu-id="d72d7-548">Legen Sie die HTTP-Methode auf **GET** fest.</span><span class="sxs-lookup"><span data-stu-id="d72d7-548">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="d72d7-549">Legen Sie die Anforderungs-URL auf `https://localhost:<port>/api/todo` fest.</span><span class="sxs-lookup"><span data-stu-id="d72d7-549">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="d72d7-550">Beispielsweise `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="d72d7-550">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="d72d7-551">Wählen Sie in Postman **Two pane view** (Ansicht mit zwei Bereichen) aus.</span><span class="sxs-lookup"><span data-stu-id="d72d7-551">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="d72d7-552">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="d72d7-552">Select **Send**.</span></span>

![Postman mit GET-Anforderung](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="d72d7-554">Hinzufügen einer Methode</span><span class="sxs-lookup"><span data-stu-id="d72d7-554">Add a Create method</span></span>

<span data-ttu-id="d72d7-555">Fügen Sie die folgende `PostTodoItem`-Methode in *Controllers/TodoController.cs* hinzu:</span><span class="sxs-lookup"><span data-stu-id="d72d7-555">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="d72d7-556">Der vorherige Code ist eine HTTP POST-Methode, die durch das Attribut [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="d72d7-556">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="d72d7-557">Die Methode ruft den Wert der Aufgabe aus dem Text der HTTP-Anforderung ab.</span><span class="sxs-lookup"><span data-stu-id="d72d7-557">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="d72d7-558">Die `CreatedAtAction`-Methode:</span><span class="sxs-lookup"><span data-stu-id="d72d7-558">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="d72d7-559">Gibt bei einer erfolgreichen Anforderung den Statuscode „HTTP 201“ zurück.</span><span class="sxs-lookup"><span data-stu-id="d72d7-559">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="d72d7-560">HTTP 201 ist die Standardantwort für eine HTTP POST-Methode, die eine neue Ressource auf dem Server erstellt.</span><span class="sxs-lookup"><span data-stu-id="d72d7-560">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="d72d7-561">Fügt der Antwort einen `Location`-Header hinzu.</span><span class="sxs-lookup"><span data-stu-id="d72d7-561">Adds a `Location` header to the response.</span></span> <span data-ttu-id="d72d7-562">Der `Location`-Header gibt den URI des neu erstellten To-Do-Elements zurück.</span><span class="sxs-lookup"><span data-stu-id="d72d7-562">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="d72d7-563">Weitere Informationen finden Sie unter [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="d72d7-563">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="d72d7-564">Verweist auf die `GetTodoItem`-Aktion zum Erstellen des URIs des `Location`-Headers.</span><span class="sxs-lookup"><span data-stu-id="d72d7-564">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="d72d7-565">Das `nameof`-Schlüsselwort von C# wird verwendet, um eine Hartcodierung des Aktionsnamens im `CreatedAtAction`-Aufruf zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="d72d7-565">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="d72d7-566">Testen der PostTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="d72d7-566">Test the PostTodoItem method</span></span>

* <span data-ttu-id="d72d7-567">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="d72d7-567">Build the project.</span></span>
* <span data-ttu-id="d72d7-568">Legen Sie in Postman die HTTP-Methode auf `POST` fest.</span><span class="sxs-lookup"><span data-stu-id="d72d7-568">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="d72d7-569">Wählen Sie die Registerkarte **Body** (Text) aus.</span><span class="sxs-lookup"><span data-stu-id="d72d7-569">Select the **Body** tab.</span></span>
* <span data-ttu-id="d72d7-570">Wählen Sie das Optionsfeld **raw** (Unformatiert) aus.</span><span class="sxs-lookup"><span data-stu-id="d72d7-570">Select the **raw** radio button.</span></span>
* <span data-ttu-id="d72d7-571">Legen Sie den Typ auf **JSON (application/json)** fest.</span><span class="sxs-lookup"><span data-stu-id="d72d7-571">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="d72d7-572">Geben Sie für die Aufgabe den Anforderungstext im JSON-Format ein:</span><span class="sxs-lookup"><span data-stu-id="d72d7-572">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="d72d7-573">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="d72d7-573">Select **Send**.</span></span>

  ![Postman mit erstellter Anforderung](first-web-api/_static/create.png)

  <span data-ttu-id="d72d7-575">Wenn Sie die Fehlermeldung „405: Methode nicht zulässig“ erhalten, wurde das Projekt wahrscheinlich nicht kompiliert, nachdem die Methode `PostTodoItem` hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="d72d7-575">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="d72d7-576">Testen des Adressheader-URIs</span><span class="sxs-lookup"><span data-stu-id="d72d7-576">Test the location header URI</span></span>

* <span data-ttu-id="d72d7-577">Wählen Sie auf der Registerkarte **Header** (Header) den Bereich **Response** (Antwort) aus.</span><span class="sxs-lookup"><span data-stu-id="d72d7-577">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="d72d7-578">Kopieren Sie den den Headerwert von **Location** (Speicherort):</span><span class="sxs-lookup"><span data-stu-id="d72d7-578">Copy the **Location** header value:</span></span>

  ![Registerkarte „Header“ in der Postman-Konsole](first-web-api/_static/pmc2.png)

* <span data-ttu-id="d72d7-580">Legen Sie die Methode auf „GET“ fest.</span><span class="sxs-lookup"><span data-stu-id="d72d7-580">Set the method to GET.</span></span>
* <span data-ttu-id="d72d7-581">Fügen Sie den URI (z. B. `https://localhost:5001/api/Todo/2`) ein.</span><span class="sxs-lookup"><span data-stu-id="d72d7-581">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="d72d7-582">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="d72d7-582">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="d72d7-583">Hinzufügen einer PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="d72d7-583">Add a PutTodoItem method</span></span>

<span data-ttu-id="d72d7-584">Fügen Sie die folgende `PutTodoItem`-Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="d72d7-584">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="d72d7-585">`PutTodoItem` ähnelt `PostTodoItem`, verwendet allerdings HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="d72d7-585">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="d72d7-586">Die Antwort ist [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="d72d7-586">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="d72d7-587">Gemäß der HTTP-Spezifikation erfordert eine PUT-Anforderung, dass der Client die gesamte aktualisierte Entität (nicht nur die Änderungen) sendet.</span><span class="sxs-lookup"><span data-stu-id="d72d7-587">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="d72d7-588">Verwenden Sie [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute), um Teilupdates zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="d72d7-588">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="d72d7-589">Wenn beim Aufrufen von `PutTodoItem` ein Fehler zurückgegeben wird, rufen Sie `GET` auf, um sicherzustellen, dass die Datenbank ein Element enthält.</span><span class="sxs-lookup"><span data-stu-id="d72d7-589">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="d72d7-590">Testen der PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="d72d7-590">Test the PutTodoItem method</span></span>

<span data-ttu-id="d72d7-591">In diesem Beispiel wird eine In-Memory-Datenbank verwendet, die jedes Mal initialisiert werden muss, wenn die App gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="d72d7-591">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="d72d7-592">Es muss ein Element in der Datenbank vorhanden sein, bevor Sie einen PUT-Aufruf durchführen.</span><span class="sxs-lookup"><span data-stu-id="d72d7-592">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="d72d7-593">Rufen Sie vor einem PUT-Aufruf GET auf, um sicherzustellen, dass ein Element in der Datenbank vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="d72d7-593">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="d72d7-594">Aktualisieren Sie die Aufgabe mit der ID = 1, und setzen Sie ihren Namen auf „feed fish“:</span><span class="sxs-lookup"><span data-stu-id="d72d7-594">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="d72d7-595">Die folgende Abbildung zeigt das Postman-Update:</span><span class="sxs-lookup"><span data-stu-id="d72d7-595">The following image shows the Postman update:</span></span>

![Postman-Konsole mit der Antwort „204 (Kein Inhalt)“](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="d72d7-597">Hinzufügen einer DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="d72d7-597">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="d72d7-598">Fügen Sie die folgende `DeleteTodoItem`-Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="d72d7-598">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="d72d7-599">Die `DeleteTodoItem`-Antwort lautet [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="d72d7-599">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="d72d7-600">Testen der DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="d72d7-600">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="d72d7-601">So löschen Sie mit Postman eine Aufgabe</span><span class="sxs-lookup"><span data-stu-id="d72d7-601">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="d72d7-602">Legen Sie die Methode auf `DELETE` fest.</span><span class="sxs-lookup"><span data-stu-id="d72d7-602">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="d72d7-603">Legen Sie den URI des zu löschenden Objekts fest, z. B. `https://localhost:5001/api/todo/1`.</span><span class="sxs-lookup"><span data-stu-id="d72d7-603">Set the URI of the object to delete (for example `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="d72d7-604">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="d72d7-604">Select **Send**.</span></span>

<span data-ttu-id="d72d7-605">Sie können in der Beispiel-App alle Elemente löschen.</span><span class="sxs-lookup"><span data-stu-id="d72d7-605">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="d72d7-606">Sobald das letzte Element gelöscht wurde, wird allerdings beim nächsten API-Aufruf vom Modellklassenkonstruktor ein neues erstellt.</span><span class="sxs-lookup"><span data-stu-id="d72d7-606">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="d72d7-607">Aufrufen der Web-API mit JavaScript</span><span class="sxs-lookup"><span data-stu-id="d72d7-607">Call the web API with JavaScript</span></span>

<span data-ttu-id="d72d7-608">In diesem Abschnitt wird eine HTML-Seite hinzugefügt, die mithilfe von JavaScript die Web-API aufruft.</span><span class="sxs-lookup"><span data-stu-id="d72d7-608">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="d72d7-609">jQuery initiiert die Anforderung.</span><span class="sxs-lookup"><span data-stu-id="d72d7-609">jQuery initiates the request.</span></span> <span data-ttu-id="d72d7-610">JavaScript aktualisiert die Seite mit den Details aus der Antwort der Web-API.</span><span class="sxs-lookup"><span data-stu-id="d72d7-610">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="d72d7-611">Konfigurieren Sie die App so, dass sie [statische Dateien bereitstellt](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_), und [aktivieren Sie die Standarddateizuordnung](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_), indem Sie *startup.cs* mit dem unten markierten Code aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="d72d7-611">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="d72d7-612">Erstellen Sie im Projektverzeichnis den Ordner *wwwwroot*.</span><span class="sxs-lookup"><span data-stu-id="d72d7-612">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="d72d7-613">Fügen Sie dem Verzeichnis *wwwroot* eine HTML-Datei namens *index.html* hinzu.</span><span class="sxs-lookup"><span data-stu-id="d72d7-613">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="d72d7-614">Ersetzen Sie den Inhalt durch folgendes Markup:</span><span class="sxs-lookup"><span data-stu-id="d72d7-614">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="d72d7-615">Fügen Sie dem Verzeichnis *wwwroot* eine JavaScript-Datei namens *site.js* hinzu.</span><span class="sxs-lookup"><span data-stu-id="d72d7-615">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="d72d7-616">Ersetzen Sie den Inhalt durch folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="d72d7-616">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="d72d7-617">Möglicherweise ist eine Änderung an den Starteinstellungen des ASP.NET Core-Projekts erforderlich, um die HTML-Seite lokal zu testen:</span><span class="sxs-lookup"><span data-stu-id="d72d7-617">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="d72d7-618">Öffnen Sie *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="d72d7-618">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="d72d7-619">Entfernen Sie die `launchUrl`-Eigenschaft, um zu erzwingen, dass die App mit *index.html* als Startseite geöffnet wird. Dies ist die Standarddatei des Projekts.</span><span class="sxs-lookup"><span data-stu-id="d72d7-619">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="d72d7-620">Dieses Beispiel ruft alle CRUD-Methoden der Web-API auf.</span><span class="sxs-lookup"><span data-stu-id="d72d7-620">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="d72d7-621">Im Folgenden werden die API-Aufrufe erläutert.</span><span class="sxs-lookup"><span data-stu-id="d72d7-621">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="d72d7-622">Abrufen einer Liste von To-Do-Elementen</span><span class="sxs-lookup"><span data-stu-id="d72d7-622">Get a list of to-do items</span></span>

<span data-ttu-id="d72d7-623">jQuery sendet eine HTTP GET-Anforderung an die Web-API, die JSON-Code mit einem Array aus To-Do-Elementen zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="d72d7-623">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="d72d7-624">Die Rückruffunktion `success` wird aufgerufen, wenn die Anforderung erfolgreich ist.</span><span class="sxs-lookup"><span data-stu-id="d72d7-624">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="d72d7-625">Im Rückruf wird DOM mit den To-Do-Informationen aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="d72d7-625">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="d72d7-626">Hinzufügen eines To-Do-Elements</span><span class="sxs-lookup"><span data-stu-id="d72d7-626">Add a to-do item</span></span>

<span data-ttu-id="d72d7-627">jQuery sendet eine HTTP POST-Anforderung mit dem To-Do-Element im Anforderungstext.</span><span class="sxs-lookup"><span data-stu-id="d72d7-627">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="d72d7-628">Die Optionen `accepts` und `contentType` werden auf `application/json` festgelegt, um den gesendeten und empfangenen Medientyp anzugeben.</span><span class="sxs-lookup"><span data-stu-id="d72d7-628">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="d72d7-629">Die Aufgabe wird mit [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) in JSON konvertiert.</span><span class="sxs-lookup"><span data-stu-id="d72d7-629">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="d72d7-630">Wenn die API den Statuscode „Erfolgreich“ zurückgibt, wird die Funktion `getData` aufgerufen, um die HTML-Tabelle zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="d72d7-630">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="d72d7-631">Aktualisieren eines To-Do-Elements</span><span class="sxs-lookup"><span data-stu-id="d72d7-631">Update a to-do item</span></span>

<span data-ttu-id="d72d7-632">Das Aktualisieren einer Aufgabe ist vergleichbar mit dem Hinzufügen einer Aufgabe.</span><span class="sxs-lookup"><span data-stu-id="d72d7-632">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="d72d7-633">Die `url` ändert sich, um den eindeutigen Bezeichner des Elements hinzuzufügen. Der `type` lautet `PUT`.</span><span class="sxs-lookup"><span data-stu-id="d72d7-633">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="d72d7-634">Löschen eines To-Do-Elements</span><span class="sxs-lookup"><span data-stu-id="d72d7-634">Delete a to-do item</span></span>

<span data-ttu-id="d72d7-635">Eine Aufgabe wird folgendermaßen gelöscht: im AJAX-Aufruf wird `type` auf `DELETE` festgelegt, und der eindeutige Bezeichner des Elements wird in der URL angegeben.</span><span class="sxs-lookup"><span data-stu-id="d72d7-635">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="d72d7-636">Hinzufügen der Authentifizierungsunterstützung zu einer Web-API</span><span class="sxs-lookup"><span data-stu-id="d72d7-636">Add authentication support to a web API</span></span>

<span data-ttu-id="d72d7-637">Informationen hierzu finden Sie im [IdentityServer4](https://identityserver4.readthedocs.io/en/latest/quickstarts/0_overview.html)-Tutorial.</span><span class="sxs-lookup"><span data-stu-id="d72d7-637">See the [IdentityServer4](https://identityserver4.readthedocs.io/en/latest/quickstarts/0_overview.html) tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d72d7-638">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="d72d7-638">Additional resources</span></span>

<span data-ttu-id="d72d7-639">[Sie können den Beispielcode für dieses Tutorial anzeigen oder herunterladen.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)</span><span class="sxs-lookup"><span data-stu-id="d72d7-639">[View or download sample code for this tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="d72d7-640">[Informationen zum Herunterladen](xref:index#how-to-download-a-sample) finden Sie hier.</span><span class="sxs-lookup"><span data-stu-id="d72d7-640">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="d72d7-641">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="d72d7-641">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="d72d7-642">Dieses Tutorial auf YouTube</span><span class="sxs-lookup"><span data-stu-id="d72d7-642">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)

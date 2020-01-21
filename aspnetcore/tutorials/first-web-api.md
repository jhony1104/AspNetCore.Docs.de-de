---
title: 'Tutorial: Erstellen einer Web-API mit ASP.NET Core'
author: rick-anderson
description: Informationen zum Erstellen einer Web-API mit ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: tutorials/first-web-api
ms.openlocfilehash: 3bf930d19684e84365f0ff0255fccd2939fb3f39
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75354925"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="ecb8b-103">Tutorial: Erstellen einer Web-API mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ecb8b-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="ecb8b-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="ecb8b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="ecb8b-105">In diesem Tutorial lernen Sie die Grundlagen der Erstellung einer Web-API mit ASP.NET Core kennen.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ecb8b-106">In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ecb8b-107">Erstellen eines Web-API-Projekts</span><span class="sxs-lookup"><span data-stu-id="ecb8b-107">Create a web API project.</span></span>
> * <span data-ttu-id="ecb8b-108">Hinzufügen einer Modellklasse und eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="ecb8b-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="ecb8b-109">Erstellen eines Controllergerüsts mit CRUD-Methoden</span><span class="sxs-lookup"><span data-stu-id="ecb8b-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="ecb8b-110">Konfigurieren des Routings, der URL-Pfade und der Rückgabewerte</span><span class="sxs-lookup"><span data-stu-id="ecb8b-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="ecb8b-111">Aufrufen der Web-API mit Postman</span><span class="sxs-lookup"><span data-stu-id="ecb8b-111">Call the web API with Postman.</span></span>

<span data-ttu-id="ecb8b-112">Abschließend steht Ihnen eine Web-API zur Verfügung, die in einer relationalen Datenbank gespeicherte To-do-Elemente verwalten kann.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="ecb8b-113">Übersicht</span><span class="sxs-lookup"><span data-stu-id="ecb8b-113">Overview</span></span>

<span data-ttu-id="ecb8b-114">In diesem Tutorial wird die folgende API erstellt:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="ecb8b-115">API</span><span class="sxs-lookup"><span data-stu-id="ecb8b-115">API</span></span> | <span data-ttu-id="ecb8b-116">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="ecb8b-116">Description</span></span> | <span data-ttu-id="ecb8b-117">Anforderungstext</span><span class="sxs-lookup"><span data-stu-id="ecb8b-117">Request body</span></span> | <span data-ttu-id="ecb8b-118">Antworttext</span><span class="sxs-lookup"><span data-stu-id="ecb8b-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="ecb8b-119">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="ecb8b-119">GET /api/TodoItems</span></span> | <span data-ttu-id="ecb8b-120">Alle To-do-Elemente abrufen</span><span class="sxs-lookup"><span data-stu-id="ecb8b-120">Get all to-do items</span></span> | <span data-ttu-id="ecb8b-121">Keine</span><span class="sxs-lookup"><span data-stu-id="ecb8b-121">None</span></span> | <span data-ttu-id="ecb8b-122">Array von To-do-Elementen</span><span class="sxs-lookup"><span data-stu-id="ecb8b-122">Array of to-do items</span></span>|
|<span data-ttu-id="ecb8b-123">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="ecb8b-123">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="ecb8b-124">Ein Element nach ID abrufen</span><span class="sxs-lookup"><span data-stu-id="ecb8b-124">Get an item by ID</span></span> | <span data-ttu-id="ecb8b-125">Keine</span><span class="sxs-lookup"><span data-stu-id="ecb8b-125">None</span></span> | <span data-ttu-id="ecb8b-126">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="ecb8b-126">To-do item</span></span>|
|<span data-ttu-id="ecb8b-127">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="ecb8b-127">POST /api/TodoItems</span></span> | <span data-ttu-id="ecb8b-128">Neues Element hinzufügen</span><span class="sxs-lookup"><span data-stu-id="ecb8b-128">Add a new item</span></span> | <span data-ttu-id="ecb8b-129">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="ecb8b-129">To-do item</span></span> | <span data-ttu-id="ecb8b-130">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="ecb8b-130">To-do item</span></span> |
|<span data-ttu-id="ecb8b-131">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="ecb8b-131">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="ecb8b-132">Vorhandenes Element aktualisieren &nbsp;</span><span class="sxs-lookup"><span data-stu-id="ecb8b-132">Update an existing item &nbsp;</span></span> | <span data-ttu-id="ecb8b-133">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="ecb8b-133">To-do item</span></span> | <span data-ttu-id="ecb8b-134">Keine</span><span class="sxs-lookup"><span data-stu-id="ecb8b-134">None</span></span> |
|<span data-ttu-id="ecb8b-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="ecb8b-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="ecb8b-136">Löschen eines Elements &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="ecb8b-136">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="ecb8b-137">Keine</span><span class="sxs-lookup"><span data-stu-id="ecb8b-137">None</span></span> | <span data-ttu-id="ecb8b-138">Keine</span><span class="sxs-lookup"><span data-stu-id="ecb8b-138">None</span></span>|

<span data-ttu-id="ecb8b-139">Das folgende Diagramm zeigt den Entwurf der App.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-139">The following diagram shows the design of the app.</span></span>

![Der Client ist das Feld ganz links.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="ecb8b-145">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="ecb8b-145">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ecb8b-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ecb8b-146">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ecb8b-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ecb8b-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="ecb8b-148">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ecb8b-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="ecb8b-149">Erstellen eines Webprojekts</span><span class="sxs-lookup"><span data-stu-id="ecb8b-149">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ecb8b-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ecb8b-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ecb8b-151">Klicken Sie im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="ecb8b-152">Wählen Sie die Vorlage **ASP.NET Core-Webanwendung** aus, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-152">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="ecb8b-153">Geben Sie dem Projekt den Namen *TodoApi*, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-153">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="ecb8b-154">Vergewissern Sie sich, dass im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Optionen **.NET Core** und **ASP.NET Core 3.1** ausgewählt sind.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-154">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="ecb8b-155">Wählen Sie die Vorlage **API** aus, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-155">Select the **API** template and click **Create**.</span></span>

![VS-Dialogfeld „Neues Projekt“](first-web-api/_static/vs3.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ecb8b-157">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ecb8b-157">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ecb8b-158">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="ecb8b-158">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="ecb8b-159">Wechseln Sie mit dem Befehl `cd`' zu dem Ordner, der den Projektordner enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-159">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="ecb8b-160">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-160">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="ecb8b-161">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem Projekt die erforderlichen Elemente hinzufügen möchten, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-161">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="ecb8b-162">Die obenstehenden Befehle haben folgende Konsequenzen:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-162">The preceding commands:</span></span>

  * <span data-ttu-id="ecb8b-163">Ein neues Web-API-Projekt wird erstellt und in Visual Studio Code geöffnet.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-163">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="ecb8b-164">Die NuGet-Pakete, die im nächsten Abschnitt erforderlich sind, werden hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-164">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="ecb8b-165">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ecb8b-165">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ecb8b-166">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-166">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="ecb8b-168">Wählen Sie **.NET Core** > **App** > **API** > **Weiter** aus.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-168">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![Dialogfeld „Neue Projektmappe“ in macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="ecb8b-170">Wählen Sie im Dialogfeld **Neue ASP.NET Core-Web-API konfigurieren**für das **Zielframework** den Eintrag \* *.NET Core 3.1* aus.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-170">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.1*.</span></span>

* <span data-ttu-id="ecb8b-171">Geben Sie für **Projektname** *TodoApi* ein, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-171">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogfeld „Konfiguration“](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="ecb8b-173">Öffnen Sie im Projektordner ein Befehlsterminal, und führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-173">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="ecb8b-174">Testen der API</span><span class="sxs-lookup"><span data-stu-id="ecb8b-174">Test the API</span></span>

<span data-ttu-id="ecb8b-175">Die Projektvorlage erstellt eine `WeatherForecast`-API.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-175">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="ecb8b-176">Rufen Sie in einem Browser die `Get`-Methode zum Testen der App auf.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-176">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ecb8b-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ecb8b-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ecb8b-178">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-178">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="ecb8b-179">Visual Studio startet einen Browser und navigiert zu `https://localhost:<port>/WeatherForecast`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-179">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="ecb8b-180">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem IIS Express-Zertifikat vertrauen, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-180">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="ecb8b-181">Wählen Sie im folgenden Dialogfeld **Sicherheitswarnung** die Option **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-181">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ecb8b-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ecb8b-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ecb8b-183">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-183">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="ecb8b-184">Rufen Sie in einem Browser die folgende URL auf: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span><span class="sxs-lookup"><span data-stu-id="ecb8b-184">In a browser, go to following URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="ecb8b-185">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ecb8b-185">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="ecb8b-186">Wählen Sie **Ausführen** > **Debugging starten** aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-186">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="ecb8b-187">Visual Studio für Mac startet einen Browser und navigiert zu `https://localhost:<port>`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-187">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="ecb8b-188">Der Fehler „HTTP 404: Nicht gefunden“ wird zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-188">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="ecb8b-189">Fügen Sie `/WeatherForecast` an die URL an, d. h., ändern Sie sie in `https://localhost:<port>/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-189">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="ecb8b-190">Der zurückgegebene JSON-Code sieht in etwa wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-190">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="ecb8b-191">Hinzufügen einer Modellklasse</span><span class="sxs-lookup"><span data-stu-id="ecb8b-191">Add a model class</span></span>

<span data-ttu-id="ecb8b-192">Ein *Modell* ist eine Gruppe von Klassen, die die Daten darstellen, die die App verwaltet.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-192">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="ecb8b-193">Das Modell für diese App ist eine einzelne `TodoItem`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-193">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ecb8b-194">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ecb8b-194">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ecb8b-195">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-195">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="ecb8b-196">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-196">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="ecb8b-197">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-197">Name the folder *Models*.</span></span>

* <span data-ttu-id="ecb8b-198">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-198">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="ecb8b-199">Nennen Sie die Klasse *TodoItem*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-199">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="ecb8b-200">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-200">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ecb8b-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ecb8b-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ecb8b-202">Fügen Sie einen Ordner namens *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-202">Add a folder named *Models*.</span></span>

* <span data-ttu-id="ecb8b-203">Fügen Sie dem Ordner *Modelle* mit dem folgenden Code eine `TodoItem`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-203">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="ecb8b-204">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ecb8b-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ecb8b-205">Klicken Sie mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-205">Right-click the project.</span></span> <span data-ttu-id="ecb8b-206">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-206">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="ecb8b-207">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-207">Name the folder *Models*.</span></span>

  ![Neuer Ordner](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="ecb8b-209">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Neue Datei** > **Allgemein** > **Leere Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-209">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="ecb8b-210">Nennen Sie die Klasse *TodoItem*, und klicken Sie dann auf **Neu**.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-210">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="ecb8b-211">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-211">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="ecb8b-212">Die `Id`-Eigenschaft fungiert als eindeutiger Schlüssel in einer relationalen Datenbank.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-212">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="ecb8b-213">Modellklassen können überall im Projekt platziert werden, doch gemäß der Konvention wird der Ordner *Modelle* verwendet.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-213">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="ecb8b-214">Hinzufügen eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="ecb8b-214">Add a database context</span></span>

<span data-ttu-id="ecb8b-215">Der *Datenbankkontext* ist die Hauptklasse, die die Entity Framework-Funktionen für ein Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-215">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="ecb8b-216">Diese Klasse wird durch Ableiten von der `Microsoft.EntityFrameworkCore.DbContext`-Klasse erstellt.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-216">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ecb8b-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ecb8b-217">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="ecb8b-218">Hinzufügen von Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="ecb8b-218">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="ecb8b-219">Klicken Sie im Menü **Extras** auf **NuGet-Paket-Manager > NuGet-Pakete für Projektmappe verwalten...** .</span><span class="sxs-lookup"><span data-stu-id="ecb8b-219">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="ecb8b-220">Klicken Sie auf die Registerkarte **Durchsuchen**, und geben Sie dann **Microsoft.EntityFrameworkCore.SqlServer** in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-220">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="ecb8b-221">Wählen Sie im linken Bereich **Microsoft.EntityFrameworkCore.SqlServer** aus.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-221">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="ecb8b-222">Aktivieren Sie das Kontrollkästchen **Projekt** im rechten Bereich, und klicken Sie dann auf **Installieren**.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-222">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="ecb8b-223">Fügen Sie mithilfe der obigen Schritte das `Microsoft.EntityFrameworkCore.InMemory`-NuGet-Paket hinzu.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-223">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![NuGet-Paket-Manager](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="ecb8b-225">Hinzufügen des TodoContext-Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="ecb8b-225">Add the TodoContext database context</span></span>

* <span data-ttu-id="ecb8b-226">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-226">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="ecb8b-227">Nennen Sie die Klasse *TodoContext*, und klicken Sie auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-227">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="ecb8b-228">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ecb8b-228">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="ecb8b-229">Fügen Sie eine `TodoContext`-Klasse zum Ordner *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-229">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="ecb8b-230">Geben Sie den folgenden Code ein:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-230">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="ecb8b-231">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="ecb8b-231">Register the database context</span></span>

<span data-ttu-id="ecb8b-232">In ASP.NET Core müssen Dienste wie der Datenbankkontext mit dem Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) registriert werden.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-232">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="ecb8b-233">Der Container stellt den Dienst für Controller bereit.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-233">The container provides the service to controllers.</span></span>

<span data-ttu-id="ecb8b-234">Aktualisieren Sie *Startup.cs* mit dem folgenden hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-234">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="ecb8b-235">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-235">The preceding code:</span></span>

* <span data-ttu-id="ecb8b-236">Entfernt nicht benötigte `using`-Deklarationen</span><span class="sxs-lookup"><span data-stu-id="ecb8b-236">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="ecb8b-237">Fügt dem Abhängigkeitscontainer den Datenbankkontext hinzu</span><span class="sxs-lookup"><span data-stu-id="ecb8b-237">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="ecb8b-238">Gibt an, dass der Datenbankkontext eine In-Memory Database verwendet</span><span class="sxs-lookup"><span data-stu-id="ecb8b-238">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="ecb8b-239">Erstellen eines Controllergerüsts</span><span class="sxs-lookup"><span data-stu-id="ecb8b-239">Scaffold a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ecb8b-240">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ecb8b-240">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ecb8b-241">Klicken Sie mit der rechten Maustaste auf den Ordner *Controller*.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-241">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="ecb8b-242">Wählen Sie **Hinzufügen** > **Neues Gerüstelement** aus.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-242">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="ecb8b-243">Klicken Sie auf **API-Controller mit Aktionen unter Verwendung von Entity Framework** und dann auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-243">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="ecb8b-244">Führen Sie im Dialogfeld **API-Controller mit Aktionen unter Verwendung von Entity Framework** folgende Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-244">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="ecb8b-245">Wählen Sie in der **Modellklasse** das Element **TodoItem (TodoApi.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-245">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="ecb8b-246">Wählen Sie in der **Datenkontextklasse** das Element **TodoContext (TodoApi.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-246">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="ecb8b-247">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-247">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="ecb8b-248">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ecb8b-248">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="ecb8b-249">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-249">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="ecb8b-250">Die obenstehenden Befehle haben folgende Konsequenzen:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-250">The preceding commands:</span></span>

* <span data-ttu-id="ecb8b-251">Die NuGet-Pakete für den Gerüstbau werden hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-251">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="ecb8b-252">Die Gerüstbau-Engine (`dotnet-aspnet-codegenerator`) wird installiert.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-252">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="ecb8b-253">Das Gerüst für `TodoItemsController` wird erstellt.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-253">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="ecb8b-254">Der generierte Code hat folgende Auswirkungen:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-254">The generated code:</span></span>

* <span data-ttu-id="ecb8b-255">Definiert eine API-Controllerklasse ohne Methoden.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-255">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="ecb8b-256">Markiert die Klasse mit dem [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute)-Attribut.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-256">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="ecb8b-257">Dieses Attribut gibt an, dass der Controller auf Web-API-Anforderungen reagiert.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-257">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="ecb8b-258">Weitere Informationen zu bestimmten Verhaltensweisen, die das Attribut ermöglicht, finden Sie unter <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-258">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="ecb8b-259">Verwendet die Abhängigkeitsinjektion, um den Datenbankkontext (`TodoContext`) dem Controller hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-259">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="ecb8b-260">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-260">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="ecb8b-261">Überblick über die PostTodoItem-Erstellungsmethode</span><span class="sxs-lookup"><span data-stu-id="ecb8b-261">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="ecb8b-262">Ersetzen Sie die Rückgabeanweisung in `PostTodoItem` durch eine Anweisung mit dem [nameof](/dotnet/csharp/language-reference/operators/nameof)-Operator:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-262">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="ecb8b-263">Der oben stehende Code ist eine HTTP POST-Methode, wie durch das [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute)-Attribut angegeben.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-263">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="ecb8b-264">Die Methode ruft den Wert der Aufgabe aus dem Text der HTTP-Anforderung ab.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-264">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="ecb8b-265">Die <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>-Methode:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-265">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="ecb8b-266">Gibt den HTTP-Statuscode 201 zurück, wenn der Vorgang erfolgreich ist.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-266">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="ecb8b-267">HTTP 201 ist die Standardantwort für eine HTTP POST-Methode, die eine neue Ressource auf dem Server erstellt.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-267">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="ecb8b-268">Fügt der Antwort einen [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location)-Header hinzu.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-268">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="ecb8b-269">Der `Location`-Header legt den [URI](https://developer.mozilla.org/docs/Glossary/URI) des neu erstellten To-do-Elements fest.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-269">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="ecb8b-270">Weitere Informationen finden Sie unter [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="ecb8b-270">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="ecb8b-271">Verweist auf die `GetTodoItem`-Aktion zum Erstellen des URIs des `Location`-Headers.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-271">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="ecb8b-272">Das `nameof`-Schlüsselwort von C# wird verwendet, um eine Hartcodierung des Aktionsnamens im `CreatedAtAction`-Aufruf zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-272">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="ecb8b-273">Installieren von Postman</span><span class="sxs-lookup"><span data-stu-id="ecb8b-273">Install Postman</span></span>

<span data-ttu-id="ecb8b-274">Dieses Tutorial verwendet Postman zum Testen der Web-API.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-274">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="ecb8b-275">Installieren Sie [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="ecb8b-275">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="ecb8b-276">Starten Sie die Web-App.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-276">Start the web app.</span></span>
* <span data-ttu-id="ecb8b-277">Starten Sie Postman.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-277">Start Postman.</span></span>
* <span data-ttu-id="ecb8b-278">Deaktivieren Sie **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="ecb8b-278">Disable **SSL certificate verification**</span></span>
* <span data-ttu-id="ecb8b-279">Deaktivieren Sie auf der Registerkarte **General** (Allgemein) unter File > Settings (**Datei** > **Einstellungen**) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="ecb8b-279">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="ecb8b-280">Aktivieren Sie die Verifizierung des SSL-Zertifikats wieder, nachdem Sie den Controller getestet haben.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-280">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="ecb8b-281">Testen von PostTodoItem mit Postman</span><span class="sxs-lookup"><span data-stu-id="ecb8b-281">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="ecb8b-282">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-282">Create a new request.</span></span>
* <span data-ttu-id="ecb8b-283">Legen Sie als HTTP-Methode `POST` fest.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-283">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="ecb8b-284">Wählen Sie die Registerkarte **Body** (Text) aus.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-284">Select the **Body** tab.</span></span>
* <span data-ttu-id="ecb8b-285">Wählen Sie das Optionsfeld **raw** (Unformatiert) aus.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-285">Select the **raw** radio button.</span></span>
* <span data-ttu-id="ecb8b-286">Legen Sie den Typ auf **JSON (application/json)** fest.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-286">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="ecb8b-287">Geben Sie für die Aufgabe den Anforderungstext im JSON-Format ein:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-287">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="ecb8b-288">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-288">Select **Send**.</span></span>

  ![Postman mit erstellter Anforderung](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="ecb8b-290">Testen des Adressheader-URIs</span><span class="sxs-lookup"><span data-stu-id="ecb8b-290">Test the location header URI</span></span>

* <span data-ttu-id="ecb8b-291">Wählen Sie auf der Registerkarte **Header** (Header) den Bereich **Response** (Antwort) aus.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-291">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="ecb8b-292">Kopieren Sie den den Headerwert von **Location** (Speicherort):</span><span class="sxs-lookup"><span data-stu-id="ecb8b-292">Copy the **Location** header value:</span></span>

  ![Registerkarte „Header“ in der Postman-Konsole](first-web-api/_static/3/create.png)

* <span data-ttu-id="ecb8b-294">Legen Sie die Methode auf „GET“ fest.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-294">Set the method to GET.</span></span>
* <span data-ttu-id="ecb8b-295">Fügen Sie den URI (z. B. `https://localhost:5001/api/TodoItems/1`) ein.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-295">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="ecb8b-296">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-296">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="ecb8b-297">Überblick über die GET-Methoden</span><span class="sxs-lookup"><span data-stu-id="ecb8b-297">Examine the GET methods</span></span>

<span data-ttu-id="ecb8b-298">Diese Methoden implementieren zwei GET-Endpunkte:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-298">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="ecb8b-299">Testen Sie die App, indem Sie die beiden Endpunkte in einem Browser oder über Postman aufrufen.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-299">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="ecb8b-300">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-300">For example:</span></span>

* [https://localhost:5001/api/TodoItems](https://localhost:5001/api/TodoItems)
* [https://localhost:5001/api/TodoItems/1](https://localhost:5001/api/TodoItems/1)

<span data-ttu-id="ecb8b-301">Durch einen Aufruf von `GetTodoItems` wird eine Antwort gesendet, die der folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-301">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="ecb8b-302">Testen von GET mit Postman</span><span class="sxs-lookup"><span data-stu-id="ecb8b-302">Test Get with Postman</span></span>

* <span data-ttu-id="ecb8b-303">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-303">Create a new request.</span></span>
* <span data-ttu-id="ecb8b-304">Legen Sie die HTTP-Methode auf **GET** fest.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-304">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="ecb8b-305">Legen Sie die Anforderungs-URL auf `https://localhost:<port>/api/TodoItems` fest.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-305">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="ecb8b-306">Beispielsweise `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-306">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="ecb8b-307">Wählen Sie in Postman **Two pane view** (Ansicht mit zwei Bereichen) aus.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-307">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="ecb8b-308">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-308">Select **Send**.</span></span>

<span data-ttu-id="ecb8b-309">Diese App verwendet eine In-Memory-Datenbank.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-309">This app uses an in-memory database.</span></span> <span data-ttu-id="ecb8b-310">Wenn die App angehalten und dann wieder gestartet wird, werden durch die vorherige GET-Anforderung keine Daten zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-310">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="ecb8b-311">Wenn keine Daten zurückgegeben werden, senden Sie mit [POST](#post) Daten an die App.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-311">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="ecb8b-312">Routing und URL-Pfade</span><span class="sxs-lookup"><span data-stu-id="ecb8b-312">Routing and URL paths</span></span>

<span data-ttu-id="ecb8b-313">Das Attribut [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) gibt eine Methode an, die auf eine HTTP GET-Anforderung antwortet.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-313">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="ecb8b-314">Der URL-Pfad für jede Methode wird wie folgt erstellt:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-314">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="ecb8b-315">Beginnen Sie mit der Vorlagenzeichenfolge im `Route`-Attribut des Controllers:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-315">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="ecb8b-316">Ersetzen Sie `[controller]` durch den Namen des Controllers, bei dem es sich standardmäßig um den Namen der Controller-Klasse ohne das Suffix „Controller“ handelt.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-316">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="ecb8b-317">In diesem Beispiel ist der Klassenname des Controllers „**TodoItems**Controller“. Der Controllername lautet also „TodoItems“.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-317">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="ecb8b-318">Beim ASP.NET Core-[Routing](xref:mvc/controllers/routing) wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-318">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="ecb8b-319">Wenn das `[HttpGet]`-Attribut eine Routenvorlage (z. B. `[HttpGet("products")]`) hat, fügen Sie diese an den Pfad an.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-319">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="ecb8b-320">In diesem Beispiel wird keine Vorlage verwendet.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-320">This sample doesn't use a template.</span></span> <span data-ttu-id="ecb8b-321">Weitere Informationen finden Sie unter [Attributrouting mit Http[Verb]-Attributen](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="ecb8b-321">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="ecb8b-322">In der folgenden `GetTodoItem`-Methode ist `"{id}"` eine Platzhaltervariable für den eindeutigen Bezeichner des To-do-Elements.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-322">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="ecb8b-323">Wenn `GetTodoItem` aufgerufen wird, wird der Wert von `"{id}"` in der URL der Methode in ihrem Parameter `id` bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-323">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="ecb8b-324">Rückgabewerte</span><span class="sxs-lookup"><span data-stu-id="ecb8b-324">Return values</span></span>

<span data-ttu-id="ecb8b-325">Der Rückgabetyp der Methoden `GetTodoItems` und `GetTodoItem` ist [ActionResult\<T>-Typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="ecb8b-325">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="ecb8b-326">ASP.NET Core serialisiert automatisch das Objekt in [JSON](https://www.json.org/) und schreibt den JSON-Code in den Text der Antwortnachricht.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-326">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="ecb8b-327">Der Antwortcode für diesen Rückgabetyp ist 200, vorausgesetzt, es gibt keine Ausnahmefehler.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-327">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="ecb8b-328">Nicht behandelte Ausnahmen werden in 5xx-Fehler übersetzt.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-328">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="ecb8b-329">`ActionResult`-Rückgabetypen können eine Vielzahl von HTTP-Statuscodes darstellen.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-329">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="ecb8b-330">Beispielsweise kann `GetTodoItem` zwei verschiedene Statuswerte zurückgeben:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-330">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="ecb8b-331">Wenn kein Element mit der angeforderten ID übereinstimmt, gibt die Methode einen 404-Fehlercode [Nicht gefunden](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) zurück.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-331">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="ecb8b-332">Andernfalls gibt die Methode 200 mit einem JSON-Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-332">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="ecb8b-333">Die Rückgabe von `item` löst eine HTTP 200-Antwort aus.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-333">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="ecb8b-334">PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="ecb8b-334">The PutTodoItem method</span></span>

<span data-ttu-id="ecb8b-335">Untersuchen Sie die `PutTodoItem`-Methode.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-335">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="ecb8b-336">`PutTodoItem` ähnelt `PostTodoItem`, verwendet allerdings HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-336">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="ecb8b-337">Die Antwort ist [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="ecb8b-337">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="ecb8b-338">Gemäß der HTTP-Spezifikation erfordert eine PUT-Anforderung, dass der Client die gesamte aktualisierte Entität (nicht nur die Änderungen) sendet.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-338">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="ecb8b-339">Verwenden Sie [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute), um Teilupdates zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-339">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="ecb8b-340">Wenn beim Aufrufen von `PutTodoItem` ein Fehler zurückgegeben wird, rufen Sie `GET` auf, um sicherzustellen, dass die Datenbank ein Element enthält.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-340">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="ecb8b-341">Testen der PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="ecb8b-341">Test the PutTodoItem method</span></span>

<span data-ttu-id="ecb8b-342">In diesem Beispiel wird eine In-Memory-Datenbank verwendet, die jedes Mal initialisiert werden muss, wenn die App gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-342">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="ecb8b-343">Es muss ein Element in der Datenbank vorhanden sein, bevor Sie einen PUT-Aufruf durchführen.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-343">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="ecb8b-344">Rufen Sie vor einem PUT-Aufruf GET auf, um sicherzustellen, dass ein Element in der Datenbank vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-344">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="ecb8b-345">Aktualisieren Sie das To-do-Element, das über den ID-Wert 1 verfügt, und legen Sie als Namen „feed fish“ fest:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-345">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="ecb8b-346">Die folgende Abbildung zeigt das Postman-Update:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-346">The following image shows the Postman update:</span></span>

![Postman-Konsole mit der Antwort „204 (Kein Inhalt)“](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="ecb8b-348">DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="ecb8b-348">The DeleteTodoItem method</span></span>

<span data-ttu-id="ecb8b-349">Untersuchen Sie die `DeleteTodoItem`-Methode.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-349">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="ecb8b-350">Testen der DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="ecb8b-350">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="ecb8b-351">So löschen Sie mit Postman eine Aufgabe</span><span class="sxs-lookup"><span data-stu-id="ecb8b-351">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="ecb8b-352">Legen Sie die Methode auf `DELETE` fest.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-352">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="ecb8b-353">Legen Sie den URI des zu löschenden Objekts fest, z. B. `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-353">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="ecb8b-354">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-354">Select **Send**.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="ecb8b-355">Aufrufen der Web-API mit JavaScript</span><span class="sxs-lookup"><span data-stu-id="ecb8b-355">Call the web API with JavaScript</span></span>

<span data-ttu-id="ecb8b-356">Mehr dazu finden Sie im [-Tutorial: Aufrufen einer ASP.NET Core-Web-API mit JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="ecb8b-356">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ecb8b-357">In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-357">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ecb8b-358">Erstellen eines Web-API-Projekts</span><span class="sxs-lookup"><span data-stu-id="ecb8b-358">Create a web API project.</span></span>
> * <span data-ttu-id="ecb8b-359">Hinzufügen einer Modellklasse und eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="ecb8b-359">Add a model class and a database context.</span></span>
> * <span data-ttu-id="ecb8b-360">Hinzufügen eines Controllers</span><span class="sxs-lookup"><span data-stu-id="ecb8b-360">Add a controller.</span></span>
> * <span data-ttu-id="ecb8b-361">Hinzufügen von CRUD-Methoden</span><span class="sxs-lookup"><span data-stu-id="ecb8b-361">Add CRUD methods.</span></span>
> * <span data-ttu-id="ecb8b-362">Konfigurieren von Routing und URL-Pfaden</span><span class="sxs-lookup"><span data-stu-id="ecb8b-362">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="ecb8b-363">Angeben von Rückgabewerten</span><span class="sxs-lookup"><span data-stu-id="ecb8b-363">Specify return values.</span></span>
> * <span data-ttu-id="ecb8b-364">Aufrufen der Web-API mit Postman</span><span class="sxs-lookup"><span data-stu-id="ecb8b-364">Call the web API with Postman.</span></span>
> * <span data-ttu-id="ecb8b-365">Aufrufen der Web-API mit JavaScript</span><span class="sxs-lookup"><span data-stu-id="ecb8b-365">Call the web API with JavaScript.</span></span>

<span data-ttu-id="ecb8b-366">Am Ende haben Sie eine Web-API, die in einer relationalen Datenbank gespeicherte „Aufgaben“ verwalten kann.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-366">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="ecb8b-367">Übersicht</span><span class="sxs-lookup"><span data-stu-id="ecb8b-367">Overview</span></span>

<span data-ttu-id="ecb8b-368">In diesem Tutorial wird die folgende API erstellt:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-368">This tutorial creates the following API:</span></span>

|<span data-ttu-id="ecb8b-369">API</span><span class="sxs-lookup"><span data-stu-id="ecb8b-369">API</span></span> | <span data-ttu-id="ecb8b-370">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="ecb8b-370">Description</span></span> | <span data-ttu-id="ecb8b-371">Anforderungstext</span><span class="sxs-lookup"><span data-stu-id="ecb8b-371">Request body</span></span> | <span data-ttu-id="ecb8b-372">Antworttext</span><span class="sxs-lookup"><span data-stu-id="ecb8b-372">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="ecb8b-373">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="ecb8b-373">GET /api/TodoItems</span></span> | <span data-ttu-id="ecb8b-374">Alle To-do-Elemente abrufen</span><span class="sxs-lookup"><span data-stu-id="ecb8b-374">Get all to-do items</span></span> | <span data-ttu-id="ecb8b-375">Keine</span><span class="sxs-lookup"><span data-stu-id="ecb8b-375">None</span></span> | <span data-ttu-id="ecb8b-376">Array von To-do-Elementen</span><span class="sxs-lookup"><span data-stu-id="ecb8b-376">Array of to-do items</span></span>|
|<span data-ttu-id="ecb8b-377">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="ecb8b-377">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="ecb8b-378">Ein Element nach ID abrufen</span><span class="sxs-lookup"><span data-stu-id="ecb8b-378">Get an item by ID</span></span> | <span data-ttu-id="ecb8b-379">Keine</span><span class="sxs-lookup"><span data-stu-id="ecb8b-379">None</span></span> | <span data-ttu-id="ecb8b-380">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="ecb8b-380">To-do item</span></span>|
|<span data-ttu-id="ecb8b-381">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="ecb8b-381">POST /api/TodoItems</span></span> | <span data-ttu-id="ecb8b-382">Neues Element hinzufügen</span><span class="sxs-lookup"><span data-stu-id="ecb8b-382">Add a new item</span></span> | <span data-ttu-id="ecb8b-383">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="ecb8b-383">To-do item</span></span> | <span data-ttu-id="ecb8b-384">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="ecb8b-384">To-do item</span></span> |
|<span data-ttu-id="ecb8b-385">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="ecb8b-385">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="ecb8b-386">Vorhandenes Element aktualisieren &nbsp;</span><span class="sxs-lookup"><span data-stu-id="ecb8b-386">Update an existing item &nbsp;</span></span> | <span data-ttu-id="ecb8b-387">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="ecb8b-387">To-do item</span></span> | <span data-ttu-id="ecb8b-388">Keine</span><span class="sxs-lookup"><span data-stu-id="ecb8b-388">None</span></span> |
|<span data-ttu-id="ecb8b-389">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="ecb8b-389">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="ecb8b-390">Löschen eines Elements &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="ecb8b-390">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="ecb8b-391">Keine</span><span class="sxs-lookup"><span data-stu-id="ecb8b-391">None</span></span> | <span data-ttu-id="ecb8b-392">Keine</span><span class="sxs-lookup"><span data-stu-id="ecb8b-392">None</span></span>|

<span data-ttu-id="ecb8b-393">Das folgende Diagramm zeigt den Entwurf der App.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-393">The following diagram shows the design of the app.</span></span>

![Der Client ist das Feld ganz links.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="ecb8b-399">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="ecb8b-399">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ecb8b-400">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ecb8b-400">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ecb8b-401">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ecb8b-401">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="ecb8b-402">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ecb8b-402">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="ecb8b-403">Erstellen eines Webprojekts</span><span class="sxs-lookup"><span data-stu-id="ecb8b-403">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ecb8b-404">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ecb8b-404">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ecb8b-405">Klicken Sie im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-405">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="ecb8b-406">Wählen Sie die Vorlage **ASP.NET Core-Webanwendung** aus, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-406">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="ecb8b-407">Geben Sie dem Projekt den Namen *TodoApi*, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-407">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="ecb8b-408">Vergewissern Sie sich, dass im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Optionen **.NET Core** und **ASP.NET Core 2.2** ausgewählt sind.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-408">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="ecb8b-409">Wählen Sie die Vorlage **API** aus, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-409">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="ecb8b-410">Wählen Sie **nicht** **Enable Docker Support** (Docker-Unterstützung aktivieren) aus.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-410">**Don't** select **Enable Docker Support**.</span></span>

![VS-Dialogfeld „Neues Projekt“](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ecb8b-412">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ecb8b-412">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ecb8b-413">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="ecb8b-413">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="ecb8b-414">Wechseln Sie mit dem Befehl `cd`' zu dem Ordner, der den Projektordner enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-414">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="ecb8b-415">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-415">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="ecb8b-416">Diese Befehle erstellen ein neues Web-API-Projekt und öffnen eine neue Visual Studio Code-Instanz im neuen Projektordner.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-416">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="ecb8b-417">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem Projekt die erforderlichen Elemente hinzufügen möchten, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-417">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="ecb8b-418">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ecb8b-418">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ecb8b-419">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-419">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="ecb8b-421">Wählen Sie **.NET Core** > **App** > **API** > **Weiter** aus.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-421">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![Dialogfeld „Neue Projektmappe“ in macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="ecb8b-423">Übernehmen Sie im Dialogfeld **Neue ASP.NET Core-Web-API konfigurieren** die Standardeinstellung **Zielframework** von \* *.NET Core 2.2*.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-423">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="ecb8b-424">Geben Sie für **Projektname** *TodoApi* ein, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-424">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogfeld „Konfiguration“](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="ecb8b-426">Testen der API</span><span class="sxs-lookup"><span data-stu-id="ecb8b-426">Test the API</span></span>

<span data-ttu-id="ecb8b-427">Die Projektvorlage erstellt eine `values`-API.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-427">The project template creates a `values` API.</span></span> <span data-ttu-id="ecb8b-428">Rufen Sie in einem Browser die `Get`-Methode zum Testen der App auf.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-428">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ecb8b-429">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ecb8b-429">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ecb8b-430">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-430">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="ecb8b-431">Visual Studio startet einen Browser und navigiert zu `https://localhost:<port>/api/values`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-431">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="ecb8b-432">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem IIS Express-Zertifikat vertrauen, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-432">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="ecb8b-433">Wählen Sie im folgenden Dialogfeld **Sicherheitswarnung** die Option **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-433">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ecb8b-434">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ecb8b-434">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ecb8b-435">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-435">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="ecb8b-436">Rufen Sie in einem Browser die folgende URL auf: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span><span class="sxs-lookup"><span data-stu-id="ecb8b-436">In a browser, go to following URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="ecb8b-437">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ecb8b-437">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="ecb8b-438">Wählen Sie **Ausführen** > **Debugging starten** aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-438">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="ecb8b-439">Visual Studio für Mac startet einen Browser und navigiert zu `https://localhost:<port>`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-439">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="ecb8b-440">Der Fehler „HTTP 404: Nicht gefunden“ wird zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-440">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="ecb8b-441">Fügen Sie der URL `/api/values` an, d.h. ändern Sie die URL in `https://localhost:<port>/api/values`.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-441">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="ecb8b-442">Die folgende JSON-Datei wird zurückgegeben:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-442">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="ecb8b-443">Hinzufügen einer Modellklasse</span><span class="sxs-lookup"><span data-stu-id="ecb8b-443">Add a model class</span></span>

<span data-ttu-id="ecb8b-444">Ein *Modell* ist eine Gruppe von Klassen, die die Daten darstellen, die die App verwaltet.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-444">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="ecb8b-445">Das Modell für diese App ist eine einzelne `TodoItem`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-445">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ecb8b-446">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ecb8b-446">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ecb8b-447">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-447">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="ecb8b-448">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-448">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="ecb8b-449">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-449">Name the folder *Models*.</span></span>

* <span data-ttu-id="ecb8b-450">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-450">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="ecb8b-451">Nennen Sie die Klasse *TodoItem*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-451">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="ecb8b-452">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-452">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ecb8b-453">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ecb8b-453">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ecb8b-454">Fügen Sie einen Ordner namens *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-454">Add a folder named *Models*.</span></span>

* <span data-ttu-id="ecb8b-455">Fügen Sie dem Ordner *Modelle* mit dem folgenden Code eine `TodoItem`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-455">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="ecb8b-456">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ecb8b-456">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ecb8b-457">Klicken Sie mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-457">Right-click the project.</span></span> <span data-ttu-id="ecb8b-458">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-458">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="ecb8b-459">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-459">Name the folder *Models*.</span></span>

  ![Neuer Ordner](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="ecb8b-461">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Neue Datei** > **Allgemein** > **Leere Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-461">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="ecb8b-462">Nennen Sie die Klasse *TodoItem*, und klicken Sie dann auf **Neu**.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-462">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="ecb8b-463">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-463">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="ecb8b-464">Die `Id`-Eigenschaft fungiert als eindeutiger Schlüssel in einer relationalen Datenbank.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-464">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="ecb8b-465">Modellklassen können überall im Projekt platziert werden, doch gemäß der Konvention wird der Ordner *Modelle* verwendet.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-465">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="ecb8b-466">Hinzufügen eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="ecb8b-466">Add a database context</span></span>

<span data-ttu-id="ecb8b-467">Der *Datenbankkontext* ist die Hauptklasse, die die Entity Framework-Funktionen für ein Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-467">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="ecb8b-468">Diese Klasse wird durch Ableiten von der `Microsoft.EntityFrameworkCore.DbContext`-Klasse erstellt.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-468">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ecb8b-469">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ecb8b-469">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ecb8b-470">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-470">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="ecb8b-471">Nennen Sie die Klasse *TodoContext*, und klicken Sie auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-471">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="ecb8b-472">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ecb8b-472">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="ecb8b-473">Fügen Sie eine `TodoContext`-Klasse zum Ordner *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-473">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="ecb8b-474">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-474">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="ecb8b-475">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="ecb8b-475">Register the database context</span></span>

<span data-ttu-id="ecb8b-476">In ASP.NET Core müssen Dienste wie der Datenbankkontext mit dem Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) registriert werden.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-476">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="ecb8b-477">Der Container stellt den Dienst für Controller bereit.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-477">The container provides the service to controllers.</span></span>

<span data-ttu-id="ecb8b-478">Aktualisieren Sie *Startup.cs* mit dem folgenden hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-478">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="ecb8b-479">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-479">The preceding code:</span></span>

* <span data-ttu-id="ecb8b-480">Entfernt nicht benötigte `using`-Deklarationen</span><span class="sxs-lookup"><span data-stu-id="ecb8b-480">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="ecb8b-481">Fügt dem Abhängigkeitscontainer den Datenbankkontext hinzu</span><span class="sxs-lookup"><span data-stu-id="ecb8b-481">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="ecb8b-482">Gibt an, dass der Datenbankkontext eine In-Memory Database verwendet</span><span class="sxs-lookup"><span data-stu-id="ecb8b-482">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="ecb8b-483">Hinzufügen eines Controllers</span><span class="sxs-lookup"><span data-stu-id="ecb8b-483">Add a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ecb8b-484">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ecb8b-484">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ecb8b-485">Klicken Sie mit der rechten Maustaste auf den Ordner *Controller*.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-485">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="ecb8b-486">Wählen Sie **Hinzufügen** > **Neues Element** aus.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-486">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="ecb8b-487">Wählen Sie im Dialogfeld **Neues Element hinzufügen** die Vorlage **API-Controllerklasse** aus.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-487">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="ecb8b-488">Benennen Sie die Klasse *TodoController*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-488">Name the class *TodoController*, and select **Add**.</span></span>

  ![Dialogfeld „Neues Element hinzufügen“ mit Controller im Suchfeld und ausgewähltem Web-API-Controller](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="ecb8b-490">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ecb8b-490">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="ecb8b-491">Erstellen Sie im Ordner *Controllers* eine Klasse namens `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-491">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="ecb8b-492">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-492">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="ecb8b-493">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-493">The preceding code:</span></span>

* <span data-ttu-id="ecb8b-494">Definiert eine API-Controllerklasse ohne Methoden.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-494">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="ecb8b-495">Markiert die Klasse mit dem [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute)-Attribut.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-495">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="ecb8b-496">Dieses Attribut gibt an, dass der Controller auf Web-API-Anforderungen reagiert.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-496">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="ecb8b-497">Weitere Informationen zu bestimmten Verhaltensweisen, die das Attribut ermöglicht, finden Sie unter <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-497">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="ecb8b-498">Verwendet die Abhängigkeitsinjektion, um den Datenbankkontext (`TodoContext`) dem Controller hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-498">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="ecb8b-499">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-499">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="ecb8b-500">Fügt der Datenbank, falls sie leer ist, ein Element mit dem Namen `Item1` hinzu.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-500">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="ecb8b-501">Dieser Code befindet sich im Konstruktor. Er wird bei jeder neuen HTTP-Anforderung ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-501">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="ecb8b-502">Wenn Sie alle Elemente löschen, erstellt der Konstruktor beim nächsten Aufruf einer API-Methode erneut `Item1`.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-502">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="ecb8b-503">So sieht es möglicherweise so aus, als sei die Löschung fehlgeschlagen, obwohl sie erfolgreich war.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-503">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="ecb8b-504">Hinzufügen von GET-Methoden</span><span class="sxs-lookup"><span data-stu-id="ecb8b-504">Add Get methods</span></span>

<span data-ttu-id="ecb8b-505">Fügen Sie der Klasse `TodoController` die folgenden Methoden hinzu, um eine API bereitzustellen, die Aufgaben abruft:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-505">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="ecb8b-506">Diese Methoden implementieren zwei GET-Endpunkte:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-506">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="ecb8b-507">Halten Sie die App an, falls diese noch ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-507">Stop the app if it's still running.</span></span> <span data-ttu-id="ecb8b-508">Führen Sie diese dann noch mal aus, damit die letzten Änderungen übernommen werden.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-508">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="ecb8b-509">Testen Sie die App, indem Sie die beiden Endpunkte in einem Browser aufrufen.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-509">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="ecb8b-510">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-510">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="ecb8b-511">Die folgende HTTP-Antwort wird durch den Aufruf von `GetTodoItems` erzeugt:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-511">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="ecb8b-512">Routing und URL-Pfade</span><span class="sxs-lookup"><span data-stu-id="ecb8b-512">Routing and URL paths</span></span>

<span data-ttu-id="ecb8b-513">Das Attribut [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) gibt eine Methode an, die auf eine HTTP GET-Anforderung antwortet.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-513">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="ecb8b-514">Der URL-Pfad für jede Methode wird wie folgt erstellt:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-514">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="ecb8b-515">Beginnen Sie mit der Vorlagenzeichenfolge im `Route`-Attribut des Controllers:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-515">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="ecb8b-516">Ersetzen Sie `[controller]` durch den Namen des Controllers, bei dem es sich standardmäßig um den Namen der Controller-Klasse ohne das Suffix „Controller“ handelt.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-516">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="ecb8b-517">Bei diesem Beispiel ist der Klassenname des Controllers „**Todo**Controller“, d.h. der Controllername lautet „todo“.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-517">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="ecb8b-518">Beim ASP.NET Core-[Routing](xref:mvc/controllers/routing) wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-518">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="ecb8b-519">Wenn das `[HttpGet]`-Attribut eine Routenvorlage (z. B. `[HttpGet("products")]`) hat, fügen Sie diese an den Pfad an.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-519">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="ecb8b-520">In diesem Beispiel wird keine Vorlage verwendet.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-520">This sample doesn't use a template.</span></span> <span data-ttu-id="ecb8b-521">Weitere Informationen finden Sie unter [Attributrouting mit Http[Verb]-Attributen](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="ecb8b-521">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="ecb8b-522">In der folgenden `GetTodoItem`-Methode ist `"{id}"` eine Platzhaltervariable für den eindeutigen Bezeichner des To-do-Elements.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-522">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="ecb8b-523">Wenn `GetTodoItem` aufgerufen wird, wird der Wert von `"{id}"` in der URL der Methode in ihrem Parameter `id` bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-523">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="ecb8b-524">Rückgabewerte</span><span class="sxs-lookup"><span data-stu-id="ecb8b-524">Return values</span></span>

<span data-ttu-id="ecb8b-525">Der Rückgabetyp der Methoden `GetTodoItems` und `GetTodoItem` ist [ActionResult\<T>-Typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="ecb8b-525">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="ecb8b-526">ASP.NET Core serialisiert automatisch das Objekt in [JSON](https://www.json.org/) und schreibt den JSON-Code in den Text der Antwortnachricht.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-526">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="ecb8b-527">Der Antwortcode für diesen Rückgabetyp ist 200, vorausgesetzt, es gibt keine Ausnahmefehler.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-527">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="ecb8b-528">Nicht behandelte Ausnahmen werden in 5xx-Fehler übersetzt.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-528">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="ecb8b-529">`ActionResult`-Rückgabetypen können eine Vielzahl von HTTP-Statuscodes darstellen.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-529">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="ecb8b-530">Beispielsweise kann `GetTodoItem` zwei verschiedene Statuswerte zurückgeben:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-530">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="ecb8b-531">Wenn kein Element mit der angeforderten ID übereinstimmt, gibt die Methode einen 404-Fehlercode [Nicht gefunden](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) zurück.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-531">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="ecb8b-532">Andernfalls gibt die Methode 200 mit einem JSON-Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-532">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="ecb8b-533">Die Rückgabe von `item` löst eine HTTP 200-Antwort aus.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-533">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="ecb8b-534">Testen der GetTodoItems-Methode</span><span class="sxs-lookup"><span data-stu-id="ecb8b-534">Test the GetTodoItems method</span></span>

<span data-ttu-id="ecb8b-535">Dieses Tutorial verwendet Postman zum Testen der Web-API.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-535">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="ecb8b-536">Installieren Sie [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="ecb8b-536">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="ecb8b-537">Starten Sie die Web-App.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-537">Start the web app.</span></span>
* <span data-ttu-id="ecb8b-538">Starten Sie Postman.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-538">Start Postman.</span></span>
* <span data-ttu-id="ecb8b-539">Deaktivieren Sie **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="ecb8b-539">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ecb8b-540">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ecb8b-540">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ecb8b-541">Deaktivieren Sie auf der Registerkarte **General** (Allgemein) unter File > Settings (**Datei** > **Einstellungen**) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="ecb8b-541">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="ecb8b-542">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ecb8b-542">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="ecb8b-543">Deaktivieren Sie auf der Registerkarte **General** (Allgemein) unter **Postman** > **Preferences** (Postman > Einstellungen) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="ecb8b-543">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="ecb8b-544">Wählen Sie alternativ den Schraubenschlüssel und **Settings** (Einstellungen) aus, und deaktivieren Sie dann die Überprüfung des SSL-Zertifikats.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-544">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="ecb8b-545">Aktivieren Sie die Verifizierung des SSL-Zertifikats wieder, nachdem Sie den Controller getestet haben.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-545">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="ecb8b-546">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-546">Create a new request.</span></span>
  * <span data-ttu-id="ecb8b-547">Legen Sie die HTTP-Methode auf **GET** fest.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-547">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="ecb8b-548">Legen Sie die Anforderungs-URL auf `https://localhost:<port>/api/todo` fest.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-548">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="ecb8b-549">Beispielsweise `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-549">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="ecb8b-550">Wählen Sie in Postman **Two pane view** (Ansicht mit zwei Bereichen) aus.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-550">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="ecb8b-551">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-551">Select **Send**.</span></span>

![Postman mit GET-Anforderung](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="ecb8b-553">Hinzufügen einer Methode</span><span class="sxs-lookup"><span data-stu-id="ecb8b-553">Add a Create method</span></span>

<span data-ttu-id="ecb8b-554">Fügen Sie die folgende `PostTodoItem`-Methode in *Controllers/TodoController.cs* hinzu:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-554">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="ecb8b-555">Der oben stehende Code ist eine HTTP POST-Methode, wie durch das [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute)-Attribut angegeben.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-555">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="ecb8b-556">Die Methode ruft den Wert der Aufgabe aus dem Text der HTTP-Anforderung ab.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-556">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="ecb8b-557">Die `CreatedAtAction`-Methode:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-557">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="ecb8b-558">Gibt bei einer erfolgreichen Anforderung den Statuscode „HTTP 201“ zurück.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-558">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="ecb8b-559">HTTP 201 ist die Standardantwort für eine HTTP POST-Methode, die eine neue Ressource auf dem Server erstellt.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-559">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="ecb8b-560">Fügt der Antwort einen `Location`-Header hinzu.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-560">Adds a `Location` header to the response.</span></span> <span data-ttu-id="ecb8b-561">Der `Location`-Header gibt den URI des neu erstellten To-Do-Elements zurück.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-561">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="ecb8b-562">Weitere Informationen finden Sie unter [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="ecb8b-562">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="ecb8b-563">Verweist auf die `GetTodoItem`-Aktion zum Erstellen des URIs des `Location`-Headers.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-563">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="ecb8b-564">Das `nameof`-Schlüsselwort von C# wird verwendet, um eine Hartcodierung des Aktionsnamens im `CreatedAtAction`-Aufruf zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-564">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="ecb8b-565">Testen der PostTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="ecb8b-565">Test the PostTodoItem method</span></span>

* <span data-ttu-id="ecb8b-566">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-566">Build the project.</span></span>
* <span data-ttu-id="ecb8b-567">Legen Sie in Postman die HTTP-Methode auf `POST` fest.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-567">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="ecb8b-568">Wählen Sie die Registerkarte **Body** (Text) aus.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-568">Select the **Body** tab.</span></span>
* <span data-ttu-id="ecb8b-569">Wählen Sie das Optionsfeld **raw** (Unformatiert) aus.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-569">Select the **raw** radio button.</span></span>
* <span data-ttu-id="ecb8b-570">Legen Sie den Typ auf **JSON (application/json)** fest.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-570">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="ecb8b-571">Geben Sie für die Aufgabe den Anforderungstext im JSON-Format ein:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-571">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="ecb8b-572">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-572">Select **Send**.</span></span>

  ![Postman mit erstellter Anforderung](first-web-api/_static/create.png)

  <span data-ttu-id="ecb8b-574">Wenn Sie die Fehlermeldung „405: Methode nicht zulässig“ erhalten, wurde das Projekt wahrscheinlich nicht kompiliert, nachdem die Methode `PostTodoItem` hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-574">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="ecb8b-575">Testen des Adressheader-URIs</span><span class="sxs-lookup"><span data-stu-id="ecb8b-575">Test the location header URI</span></span>

* <span data-ttu-id="ecb8b-576">Wählen Sie auf der Registerkarte **Header** (Header) den Bereich **Response** (Antwort) aus.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-576">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="ecb8b-577">Kopieren Sie den den Headerwert von **Location** (Speicherort):</span><span class="sxs-lookup"><span data-stu-id="ecb8b-577">Copy the **Location** header value:</span></span>

  ![Registerkarte „Header“ in der Postman-Konsole](first-web-api/_static/pmc2.png)

* <span data-ttu-id="ecb8b-579">Legen Sie die Methode auf „GET“ fest.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-579">Set the method to GET.</span></span>
* <span data-ttu-id="ecb8b-580">Fügen Sie den URI (z. B. `https://localhost:5001/api/Todo/2`) ein.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-580">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="ecb8b-581">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-581">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="ecb8b-582">Hinzufügen einer PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="ecb8b-582">Add a PutTodoItem method</span></span>

<span data-ttu-id="ecb8b-583">Fügen Sie die folgende `PutTodoItem`-Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-583">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="ecb8b-584">`PutTodoItem` ähnelt `PostTodoItem`, verwendet allerdings HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-584">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="ecb8b-585">Die Antwort ist [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="ecb8b-585">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="ecb8b-586">Gemäß der HTTP-Spezifikation erfordert eine PUT-Anforderung, dass der Client die gesamte aktualisierte Entität (nicht nur die Änderungen) sendet.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-586">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="ecb8b-587">Verwenden Sie [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute), um Teilupdates zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-587">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="ecb8b-588">Wenn beim Aufrufen von `PutTodoItem` ein Fehler zurückgegeben wird, rufen Sie `GET` auf, um sicherzustellen, dass die Datenbank ein Element enthält.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-588">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="ecb8b-589">Testen der PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="ecb8b-589">Test the PutTodoItem method</span></span>

<span data-ttu-id="ecb8b-590">In diesem Beispiel wird eine In-Memory-Datenbank verwendet, die jedes Mal initialisiert werden muss, wenn die App gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-590">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="ecb8b-591">Es muss ein Element in der Datenbank vorhanden sein, bevor Sie einen PUT-Aufruf durchführen.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-591">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="ecb8b-592">Rufen Sie vor einem PUT-Aufruf GET auf, um sicherzustellen, dass ein Element in der Datenbank vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-592">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="ecb8b-593">Aktualisieren Sie die Aufgabe mit der ID = 1, und setzen Sie ihren Namen auf „feed fish“:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-593">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="ecb8b-594">Die folgende Abbildung zeigt das Postman-Update:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-594">The following image shows the Postman update:</span></span>

![Postman-Konsole mit der Antwort „204 (Kein Inhalt)“](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="ecb8b-596">Hinzufügen einer DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="ecb8b-596">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="ecb8b-597">Fügen Sie die folgende `DeleteTodoItem`-Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-597">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="ecb8b-598">Die `DeleteTodoItem`-Antwort lautet [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="ecb8b-598">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="ecb8b-599">Testen der DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="ecb8b-599">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="ecb8b-600">So löschen Sie mit Postman eine Aufgabe</span><span class="sxs-lookup"><span data-stu-id="ecb8b-600">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="ecb8b-601">Legen Sie die Methode auf `DELETE` fest.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-601">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="ecb8b-602">Legen Sie den URI des zu löschenden Objekts fest, z. B. `https://localhost:5001/api/todo/1`.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-602">Set the URI of the object to delete (for example `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="ecb8b-603">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-603">Select **Send**.</span></span>

<span data-ttu-id="ecb8b-604">Sie können in der Beispiel-App alle Elemente löschen.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-604">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="ecb8b-605">Sobald das letzte Element gelöscht wurde, wird allerdings beim nächsten API-Aufruf vom Modellklassenkonstruktor ein neues erstellt.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-605">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="ecb8b-606">Aufrufen der Web-API mit JavaScript</span><span class="sxs-lookup"><span data-stu-id="ecb8b-606">Call the web API with JavaScript</span></span>

<span data-ttu-id="ecb8b-607">In diesem Abschnitt wird eine HTML-Seite hinzugefügt, die mithilfe von JavaScript die Web-API aufruft.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-607">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="ecb8b-608">jQuery initiiert die Anforderung.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-608">jQuery initiates the request.</span></span> <span data-ttu-id="ecb8b-609">JavaScript aktualisiert die Seite mit den Details aus der Antwort der Web-API.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-609">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="ecb8b-610">Konfigurieren Sie die App so, dass sie [statische Dateien bereitstellt](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_), und [aktivieren Sie die Standarddateizuordnung](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_), indem Sie *startup.cs* mit dem unten markierten Code aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-610">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="ecb8b-611">Erstellen Sie im Projektverzeichnis den Ordner *wwwwroot*.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-611">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="ecb8b-612">Fügen Sie dem Verzeichnis *wwwroot* eine HTML-Datei namens *index.html* hinzu.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-612">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="ecb8b-613">Ersetzen Sie den Inhalt durch folgendes Markup:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-613">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="ecb8b-614">Fügen Sie dem Verzeichnis *wwwroot* eine JavaScript-Datei namens *site.js* hinzu.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-614">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="ecb8b-615">Ersetzen Sie den Inhalt durch folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-615">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="ecb8b-616">Möglicherweise ist eine Änderung an den Starteinstellungen des ASP.NET Core-Projekts erforderlich, um die HTML-Seite lokal zu testen:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-616">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="ecb8b-617">Öffnen Sie *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-617">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="ecb8b-618">Entfernen Sie die `launchUrl`-Eigenschaft, um zu erzwingen, dass die App mit *index.html* als Startseite geöffnet wird. Dies ist die Standarddatei des Projekts.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-618">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="ecb8b-619">Dieses Beispiel ruft alle CRUD-Methoden der Web-API auf.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-619">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="ecb8b-620">Im Folgenden werden die API-Aufrufe erläutert.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-620">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="ecb8b-621">Abrufen einer Liste von To-Do-Elementen</span><span class="sxs-lookup"><span data-stu-id="ecb8b-621">Get a list of to-do items</span></span>

<span data-ttu-id="ecb8b-622">jQuery sendet eine HTTP GET-Anforderung an die Web-API, die JSON-Code mit einem Array aus To-Do-Elementen zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-622">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="ecb8b-623">Die Rückruffunktion `success` wird aufgerufen, wenn die Anforderung erfolgreich ist.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-623">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="ecb8b-624">Im Rückruf wird DOM mit den To-Do-Informationen aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-624">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="ecb8b-625">Hinzufügen eines To-Do-Elements</span><span class="sxs-lookup"><span data-stu-id="ecb8b-625">Add a to-do item</span></span>

<span data-ttu-id="ecb8b-626">jQuery sendet eine HTTP POST-Anforderung mit dem To-Do-Element im Anforderungstext.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-626">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="ecb8b-627">Die Optionen `accepts` und `contentType` werden auf `application/json` festgelegt, um den gesendeten und empfangenen Medientyp anzugeben.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-627">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="ecb8b-628">Die Aufgabe wird mit [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) in JSON konvertiert.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-628">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="ecb8b-629">Wenn die API den Statuscode „Erfolgreich“ zurückgibt, wird die Funktion `getData` aufgerufen, um die HTML-Tabelle zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-629">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="ecb8b-630">Aktualisieren eines To-Do-Elements</span><span class="sxs-lookup"><span data-stu-id="ecb8b-630">Update a to-do item</span></span>

<span data-ttu-id="ecb8b-631">Das Aktualisieren einer Aufgabe ist vergleichbar mit dem Hinzufügen einer Aufgabe.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-631">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="ecb8b-632">Die `url` ändert sich, um den eindeutigen Bezeichner des Elements hinzuzufügen. Der `type` lautet `PUT`.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-632">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="ecb8b-633">Löschen eines To-Do-Elements</span><span class="sxs-lookup"><span data-stu-id="ecb8b-633">Delete a to-do item</span></span>

<span data-ttu-id="ecb8b-634">Eine Aufgabe wird folgendermaßen gelöscht: im AJAX-Aufruf wird `type` auf `DELETE` festgelegt, und der eindeutige Bezeichner des Elements wird in der URL angegeben.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-634">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="ecb8b-635">Hinzufügen der Authentifizierungsunterstützung zu einer Web-API</span><span class="sxs-lookup"><span data-stu-id="ecb8b-635">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="ecb8b-636">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="ecb8b-636">Additional resources</span></span>

<span data-ttu-id="ecb8b-637">[Sie können den Beispielcode für dieses Tutorial anzeigen oder herunterladen.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)</span><span class="sxs-lookup"><span data-stu-id="ecb8b-637">[View or download sample code for this tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="ecb8b-638">[Informationen zum Herunterladen](xref:index#how-to-download-a-sample) finden Sie hier.</span><span class="sxs-lookup"><span data-stu-id="ecb8b-638">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="ecb8b-639">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="ecb8b-639">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="ecb8b-640">Dieses Tutorial auf YouTube</span><span class="sxs-lookup"><span data-stu-id="ecb8b-640">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)

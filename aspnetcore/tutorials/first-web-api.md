---
title: 'Tutorial: Erstellen einer Web-API mit ASP.NET Core'
author: rick-anderson
description: Informationen zum Erstellen einer Web-API mit ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 08/05/2019
uid: tutorials/first-web-api
ms.openlocfilehash: 855d05fa2b9c1a7572212c40adbe61bb396f4bac
ms.sourcegitcommit: 2eb605f4f20ac4dd9de6c3b3e3453e108a357a21
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68819833"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="122aa-103">Tutorial: Erstellen einer Web-API mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="122aa-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="122aa-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="122aa-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="122aa-105">In diesem Tutorial lernen Sie die Grundlagen der Erstellung einer Web-API mit ASP.NET Core kennen.</span><span class="sxs-lookup"><span data-stu-id="122aa-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="122aa-106">In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="122aa-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="122aa-107">Erstellen eines Web-API-Projekts</span><span class="sxs-lookup"><span data-stu-id="122aa-107">Create a web API project.</span></span>
> * <span data-ttu-id="122aa-108">Hinzufügen einer Modellklasse und eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="122aa-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="122aa-109">Erstellen eines Controllergerüsts mit CRUD-Methoden</span><span class="sxs-lookup"><span data-stu-id="122aa-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="122aa-110">Konfigurieren des Routings, der URL-Pfade und der Rückgabewerte</span><span class="sxs-lookup"><span data-stu-id="122aa-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="122aa-111">Aufrufen der Web-API mit Postman</span><span class="sxs-lookup"><span data-stu-id="122aa-111">Call the web API with Postman.</span></span>

<span data-ttu-id="122aa-112">Abschließend steht Ihnen eine Web-API zur Verfügung, die in einer relationalen Datenbank gespeicherte To-do-Elemente verwalten kann.</span><span class="sxs-lookup"><span data-stu-id="122aa-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="122aa-113">Übersicht</span><span class="sxs-lookup"><span data-stu-id="122aa-113">Overview</span></span>

<span data-ttu-id="122aa-114">In diesem Tutorial wird die folgende API erstellt:</span><span class="sxs-lookup"><span data-stu-id="122aa-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="122aa-115">API</span><span class="sxs-lookup"><span data-stu-id="122aa-115">API</span></span> | <span data-ttu-id="122aa-116">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="122aa-116">Description</span></span> | <span data-ttu-id="122aa-117">Anforderungstext</span><span class="sxs-lookup"><span data-stu-id="122aa-117">Request body</span></span> | <span data-ttu-id="122aa-118">Antworttext</span><span class="sxs-lookup"><span data-stu-id="122aa-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="122aa-119">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="122aa-119">GET /api/TodoItems</span></span> | <span data-ttu-id="122aa-120">Alle To-do-Elemente abrufen</span><span class="sxs-lookup"><span data-stu-id="122aa-120">Get all to-do items</span></span> | <span data-ttu-id="122aa-121">Keine</span><span class="sxs-lookup"><span data-stu-id="122aa-121">None</span></span> | <span data-ttu-id="122aa-122">Array von To-do-Elementen</span><span class="sxs-lookup"><span data-stu-id="122aa-122">Array of to-do items</span></span>|
|<span data-ttu-id="122aa-123">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="122aa-123">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="122aa-124">Ein Element nach ID abrufen</span><span class="sxs-lookup"><span data-stu-id="122aa-124">Get an item by ID</span></span> | <span data-ttu-id="122aa-125">Keine</span><span class="sxs-lookup"><span data-stu-id="122aa-125">None</span></span> | <span data-ttu-id="122aa-126">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="122aa-126">To-do item</span></span>|
|<span data-ttu-id="122aa-127">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="122aa-127">POST /api/TodoItems</span></span> | <span data-ttu-id="122aa-128">Neues Element hinzufügen</span><span class="sxs-lookup"><span data-stu-id="122aa-128">Add a new item</span></span> | <span data-ttu-id="122aa-129">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="122aa-129">To-do item</span></span> | <span data-ttu-id="122aa-130">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="122aa-130">To-do item</span></span> |
|<span data-ttu-id="122aa-131">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="122aa-131">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="122aa-132">Vorhandenes Element aktualisieren &nbsp;</span><span class="sxs-lookup"><span data-stu-id="122aa-132">Update an existing item &nbsp;</span></span> | <span data-ttu-id="122aa-133">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="122aa-133">To-do item</span></span> | <span data-ttu-id="122aa-134">Keine</span><span class="sxs-lookup"><span data-stu-id="122aa-134">None</span></span> |
|<span data-ttu-id="122aa-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="122aa-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="122aa-136">Löschen eines Elements &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="122aa-136">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="122aa-137">Keine</span><span class="sxs-lookup"><span data-stu-id="122aa-137">None</span></span> | <span data-ttu-id="122aa-138">Keine</span><span class="sxs-lookup"><span data-stu-id="122aa-138">None</span></span>|

<span data-ttu-id="122aa-139">Das folgende Diagramm zeigt den Entwurf der App.</span><span class="sxs-lookup"><span data-stu-id="122aa-139">The following diagram shows the design of the app.</span></span>

![Der Client ist das Feld ganz links.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="122aa-145">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="122aa-145">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="122aa-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="122aa-146">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="122aa-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="122aa-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="122aa-148">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="122aa-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="122aa-149">Erstellen eines Webprojekts</span><span class="sxs-lookup"><span data-stu-id="122aa-149">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="122aa-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="122aa-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="122aa-151">Klicken Sie im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="122aa-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="122aa-152">Wählen Sie die Vorlage **ASP.NET Core-Webanwendung** aus, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="122aa-152">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="122aa-153">Geben Sie dem Projekt den Namen *TodoApi*, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="122aa-153">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="122aa-154">Vergewissern Sie sich, dass im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Optionen **.NET Core** und **ASP.NET Core 3.0** ausgewählt sind.</span><span class="sxs-lookup"><span data-stu-id="122aa-154">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span> <span data-ttu-id="122aa-155">Wählen Sie die Vorlage **API** aus, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="122aa-155">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="122aa-156">Wählen Sie **nicht** **Enable Docker Support** (Docker-Unterstützung aktivieren) aus.</span><span class="sxs-lookup"><span data-stu-id="122aa-156">**Don't** select **Enable Docker Support**.</span></span>

![VS-Dialogfeld „Neues Projekt“](first-web-api/_static/vs3.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="122aa-158">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="122aa-158">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="122aa-159">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="122aa-159">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="122aa-160">Wechseln Sie vom Verzeichnis (`cd`) zum Ordner, der den Projektordner enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="122aa-160">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="122aa-161">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="122aa-161">Run the following commands:</span></span>

   ```console
   dotnet new webapi -o TodoApi
   cd TodoAPI
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.0.0-*
   dotnet add package Microsoft.EntityFrameworkCore.InMemory --version 3.0.0-*
   code -r ../TodoApi
   ```

* <span data-ttu-id="122aa-162">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem Projekt die erforderlichen Elemente hinzufügen möchten, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="122aa-162">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="122aa-163">Die obenstehenden Befehle haben folgende Konsequenzen:</span><span class="sxs-lookup"><span data-stu-id="122aa-163">The preceding commands:</span></span>

  * <span data-ttu-id="122aa-164">Ein neues Web-API-Projekt wird erstellt und in Visual Studio Code geöffnet.</span><span class="sxs-lookup"><span data-stu-id="122aa-164">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="122aa-165">Die NuGet-Pakete, die im nächsten Abschnitt erforderlich sind, werden hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="122aa-165">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="122aa-166">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="122aa-166">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="122aa-167">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="122aa-167">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="122aa-169">Klicken Sie auf **.NET Core** > **App** > **API** > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="122aa-169">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![Dialogfeld „Neue Projektmappe“ in macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="122aa-171">Wählen Sie im Dialogfeld **Configure your new ASP.NET Core Web API** (Neue ASP.NET Core-Web-API konfigurieren) für das **Zielframework** den Eintrag \* *.NET Core 3.0* aus.</span><span class="sxs-lookup"><span data-stu-id="122aa-171">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.0*.</span></span>

* <span data-ttu-id="122aa-172">Geben Sie für **Projektname** *TodoApi* ein, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="122aa-172">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogfeld „Konfiguration“](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="122aa-174">Testen der API</span><span class="sxs-lookup"><span data-stu-id="122aa-174">Test the API</span></span>

<span data-ttu-id="122aa-175">Die Projektvorlage erstellt eine `WeatherForecast`-API.</span><span class="sxs-lookup"><span data-stu-id="122aa-175">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="122aa-176">Rufen Sie in einem Browser die `Get`-Methode zum Testen der App auf.</span><span class="sxs-lookup"><span data-stu-id="122aa-176">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="122aa-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="122aa-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="122aa-178">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="122aa-178">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="122aa-179">Visual Studio startet einen Browser und navigiert zu `https://localhost:<port>/WeatherForecast`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="122aa-179">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="122aa-180">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem IIS Express-Zertifikat vertrauen, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="122aa-180">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="122aa-181">Wählen Sie im folgenden Dialogfeld **Sicherheitswarnung** die Option **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="122aa-181">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="122aa-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="122aa-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="122aa-183">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="122aa-183">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="122aa-184">Rufen Sie in einem Browser die folgende URL auf: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span><span class="sxs-lookup"><span data-stu-id="122aa-184">In a browser, go to following URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="122aa-185">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="122aa-185">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="122aa-186">Wählen Sie **Run** > **Start Debugging** (Ausführen > Debugging starten) aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="122aa-186">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="122aa-187">Visual Studio für Mac startet einen Browser und navigiert zu `https://localhost:<port>`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="122aa-187">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="122aa-188">Der Fehler „HTTP 404: Nicht gefunden“ wird zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="122aa-188">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="122aa-189">Fügen Sie der URL `/WeatherForecast` an, d.h. ändern Sie die URL in `https://localhost:<port>/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="122aa-189">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="122aa-190">Der zurückgegebene JSON-Code sieht in etwa wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="122aa-190">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="122aa-191">Hinzufügen einer Modellklasse</span><span class="sxs-lookup"><span data-stu-id="122aa-191">Add a model class</span></span>

<span data-ttu-id="122aa-192">Ein *Modell* ist eine Gruppe von Klassen, die die Daten darstellen, die die App verwaltet.</span><span class="sxs-lookup"><span data-stu-id="122aa-192">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="122aa-193">Das Modell für diese App ist eine einzelne `TodoItem`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="122aa-193">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="122aa-194">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="122aa-194">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="122aa-195">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="122aa-195">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="122aa-196">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="122aa-196">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="122aa-197">Geben Sie dem Ordner den Namen *Models*.</span><span class="sxs-lookup"><span data-stu-id="122aa-197">Name the folder *Models*.</span></span>

* <span data-ttu-id="122aa-198">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="122aa-198">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="122aa-199">Nennen Sie die Klasse *TodoItem*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="122aa-199">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="122aa-200">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="122aa-200">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="122aa-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="122aa-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="122aa-202">Fügen Sie einen Ordner namens *Models* hinzu.</span><span class="sxs-lookup"><span data-stu-id="122aa-202">Add a folder named *Models*.</span></span>

* <span data-ttu-id="122aa-203">Fügen Sie dem Ordner *Modelle* mit dem folgenden Code eine `TodoItem`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="122aa-203">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="122aa-204">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="122aa-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="122aa-205">Klicken Sie mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="122aa-205">Right-click the project.</span></span> <span data-ttu-id="122aa-206">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="122aa-206">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="122aa-207">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="122aa-207">Name the folder *Models*.</span></span>

  ![Neuer Ordner](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="122aa-209">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und klicken Sie auf **Hinzufügen** > **Neue Datei** > **Allgemein** > **Leere Klasse**.</span><span class="sxs-lookup"><span data-stu-id="122aa-209">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="122aa-210">Nennen Sie die Klasse *TodoItem*, und klicken Sie dann auf **Neu**.</span><span class="sxs-lookup"><span data-stu-id="122aa-210">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="122aa-211">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="122aa-211">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="122aa-212">Die `Id`-Eigenschaft fungiert als eindeutiger Schlüssel in einer relationalen Datenbank.</span><span class="sxs-lookup"><span data-stu-id="122aa-212">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="122aa-213">Modellklassen können überall im Projekt platziert werden, doch gemäß der Konvention wird der Ordner *Modelle* verwendet.</span><span class="sxs-lookup"><span data-stu-id="122aa-213">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="122aa-214">Hinzufügen eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="122aa-214">Add a database context</span></span>

<span data-ttu-id="122aa-215">Der *Datenbankkontext* ist die Hauptklasse, die die Entity Framework-Funktionen für ein Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="122aa-215">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="122aa-216">Diese Klasse wird durch Ableiten von der `Microsoft.EntityFrameworkCore.DbContext`-Klasse erstellt.</span><span class="sxs-lookup"><span data-stu-id="122aa-216">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="122aa-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="122aa-217">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="122aa-218">Hinzufügen von Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="122aa-218">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="122aa-219">Klicken Sie im Menü **Extras** auf **NuGet-Paket-Manager > NuGet-Pakete für Projektmappe verwalten...** .</span><span class="sxs-lookup"><span data-stu-id="122aa-219">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="122aa-220">Aktivieren Sie das Kontrollkästchen **Vorabversion einbeziehen**.</span><span class="sxs-lookup"><span data-stu-id="122aa-220">Select the **Include prerelease** checkbox.</span></span>
* <span data-ttu-id="122aa-221">Klicken Sie auf die Registerkarte **Durchsuchen**, und geben Sie dann **Microsoft.EntityFrameworkCore.SqlServer** in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="122aa-221">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="122aa-222">Wählen Sie im linken Bereich **Microsoft.EntityFrameworkCore.SqlServer V3.0.0-preview** aus.</span><span class="sxs-lookup"><span data-stu-id="122aa-222">Select  **Microsoft.EntityFrameworkCore.SqlServer V3.0.0-preview** in the left pane.</span></span>
* <span data-ttu-id="122aa-223">Aktivieren Sie das Kontrollkästchen **Projekt** im rechten Bereich, und klicken Sie dann auf **Installieren**.</span><span class="sxs-lookup"><span data-stu-id="122aa-223">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="122aa-224">Fügen Sie mithilfe der obigen Schritte das `Microsoft.EntityFrameworkCore.InMemory`-NuGet-Paket hinzu.</span><span class="sxs-lookup"><span data-stu-id="122aa-224">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![NuGet-Paket-Manager](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="122aa-226">Hinzufügen des TodoContext-Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="122aa-226">Add the TodoContext database context</span></span>

* <span data-ttu-id="122aa-227">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="122aa-227">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="122aa-228">Nennen Sie die Klasse *TodoContext*, und klicken Sie auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="122aa-228">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="122aa-229">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="122aa-229">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="122aa-230">Fügen Sie eine `TodoContext`-Klasse zum Ordner *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="122aa-230">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="122aa-231">Geben Sie den folgenden Code ein:</span><span class="sxs-lookup"><span data-stu-id="122aa-231">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="122aa-232">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="122aa-232">Register the database context</span></span>

<span data-ttu-id="122aa-233">In ASP.NET Core müssen Dienste wie der Datenbankkontext mit dem Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) registriert werden.</span><span class="sxs-lookup"><span data-stu-id="122aa-233">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="122aa-234">Der Container stellt den Dienst für Controller bereit.</span><span class="sxs-lookup"><span data-stu-id="122aa-234">The container provides the service to controllers.</span></span>

<span data-ttu-id="122aa-235">Aktualisieren Sie *Startup.cs* mit dem folgenden hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="122aa-235">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="122aa-236">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="122aa-236">The preceding code:</span></span>

* <span data-ttu-id="122aa-237">Entfernt nicht benötigte `using`-Deklarationen</span><span class="sxs-lookup"><span data-stu-id="122aa-237">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="122aa-238">Fügt dem Abhängigkeitscontainer den Datenbankkontext hinzu</span><span class="sxs-lookup"><span data-stu-id="122aa-238">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="122aa-239">Gibt an, dass der Datenbankkontext eine In-Memory Database verwendet</span><span class="sxs-lookup"><span data-stu-id="122aa-239">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="122aa-240">Erstellen eines Controllergerüsts</span><span class="sxs-lookup"><span data-stu-id="122aa-240">Scaffold a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="122aa-241">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="122aa-241">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="122aa-242">Klicken Sie mit der rechten Maustaste auf den Ordner *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="122aa-242">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="122aa-243">Klicken Sie auf **Hinzufügen** > **New Scaffolded Item** (Neues Gerüstelement).</span><span class="sxs-lookup"><span data-stu-id="122aa-243">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="122aa-244">Klicken Sie auf **API-Controller mit Aktionen unter Verwendung von Entity Framework** und dann auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="122aa-244">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="122aa-245">Führen Sie im Dialogfeld **API-Controller mit Aktionen unter Verwendung von Entity Framework** folgende Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="122aa-245">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="122aa-246">Wählen Sie in der **Modellklasse** das Element **TodoItem (TodoAPI.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="122aa-246">Select **TodoItem (TodoAPI.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="122aa-247">Wählen Sie in der **Datenkontextklasse** das Element **TodoContext (TodoAPI.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="122aa-247">Select **TodoContext (TodoAPI.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="122aa-248">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="122aa-248">Select **Add**</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="122aa-249">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="122aa-249">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="122aa-250">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="122aa-250">Run the following commands:</span></span>

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.0.0-*
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext  -outDir Controllers
```

<span data-ttu-id="122aa-251">Die obenstehenden Befehle haben folgende Konsequenzen:</span><span class="sxs-lookup"><span data-stu-id="122aa-251">The preceding commands:</span></span>

* <span data-ttu-id="122aa-252">Die NuGet-Pakete für den Gerüstbau werden hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="122aa-252">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="122aa-253">Die Gerüstbau-Engine (`dotnet-aspnet-codegenerator`) wird installiert.</span><span class="sxs-lookup"><span data-stu-id="122aa-253">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="122aa-254">Das Gerüst für `TodoItemsController` wird erstellt.</span><span class="sxs-lookup"><span data-stu-id="122aa-254">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="122aa-255">Der generierte Code hat folgende Auswirkungen:</span><span class="sxs-lookup"><span data-stu-id="122aa-255">The generated code:</span></span>

* <span data-ttu-id="122aa-256">Er definiert eine API-Controllerklasse ohne Methoden.</span><span class="sxs-lookup"><span data-stu-id="122aa-256">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="122aa-257">Ergänzt die Klasse mit dem Attribut [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute).</span><span class="sxs-lookup"><span data-stu-id="122aa-257">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="122aa-258">Dieses Attribut gibt an, dass der Controller auf Web-API-Anforderungen reagiert.</span><span class="sxs-lookup"><span data-stu-id="122aa-258">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="122aa-259">Weitere Informationen zu bestimmten Verhaltensweisen, die das Attribut ermöglicht, finden Sie unter <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="122aa-259">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="122aa-260">Verwendet die Abhängigkeitsinjektion, um den Datenbankkontext (`TodoContext`) dem Controller hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="122aa-260">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="122aa-261">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="122aa-261">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="122aa-262">Überblick über die PostTodoItem-Erstellungsmethode</span><span class="sxs-lookup"><span data-stu-id="122aa-262">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="122aa-263">Ersetzen Sie die Rückgabeanweisung in `PostTodoItem` durch eine Anweisung mit dem [nameof](/dotnet/csharp/language-reference/operators/nameof)-Operator:</span><span class="sxs-lookup"><span data-stu-id="122aa-263">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="122aa-264">Der vorherige Code ist eine HTTP POST-Methode, die durch das Attribut [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="122aa-264">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="122aa-265">Die Methode ruft den Wert der Aufgabe aus dem Text der HTTP-Anforderung ab.</span><span class="sxs-lookup"><span data-stu-id="122aa-265">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="122aa-266">Die <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>-Methode:</span><span class="sxs-lookup"><span data-stu-id="122aa-266">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="122aa-267">Sie gibt den HTTP-Statuscode 201 zurück, wenn der Vorgang erfolgreich ist.</span><span class="sxs-lookup"><span data-stu-id="122aa-267">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="122aa-268">HTTP 201 ist die Standardantwort für eine HTTP POST-Methode, die eine neue Ressource auf dem Server erstellt.</span><span class="sxs-lookup"><span data-stu-id="122aa-268">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="122aa-269">Sie fügt der Antwort einen [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location)-Header hinzu.</span><span class="sxs-lookup"><span data-stu-id="122aa-269">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="122aa-270">Der `Location`-Header legt den [URI](https://developer.mozilla.org/docs/Glossary/URI) des neu erstellten To-do-Elements fest.</span><span class="sxs-lookup"><span data-stu-id="122aa-270">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="122aa-271">Weitere Informationen finden Sie unter [10.2.2 201 Erstellt](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="122aa-271">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="122aa-272">Verweist auf die `GetTodoItem`-Aktion zum Erstellen des URIs des `Location`-Headers.</span><span class="sxs-lookup"><span data-stu-id="122aa-272">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="122aa-273">Das `nameof`-Schlüsselwort von C# wird verwendet, um eine Hartcodierung des Aktionsnamens im `CreatedAtAction`-Aufruf zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="122aa-273">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="122aa-274">Installieren von Postman</span><span class="sxs-lookup"><span data-stu-id="122aa-274">Install Postman</span></span>

<span data-ttu-id="122aa-275">Dieses Tutorial verwendet Postman zum Testen der Web-API.</span><span class="sxs-lookup"><span data-stu-id="122aa-275">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="122aa-276">Installieren Sie [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="122aa-276">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="122aa-277">Starten Sie die Web-App.</span><span class="sxs-lookup"><span data-stu-id="122aa-277">Start the web app.</span></span>
* <span data-ttu-id="122aa-278">Starten Sie Postman.</span><span class="sxs-lookup"><span data-stu-id="122aa-278">Start Postman.</span></span>
* <span data-ttu-id="122aa-279">Deaktivieren Sie **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="122aa-279">Disable **SSL certificate verification**</span></span>
* <span data-ttu-id="122aa-280">Deaktivieren Sie auf der Registerkarte \**General* (Allgemein) unter **File > Settings** (Datei > Einstellungen) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="122aa-280">From  **File > Settings** (\**General* tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="122aa-281">Aktivieren Sie die Verifizierung des SSL-Zertifikats wieder, nachdem Sie den Controller getestet haben.</span><span class="sxs-lookup"><span data-stu-id="122aa-281">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="122aa-282">Testen von PostTodoItem mit Postman</span><span class="sxs-lookup"><span data-stu-id="122aa-282">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="122aa-283">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="122aa-283">Create a new request.</span></span>
* <span data-ttu-id="122aa-284">Legen Sie als HTTP-Methode `POST` fest.</span><span class="sxs-lookup"><span data-stu-id="122aa-284">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="122aa-285">Wählen Sie die Registerkarte **Body** (Text) aus.</span><span class="sxs-lookup"><span data-stu-id="122aa-285">Select the **Body** tab.</span></span>
* <span data-ttu-id="122aa-286">Wählen Sie das Optionsfeld **raw** (Unformatiert) aus.</span><span class="sxs-lookup"><span data-stu-id="122aa-286">Select the **raw** radio button.</span></span>
* <span data-ttu-id="122aa-287">Legen Sie den Typ auf **JSON (application/json)** fest.</span><span class="sxs-lookup"><span data-stu-id="122aa-287">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="122aa-288">Geben Sie für die Aufgabe den Anforderungstext im JSON-Format ein:</span><span class="sxs-lookup"><span data-stu-id="122aa-288">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="122aa-289">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="122aa-289">Select **Send**.</span></span>

  ![Postman mit erstellter Anforderung](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="122aa-291">Testen des Adressheader-URIs</span><span class="sxs-lookup"><span data-stu-id="122aa-291">Test the location header URI</span></span>

* <span data-ttu-id="122aa-292">Wählen Sie auf der Registerkarte **Header** (Header) den Bereich **Response** (Antwort) aus.</span><span class="sxs-lookup"><span data-stu-id="122aa-292">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="122aa-293">Kopieren Sie den den Headerwert von **Location** (Speicherort):</span><span class="sxs-lookup"><span data-stu-id="122aa-293">Copy the **Location** header value:</span></span>

  ![Registerkarte „Header“ in der Postman-Konsole](first-web-api/_static/3/create.png)

* <span data-ttu-id="122aa-295">Legen Sie die Methode auf „GET“ fest.</span><span class="sxs-lookup"><span data-stu-id="122aa-295">Set the method to GET.</span></span>
* <span data-ttu-id="122aa-296">Fügen Sie den URI (z. B. `https://localhost:5001/api/TodoItems/1`) ein.</span><span class="sxs-lookup"><span data-stu-id="122aa-296">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`)</span></span>
* <span data-ttu-id="122aa-297">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="122aa-297">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="122aa-298">Überblick über die GET-Methoden</span><span class="sxs-lookup"><span data-stu-id="122aa-298">Examine the GET methods</span></span>

<span data-ttu-id="122aa-299">Diese Methoden implementieren zwei GET-Endpunkte:</span><span class="sxs-lookup"><span data-stu-id="122aa-299">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="122aa-300">Testen Sie die App, indem Sie die beiden Endpunkte in einem Browser oder über Postman aufrufen.</span><span class="sxs-lookup"><span data-stu-id="122aa-300">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="122aa-301">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="122aa-301">For example:</span></span>

* [https://localhost:5001/api/TodoItems](https://localhost:5001/api/TodoItems)
* [https://localhost:5001/api/TodoItems/1](https://localhost:5001/api/TodoItems/1)

<span data-ttu-id="122aa-302">Durch einen Aufruf von `GetTodoItems` wird eine Antwort gesendet, die der folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="122aa-302">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="122aa-303">Testen von GET mit Postman</span><span class="sxs-lookup"><span data-stu-id="122aa-303">Test Get with Postman</span></span>

* <span data-ttu-id="122aa-304">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="122aa-304">Create a new request.</span></span>
* <span data-ttu-id="122aa-305">Legen Sie die HTTP-Methode auf **GET** fest.</span><span class="sxs-lookup"><span data-stu-id="122aa-305">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="122aa-306">Legen Sie die Anforderungs-URL auf `https://localhost:<port>/api/TodoItems` fest.</span><span class="sxs-lookup"><span data-stu-id="122aa-306">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="122aa-307">Beispielsweise `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="122aa-307">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="122aa-308">Wählen Sie in Postman **Two pane view** (Ansicht mit zwei Bereichen) aus.</span><span class="sxs-lookup"><span data-stu-id="122aa-308">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="122aa-309">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="122aa-309">Select **Send**.</span></span>

<span data-ttu-id="122aa-310">Diese App verwendet eine In-Memory-Datenbank.</span><span class="sxs-lookup"><span data-stu-id="122aa-310">This app uses an in-memory database.</span></span> <span data-ttu-id="122aa-311">Wenn die App angehalten und dann wieder gestartet wird, werden durch die vorherige GET-Anforderung keine Daten zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="122aa-311">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="122aa-312">Wenn keine Daten zurückgegeben werden, senden Sie mit [POST](#post) Daten an die App.</span><span class="sxs-lookup"><span data-stu-id="122aa-312">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="122aa-313">Routing und URL-Pfade</span><span class="sxs-lookup"><span data-stu-id="122aa-313">Routing and URL paths</span></span>

<span data-ttu-id="122aa-314">Das Attribut [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) gibt eine Methode an, die auf eine HTTP GET-Anforderung antwortet.</span><span class="sxs-lookup"><span data-stu-id="122aa-314">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="122aa-315">Der URL-Pfad für jede Methode wird wie folgt erstellt:</span><span class="sxs-lookup"><span data-stu-id="122aa-315">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="122aa-316">Beginnen Sie mit der Vorlagenzeichenfolge im `Route`-Attribut des Controllers:</span><span class="sxs-lookup"><span data-stu-id="122aa-316">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="122aa-317">Ersetzen Sie `[controller]` durch den Namen des Controllers, bei dem es sich standardmäßig um den Namen der Controller-Klasse ohne das Suffix „Controller“ handelt.</span><span class="sxs-lookup"><span data-stu-id="122aa-317">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="122aa-318">In diesem Beispiel ist der Klassenname des Controllers „**TodoItems**Controller“. Der Controllername lautet also „TodoItems“.</span><span class="sxs-lookup"><span data-stu-id="122aa-318">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="122aa-319">Beim ASP.NET Core-[Routing](xref:mvc/controllers/routing) wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="122aa-319">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="122aa-320">Wenn das `[HttpGet]`-Attribut eine Routenvorlage (z.B. `[HttpGet("products")]`) hat, fügen Sie diese an den Pfad an.</span><span class="sxs-lookup"><span data-stu-id="122aa-320">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="122aa-321">In diesem Beispiel wird keine Vorlage verwendet.</span><span class="sxs-lookup"><span data-stu-id="122aa-321">This sample doesn't use a template.</span></span> <span data-ttu-id="122aa-322">Weitere Informationen finden Sie unter [Attributrouting mit Http[Verb]-Attributen](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="122aa-322">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="122aa-323">In der folgenden `GetTodoItem`-Methode ist `"{id}"` eine Platzhaltervariable für den eindeutigen Bezeichners des To-do-Elements.</span><span class="sxs-lookup"><span data-stu-id="122aa-323">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="122aa-324">Wenn `GetTodoItem` aufgerufen wird, wird der Wert von `"{id}"` in der URL der Methode in ihrem Parameter `id` bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="122aa-324">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="122aa-325">Rückgabewert</span><span class="sxs-lookup"><span data-stu-id="122aa-325">Return values</span></span>

<span data-ttu-id="122aa-326">Der Rückgabetyp der Methoden `GetTodoItems` und `GetTodoItem` ist [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="122aa-326">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="122aa-327">ASP.NET Core serialisiert automatisch das Objekt in [JSON](https://www.json.org/) und schreibt den JSON-Code in den Text der Antwortnachricht.</span><span class="sxs-lookup"><span data-stu-id="122aa-327">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="122aa-328">Der Antwortcode für diesen Rückgabetyp ist 200, vorausgesetzt, es gibt keine Ausnahmefehler.</span><span class="sxs-lookup"><span data-stu-id="122aa-328">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="122aa-329">Nicht behandelte Ausnahmen werden in 5xx-Fehler übersetzt.</span><span class="sxs-lookup"><span data-stu-id="122aa-329">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="122aa-330">`ActionResult`-Rückgabetypen können eine Vielzahl von HTTP-Statuscodes darstellen.</span><span class="sxs-lookup"><span data-stu-id="122aa-330">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="122aa-331">Beispielsweise kann `GetTodoItem` zwei verschiedene Statuswerte zurückgeben:</span><span class="sxs-lookup"><span data-stu-id="122aa-331">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="122aa-332">Wenn kein Element mit der angeforderten ID übereinstimmt, gibt die Methode einen 404-Fehlercode [Nicht gefunden](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) zurück.</span><span class="sxs-lookup"><span data-stu-id="122aa-332">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="122aa-333">Andernfalls gibt die Methode 200 mit einem JSON-Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="122aa-333">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="122aa-334">Die Rückgabe von `item` löst eine HTTP 200-Antwort aus.</span><span class="sxs-lookup"><span data-stu-id="122aa-334">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="122aa-335">PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="122aa-335">The PutTodoItem method</span></span>

<span data-ttu-id="122aa-336">Untersuchen Sie die `PutTodoItem`-Methode.</span><span class="sxs-lookup"><span data-stu-id="122aa-336">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="122aa-337">`PutTodoItem` ähnelt `PostTodoItem`, verwendet allerdings HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="122aa-337">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="122aa-338">Die Antwort ist [204 (Kein Inhalt)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="122aa-338">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="122aa-339">Gemäß der HTTP-Spezifikation erfordert eine PUT-Anforderung, dass der Client die gesamte aktualisierte Entität (nicht nur die Änderungen) sendet.</span><span class="sxs-lookup"><span data-stu-id="122aa-339">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="122aa-340">Verwenden Sie [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute), um Teilupdates zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="122aa-340">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="122aa-341">Wenn beim Aufrufen von `PutTodoItem` ein Fehler zurückgegeben wird, rufen Sie `GET` auf, um sicherzustellen, dass die Datenbank ein Element enthält.</span><span class="sxs-lookup"><span data-stu-id="122aa-341">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="122aa-342">Testen der PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="122aa-342">Test the PutTodoItem method</span></span>

<span data-ttu-id="122aa-343">In diesem Beispiel wird eine In-Memory Database verwendet, die jedes Mal initialisiert werden muss, wenn die App gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="122aa-343">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="122aa-344">Es muss ein Element in der Datenbank vorhanden sein, bevor Sie einen PUT-Aufruf durchführen.</span><span class="sxs-lookup"><span data-stu-id="122aa-344">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="122aa-345">Rufen Sie vor einem PUT-Aufruf GET auf, um sicherzustellen, dass ein Element in der Datenbank vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="122aa-345">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="122aa-346">Aktualisieren Sie das To-do-Element, das über den ID-Wert 1 verfügt, und legen Sie als Namen „feed fish“ fest:</span><span class="sxs-lookup"><span data-stu-id="122aa-346">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="122aa-347">Die folgende Abbildung zeigt das Postman-Update:</span><span class="sxs-lookup"><span data-stu-id="122aa-347">The following image shows the Postman update:</span></span>

![Postman-Konsole mit der Antwort „204 (Kein Inhalt)“](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="122aa-349">DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="122aa-349">The DeleteTodoItem method</span></span>

<span data-ttu-id="122aa-350">Untersuchen Sie die `DeleteTodoItem`-Methode.</span><span class="sxs-lookup"><span data-stu-id="122aa-350">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

<span data-ttu-id="122aa-351">Die `DeleteTodoItem`-Antwort lautet [204 (Kein Inhalt)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="122aa-351">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="122aa-352">Testen der DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="122aa-352">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="122aa-353">So löschen Sie mit Postman eine Aufgabe</span><span class="sxs-lookup"><span data-stu-id="122aa-353">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="122aa-354">Legen Sie die Methode auf `DELETE` fest.</span><span class="sxs-lookup"><span data-stu-id="122aa-354">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="122aa-355">Legen Sie den URI des zu löschenden Objekts fest, z. B. `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="122aa-355">Set the URI of the object to delete, for example `https://localhost:5001/api/TodoItems/1`</span></span>
* <span data-ttu-id="122aa-356">Klicken Sie auf **Send**.</span><span class="sxs-lookup"><span data-stu-id="122aa-356">Select **Send**</span></span>

## <a name="call-the-api-from-jquery"></a><span data-ttu-id="122aa-357">Aufrufen einer API über jQuery</span><span class="sxs-lookup"><span data-stu-id="122aa-357">Call the API from jQuery</span></span>

<span data-ttu-id="122aa-358">Eine Schritt-für-Schritt-Anleitung finden Sie im [Tutorial: Aufrufen einer ASP.NET Core-Web-API mit jQuery](xref:tutorials/web-api-jquery).</span><span class="sxs-lookup"><span data-stu-id="122aa-358">See [Tutorial: Call an ASP.NET Core web API with jQuery](xref:tutorials/web-api-jquery).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="122aa-359">In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="122aa-359">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="122aa-360">Erstellen eines Web-API-Projekts</span><span class="sxs-lookup"><span data-stu-id="122aa-360">Create a web API project.</span></span>
> * <span data-ttu-id="122aa-361">Hinzufügen einer Modellklasse und eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="122aa-361">Add a model class and a database context.</span></span>
> * <span data-ttu-id="122aa-362">Hinzufügen eines Controllers</span><span class="sxs-lookup"><span data-stu-id="122aa-362">Add a controller.</span></span>
> * <span data-ttu-id="122aa-363">Hinzufügen von CRUD-Methoden</span><span class="sxs-lookup"><span data-stu-id="122aa-363">Add CRUD methods.</span></span>
> * <span data-ttu-id="122aa-364">Konfigurieren von Routing und URL-Pfaden</span><span class="sxs-lookup"><span data-stu-id="122aa-364">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="122aa-365">Angeben von Rückgabewerten</span><span class="sxs-lookup"><span data-stu-id="122aa-365">Specify return values.</span></span>
> * <span data-ttu-id="122aa-366">Aufrufen der Web-API mit Postman</span><span class="sxs-lookup"><span data-stu-id="122aa-366">Call the web API with Postman.</span></span>
> * <span data-ttu-id="122aa-367">Aufrufen der Web-API mit jQuery</span><span class="sxs-lookup"><span data-stu-id="122aa-367">Call the web API with jQuery.</span></span>

<span data-ttu-id="122aa-368">Am Ende haben Sie eine Web-API, die in einer relationalen Datenbank gespeicherte „Aufgaben“ verwalten kann.</span><span class="sxs-lookup"><span data-stu-id="122aa-368">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>
## <a name="overview"></a><span data-ttu-id="122aa-369">Übersicht</span><span class="sxs-lookup"><span data-stu-id="122aa-369">Overview</span></span>

<span data-ttu-id="122aa-370">In diesem Tutorial wird die folgende API erstellt:</span><span class="sxs-lookup"><span data-stu-id="122aa-370">This tutorial creates the following API:</span></span>

|<span data-ttu-id="122aa-371">API</span><span class="sxs-lookup"><span data-stu-id="122aa-371">API</span></span> | <span data-ttu-id="122aa-372">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="122aa-372">Description</span></span> | <span data-ttu-id="122aa-373">Anforderungstext</span><span class="sxs-lookup"><span data-stu-id="122aa-373">Request body</span></span> | <span data-ttu-id="122aa-374">Antworttext</span><span class="sxs-lookup"><span data-stu-id="122aa-374">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="122aa-375">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="122aa-375">GET /api/TodoItems</span></span> | <span data-ttu-id="122aa-376">Alle To-do-Elemente abrufen</span><span class="sxs-lookup"><span data-stu-id="122aa-376">Get all to-do items</span></span> | <span data-ttu-id="122aa-377">Keine</span><span class="sxs-lookup"><span data-stu-id="122aa-377">None</span></span> | <span data-ttu-id="122aa-378">Array von To-do-Elementen</span><span class="sxs-lookup"><span data-stu-id="122aa-378">Array of to-do items</span></span>|
|<span data-ttu-id="122aa-379">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="122aa-379">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="122aa-380">Ein Element nach ID abrufen</span><span class="sxs-lookup"><span data-stu-id="122aa-380">Get an item by ID</span></span> | <span data-ttu-id="122aa-381">Keine</span><span class="sxs-lookup"><span data-stu-id="122aa-381">None</span></span> | <span data-ttu-id="122aa-382">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="122aa-382">To-do item</span></span>|
|<span data-ttu-id="122aa-383">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="122aa-383">POST /api/TodoItems</span></span> | <span data-ttu-id="122aa-384">Neues Element hinzufügen</span><span class="sxs-lookup"><span data-stu-id="122aa-384">Add a new item</span></span> | <span data-ttu-id="122aa-385">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="122aa-385">To-do item</span></span> | <span data-ttu-id="122aa-386">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="122aa-386">To-do item</span></span> |
|<span data-ttu-id="122aa-387">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="122aa-387">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="122aa-388">Vorhandenes Element aktualisieren &nbsp;</span><span class="sxs-lookup"><span data-stu-id="122aa-388">Update an existing item &nbsp;</span></span> | <span data-ttu-id="122aa-389">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="122aa-389">To-do item</span></span> | <span data-ttu-id="122aa-390">Keine</span><span class="sxs-lookup"><span data-stu-id="122aa-390">None</span></span> |
|<span data-ttu-id="122aa-391">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="122aa-391">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="122aa-392">Löschen eines Elements &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="122aa-392">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="122aa-393">Keine</span><span class="sxs-lookup"><span data-stu-id="122aa-393">None</span></span> | <span data-ttu-id="122aa-394">Keine</span><span class="sxs-lookup"><span data-stu-id="122aa-394">None</span></span>|

<span data-ttu-id="122aa-395">Das folgende Diagramm zeigt den Entwurf der App.</span><span class="sxs-lookup"><span data-stu-id="122aa-395">The following diagram shows the design of the app.</span></span>

![Der Client ist das Feld ganz links.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="122aa-401">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="122aa-401">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="122aa-402">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="122aa-402">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="122aa-403">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="122aa-403">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="122aa-404">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="122aa-404">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="122aa-405">Erstellen eines Webprojekts</span><span class="sxs-lookup"><span data-stu-id="122aa-405">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="122aa-406">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="122aa-406">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="122aa-407">Klicken Sie im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="122aa-407">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="122aa-408">Wählen Sie die Vorlage **ASP.NET Core-Webanwendung** aus, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="122aa-408">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="122aa-409">Geben Sie dem Projekt den Namen *TodoApi*, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="122aa-409">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="122aa-410">Vergewissern Sie sich, dass im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Optionen **.NET Core** und **ASP.NET Core 2.2** ausgewählt sind.</span><span class="sxs-lookup"><span data-stu-id="122aa-410">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="122aa-411">Wählen Sie die Vorlage **API** aus, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="122aa-411">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="122aa-412">Wählen Sie **nicht** **Enable Docker Support** (Docker-Unterstützung aktivieren) aus.</span><span class="sxs-lookup"><span data-stu-id="122aa-412">**Don't** select **Enable Docker Support**.</span></span>

![VS-Dialogfeld „Neues Projekt“](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="122aa-414">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="122aa-414">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="122aa-415">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="122aa-415">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="122aa-416">Wechseln Sie vom Verzeichnis (`cd`) zum Ordner, der den Projektordner enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="122aa-416">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="122aa-417">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="122aa-417">Run the following commands:</span></span>

   ```console
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="122aa-418">Diese Befehle erstellen ein neues Web-API-Projekt und öffnen eine neue Visual Studio Code-Instanz im neuen Projektordner.</span><span class="sxs-lookup"><span data-stu-id="122aa-418">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="122aa-419">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem Projekt die erforderlichen Elemente hinzufügen möchten, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="122aa-419">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="122aa-420">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="122aa-420">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="122aa-421">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="122aa-421">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="122aa-423">Klicken Sie auf **.NET Core** > **App** > **API** > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="122aa-423">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![Dialogfeld „Neue Projektmappe“ in macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="122aa-425">Übernehmen Sie im Dialogfeld **Neue ASP.NET Core-Web-API konfigurieren** die Standardeinstellung **Zielframework** von \* *.NET Core 2.2*.</span><span class="sxs-lookup"><span data-stu-id="122aa-425">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="122aa-426">Geben Sie für **Projektname** *TodoApi* ein, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="122aa-426">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogfeld „Konfiguration“](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="122aa-428">Testen der API</span><span class="sxs-lookup"><span data-stu-id="122aa-428">Test the API</span></span>

<span data-ttu-id="122aa-429">Die Projektvorlage erstellt eine `values`-API.</span><span class="sxs-lookup"><span data-stu-id="122aa-429">The project template creates a `values` API.</span></span> <span data-ttu-id="122aa-430">Rufen Sie in einem Browser die `Get`-Methode zum Testen der App auf.</span><span class="sxs-lookup"><span data-stu-id="122aa-430">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="122aa-431">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="122aa-431">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="122aa-432">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="122aa-432">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="122aa-433">Visual Studio startet einen Browser und navigiert zu `https://localhost:<port>/api/values`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="122aa-433">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="122aa-434">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem IIS Express-Zertifikat vertrauen, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="122aa-434">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="122aa-435">Wählen Sie im folgenden Dialogfeld **Sicherheitswarnung** die Option **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="122aa-435">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="122aa-436">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="122aa-436">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="122aa-437">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="122aa-437">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="122aa-438">Rufen Sie in einem Browser die folgende URL auf: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span><span class="sxs-lookup"><span data-stu-id="122aa-438">In a browser, go to following URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="122aa-439">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="122aa-439">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="122aa-440">Wählen Sie **Run** > **Start Debugging** (Ausführen > Debugging starten) aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="122aa-440">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="122aa-441">Visual Studio für Mac startet einen Browser und navigiert zu `https://localhost:<port>`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="122aa-441">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="122aa-442">Der Fehler „HTTP 404: Nicht gefunden“ wird zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="122aa-442">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="122aa-443">Fügen Sie der URL `/api/values` an, d.h. ändern Sie die URL in `https://localhost:<port>/api/values`.</span><span class="sxs-lookup"><span data-stu-id="122aa-443">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="122aa-444">Die folgende JSON-Datei wird zurückgegeben:</span><span class="sxs-lookup"><span data-stu-id="122aa-444">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="122aa-445">Hinzufügen einer Modellklasse</span><span class="sxs-lookup"><span data-stu-id="122aa-445">Add a model class</span></span>

<span data-ttu-id="122aa-446">Ein *Modell* ist eine Gruppe von Klassen, die die Daten darstellen, die die App verwaltet.</span><span class="sxs-lookup"><span data-stu-id="122aa-446">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="122aa-447">Das Modell für diese App ist eine einzelne `TodoItem`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="122aa-447">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="122aa-448">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="122aa-448">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="122aa-449">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="122aa-449">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="122aa-450">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="122aa-450">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="122aa-451">Geben Sie dem Ordner den Namen *Models*.</span><span class="sxs-lookup"><span data-stu-id="122aa-451">Name the folder *Models*.</span></span>

* <span data-ttu-id="122aa-452">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="122aa-452">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="122aa-453">Nennen Sie die Klasse *TodoItem*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="122aa-453">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="122aa-454">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="122aa-454">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="122aa-455">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="122aa-455">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="122aa-456">Fügen Sie einen Ordner namens *Models* hinzu.</span><span class="sxs-lookup"><span data-stu-id="122aa-456">Add a folder named *Models*.</span></span>

* <span data-ttu-id="122aa-457">Fügen Sie dem Ordner *Modelle* mit dem folgenden Code eine `TodoItem`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="122aa-457">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="122aa-458">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="122aa-458">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="122aa-459">Klicken Sie mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="122aa-459">Right-click the project.</span></span> <span data-ttu-id="122aa-460">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="122aa-460">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="122aa-461">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="122aa-461">Name the folder *Models*.</span></span>

  ![Neuer Ordner](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="122aa-463">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und klicken Sie auf **Hinzufügen** > **Neue Datei** > **Allgemein** > **Leere Klasse**.</span><span class="sxs-lookup"><span data-stu-id="122aa-463">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="122aa-464">Nennen Sie die Klasse *TodoItem*, und klicken Sie dann auf **Neu**.</span><span class="sxs-lookup"><span data-stu-id="122aa-464">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="122aa-465">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="122aa-465">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="122aa-466">Die `Id`-Eigenschaft fungiert als eindeutiger Schlüssel in einer relationalen Datenbank.</span><span class="sxs-lookup"><span data-stu-id="122aa-466">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="122aa-467">Modellklassen können überall im Projekt platziert werden, doch gemäß der Konvention wird der Ordner *Modelle* verwendet.</span><span class="sxs-lookup"><span data-stu-id="122aa-467">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="122aa-468">Hinzufügen eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="122aa-468">Add a database context</span></span>

<span data-ttu-id="122aa-469">Der *Datenbankkontext* ist die Hauptklasse, die die Entity Framework-Funktionen für ein Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="122aa-469">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="122aa-470">Diese Klasse wird durch Ableiten von der `Microsoft.EntityFrameworkCore.DbContext`-Klasse erstellt.</span><span class="sxs-lookup"><span data-stu-id="122aa-470">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="122aa-471">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="122aa-471">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="122aa-472">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="122aa-472">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="122aa-473">Nennen Sie die Klasse *TodoContext*, und klicken Sie auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="122aa-473">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="122aa-474">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="122aa-474">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="122aa-475">Fügen Sie eine `TodoContext`-Klasse zum Ordner *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="122aa-475">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="122aa-476">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="122aa-476">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="122aa-477">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="122aa-477">Register the database context</span></span>

<span data-ttu-id="122aa-478">In ASP.NET Core müssen Dienste wie der Datenbankkontext mit dem Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) registriert werden.</span><span class="sxs-lookup"><span data-stu-id="122aa-478">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="122aa-479">Der Container stellt den Dienst für Controller bereit.</span><span class="sxs-lookup"><span data-stu-id="122aa-479">The container provides the service to controllers.</span></span>

<span data-ttu-id="122aa-480">Aktualisieren Sie *Startup.cs* mit dem folgenden hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="122aa-480">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="122aa-481">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="122aa-481">The preceding code:</span></span>

* <span data-ttu-id="122aa-482">Entfernt nicht benötigte `using`-Deklarationen</span><span class="sxs-lookup"><span data-stu-id="122aa-482">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="122aa-483">Fügt dem Abhängigkeitscontainer den Datenbankkontext hinzu</span><span class="sxs-lookup"><span data-stu-id="122aa-483">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="122aa-484">Gibt an, dass der Datenbankkontext eine In-Memory Database verwendet</span><span class="sxs-lookup"><span data-stu-id="122aa-484">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="122aa-485">Hinzufügen eines Controllers</span><span class="sxs-lookup"><span data-stu-id="122aa-485">Add a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="122aa-486">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="122aa-486">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="122aa-487">Klicken Sie mit der rechten Maustaste auf den Ordner *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="122aa-487">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="122aa-488">Klicken Sie auf **Hinzufügen** > **Neues Element**.</span><span class="sxs-lookup"><span data-stu-id="122aa-488">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="122aa-489">Wählen Sie im Dialogfeld **Neues Element hinzufügen** die Vorlage **API-Controllerklasse** aus.</span><span class="sxs-lookup"><span data-stu-id="122aa-489">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="122aa-490">Benennen Sie die Klasse *TodoController*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="122aa-490">Name the class *TodoController*, and select **Add**.</span></span>

  ![Dialogfeld „Neues Element hinzufügen“ mit Controller im Suchfeld und ausgewähltem Web-API-Controller](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="122aa-492">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="122aa-492">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="122aa-493">Erstellen Sie im Ordner *Controllers* eine Klasse namens `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="122aa-493">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="122aa-494">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="122aa-494">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="122aa-495">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="122aa-495">The preceding code:</span></span>

* <span data-ttu-id="122aa-496">Er definiert eine API-Controllerklasse ohne Methoden.</span><span class="sxs-lookup"><span data-stu-id="122aa-496">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="122aa-497">Ergänzt die Klasse mit dem Attribut [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute).</span><span class="sxs-lookup"><span data-stu-id="122aa-497">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="122aa-498">Dieses Attribut gibt an, dass der Controller auf Web-API-Anforderungen reagiert.</span><span class="sxs-lookup"><span data-stu-id="122aa-498">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="122aa-499">Weitere Informationen zu bestimmten Verhaltensweisen, die das Attribut ermöglicht, finden Sie unter <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="122aa-499">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="122aa-500">Verwendet die Abhängigkeitsinjektion, um den Datenbankkontext (`TodoContext`) dem Controller hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="122aa-500">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="122aa-501">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="122aa-501">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="122aa-502">Fügt der Datenbank, falls sie leer ist, ein Element mit dem Namen `Item1` hinzu.</span><span class="sxs-lookup"><span data-stu-id="122aa-502">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="122aa-503">Dieser Code befindet sich im Konstruktor. Er wird bei jeder neuen HTTP-Anforderung ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="122aa-503">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="122aa-504">Wenn Sie alle Elemente löschen, erstellt der Konstruktor beim nächsten Aufruf einer API-Methode erneut `Item1`.</span><span class="sxs-lookup"><span data-stu-id="122aa-504">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="122aa-505">So sieht es möglicherweise so aus, als sei die Löschung fehlgeschlagen, obwohl sie erfolgreich war.</span><span class="sxs-lookup"><span data-stu-id="122aa-505">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="122aa-506">Hinzufügen von GET-Methoden</span><span class="sxs-lookup"><span data-stu-id="122aa-506">Add Get methods</span></span>

<span data-ttu-id="122aa-507">Fügen Sie der Klasse `TodoController` die folgenden Methoden hinzu, um eine API bereitzustellen, die Aufgaben abruft:</span><span class="sxs-lookup"><span data-stu-id="122aa-507">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="122aa-508">Diese Methoden implementieren zwei GET-Endpunkte:</span><span class="sxs-lookup"><span data-stu-id="122aa-508">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="122aa-509">Halten Sie die App an, falls diese noch ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="122aa-509">Stop the app if it's still running.</span></span> <span data-ttu-id="122aa-510">Führen Sie diese dann noch mal aus, damit die letzten Änderungen übernommen werden.</span><span class="sxs-lookup"><span data-stu-id="122aa-510">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="122aa-511">Testen Sie die App, indem Sie die beiden Endpunkte in einem Browser aufrufen.</span><span class="sxs-lookup"><span data-stu-id="122aa-511">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="122aa-512">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="122aa-512">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="122aa-513">Die folgende HTTP-Antwort wird durch den Aufruf von `GetTodoItems` erzeugt:</span><span class="sxs-lookup"><span data-stu-id="122aa-513">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="122aa-514">Routing und URL-Pfade</span><span class="sxs-lookup"><span data-stu-id="122aa-514">Routing and URL paths</span></span>

<span data-ttu-id="122aa-515">Das Attribut [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) gibt eine Methode an, die auf eine HTTP GET-Anforderung antwortet.</span><span class="sxs-lookup"><span data-stu-id="122aa-515">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="122aa-516">Der URL-Pfad für jede Methode wird wie folgt erstellt:</span><span class="sxs-lookup"><span data-stu-id="122aa-516">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="122aa-517">Beginnen Sie mit der Vorlagenzeichenfolge im `Route`-Attribut des Controllers:</span><span class="sxs-lookup"><span data-stu-id="122aa-517">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="122aa-518">Ersetzen Sie `[controller]` durch den Namen des Controllers, bei dem es sich standardmäßig um den Namen der Controller-Klasse ohne das Suffix „Controller“ handelt.</span><span class="sxs-lookup"><span data-stu-id="122aa-518">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="122aa-519">Bei diesem Beispiel ist der Klassenname des Controllers „**Todo**Controller“, d.h. der Controllername lautet „todo“.</span><span class="sxs-lookup"><span data-stu-id="122aa-519">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="122aa-520">Beim ASP.NET Core-[Routing](xref:mvc/controllers/routing) wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="122aa-520">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="122aa-521">Wenn das `[HttpGet]`-Attribut eine Routenvorlage (z.B. `[HttpGet("products")]`) hat, fügen Sie diese an den Pfad an.</span><span class="sxs-lookup"><span data-stu-id="122aa-521">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="122aa-522">In diesem Beispiel wird keine Vorlage verwendet.</span><span class="sxs-lookup"><span data-stu-id="122aa-522">This sample doesn't use a template.</span></span> <span data-ttu-id="122aa-523">Weitere Informationen finden Sie unter [Attributrouting mit Http[Verb]-Attributen](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="122aa-523">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="122aa-524">In der folgenden `GetTodoItem`-Methode ist `"{id}"` eine Platzhaltervariable für den eindeutigen Bezeichners des To-do-Elements.</span><span class="sxs-lookup"><span data-stu-id="122aa-524">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="122aa-525">Wenn `GetTodoItem` aufgerufen wird, wird der Wert von `"{id}"` in der URL der Methode in ihrem Parameter `id` bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="122aa-525">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="122aa-526">Rückgabewert</span><span class="sxs-lookup"><span data-stu-id="122aa-526">Return values</span></span>

<span data-ttu-id="122aa-527">Der Rückgabetyp der Methoden `GetTodoItems` und `GetTodoItem` ist [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="122aa-527">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="122aa-528">ASP.NET Core serialisiert automatisch das Objekt in [JSON](https://www.json.org/) und schreibt den JSON-Code in den Text der Antwortnachricht.</span><span class="sxs-lookup"><span data-stu-id="122aa-528">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="122aa-529">Der Antwortcode für diesen Rückgabetyp ist 200, vorausgesetzt, es gibt keine Ausnahmefehler.</span><span class="sxs-lookup"><span data-stu-id="122aa-529">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="122aa-530">Nicht behandelte Ausnahmen werden in 5xx-Fehler übersetzt.</span><span class="sxs-lookup"><span data-stu-id="122aa-530">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="122aa-531">`ActionResult`-Rückgabetypen können eine Vielzahl von HTTP-Statuscodes darstellen.</span><span class="sxs-lookup"><span data-stu-id="122aa-531">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="122aa-532">Beispielsweise kann `GetTodoItem` zwei verschiedene Statuswerte zurückgeben:</span><span class="sxs-lookup"><span data-stu-id="122aa-532">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="122aa-533">Wenn kein Element mit der angeforderten ID übereinstimmt, gibt die Methode einen 404-Fehlercode [Nicht gefunden](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) zurück.</span><span class="sxs-lookup"><span data-stu-id="122aa-533">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="122aa-534">Andernfalls gibt die Methode 200 mit einem JSON-Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="122aa-534">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="122aa-535">Die Rückgabe von `item` löst eine HTTP 200-Antwort aus.</span><span class="sxs-lookup"><span data-stu-id="122aa-535">Returning `item` results in an HTTP 200 response.</span></span>


## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="122aa-536">Testen der GetTodoItems-Methode</span><span class="sxs-lookup"><span data-stu-id="122aa-536">Test the GetTodoItems method</span></span>

<span data-ttu-id="122aa-537">Dieses Tutorial verwendet Postman zum Testen der Web-API.</span><span class="sxs-lookup"><span data-stu-id="122aa-537">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="122aa-538">Installieren Sie [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="122aa-538">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="122aa-539">Starten Sie die Web-App.</span><span class="sxs-lookup"><span data-stu-id="122aa-539">Start the web app.</span></span>
* <span data-ttu-id="122aa-540">Starten Sie Postman.</span><span class="sxs-lookup"><span data-stu-id="122aa-540">Start Postman.</span></span>
* <span data-ttu-id="122aa-541">Deaktivieren Sie **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="122aa-541">Disable **SSL certificate verification**</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="122aa-542">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="122aa-542">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="122aa-543">Deaktivieren Sie auf der Registerkarte **General** (Allgemein) unter **File**>**Settings** (Datei > Einstellungen) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="122aa-543">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="122aa-544">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="122aa-544">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="122aa-545">Deaktivieren Sie auf der Registerkarte **General** (Allgemein) unter **Postman** > **Preferences** (Postman > Einstellungen) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="122aa-545">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="122aa-546">Wählen Sie alternativ den Schraubenschlüssel und **Settings** (Einstellungen) aus, und deaktivieren Sie dann die Überprüfung des SSL-Zertifikats.</span><span class="sxs-lookup"><span data-stu-id="122aa-546">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="122aa-547">Aktivieren Sie die Verifizierung des SSL-Zertifikats wieder, nachdem Sie den Controller getestet haben.</span><span class="sxs-lookup"><span data-stu-id="122aa-547">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="122aa-548">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="122aa-548">Create a new request.</span></span>
  * <span data-ttu-id="122aa-549">Legen Sie die HTTP-Methode auf **GET** fest.</span><span class="sxs-lookup"><span data-stu-id="122aa-549">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="122aa-550">Legen Sie die Anforderungs-URL auf `https://localhost:<port>/api/todo` fest.</span><span class="sxs-lookup"><span data-stu-id="122aa-550">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="122aa-551">Beispielsweise `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="122aa-551">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="122aa-552">Wählen Sie in Postman **Two pane view** (Ansicht mit zwei Bereichen) aus.</span><span class="sxs-lookup"><span data-stu-id="122aa-552">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="122aa-553">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="122aa-553">Select **Send**.</span></span>

![Postman mit GET-Anforderung](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="122aa-555">Hinzufügen einer Methode</span><span class="sxs-lookup"><span data-stu-id="122aa-555">Add a Create method</span></span>

<span data-ttu-id="122aa-556">Fügen Sie die folgende `PostTodoItem`-Methode in *Controllers/TodoController.cs* hinzu:</span><span class="sxs-lookup"><span data-stu-id="122aa-556">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="122aa-557">Der vorherige Code ist eine HTTP POST-Methode, die durch das Attribut [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="122aa-557">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="122aa-558">Die Methode ruft den Wert der Aufgabe aus dem Text der HTTP-Anforderung ab.</span><span class="sxs-lookup"><span data-stu-id="122aa-558">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="122aa-559">Die `CreatedAtAction`-Methode:</span><span class="sxs-lookup"><span data-stu-id="122aa-559">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="122aa-560">Gibt bei einer erfolgreichen Anforderung den Statuscode „HTTP 201“ zurück.</span><span class="sxs-lookup"><span data-stu-id="122aa-560">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="122aa-561">HTTP 201 ist die Standardantwort für eine HTTP POST-Methode, die eine neue Ressource auf dem Server erstellt.</span><span class="sxs-lookup"><span data-stu-id="122aa-561">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="122aa-562">Fügt der Antwort einen `Location`-Header hinzu.</span><span class="sxs-lookup"><span data-stu-id="122aa-562">Adds a `Location` header to the response.</span></span> <span data-ttu-id="122aa-563">Der `Location`-Header gibt den URI des neu erstellten To-Do-Elements zurück.</span><span class="sxs-lookup"><span data-stu-id="122aa-563">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="122aa-564">Weitere Informationen finden Sie unter [10.2.2 201 Erstellt](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="122aa-564">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="122aa-565">Verweist auf die `GetTodoItem`-Aktion zum Erstellen des URIs des `Location`-Headers.</span><span class="sxs-lookup"><span data-stu-id="122aa-565">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="122aa-566">Das `nameof`-Schlüsselwort von C# wird verwendet, um eine Hartcodierung des Aktionsnamens im `CreatedAtAction`-Aufruf zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="122aa-566">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="122aa-567">Testen der PostTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="122aa-567">Test the PostTodoItem method</span></span>

* <span data-ttu-id="122aa-568">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="122aa-568">Build the project.</span></span>
* <span data-ttu-id="122aa-569">Legen Sie in Postman die HTTP-Methode auf `POST` fest.</span><span class="sxs-lookup"><span data-stu-id="122aa-569">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="122aa-570">Wählen Sie die Registerkarte **Body** (Text) aus.</span><span class="sxs-lookup"><span data-stu-id="122aa-570">Select the **Body** tab.</span></span>
* <span data-ttu-id="122aa-571">Wählen Sie das Optionsfeld **raw** (Unformatiert) aus.</span><span class="sxs-lookup"><span data-stu-id="122aa-571">Select the **raw** radio button.</span></span>
* <span data-ttu-id="122aa-572">Legen Sie den Typ auf **JSON (application/json)** fest.</span><span class="sxs-lookup"><span data-stu-id="122aa-572">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="122aa-573">Geben Sie für die Aufgabe den Anforderungstext im JSON-Format ein:</span><span class="sxs-lookup"><span data-stu-id="122aa-573">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="122aa-574">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="122aa-574">Select **Send**.</span></span>

  ![Postman mit erstellter Anforderung](first-web-api/_static/create.png)

  <span data-ttu-id="122aa-576">Wenn Sie die Fehlermeldung „405: Methode nicht zulässig“ erhalten, wurde das Projekt wahrscheinlich nicht kompiliert, nachdem die Methode `PostTodoItem` hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="122aa-576">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="122aa-577">Testen des Adressheader-URIs</span><span class="sxs-lookup"><span data-stu-id="122aa-577">Test the location header URI</span></span>

* <span data-ttu-id="122aa-578">Wählen Sie auf der Registerkarte **Header** (Header) den Bereich **Response** (Antwort) aus.</span><span class="sxs-lookup"><span data-stu-id="122aa-578">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="122aa-579">Kopieren Sie den den Headerwert von **Location** (Speicherort):</span><span class="sxs-lookup"><span data-stu-id="122aa-579">Copy the **Location** header value:</span></span>

  ![Registerkarte „Header“ in der Postman-Konsole](first-web-api/_static/pmc2.png)

* <span data-ttu-id="122aa-581">Legen Sie die Methode auf „GET“ fest.</span><span class="sxs-lookup"><span data-stu-id="122aa-581">Set the method to GET.</span></span>
* <span data-ttu-id="122aa-582">Fügen Sie den URI (z. B. `https://localhost:5001/api/Todo/2`) ein.</span><span class="sxs-lookup"><span data-stu-id="122aa-582">Paste the URI (for example, `https://localhost:5001/api/Todo/2`)</span></span>
* <span data-ttu-id="122aa-583">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="122aa-583">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="122aa-584">Hinzufügen einer PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="122aa-584">Add a PutTodoItem method</span></span>

<span data-ttu-id="122aa-585">Fügen Sie die folgende `PutTodoItem`-Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="122aa-585">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="122aa-586">`PutTodoItem` ähnelt `PostTodoItem`, verwendet allerdings HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="122aa-586">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="122aa-587">Die Antwort ist [204 (Kein Inhalt)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="122aa-587">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="122aa-588">Gemäß der HTTP-Spezifikation erfordert eine PUT-Anforderung, dass der Client die gesamte aktualisierte Entität (nicht nur die Änderungen) sendet.</span><span class="sxs-lookup"><span data-stu-id="122aa-588">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="122aa-589">Verwenden Sie [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute), um Teilupdates zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="122aa-589">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="122aa-590">Wenn beim Aufrufen von `PutTodoItem` ein Fehler zurückgegeben wird, rufen Sie `GET` auf, um sicherzustellen, dass die Datenbank ein Element enthält.</span><span class="sxs-lookup"><span data-stu-id="122aa-590">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="122aa-591">Testen der PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="122aa-591">Test the PutTodoItem method</span></span>

<span data-ttu-id="122aa-592">In diesem Beispiel wird eine In-Memory Database verwendet, die jedes Mal initialisiert werden muss, wenn die App gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="122aa-592">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="122aa-593">Es muss ein Element in der Datenbank vorhanden sein, bevor Sie einen PUT-Aufruf durchführen.</span><span class="sxs-lookup"><span data-stu-id="122aa-593">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="122aa-594">Rufen Sie vor einem PUT-Aufruf GET auf, um sicherzustellen, dass ein Element in der Datenbank vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="122aa-594">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="122aa-595">Aktualisieren Sie die Aufgabe mit der ID = 1, und setzen Sie ihren Namen auf „feed fish“:</span><span class="sxs-lookup"><span data-stu-id="122aa-595">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="122aa-596">Die folgende Abbildung zeigt das Postman-Update:</span><span class="sxs-lookup"><span data-stu-id="122aa-596">The following image shows the Postman update:</span></span>

![Postman-Konsole mit der Antwort „204 (Kein Inhalt)“](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="122aa-598">Hinzufügen einer DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="122aa-598">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="122aa-599">Fügen Sie die folgende `DeleteTodoItem`-Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="122aa-599">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="122aa-600">Die `DeleteTodoItem`-Antwort lautet [204 (Kein Inhalt)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="122aa-600">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="122aa-601">Testen der DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="122aa-601">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="122aa-602">So löschen Sie mit Postman eine Aufgabe</span><span class="sxs-lookup"><span data-stu-id="122aa-602">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="122aa-603">Legen Sie die Methode auf `DELETE` fest.</span><span class="sxs-lookup"><span data-stu-id="122aa-603">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="122aa-604">Legen Sie den URI des zu löschenden Objekts fest, z. B. `https://localhost:5001/api/todo/1`.</span><span class="sxs-lookup"><span data-stu-id="122aa-604">Set the URI of the object to delete, for example `https://localhost:5001/api/todo/1`</span></span>
* <span data-ttu-id="122aa-605">Klicken Sie auf **Send**.</span><span class="sxs-lookup"><span data-stu-id="122aa-605">Select **Send**</span></span>

<span data-ttu-id="122aa-606">Sie können in der Beispiel-App alle Elemente löschen.</span><span class="sxs-lookup"><span data-stu-id="122aa-606">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="122aa-607">Sobald das letzte Element gelöscht wurde, wird allerdings beim nächsten API-Aufruf vom Modellklassenkonstruktor ein neues erstellt.</span><span class="sxs-lookup"><span data-stu-id="122aa-607">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-api-with-jquery"></a><span data-ttu-id="122aa-608">Aufrufen der API mit jQuery</span><span class="sxs-lookup"><span data-stu-id="122aa-608">Call the API with jQuery</span></span>

<span data-ttu-id="122aa-609">In diesem Abschnitt wird eine HTML-Seite hinzugefügt, die mithilfe von jQuery die Web-API aufruft.</span><span class="sxs-lookup"><span data-stu-id="122aa-609">In this section, an HTML page is added that uses jQuery to call the web api.</span></span> <span data-ttu-id="122aa-610">jQuery initiiert die Anforderung und aktualisiert die Seite mit den Informationen aus der Antwort der API.</span><span class="sxs-lookup"><span data-stu-id="122aa-610">jQuery initiates the request and updates the page with the details from the API's response.</span></span>

<span data-ttu-id="122aa-611">Konfigurieren Sie die App so, dass sie [statische Dateien bereitstellt](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_), und [aktivieren Sie die Standarddateizuordnung](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_), indem Sie *startup.cs* mit dem unten markierten Code aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="122aa-611">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="122aa-612">Erstellen Sie im Projektverzeichnis den Ordner *wwwwroot*.</span><span class="sxs-lookup"><span data-stu-id="122aa-612">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="122aa-613">Fügen Sie dem Verzeichnis *wwwroot* eine HTML-Datei namens *index.html* hinzu.</span><span class="sxs-lookup"><span data-stu-id="122aa-613">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="122aa-614">Ersetzen Sie den Inhalt durch folgendes Markup:</span><span class="sxs-lookup"><span data-stu-id="122aa-614">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="122aa-615">Fügen Sie dem Verzeichnis *wwwroot* eine JavaScript-Datei namens *site.js* hinzu.</span><span class="sxs-lookup"><span data-stu-id="122aa-615">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="122aa-616">Ersetzen Sie den Inhalt durch folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="122aa-616">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="122aa-617">Möglicherweise ist eine Änderung an den Starteinstellungen des ASP.NET Core-Projekts erforderlich, um die HTML-Seite lokal zu testen:</span><span class="sxs-lookup"><span data-stu-id="122aa-617">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="122aa-618">Öffnen Sie *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="122aa-618">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="122aa-619">Entfernen Sie die `launchUrl`-Eigenschaft, um zu erzwingen, dass die App mit *index.html* als Startseite geöffnet wird. Dies ist die Standarddatei des Projekts.</span><span class="sxs-lookup"><span data-stu-id="122aa-619">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="122aa-620">Es gibt mehrere Möglichkeiten, um jQuery herunterzuladen.</span><span class="sxs-lookup"><span data-stu-id="122aa-620">There are several ways to get jQuery.</span></span> <span data-ttu-id="122aa-621">Im vorherigen Codeausschnitt wurde die Bibliothek aus einem Content Delivery Network (CDN) geladen.</span><span class="sxs-lookup"><span data-stu-id="122aa-621">In the preceding snippet, the library is loaded from a CDN.</span></span>

<span data-ttu-id="122aa-622">Dieses Beispiel ruft alle CRUD-Methoden der API auf.</span><span class="sxs-lookup"><span data-stu-id="122aa-622">This sample calls all of the CRUD methods of the API.</span></span> <span data-ttu-id="122aa-623">Im Folgenden werden die API-Aufrufe erläutert.</span><span class="sxs-lookup"><span data-stu-id="122aa-623">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="122aa-624">Abrufen einer Liste von To-Do-Elementen</span><span class="sxs-lookup"><span data-stu-id="122aa-624">Get a list of to-do items</span></span>

<span data-ttu-id="122aa-625">Die jQuery-Funktion [ajax](https://api.jquery.com/jquery.ajax/) sendet eine `GET`-Anforderung an die API, die JSON-Code zurückgibt, der ein Aufgabenarray darstellt.</span><span class="sxs-lookup"><span data-stu-id="122aa-625">The jQuery [ajax](https://api.jquery.com/jquery.ajax/) function sends a `GET` request to the API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="122aa-626">Die Rückruffunktion `success` wird aufgerufen, wenn die Anforderung erfolgreich ist.</span><span class="sxs-lookup"><span data-stu-id="122aa-626">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="122aa-627">Im Rückruf wird DOM mit den To-Do-Informationen aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="122aa-627">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="122aa-628">Hinzufügen eines To-Do-Elements</span><span class="sxs-lookup"><span data-stu-id="122aa-628">Add a to-do item</span></span>

<span data-ttu-id="122aa-629">Die Funktion [ajax](https://api.jquery.com/jquery.ajax/) sendet eine `POST`-Anforderung mit der Aufgabe im Anforderungstext.</span><span class="sxs-lookup"><span data-stu-id="122aa-629">The [ajax](https://api.jquery.com/jquery.ajax/) function sends a `POST` request with the to-do item in the request body.</span></span> <span data-ttu-id="122aa-630">Die Optionen `accepts` und `contentType` werden auf `application/json` festgelegt, um den gesendeten und empfangenen Medientyp anzugeben.</span><span class="sxs-lookup"><span data-stu-id="122aa-630">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="122aa-631">Die Aufgabe wird mit [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) in JSON konvertiert.</span><span class="sxs-lookup"><span data-stu-id="122aa-631">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="122aa-632">Wenn die API den Statuscode „Erfolgreich“ zurückgibt, wird die Funktion `getData` aufgerufen, um die HTML-Tabelle zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="122aa-632">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="122aa-633">Aktualisieren eines To-Do-Elements</span><span class="sxs-lookup"><span data-stu-id="122aa-633">Update a to-do item</span></span>

<span data-ttu-id="122aa-634">Das Aktualisieren einer Aufgabe ist vergleichbar mit dem Hinzufügen einer Aufgabe.</span><span class="sxs-lookup"><span data-stu-id="122aa-634">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="122aa-635">Die `url` ändert sich, um den eindeutigen Bezeichner des Elements hinzuzufügen. Der `type` lautet `PUT`.</span><span class="sxs-lookup"><span data-stu-id="122aa-635">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="122aa-636">Löschen eines To-Do-Elements</span><span class="sxs-lookup"><span data-stu-id="122aa-636">Delete a to-do item</span></span>

<span data-ttu-id="122aa-637">Eine Aufgabe wird folgendermaßen gelöscht: im AJAX-Aufruf wird `type` auf `DELETE` festgelegt, und der eindeutige Bezeichner des Elements wird in der URL angegeben.</span><span class="sxs-lookup"><span data-stu-id="122aa-637">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="122aa-638">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="122aa-638">Additional resources</span></span>

<span data-ttu-id="122aa-639">[Sie können den Beispielcode für dieses Tutorial anzeigen oder herunterladen.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)</span><span class="sxs-lookup"><span data-stu-id="122aa-639">[View or download sample code for this tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="122aa-640">Informationen [zum Herunterladen](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="122aa-640">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="122aa-641">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="122aa-641">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/index>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="122aa-642">Dieses Tutorial auf YouTube</span><span class="sxs-lookup"><span data-stu-id="122aa-642">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)

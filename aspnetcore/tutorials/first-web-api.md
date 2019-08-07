---
title: 'Tutorial: Erstellen einer Web-API mit ASP.NET Core'
author: rick-anderson
description: Informationen zum Erstellen einer Web-API mit ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
uid: tutorials/first-web-api
ms.openlocfilehash: 60235af56077127093ac1d77338bc228a6edf073
ms.sourcegitcommit: 0efb9e219fef481dee35f7b763165e488aa6cf9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68602512"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="4374c-103">Tutorial: Erstellen einer Web-API mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4374c-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="4374c-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="4374c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="4374c-105">In diesem Tutorial lernen Sie die Grundlagen der Erstellung einer Web-API mit ASP.NET Core kennen.</span><span class="sxs-lookup"><span data-stu-id="4374c-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4374c-106">In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="4374c-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4374c-107">Erstellen eines Web-API-Projekts</span><span class="sxs-lookup"><span data-stu-id="4374c-107">Create a web API project.</span></span>
> * <span data-ttu-id="4374c-108">Hinzufügen einer Modellklasse und eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="4374c-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="4374c-109">Erstellen eines Controllergerüsts mit CRUD-Methoden</span><span class="sxs-lookup"><span data-stu-id="4374c-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="4374c-110">Konfigurieren des Routings, der URL-Pfade und der Rückgabewerte</span><span class="sxs-lookup"><span data-stu-id="4374c-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="4374c-111">Aufrufen der Web-API mit Postman</span><span class="sxs-lookup"><span data-stu-id="4374c-111">Call the web API with Postman.</span></span>

<span data-ttu-id="4374c-112">Abschließend steht Ihnen eine Web-API zur Verfügung, die in einer relationalen Datenbank gespeicherte To-do-Elemente verwalten kann.</span><span class="sxs-lookup"><span data-stu-id="4374c-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="4374c-113">Übersicht</span><span class="sxs-lookup"><span data-stu-id="4374c-113">Overview</span></span>

<span data-ttu-id="4374c-114">In diesem Tutorial wird die folgende API erstellt:</span><span class="sxs-lookup"><span data-stu-id="4374c-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="4374c-115">API</span><span class="sxs-lookup"><span data-stu-id="4374c-115">API</span></span> | <span data-ttu-id="4374c-116">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="4374c-116">Description</span></span> | <span data-ttu-id="4374c-117">Anforderungstext</span><span class="sxs-lookup"><span data-stu-id="4374c-117">Request body</span></span> | <span data-ttu-id="4374c-118">Antworttext</span><span class="sxs-lookup"><span data-stu-id="4374c-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="4374c-119">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="4374c-119">GET /api/TodoItems</span></span> | <span data-ttu-id="4374c-120">Alle To-do-Elemente abrufen</span><span class="sxs-lookup"><span data-stu-id="4374c-120">Get all to-do items</span></span> | <span data-ttu-id="4374c-121">Keine</span><span class="sxs-lookup"><span data-stu-id="4374c-121">None</span></span> | <span data-ttu-id="4374c-122">Array von To-do-Elementen</span><span class="sxs-lookup"><span data-stu-id="4374c-122">Array of to-do items</span></span>|
|<span data-ttu-id="4374c-123">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="4374c-123">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="4374c-124">Ein Element nach ID abrufen</span><span class="sxs-lookup"><span data-stu-id="4374c-124">Get an item by ID</span></span> | <span data-ttu-id="4374c-125">Keine</span><span class="sxs-lookup"><span data-stu-id="4374c-125">None</span></span> | <span data-ttu-id="4374c-126">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="4374c-126">To-do item</span></span>|
|<span data-ttu-id="4374c-127">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="4374c-127">POST /api/TodoItems</span></span> | <span data-ttu-id="4374c-128">Neues Element hinzufügen</span><span class="sxs-lookup"><span data-stu-id="4374c-128">Add a new item</span></span> | <span data-ttu-id="4374c-129">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="4374c-129">To-do item</span></span> | <span data-ttu-id="4374c-130">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="4374c-130">To-do item</span></span> |
|<span data-ttu-id="4374c-131">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="4374c-131">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="4374c-132">Vorhandenes Element aktualisieren &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4374c-132">Update an existing item &nbsp;</span></span> | <span data-ttu-id="4374c-133">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="4374c-133">To-do item</span></span> | <span data-ttu-id="4374c-134">Keine</span><span class="sxs-lookup"><span data-stu-id="4374c-134">None</span></span> |
|<span data-ttu-id="4374c-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4374c-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="4374c-136">Löschen eines Elements &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4374c-136">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="4374c-137">Keine</span><span class="sxs-lookup"><span data-stu-id="4374c-137">None</span></span> | <span data-ttu-id="4374c-138">Keine</span><span class="sxs-lookup"><span data-stu-id="4374c-138">None</span></span>|

<span data-ttu-id="4374c-139">Das folgende Diagramm zeigt den Entwurf der App.</span><span class="sxs-lookup"><span data-stu-id="4374c-139">The following diagram shows the design of the app.</span></span>

![Der Client ist das Feld ganz links.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="4374c-145">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="4374c-145">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4374c-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4374c-146">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4374c-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4374c-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4374c-148">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4374c-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="4374c-149">Erstellen eines Webprojekts</span><span class="sxs-lookup"><span data-stu-id="4374c-149">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4374c-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4374c-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4374c-151">Klicken Sie im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="4374c-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="4374c-152">Wählen Sie die Vorlage **ASP.NET Core-Webanwendung** aus, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="4374c-152">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="4374c-153">Geben Sie dem Projekt den Namen *TodoApi*, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="4374c-153">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="4374c-154">Vergewissern Sie sich, dass im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Optionen **.NET Core** und **ASP.NET Core 3.0** ausgewählt sind.</span><span class="sxs-lookup"><span data-stu-id="4374c-154">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span> <span data-ttu-id="4374c-155">Wählen Sie die Vorlage **API** aus, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="4374c-155">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="4374c-156">Wählen Sie **nicht** **Enable Docker Support** (Docker-Unterstützung aktivieren) aus.</span><span class="sxs-lookup"><span data-stu-id="4374c-156">**Don't** select **Enable Docker Support**.</span></span>

![VS-Dialogfeld „Neues Projekt“](first-web-api/_static/vs3.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4374c-158">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4374c-158">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4374c-159">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="4374c-159">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="4374c-160">Wechseln Sie vom Verzeichnis (`cd`) zum Ordner, der den Projektordner enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="4374c-160">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="4374c-161">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="4374c-161">Run the following commands:</span></span>

   ```console
   dotnet new webapi -o TodoApi
   cd TodoAPI
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.0.0-*
   dotnet add package Microsoft.EntityFrameworkCore.InMemory --version 3.0.0-*
   code -r ../TodoApi
   ```

* <span data-ttu-id="4374c-162">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem Projekt die erforderlichen Elemente hinzufügen möchten, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="4374c-162">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="4374c-163">Die obenstehenden Befehle haben folgende Konsequenzen:</span><span class="sxs-lookup"><span data-stu-id="4374c-163">The preceding commands:</span></span>

  * <span data-ttu-id="4374c-164">Ein neues Web-API-Projekt wird erstellt und in Visual Studio Code geöffnet.</span><span class="sxs-lookup"><span data-stu-id="4374c-164">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="4374c-165">Die NuGet-Pakete, die im nächsten Abschnitt erforderlich sind, werden hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="4374c-165">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4374c-166">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4374c-166">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4374c-167">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="4374c-167">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="4374c-169">Klicken Sie auf **.NET Core** > **App** > **API** > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="4374c-169">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![Dialogfeld „Neue Projektmappe“ in macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="4374c-171">Wählen Sie im Dialogfeld **Configure your new ASP.NET Core Web API** (Neue ASP.NET Core-Web-API konfigurieren) für das **Zielframework** den Eintrag \* *.NET Core 3.0* aus.</span><span class="sxs-lookup"><span data-stu-id="4374c-171">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.0*.</span></span>

* <span data-ttu-id="4374c-172">Geben Sie für **Projektname** *TodoApi* ein, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="4374c-172">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogfeld „Konfiguration“](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="4374c-174">Testen der API</span><span class="sxs-lookup"><span data-stu-id="4374c-174">Test the API</span></span>

<span data-ttu-id="4374c-175">Die Projektvorlage erstellt eine `WeatherForecast`-API.</span><span class="sxs-lookup"><span data-stu-id="4374c-175">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="4374c-176">Rufen Sie in einem Browser die `Get`-Methode zum Testen der App auf.</span><span class="sxs-lookup"><span data-stu-id="4374c-176">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4374c-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4374c-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4374c-178">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="4374c-178">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="4374c-179">Visual Studio startet einen Browser und navigiert zu `https://localhost:<port>/WeatherForecast`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="4374c-179">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="4374c-180">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem IIS Express-Zertifikat vertrauen, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="4374c-180">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="4374c-181">Wählen Sie im folgenden Dialogfeld **Sicherheitswarnung** die Option **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="4374c-181">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4374c-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4374c-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4374c-183">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="4374c-183">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="4374c-184">Rufen Sie in einem Browser die folgende URL auf: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span><span class="sxs-lookup"><span data-stu-id="4374c-184">In a browser, go to following URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4374c-185">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4374c-185">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4374c-186">Wählen Sie **Run** > **Start Debugging** (Ausführen > Debugging starten) aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="4374c-186">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="4374c-187">Visual Studio für Mac startet einen Browser und navigiert zu `https://localhost:<port>`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="4374c-187">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="4374c-188">Der Fehler „HTTP 404: Nicht gefunden“ wird zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="4374c-188">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="4374c-189">Fügen Sie der URL `/WeatherForecast` an, d.h. ändern Sie die URL in `https://localhost:<port>/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="4374c-189">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="4374c-190">Der zurückgegebene JSON-Code sieht in etwa wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="4374c-190">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="4374c-191">Hinzufügen einer Modellklasse</span><span class="sxs-lookup"><span data-stu-id="4374c-191">Add a model class</span></span>

<span data-ttu-id="4374c-192">Ein *Modell* ist eine Gruppe von Klassen, die die Daten darstellen, die die App verwaltet.</span><span class="sxs-lookup"><span data-stu-id="4374c-192">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="4374c-193">Das Modell für diese App ist eine einzelne `TodoItem`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="4374c-193">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4374c-194">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4374c-194">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4374c-195">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="4374c-195">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="4374c-196">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="4374c-196">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="4374c-197">Geben Sie dem Ordner den Namen *Models*.</span><span class="sxs-lookup"><span data-stu-id="4374c-197">Name the folder *Models*.</span></span>

* <span data-ttu-id="4374c-198">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="4374c-198">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="4374c-199">Nennen Sie die Klasse *TodoItem*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="4374c-199">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="4374c-200">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="4374c-200">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4374c-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4374c-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4374c-202">Fügen Sie einen Ordner namens *Models* hinzu.</span><span class="sxs-lookup"><span data-stu-id="4374c-202">Add a folder named *Models*.</span></span>

* <span data-ttu-id="4374c-203">Fügen Sie dem Ordner *Modelle* mit dem folgenden Code eine `TodoItem`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="4374c-203">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4374c-204">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4374c-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4374c-205">Klicken Sie mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="4374c-205">Right-click the project.</span></span> <span data-ttu-id="4374c-206">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="4374c-206">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="4374c-207">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="4374c-207">Name the folder *Models*.</span></span>

  ![Neuer Ordner](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="4374c-209">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und klicken Sie auf **Hinzufügen** > **Neue Datei** > **Allgemein** > **Leere Klasse**.</span><span class="sxs-lookup"><span data-stu-id="4374c-209">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="4374c-210">Nennen Sie die Klasse *TodoItem*, und klicken Sie dann auf **Neu**.</span><span class="sxs-lookup"><span data-stu-id="4374c-210">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="4374c-211">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="4374c-211">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="4374c-212">Die `Id`-Eigenschaft fungiert als eindeutiger Schlüssel in einer relationalen Datenbank.</span><span class="sxs-lookup"><span data-stu-id="4374c-212">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="4374c-213">Modellklassen können überall im Projekt platziert werden, doch gemäß der Konvention wird der Ordner *Modelle* verwendet.</span><span class="sxs-lookup"><span data-stu-id="4374c-213">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="4374c-214">Hinzufügen eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="4374c-214">Add a database context</span></span>

<span data-ttu-id="4374c-215">Der *Datenbankkontext* ist die Hauptklasse, die die Entity Framework-Funktionen für ein Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="4374c-215">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="4374c-216">Diese Klasse wird durch Ableiten von der `Microsoft.EntityFrameworkCore.DbContext`-Klasse erstellt.</span><span class="sxs-lookup"><span data-stu-id="4374c-216">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4374c-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4374c-217">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="4374c-218">Hinzufügen von Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="4374c-218">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="4374c-219">Klicken Sie im Menü **Extras** auf **NuGet-Paket-Manager > NuGet-Pakete für Projektmappe verwalten...** .</span><span class="sxs-lookup"><span data-stu-id="4374c-219">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="4374c-220">Aktivieren Sie das Kontrollkästchen **Vorabversion einbeziehen**.</span><span class="sxs-lookup"><span data-stu-id="4374c-220">Select the **Include prerelease** checkbox.</span></span>
* <span data-ttu-id="4374c-221">Klicken Sie auf die Registerkarte **Durchsuchen**, und geben Sie dann **Microsoft.EntityFrameworkCore.SqlServer** in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="4374c-221">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="4374c-222">Wählen Sie im linken Bereich **Microsoft.EntityFrameworkCore.SqlServer V3.0.0-preview** aus.</span><span class="sxs-lookup"><span data-stu-id="4374c-222">Select  **Microsoft.EntityFrameworkCore.SqlServer V3.0.0-preview** in the left pane.</span></span>
* <span data-ttu-id="4374c-223">Aktivieren Sie das Kontrollkästchen **Projekt** im rechten Bereich, und klicken Sie dann auf **Installieren**.</span><span class="sxs-lookup"><span data-stu-id="4374c-223">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="4374c-224">Fügen Sie mithilfe der obigen Schritte das `Microsoft.EntityFrameworkCore.InMemory`-NuGet-Paket hinzu.</span><span class="sxs-lookup"><span data-stu-id="4374c-224">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![NuGet-Paket-Manager](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="4374c-226">Hinzufügen des TodoContext-Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="4374c-226">Add the TodoContext database context</span></span>

* <span data-ttu-id="4374c-227">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="4374c-227">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="4374c-228">Nennen Sie die Klasse *TodoContext*, und klicken Sie auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="4374c-228">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="4374c-229">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4374c-229">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="4374c-230">Fügen Sie eine `TodoContext`-Klasse zum Ordner *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="4374c-230">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="4374c-231">Geben Sie den folgenden Code ein:</span><span class="sxs-lookup"><span data-stu-id="4374c-231">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="4374c-232">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="4374c-232">Register the database context</span></span>

<span data-ttu-id="4374c-233">In ASP.NET Core müssen Dienste wie der Datenbankkontext mit dem Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) registriert werden.</span><span class="sxs-lookup"><span data-stu-id="4374c-233">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="4374c-234">Der Container stellt den Dienst für Controller bereit.</span><span class="sxs-lookup"><span data-stu-id="4374c-234">The container provides the service to controllers.</span></span>

<span data-ttu-id="4374c-235">Aktualisieren Sie *Startup.cs* mit dem folgenden hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="4374c-235">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="4374c-236">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="4374c-236">The preceding code:</span></span>

* <span data-ttu-id="4374c-237">Entfernt nicht benötigte `using`-Deklarationen</span><span class="sxs-lookup"><span data-stu-id="4374c-237">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="4374c-238">Fügt dem Abhängigkeitscontainer den Datenbankkontext hinzu</span><span class="sxs-lookup"><span data-stu-id="4374c-238">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="4374c-239">Gibt an, dass der Datenbankkontext eine In-Memory Database verwendet</span><span class="sxs-lookup"><span data-stu-id="4374c-239">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="4374c-240">Erstellen eines Controllergerüsts</span><span class="sxs-lookup"><span data-stu-id="4374c-240">Scaffold a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4374c-241">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4374c-241">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4374c-242">Klicken Sie mit der rechten Maustaste auf den Ordner *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="4374c-242">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="4374c-243">Klicken Sie auf **Hinzufügen** > **New Scaffolded Item** (Neues Gerüstelement).</span><span class="sxs-lookup"><span data-stu-id="4374c-243">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="4374c-244">Klicken Sie auf **API-Controller mit Aktionen unter Verwendung von Entity Framework** und dann auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="4374c-244">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="4374c-245">Führen Sie im Dialogfeld **API-Controller mit Aktionen unter Verwendung von Entity Framework** folgende Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="4374c-245">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="4374c-246">Wählen Sie in der **Modellklasse** das Element **TodoItem (TodoAPI.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="4374c-246">Select **TodoItem (TodoAPI.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="4374c-247">Wählen Sie in der **Datenkontextklasse** das Element **TodoContext (TodoAPI.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="4374c-247">Select **TodoContext (TodoAPI.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="4374c-248">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="4374c-248">Select **Add**</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="4374c-249">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4374c-249">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="4374c-250">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="4374c-250">Run the following commands:</span></span>

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.0.0-*
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext  -outDir Controllers
```

<span data-ttu-id="4374c-251">Die obenstehenden Befehle haben folgende Konsequenzen:</span><span class="sxs-lookup"><span data-stu-id="4374c-251">The preceding commands:</span></span>

* <span data-ttu-id="4374c-252">Die NuGet-Pakete für den Gerüstbau werden hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="4374c-252">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="4374c-253">Die Gerüstbau-Engine (`dotnet-aspnet-codegenerator`) wird installiert.</span><span class="sxs-lookup"><span data-stu-id="4374c-253">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="4374c-254">Das Gerüst für `TodoItemsController` wird erstellt.</span><span class="sxs-lookup"><span data-stu-id="4374c-254">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="4374c-255">Der generierte Code hat folgende Auswirkungen:</span><span class="sxs-lookup"><span data-stu-id="4374c-255">The generated code:</span></span>

* <span data-ttu-id="4374c-256">Er definiert eine API-Controllerklasse ohne Methoden.</span><span class="sxs-lookup"><span data-stu-id="4374c-256">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="4374c-257">Er ergänzt die Klasse mit dem Attribut [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute).</span><span class="sxs-lookup"><span data-stu-id="4374c-257">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="4374c-258">Dieses Attribut gibt an, dass der Controller auf Web-API-Anforderungen reagiert.</span><span class="sxs-lookup"><span data-stu-id="4374c-258">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="4374c-259">Weitere Informationen zu bestimmten Verhaltensweisen, die das Attribut ermöglicht, finden Sie unter <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="4374c-259">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="4374c-260">Er verwendet die Abhängigkeitsinjektion, um den Datenbankkontext (`TodoContext`) dem Controller hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="4374c-260">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="4374c-261">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="4374c-261">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="4374c-262">Überblick über die PostTodoItem-Erstellungsmethode</span><span class="sxs-lookup"><span data-stu-id="4374c-262">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="4374c-263">Ersetzen Sie die Rückgabeanweisung in `PostTodoItem` durch eine Anweisung mit dem [nameof](/dotnet/csharp/language-reference/operators/nameof)-Operator:</span><span class="sxs-lookup"><span data-stu-id="4374c-263">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="4374c-264">Der vorherige Code ist eine HTTP POST-Methode, die durch das Attribut [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="4374c-264">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="4374c-265">Die Methode ruft den Wert der Aufgabe aus dem Text der HTTP-Anforderung ab.</span><span class="sxs-lookup"><span data-stu-id="4374c-265">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="4374c-266">Die <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>-Methode führt folgende Aktionen durch:</span><span class="sxs-lookup"><span data-stu-id="4374c-266">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="4374c-267">Sie gibt den HTTP-Statuscode 201 zurück, wenn der Vorgang erfolgreich ist.</span><span class="sxs-lookup"><span data-stu-id="4374c-267">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="4374c-268">HTTP 201 ist die Standardantwort für eine HTTP POST-Methode, die eine neue Ressource auf dem Server erstellt.</span><span class="sxs-lookup"><span data-stu-id="4374c-268">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="4374c-269">Sie fügt der Antwort einen [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location)-Header hinzu.</span><span class="sxs-lookup"><span data-stu-id="4374c-269">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="4374c-270">Der `Location`-Header legt den [URI](https://developer.mozilla.org/docs/Glossary/URI) des neu erstellten To-do-Elements fest.</span><span class="sxs-lookup"><span data-stu-id="4374c-270">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="4374c-271">Weitere Informationen finden Sie unter [10.2.2 201 Erstellt](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="4374c-271">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="4374c-272">Verweist auf die `GetTodoItem`-Aktion zum Erstellen des URIs des `Location`-Headers.</span><span class="sxs-lookup"><span data-stu-id="4374c-272">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="4374c-273">Das `nameof`-Schlüsselwort von C# wird verwendet, um eine Hartcodierung des Aktionsnamens im `CreatedAtAction`-Aufruf zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="4374c-273">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="4374c-274">Installieren von Postman</span><span class="sxs-lookup"><span data-stu-id="4374c-274">Install Postman</span></span>

<span data-ttu-id="4374c-275">Dieses Tutorial verwendet Postman zum Testen der Web-API.</span><span class="sxs-lookup"><span data-stu-id="4374c-275">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="4374c-276">Installieren Sie [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="4374c-276">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="4374c-277">Starten Sie die Web-App.</span><span class="sxs-lookup"><span data-stu-id="4374c-277">Start the web app.</span></span>
* <span data-ttu-id="4374c-278">Starten Sie Postman.</span><span class="sxs-lookup"><span data-stu-id="4374c-278">Start Postman.</span></span>
* <span data-ttu-id="4374c-279">Deaktivieren Sie **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="4374c-279">Disable **SSL certificate verification**</span></span>
* <span data-ttu-id="4374c-280">Deaktivieren Sie auf der Registerkarte \**General* (Allgemein) unter **File > Settings** (Datei > Einstellungen) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="4374c-280">From  **File > Settings** (\**General* tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="4374c-281">Aktivieren Sie die Verifizierung des SSL-Zertifikats wieder, nachdem Sie den Controller getestet haben.</span><span class="sxs-lookup"><span data-stu-id="4374c-281">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="4374c-282">Testen von PostTodoItem mit Postman</span><span class="sxs-lookup"><span data-stu-id="4374c-282">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="4374c-283">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="4374c-283">Create a new request.</span></span>
* <span data-ttu-id="4374c-284">Legen Sie als HTTP-Methode `POST` fest.</span><span class="sxs-lookup"><span data-stu-id="4374c-284">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="4374c-285">Wählen Sie die Registerkarte **Body** (Text) aus.</span><span class="sxs-lookup"><span data-stu-id="4374c-285">Select the **Body** tab.</span></span>
* <span data-ttu-id="4374c-286">Wählen Sie das Optionsfeld **raw** (Unformatiert) aus.</span><span class="sxs-lookup"><span data-stu-id="4374c-286">Select the **raw** radio button.</span></span>
* <span data-ttu-id="4374c-287">Legen Sie den Typ auf **JSON (application/json)** fest.</span><span class="sxs-lookup"><span data-stu-id="4374c-287">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="4374c-288">Geben Sie für die Aufgabe den Anforderungstext im JSON-Format ein:</span><span class="sxs-lookup"><span data-stu-id="4374c-288">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="4374c-289">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="4374c-289">Select **Send**.</span></span>

  ![Postman mit erstellter Anforderung](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="4374c-291">Testen des Adressheader-URIs</span><span class="sxs-lookup"><span data-stu-id="4374c-291">Test the location header URI</span></span>

* <span data-ttu-id="4374c-292">Wählen Sie auf der Registerkarte **Header** (Header) den Bereich **Response** (Antwort) aus.</span><span class="sxs-lookup"><span data-stu-id="4374c-292">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="4374c-293">Kopieren Sie den den Headerwert von **Location** (Speicherort):</span><span class="sxs-lookup"><span data-stu-id="4374c-293">Copy the **Location** header value:</span></span>

  ![Registerkarte „Header“ in der Postman-Konsole](first-web-api/_static/3/create.png)

* <span data-ttu-id="4374c-295">Legen Sie die Methode auf „GET“ fest.</span><span class="sxs-lookup"><span data-stu-id="4374c-295">Set the method to GET.</span></span>
* <span data-ttu-id="4374c-296">Fügen Sie den URI (z. B. `https://localhost:5001/api/TodoItems/1`) ein.</span><span class="sxs-lookup"><span data-stu-id="4374c-296">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`)</span></span>
* <span data-ttu-id="4374c-297">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="4374c-297">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="4374c-298">Überblick über die GET-Methoden</span><span class="sxs-lookup"><span data-stu-id="4374c-298">Examine the GET methods</span></span>

<span data-ttu-id="4374c-299">Diese Methoden implementieren zwei GET-Endpunkte:</span><span class="sxs-lookup"><span data-stu-id="4374c-299">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="4374c-300">Testen Sie die App, indem Sie die beiden Endpunkte in einem Browser oder über Postman aufrufen.</span><span class="sxs-lookup"><span data-stu-id="4374c-300">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="4374c-301">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="4374c-301">For example:</span></span>

* [https://localhost:5001/api/TodoItems](https://localhost:5001/api/TodoItems)
* [https://localhost:5001/api/TodoItems/1](https://localhost:5001/api/TodoItems/1)

<span data-ttu-id="4374c-302">Durch einen Aufruf von `GetTodoItems` wird eine Antwort gesendet, die der folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="4374c-302">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="4374c-303">Testen von GET mit Postman</span><span class="sxs-lookup"><span data-stu-id="4374c-303">Test Get with Postman</span></span>

* <span data-ttu-id="4374c-304">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="4374c-304">Create a new request.</span></span>
* <span data-ttu-id="4374c-305">Legen Sie die HTTP-Methode auf **GET** fest.</span><span class="sxs-lookup"><span data-stu-id="4374c-305">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="4374c-306">Legen Sie die Anforderungs-URL auf `https://localhost:<port>/api/TodoItems` fest.</span><span class="sxs-lookup"><span data-stu-id="4374c-306">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="4374c-307">Beispielsweise `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="4374c-307">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="4374c-308">Wählen Sie in Postman **Two pane view** (Ansicht mit zwei Bereichen) aus.</span><span class="sxs-lookup"><span data-stu-id="4374c-308">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="4374c-309">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="4374c-309">Select **Send**.</span></span>

<span data-ttu-id="4374c-310">Diese App verwendet eine In-Memory-Datenbank.</span><span class="sxs-lookup"><span data-stu-id="4374c-310">This app uses an in-memory database.</span></span> <span data-ttu-id="4374c-311">Wenn die App angehalten und dann wieder gestartet wird, werden durch die vorherige GET-Anforderung keine Daten zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="4374c-311">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="4374c-312">Wenn keine Daten zurückgegeben werden, senden Sie mit [POST](#post) Daten an die App.</span><span class="sxs-lookup"><span data-stu-id="4374c-312">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="4374c-313">Routing und URL-Pfade</span><span class="sxs-lookup"><span data-stu-id="4374c-313">Routing and URL paths</span></span>

<span data-ttu-id="4374c-314">Das Attribut [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) gibt eine Methode an, die auf eine HTTP GET-Anforderung antwortet.</span><span class="sxs-lookup"><span data-stu-id="4374c-314">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="4374c-315">Der URL-Pfad für jede Methode wird wie folgt erstellt:</span><span class="sxs-lookup"><span data-stu-id="4374c-315">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="4374c-316">Beginnen Sie mit der Vorlagenzeichenfolge im `Route`-Attribut des Controllers:</span><span class="sxs-lookup"><span data-stu-id="4374c-316">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="4374c-317">Ersetzen Sie `[controller]` durch den Namen des Controllers, bei dem es sich standardmäßig um den Namen der Controller-Klasse ohne das Suffix „Controller“ handelt.</span><span class="sxs-lookup"><span data-stu-id="4374c-317">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="4374c-318">In diesem Beispiel ist der Klassenname des Controllers „**TodoItems**Controller“. Der Controllername lautet also „TodoItems“.</span><span class="sxs-lookup"><span data-stu-id="4374c-318">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="4374c-319">Beim ASP.NET Core-[Routing](xref:mvc/controllers/routing) wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="4374c-319">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="4374c-320">Wenn das `[HttpGet]`-Attribut eine Routenvorlage (z.B. `[HttpGet("products")]`) hat, fügen Sie diese an den Pfad an.</span><span class="sxs-lookup"><span data-stu-id="4374c-320">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="4374c-321">In diesem Beispiel wird keine Vorlage verwendet.</span><span class="sxs-lookup"><span data-stu-id="4374c-321">This sample doesn't use a template.</span></span> <span data-ttu-id="4374c-322">Weitere Informationen finden Sie unter [Attributrouting mit Http[Verb]-Attributen](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="4374c-322">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="4374c-323">In der folgenden `GetTodoItem`-Methode ist `"{id}"` eine Platzhaltervariable für den eindeutigen Bezeichners des To-do-Elements.</span><span class="sxs-lookup"><span data-stu-id="4374c-323">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="4374c-324">Wenn `GetTodoItem` aufgerufen wird, wird der Wert von `"{id}"` in der URL der Methode in ihrem Parameter `id` bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="4374c-324">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="4374c-325">Rückgabewert</span><span class="sxs-lookup"><span data-stu-id="4374c-325">Return values</span></span>

<span data-ttu-id="4374c-326">Der Rückgabetyp der Methoden `GetTodoItems` und `GetTodoItem` ist [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="4374c-326">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="4374c-327">ASP.NET Core serialisiert automatisch das Objekt in [JSON](https://www.json.org/) und schreibt den JSON-Code in den Text der Antwortnachricht.</span><span class="sxs-lookup"><span data-stu-id="4374c-327">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="4374c-328">Der Antwortcode für diesen Rückgabetyp ist 200, vorausgesetzt, es gibt keine Ausnahmefehler.</span><span class="sxs-lookup"><span data-stu-id="4374c-328">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="4374c-329">Nicht behandelte Ausnahmen werden in 5xx-Fehler übersetzt.</span><span class="sxs-lookup"><span data-stu-id="4374c-329">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="4374c-330">`ActionResult`-Rückgabetypen können eine Vielzahl von HTTP-Statuscodes darstellen.</span><span class="sxs-lookup"><span data-stu-id="4374c-330">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="4374c-331">Beispielsweise kann `GetTodoItem` zwei verschiedene Statuswerte zurückgeben:</span><span class="sxs-lookup"><span data-stu-id="4374c-331">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="4374c-332">Wenn kein Element mit der angeforderten ID übereinstimmt, gibt die Methode einen 404-Fehlercode [Nicht gefunden](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) zurück.</span><span class="sxs-lookup"><span data-stu-id="4374c-332">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="4374c-333">Andernfalls gibt die Methode 200 mit einem JSON-Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="4374c-333">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="4374c-334">Die Rückgabe von `item` löst eine HTTP 200-Antwort aus.</span><span class="sxs-lookup"><span data-stu-id="4374c-334">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="4374c-335">PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="4374c-335">The PutTodoItem method</span></span>

<span data-ttu-id="4374c-336">Untersuchen Sie die `PutTodoItem`-Methode.</span><span class="sxs-lookup"><span data-stu-id="4374c-336">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="4374c-337">`PutTodoItem` ähnelt `PostTodoItem`, verwendet allerdings HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="4374c-337">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="4374c-338">Die Antwort ist [204 (Kein Inhalt)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="4374c-338">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="4374c-339">Gemäß der HTTP-Spezifikation erfordert eine PUT-Anforderung, dass der Client die gesamte aktualisierte Entität (nicht nur die Änderungen) sendet.</span><span class="sxs-lookup"><span data-stu-id="4374c-339">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="4374c-340">Verwenden Sie [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute), um Teilupdates zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="4374c-340">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="4374c-341">Wenn beim Aufrufen von `PutTodoItem` ein Fehler zurückgegeben wird, rufen Sie `GET` auf, um sicherzustellen, dass die Datenbank ein Element enthält.</span><span class="sxs-lookup"><span data-stu-id="4374c-341">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="4374c-342">Testen der PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="4374c-342">Test the PutTodoItem method</span></span>

<span data-ttu-id="4374c-343">In diesem Beispiel wird eine In-Memory Database verwendet, die jedes Mal initialisiert werden muss, wenn die App gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="4374c-343">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="4374c-344">Es muss ein Element in der Datenbank vorhanden sein, bevor Sie einen PUT-Aufruf durchführen.</span><span class="sxs-lookup"><span data-stu-id="4374c-344">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="4374c-345">Rufen Sie vor einem PUT-Aufruf GET auf, um sicherzustellen, dass ein Element in der Datenbank vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="4374c-345">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="4374c-346">Aktualisieren Sie das To-do-Element, das über den ID-Wert 1 verfügt, und legen Sie als Namen „feed fish“ fest:</span><span class="sxs-lookup"><span data-stu-id="4374c-346">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="4374c-347">Die folgende Abbildung zeigt das Postman-Update:</span><span class="sxs-lookup"><span data-stu-id="4374c-347">The following image shows the Postman update:</span></span>

![Postman-Konsole mit der Antwort „204 (Kein Inhalt)“](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="4374c-349">DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="4374c-349">The DeleteTodoItem method</span></span>

<span data-ttu-id="4374c-350">Untersuchen Sie die `DeleteTodoItem`-Methode.</span><span class="sxs-lookup"><span data-stu-id="4374c-350">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

<span data-ttu-id="4374c-351">Die `DeleteTodoItem`-Antwort lautet [204 (Kein Inhalt)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="4374c-351">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="4374c-352">Testen der DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="4374c-352">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="4374c-353">So löschen Sie mit Postman eine Aufgabe</span><span class="sxs-lookup"><span data-stu-id="4374c-353">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="4374c-354">Legen Sie die Methode auf `DELETE` fest.</span><span class="sxs-lookup"><span data-stu-id="4374c-354">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="4374c-355">Legen Sie den URI des zu löschenden Objekts fest, z. B. `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="4374c-355">Set the URI of the object to delete, for example `https://localhost:5001/api/TodoItems/1`</span></span>
* <span data-ttu-id="4374c-356">Klicken Sie auf **Send**.</span><span class="sxs-lookup"><span data-stu-id="4374c-356">Select **Send**</span></span>

## <a name="call-the-api-from-jquery"></a><span data-ttu-id="4374c-357">Aufrufen einer API über jQuery</span><span class="sxs-lookup"><span data-stu-id="4374c-357">Call the API from jQuery</span></span>

<span data-ttu-id="4374c-358">Eine Schritt-für-Schritt-Anleitung finden Sie im [Tutorial: Aufrufen einer ASP.NET Core-Web-API mit jQuery](xref:tutorials/web-api-jquery).</span><span class="sxs-lookup"><span data-stu-id="4374c-358">See [Tutorial: Call an ASP.NET Core web API with jQuery](xref:tutorials/web-api-jquery).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4374c-359">In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="4374c-359">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4374c-360">Erstellen eines Web-API-Projekts</span><span class="sxs-lookup"><span data-stu-id="4374c-360">Create a web API project.</span></span>
> * <span data-ttu-id="4374c-361">Hinzufügen einer Modellklasse und eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="4374c-361">Add a model class and a database context.</span></span>
> * <span data-ttu-id="4374c-362">Hinzufügen eines Controllers</span><span class="sxs-lookup"><span data-stu-id="4374c-362">Add a controller.</span></span>
> * <span data-ttu-id="4374c-363">Hinzufügen von CRUD-Methoden</span><span class="sxs-lookup"><span data-stu-id="4374c-363">Add CRUD methods.</span></span>
> * <span data-ttu-id="4374c-364">Konfigurieren von Routing und URL-Pfaden</span><span class="sxs-lookup"><span data-stu-id="4374c-364">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="4374c-365">Angeben von Rückgabewerten</span><span class="sxs-lookup"><span data-stu-id="4374c-365">Specify return values.</span></span>
> * <span data-ttu-id="4374c-366">Aufrufen der Web-API mit Postman</span><span class="sxs-lookup"><span data-stu-id="4374c-366">Call the web API with Postman.</span></span>
> * <span data-ttu-id="4374c-367">Aufrufen der Web-API mit jQuery</span><span class="sxs-lookup"><span data-stu-id="4374c-367">Call the web API with jQuery.</span></span>

<span data-ttu-id="4374c-368">Am Ende haben Sie eine Web-API, die in einer relationalen Datenbank gespeicherte „Aufgaben“ verwalten kann.</span><span class="sxs-lookup"><span data-stu-id="4374c-368">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>
## <a name="overview"></a><span data-ttu-id="4374c-369">Übersicht</span><span class="sxs-lookup"><span data-stu-id="4374c-369">Overview</span></span>

<span data-ttu-id="4374c-370">In diesem Tutorial wird die folgende API erstellt:</span><span class="sxs-lookup"><span data-stu-id="4374c-370">This tutorial creates the following API:</span></span>

|<span data-ttu-id="4374c-371">API</span><span class="sxs-lookup"><span data-stu-id="4374c-371">API</span></span> | <span data-ttu-id="4374c-372">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="4374c-372">Description</span></span> | <span data-ttu-id="4374c-373">Anforderungstext</span><span class="sxs-lookup"><span data-stu-id="4374c-373">Request body</span></span> | <span data-ttu-id="4374c-374">Antworttext</span><span class="sxs-lookup"><span data-stu-id="4374c-374">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="4374c-375">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="4374c-375">GET /api/TodoItems</span></span> | <span data-ttu-id="4374c-376">Alle To-do-Elemente abrufen</span><span class="sxs-lookup"><span data-stu-id="4374c-376">Get all to-do items</span></span> | <span data-ttu-id="4374c-377">Keine</span><span class="sxs-lookup"><span data-stu-id="4374c-377">None</span></span> | <span data-ttu-id="4374c-378">Array von To-do-Elementen</span><span class="sxs-lookup"><span data-stu-id="4374c-378">Array of to-do items</span></span>|
|<span data-ttu-id="4374c-379">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="4374c-379">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="4374c-380">Ein Element nach ID abrufen</span><span class="sxs-lookup"><span data-stu-id="4374c-380">Get an item by ID</span></span> | <span data-ttu-id="4374c-381">Keine</span><span class="sxs-lookup"><span data-stu-id="4374c-381">None</span></span> | <span data-ttu-id="4374c-382">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="4374c-382">To-do item</span></span>|
|<span data-ttu-id="4374c-383">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="4374c-383">POST /api/TodoItems</span></span> | <span data-ttu-id="4374c-384">Neues Element hinzufügen</span><span class="sxs-lookup"><span data-stu-id="4374c-384">Add a new item</span></span> | <span data-ttu-id="4374c-385">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="4374c-385">To-do item</span></span> | <span data-ttu-id="4374c-386">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="4374c-386">To-do item</span></span> |
|<span data-ttu-id="4374c-387">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="4374c-387">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="4374c-388">Vorhandenes Element aktualisieren &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4374c-388">Update an existing item &nbsp;</span></span> | <span data-ttu-id="4374c-389">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="4374c-389">To-do item</span></span> | <span data-ttu-id="4374c-390">Keine</span><span class="sxs-lookup"><span data-stu-id="4374c-390">None</span></span> |
|<span data-ttu-id="4374c-391">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4374c-391">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="4374c-392">Löschen eines Elements &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4374c-392">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="4374c-393">Keine</span><span class="sxs-lookup"><span data-stu-id="4374c-393">None</span></span> | <span data-ttu-id="4374c-394">Keine</span><span class="sxs-lookup"><span data-stu-id="4374c-394">None</span></span>|

<span data-ttu-id="4374c-395">Das folgende Diagramm zeigt den Entwurf der App.</span><span class="sxs-lookup"><span data-stu-id="4374c-395">The following diagram shows the design of the app.</span></span>

![Der Client ist das Feld ganz links.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="4374c-401">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="4374c-401">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4374c-402">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4374c-402">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4374c-403">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4374c-403">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4374c-404">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4374c-404">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="4374c-405">Erstellen eines Webprojekts</span><span class="sxs-lookup"><span data-stu-id="4374c-405">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4374c-406">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4374c-406">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4374c-407">Klicken Sie im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="4374c-407">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="4374c-408">Wählen Sie die Vorlage **ASP.NET Core-Webanwendung** aus, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="4374c-408">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="4374c-409">Geben Sie dem Projekt den Namen *TodoApi*, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="4374c-409">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="4374c-410">Vergewissern Sie sich, dass im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Optionen **.NET Core** und **ASP.NET Core 2.2** ausgewählt sind.</span><span class="sxs-lookup"><span data-stu-id="4374c-410">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="4374c-411">Wählen Sie die Vorlage **API** aus, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="4374c-411">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="4374c-412">Wählen Sie **nicht** **Enable Docker Support** (Docker-Unterstützung aktivieren) aus.</span><span class="sxs-lookup"><span data-stu-id="4374c-412">**Don't** select **Enable Docker Support**.</span></span>

![VS-Dialogfeld „Neues Projekt“](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4374c-414">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4374c-414">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4374c-415">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="4374c-415">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="4374c-416">Wechseln Sie vom Verzeichnis (`cd`) zum Ordner, der den Projektordner enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="4374c-416">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="4374c-417">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="4374c-417">Run the following commands:</span></span>

   ```console
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="4374c-418">Diese Befehle erstellen ein neues Web-API-Projekt und öffnen eine neue Visual Studio Code-Instanz im neuen Projektordner.</span><span class="sxs-lookup"><span data-stu-id="4374c-418">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="4374c-419">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem Projekt die erforderlichen Elemente hinzufügen möchten, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="4374c-419">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4374c-420">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4374c-420">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4374c-421">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="4374c-421">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="4374c-423">Klicken Sie auf **.NET Core** > **App** > **API** > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="4374c-423">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![Dialogfeld „Neue Projektmappe“ in macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="4374c-425">Übernehmen Sie im Dialogfeld **Neue ASP.NET Core-Web-API konfigurieren** die Standardeinstellung **Zielframework** von \* *.NET Core 2.2*.</span><span class="sxs-lookup"><span data-stu-id="4374c-425">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="4374c-426">Geben Sie für **Projektname** *TodoApi* ein, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="4374c-426">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogfeld „Konfiguration“](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="4374c-428">Testen der API</span><span class="sxs-lookup"><span data-stu-id="4374c-428">Test the API</span></span>

<span data-ttu-id="4374c-429">Die Projektvorlage erstellt eine `values`-API.</span><span class="sxs-lookup"><span data-stu-id="4374c-429">The project template creates a `values` API.</span></span> <span data-ttu-id="4374c-430">Rufen Sie in einem Browser die `Get`-Methode zum Testen der App auf.</span><span class="sxs-lookup"><span data-stu-id="4374c-430">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4374c-431">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4374c-431">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4374c-432">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="4374c-432">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="4374c-433">Visual Studio startet einen Browser und navigiert zu `https://localhost:<port>/api/values`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="4374c-433">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="4374c-434">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem IIS Express-Zertifikat vertrauen, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="4374c-434">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="4374c-435">Wählen Sie im folgenden Dialogfeld **Sicherheitswarnung** die Option **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="4374c-435">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4374c-436">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4374c-436">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4374c-437">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="4374c-437">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="4374c-438">Rufen Sie in einem Browser die folgende URL auf: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span><span class="sxs-lookup"><span data-stu-id="4374c-438">In a browser, go to following URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4374c-439">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4374c-439">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4374c-440">Wählen Sie **Run** > **Start Debugging** (Ausführen > Debugging starten) aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="4374c-440">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="4374c-441">Visual Studio für Mac startet einen Browser und navigiert zu `https://localhost:<port>`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="4374c-441">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="4374c-442">Der Fehler „HTTP 404: Nicht gefunden“ wird zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="4374c-442">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="4374c-443">Fügen Sie der URL `/api/values` an, d.h. ändern Sie die URL in `https://localhost:<port>/api/values`.</span><span class="sxs-lookup"><span data-stu-id="4374c-443">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="4374c-444">Die folgende JSON-Datei wird zurückgegeben:</span><span class="sxs-lookup"><span data-stu-id="4374c-444">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="4374c-445">Hinzufügen einer Modellklasse</span><span class="sxs-lookup"><span data-stu-id="4374c-445">Add a model class</span></span>

<span data-ttu-id="4374c-446">Ein *Modell* ist eine Gruppe von Klassen, die die Daten darstellen, die die App verwaltet.</span><span class="sxs-lookup"><span data-stu-id="4374c-446">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="4374c-447">Das Modell für diese App ist eine einzelne `TodoItem`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="4374c-447">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4374c-448">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4374c-448">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4374c-449">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="4374c-449">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="4374c-450">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="4374c-450">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="4374c-451">Geben Sie dem Ordner den Namen *Models*.</span><span class="sxs-lookup"><span data-stu-id="4374c-451">Name the folder *Models*.</span></span>

* <span data-ttu-id="4374c-452">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="4374c-452">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="4374c-453">Nennen Sie die Klasse *TodoItem*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="4374c-453">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="4374c-454">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="4374c-454">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4374c-455">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4374c-455">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4374c-456">Fügen Sie einen Ordner namens *Models* hinzu.</span><span class="sxs-lookup"><span data-stu-id="4374c-456">Add a folder named *Models*.</span></span>

* <span data-ttu-id="4374c-457">Fügen Sie dem Ordner *Modelle* mit dem folgenden Code eine `TodoItem`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="4374c-457">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4374c-458">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4374c-458">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4374c-459">Klicken Sie mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="4374c-459">Right-click the project.</span></span> <span data-ttu-id="4374c-460">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="4374c-460">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="4374c-461">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="4374c-461">Name the folder *Models*.</span></span>

  ![Neuer Ordner](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="4374c-463">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und klicken Sie auf **Hinzufügen** > **Neue Datei** > **Allgemein** > **Leere Klasse**.</span><span class="sxs-lookup"><span data-stu-id="4374c-463">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="4374c-464">Nennen Sie die Klasse *TodoItem*, und klicken Sie dann auf **Neu**.</span><span class="sxs-lookup"><span data-stu-id="4374c-464">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="4374c-465">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="4374c-465">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="4374c-466">Die `Id`-Eigenschaft fungiert als eindeutiger Schlüssel in einer relationalen Datenbank.</span><span class="sxs-lookup"><span data-stu-id="4374c-466">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="4374c-467">Modellklassen können überall im Projekt platziert werden, doch gemäß der Konvention wird der Ordner *Modelle* verwendet.</span><span class="sxs-lookup"><span data-stu-id="4374c-467">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="4374c-468">Hinzufügen eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="4374c-468">Add a database context</span></span>

<span data-ttu-id="4374c-469">Der *Datenbankkontext* ist die Hauptklasse, die die Entity Framework-Funktionen für ein Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="4374c-469">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="4374c-470">Diese Klasse wird durch Ableiten von der `Microsoft.EntityFrameworkCore.DbContext`-Klasse erstellt.</span><span class="sxs-lookup"><span data-stu-id="4374c-470">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4374c-471">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4374c-471">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4374c-472">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="4374c-472">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="4374c-473">Nennen Sie die Klasse *TodoContext*, und klicken Sie auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="4374c-473">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="4374c-474">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4374c-474">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="4374c-475">Fügen Sie eine `TodoContext`-Klasse zum Ordner *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="4374c-475">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="4374c-476">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="4374c-476">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="4374c-477">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="4374c-477">Register the database context</span></span>

<span data-ttu-id="4374c-478">In ASP.NET Core müssen Dienste wie der Datenbankkontext mit dem Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) registriert werden.</span><span class="sxs-lookup"><span data-stu-id="4374c-478">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="4374c-479">Der Container stellt den Dienst für Controller bereit.</span><span class="sxs-lookup"><span data-stu-id="4374c-479">The container provides the service to controllers.</span></span>

<span data-ttu-id="4374c-480">Aktualisieren Sie *Startup.cs* mit dem folgenden hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="4374c-480">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="4374c-481">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="4374c-481">The preceding code:</span></span>

* <span data-ttu-id="4374c-482">Entfernt nicht benötigte `using`-Deklarationen</span><span class="sxs-lookup"><span data-stu-id="4374c-482">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="4374c-483">Fügt dem Abhängigkeitscontainer den Datenbankkontext hinzu</span><span class="sxs-lookup"><span data-stu-id="4374c-483">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="4374c-484">Gibt an, dass der Datenbankkontext eine In-Memory Database verwendet</span><span class="sxs-lookup"><span data-stu-id="4374c-484">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="4374c-485">Hinzufügen eines Controllers</span><span class="sxs-lookup"><span data-stu-id="4374c-485">Add a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4374c-486">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4374c-486">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4374c-487">Klicken Sie mit der rechten Maustaste auf den Ordner *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="4374c-487">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="4374c-488">Klicken Sie auf **Hinzufügen** > **Neues Element**.</span><span class="sxs-lookup"><span data-stu-id="4374c-488">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="4374c-489">Wählen Sie im Dialogfeld **Neues Element hinzufügen** die Vorlage **API-Controllerklasse** aus.</span><span class="sxs-lookup"><span data-stu-id="4374c-489">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="4374c-490">Benennen Sie die Klasse *TodoController*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="4374c-490">Name the class *TodoController*, and select **Add**.</span></span>

  ![Dialogfeld „Neues Element hinzufügen“ mit Controller im Suchfeld und ausgewähltem Web-API-Controller](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="4374c-492">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4374c-492">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="4374c-493">Erstellen Sie im Ordner *Controllers* eine Klasse namens `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="4374c-493">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="4374c-494">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="4374c-494">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="4374c-495">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="4374c-495">The preceding code:</span></span>

* <span data-ttu-id="4374c-496">Er definiert eine API-Controllerklasse ohne Methoden.</span><span class="sxs-lookup"><span data-stu-id="4374c-496">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="4374c-497">Ergänzt die Klasse mit dem Attribut [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute).</span><span class="sxs-lookup"><span data-stu-id="4374c-497">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="4374c-498">Dieses Attribut gibt an, dass der Controller auf Web-API-Anforderungen reagiert.</span><span class="sxs-lookup"><span data-stu-id="4374c-498">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="4374c-499">Weitere Informationen zu bestimmten Verhaltensweisen, die das Attribut ermöglicht, finden Sie unter <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="4374c-499">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="4374c-500">Verwendet die Abhängigkeitsinjektion, um den Datenbankkontext (`TodoContext`) dem Controller hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="4374c-500">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="4374c-501">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="4374c-501">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="4374c-502">Fügt der Datenbank, falls sie leer ist, ein Element mit dem Namen `Item1` hinzu.</span><span class="sxs-lookup"><span data-stu-id="4374c-502">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="4374c-503">Dieser Code befindet sich im Konstruktor. Er wird bei jeder neuen HTTP-Anforderung ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="4374c-503">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="4374c-504">Wenn Sie alle Elemente löschen, erstellt der Konstruktor beim nächsten Aufruf einer API-Methode erneut `Item1`.</span><span class="sxs-lookup"><span data-stu-id="4374c-504">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="4374c-505">So sieht es möglicherweise so aus, als sei die Löschung fehlgeschlagen, obwohl sie erfolgreich war.</span><span class="sxs-lookup"><span data-stu-id="4374c-505">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="4374c-506">Hinzufügen von GET-Methoden</span><span class="sxs-lookup"><span data-stu-id="4374c-506">Add Get methods</span></span>

<span data-ttu-id="4374c-507">Fügen Sie der Klasse `TodoController` die folgenden Methoden hinzu, um eine API bereitzustellen, die Aufgaben abruft:</span><span class="sxs-lookup"><span data-stu-id="4374c-507">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="4374c-508">Diese Methoden implementieren zwei GET-Endpunkte:</span><span class="sxs-lookup"><span data-stu-id="4374c-508">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="4374c-509">Halten Sie die App an, falls diese noch ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="4374c-509">Stop the app if it's still running.</span></span> <span data-ttu-id="4374c-510">Führen Sie diese dann noch mal aus, damit die letzten Änderungen übernommen werden.</span><span class="sxs-lookup"><span data-stu-id="4374c-510">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="4374c-511">Testen Sie die App, indem Sie die beiden Endpunkte in einem Browser aufrufen.</span><span class="sxs-lookup"><span data-stu-id="4374c-511">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="4374c-512">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="4374c-512">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="4374c-513">Die folgende HTTP-Antwort wird durch den Aufruf von `GetTodoItems` erzeugt:</span><span class="sxs-lookup"><span data-stu-id="4374c-513">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="4374c-514">Routing und URL-Pfade</span><span class="sxs-lookup"><span data-stu-id="4374c-514">Routing and URL paths</span></span>

<span data-ttu-id="4374c-515">Das Attribut [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) gibt eine Methode an, die auf eine HTTP GET-Anforderung antwortet.</span><span class="sxs-lookup"><span data-stu-id="4374c-515">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="4374c-516">Der URL-Pfad für jede Methode wird wie folgt erstellt:</span><span class="sxs-lookup"><span data-stu-id="4374c-516">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="4374c-517">Beginnen Sie mit der Vorlagenzeichenfolge im `Route`-Attribut des Controllers:</span><span class="sxs-lookup"><span data-stu-id="4374c-517">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="4374c-518">Ersetzen Sie `[controller]` durch den Namen des Controllers, bei dem es sich standardmäßig um den Namen der Controller-Klasse ohne das Suffix „Controller“ handelt.</span><span class="sxs-lookup"><span data-stu-id="4374c-518">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="4374c-519">Bei diesem Beispiel ist der Klassenname des Controllers „**Todo**Controller“, d.h. der Controllername lautet „todo“.</span><span class="sxs-lookup"><span data-stu-id="4374c-519">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="4374c-520">Beim ASP.NET Core-[Routing](xref:mvc/controllers/routing) wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="4374c-520">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="4374c-521">Wenn das `[HttpGet]`-Attribut eine Routenvorlage (z.B. `[HttpGet("products")]`) hat, fügen Sie diese an den Pfad an.</span><span class="sxs-lookup"><span data-stu-id="4374c-521">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="4374c-522">In diesem Beispiel wird keine Vorlage verwendet.</span><span class="sxs-lookup"><span data-stu-id="4374c-522">This sample doesn't use a template.</span></span> <span data-ttu-id="4374c-523">Weitere Informationen finden Sie unter [Attributrouting mit Http[Verb]-Attributen](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="4374c-523">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="4374c-524">In der folgenden `GetTodoItem`-Methode ist `"{id}"` eine Platzhaltervariable für den eindeutigen Bezeichners des To-do-Elements.</span><span class="sxs-lookup"><span data-stu-id="4374c-524">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="4374c-525">Wenn `GetTodoItem` aufgerufen wird, wird der Wert von `"{id}"` in der URL der Methode in ihrem Parameter `id` bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="4374c-525">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="4374c-526">Rückgabewert</span><span class="sxs-lookup"><span data-stu-id="4374c-526">Return values</span></span>

<span data-ttu-id="4374c-527">Der Rückgabetyp der Methoden `GetTodoItems` und `GetTodoItem` ist [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="4374c-527">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="4374c-528">ASP.NET Core serialisiert automatisch das Objekt in [JSON](https://www.json.org/) und schreibt den JSON-Code in den Text der Antwortnachricht.</span><span class="sxs-lookup"><span data-stu-id="4374c-528">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="4374c-529">Der Antwortcode für diesen Rückgabetyp ist 200, vorausgesetzt, es gibt keine Ausnahmefehler.</span><span class="sxs-lookup"><span data-stu-id="4374c-529">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="4374c-530">Nicht behandelte Ausnahmen werden in 5xx-Fehler übersetzt.</span><span class="sxs-lookup"><span data-stu-id="4374c-530">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="4374c-531">`ActionResult`-Rückgabetypen können eine Vielzahl von HTTP-Statuscodes darstellen.</span><span class="sxs-lookup"><span data-stu-id="4374c-531">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="4374c-532">Beispielsweise kann `GetTodoItem` zwei verschiedene Statuswerte zurückgeben:</span><span class="sxs-lookup"><span data-stu-id="4374c-532">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="4374c-533">Wenn kein Element mit der angeforderten ID übereinstimmt, gibt die Methode einen 404-Fehlercode [Nicht gefunden](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) zurück.</span><span class="sxs-lookup"><span data-stu-id="4374c-533">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="4374c-534">Andernfalls gibt die Methode 200 mit einem JSON-Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="4374c-534">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="4374c-535">Die Rückgabe von `item` löst eine HTTP 200-Antwort aus.</span><span class="sxs-lookup"><span data-stu-id="4374c-535">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="4374c-536">Testen der GetTodoItems-Methode</span><span class="sxs-lookup"><span data-stu-id="4374c-536">Test the GetTodoItems method</span></span>

<span data-ttu-id="4374c-537">Dieses Tutorial verwendet Postman zum Testen der Web-API.</span><span class="sxs-lookup"><span data-stu-id="4374c-537">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="4374c-538">Installieren Sie [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="4374c-538">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="4374c-539">Starten Sie die Web-App.</span><span class="sxs-lookup"><span data-stu-id="4374c-539">Start the web app.</span></span>
* <span data-ttu-id="4374c-540">Starten Sie Postman.</span><span class="sxs-lookup"><span data-stu-id="4374c-540">Start Postman.</span></span>
* <span data-ttu-id="4374c-541">Deaktivieren Sie **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="4374c-541">Disable **SSL certificate verification**</span></span>
  
  * <span data-ttu-id="4374c-542">Deaktivieren Sie auf der Registerkarte \**General* (Allgemein) unter **File > Settings** (Datei > Einstellungen) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="4374c-542">From  **File > Settings** (\**General* tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="4374c-543">Aktivieren Sie die Verifizierung des SSL-Zertifikats wieder, nachdem Sie den Controller getestet haben.</span><span class="sxs-lookup"><span data-stu-id="4374c-543">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="4374c-544">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="4374c-544">Create a new request.</span></span>
  * <span data-ttu-id="4374c-545">Legen Sie die HTTP-Methode auf **GET** fest.</span><span class="sxs-lookup"><span data-stu-id="4374c-545">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="4374c-546">Legen Sie die Anforderungs-URL auf `https://localhost:<port>/api/todo` fest.</span><span class="sxs-lookup"><span data-stu-id="4374c-546">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="4374c-547">Beispielsweise `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="4374c-547">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="4374c-548">Wählen Sie in Postman **Two pane view** (Ansicht mit zwei Bereichen) aus.</span><span class="sxs-lookup"><span data-stu-id="4374c-548">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="4374c-549">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="4374c-549">Select **Send**.</span></span>

![Postman mit GET-Anforderung](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="4374c-551">Hinzufügen einer Methode</span><span class="sxs-lookup"><span data-stu-id="4374c-551">Add a Create method</span></span>

<span data-ttu-id="4374c-552">Fügen Sie die folgende `PostTodoItem`-Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="4374c-552">Add the following `PostTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="4374c-553">Der vorherige Code ist eine HTTP POST-Methode, die durch das Attribut [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="4374c-553">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="4374c-554">Die Methode ruft den Wert der Aufgabe aus dem Text der HTTP-Anforderung ab.</span><span class="sxs-lookup"><span data-stu-id="4374c-554">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="4374c-555">Die `CreatedAtAction`-Methode:</span><span class="sxs-lookup"><span data-stu-id="4374c-555">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="4374c-556">Gibt bei einer erfolgreichen Anforderung den Statuscode „HTTP 201“ zurück.</span><span class="sxs-lookup"><span data-stu-id="4374c-556">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="4374c-557">HTTP 201 ist die Standardantwort für eine HTTP POST-Methode, die eine neue Ressource auf dem Server erstellt.</span><span class="sxs-lookup"><span data-stu-id="4374c-557">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="4374c-558">Fügt der Antwort einen `Location`-Header hinzu.</span><span class="sxs-lookup"><span data-stu-id="4374c-558">Adds a `Location` header to the response.</span></span> <span data-ttu-id="4374c-559">Der `Location`-Header gibt den URI des neu erstellten To-Do-Elements zurück.</span><span class="sxs-lookup"><span data-stu-id="4374c-559">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="4374c-560">Weitere Informationen finden Sie unter [10.2.2 201 Erstellt](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="4374c-560">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="4374c-561">Verweist auf die `GetTodoItem`-Aktion zum Erstellen des URIs des `Location`-Headers.</span><span class="sxs-lookup"><span data-stu-id="4374c-561">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="4374c-562">Das `nameof`-Schlüsselwort von C# wird verwendet, um eine Hartcodierung des Aktionsnamens im `CreatedAtAction`-Aufruf zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="4374c-562">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="4374c-563">Testen der PostTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="4374c-563">Test the PostTodoItem method</span></span>

* <span data-ttu-id="4374c-564">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="4374c-564">Build the project.</span></span>
* <span data-ttu-id="4374c-565">Legen Sie in Postman die HTTP-Methode auf `POST` fest.</span><span class="sxs-lookup"><span data-stu-id="4374c-565">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="4374c-566">Wählen Sie die Registerkarte **Body** (Text) aus.</span><span class="sxs-lookup"><span data-stu-id="4374c-566">Select the **Body** tab.</span></span>
* <span data-ttu-id="4374c-567">Wählen Sie das Optionsfeld **raw** (Unformatiert) aus.</span><span class="sxs-lookup"><span data-stu-id="4374c-567">Select the **raw** radio button.</span></span>
* <span data-ttu-id="4374c-568">Legen Sie den Typ auf **JSON (application/json)** fest.</span><span class="sxs-lookup"><span data-stu-id="4374c-568">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="4374c-569">Geben Sie für die Aufgabe den Anforderungstext im JSON-Format ein:</span><span class="sxs-lookup"><span data-stu-id="4374c-569">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="4374c-570">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="4374c-570">Select **Send**.</span></span>

  ![Postman mit erstellter Anforderung](first-web-api/_static/create.png)

  <span data-ttu-id="4374c-572">Wenn Sie die Fehlermeldung „405: Methode nicht zulässig“ erhalten, wurde das Projekt wahrscheinlich nicht kompiliert, nachdem die Methode `PostTodoItem` hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="4374c-572">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="4374c-573">Testen des Adressheader-URIs</span><span class="sxs-lookup"><span data-stu-id="4374c-573">Test the location header URI</span></span>

* <span data-ttu-id="4374c-574">Wählen Sie auf der Registerkarte **Header** (Header) den Bereich **Response** (Antwort) aus.</span><span class="sxs-lookup"><span data-stu-id="4374c-574">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="4374c-575">Kopieren Sie den den Headerwert von **Location** (Speicherort):</span><span class="sxs-lookup"><span data-stu-id="4374c-575">Copy the **Location** header value:</span></span>

  ![Registerkarte „Header“ in der Postman-Konsole](first-web-api/_static/pmc2.png)

* <span data-ttu-id="4374c-577">Legen Sie die Methode auf „GET“ fest.</span><span class="sxs-lookup"><span data-stu-id="4374c-577">Set the method to GET.</span></span>
* <span data-ttu-id="4374c-578">Fügen Sie den URI (z. B. `https://localhost:5001/api/Todo/2`) ein.</span><span class="sxs-lookup"><span data-stu-id="4374c-578">Paste the URI (for example, `https://localhost:5001/api/Todo/2`)</span></span>
* <span data-ttu-id="4374c-579">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="4374c-579">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="4374c-580">Hinzufügen einer PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="4374c-580">Add a PutTodoItem method</span></span>

<span data-ttu-id="4374c-581">Fügen Sie die folgende `PutTodoItem`-Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="4374c-581">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="4374c-582">`PutTodoItem` ähnelt `PostTodoItem`, verwendet allerdings HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="4374c-582">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="4374c-583">Die Antwort ist [204 (Kein Inhalt)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="4374c-583">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="4374c-584">Gemäß der HTTP-Spezifikation erfordert eine PUT-Anforderung, dass der Client die gesamte aktualisierte Entität (nicht nur die Änderungen) sendet.</span><span class="sxs-lookup"><span data-stu-id="4374c-584">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="4374c-585">Verwenden Sie [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute), um Teilupdates zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="4374c-585">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="4374c-586">Wenn beim Aufrufen von `PutTodoItem` ein Fehler zurückgegeben wird, rufen Sie `GET` auf, um sicherzustellen, dass die Datenbank ein Element enthält.</span><span class="sxs-lookup"><span data-stu-id="4374c-586">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="4374c-587">Testen der PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="4374c-587">Test the PutTodoItem method</span></span>

<span data-ttu-id="4374c-588">In diesem Beispiel wird eine In-Memory Database verwendet, die jedes Mal initialisiert werden muss, wenn die App gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="4374c-588">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="4374c-589">Es muss ein Element in der Datenbank vorhanden sein, bevor Sie einen PUT-Aufruf durchführen.</span><span class="sxs-lookup"><span data-stu-id="4374c-589">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="4374c-590">Rufen Sie vor einem PUT-Aufruf GET auf, um sicherzustellen, dass ein Element in der Datenbank vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="4374c-590">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="4374c-591">Aktualisieren Sie die Aufgabe mit der ID = 1, und setzen Sie ihren Namen auf „feed fish“:</span><span class="sxs-lookup"><span data-stu-id="4374c-591">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="4374c-592">Die folgende Abbildung zeigt das Postman-Update:</span><span class="sxs-lookup"><span data-stu-id="4374c-592">The following image shows the Postman update:</span></span>

![Postman-Konsole mit der Antwort „204 (Kein Inhalt)“](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="4374c-594">Hinzufügen einer DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="4374c-594">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="4374c-595">Fügen Sie die folgende `DeleteTodoItem`-Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="4374c-595">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="4374c-596">Die `DeleteTodoItem`-Antwort lautet [204 (Kein Inhalt)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="4374c-596">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="4374c-597">Testen der DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="4374c-597">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="4374c-598">So löschen Sie mit Postman eine Aufgabe</span><span class="sxs-lookup"><span data-stu-id="4374c-598">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="4374c-599">Legen Sie die Methode auf `DELETE` fest.</span><span class="sxs-lookup"><span data-stu-id="4374c-599">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="4374c-600">Legen Sie den URI des zu löschenden Objekts fest, z. B. `https://localhost:5001/api/todo/1`.</span><span class="sxs-lookup"><span data-stu-id="4374c-600">Set the URI of the object to delete, for example `https://localhost:5001/api/todo/1`</span></span>
* <span data-ttu-id="4374c-601">Klicken Sie auf **Send**.</span><span class="sxs-lookup"><span data-stu-id="4374c-601">Select **Send**</span></span>

<span data-ttu-id="4374c-602">Sie können in der Beispiel-App alle Elemente löschen.</span><span class="sxs-lookup"><span data-stu-id="4374c-602">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="4374c-603">Sobald das letzte Element gelöscht wurde, wird allerdings beim nächsten API-Aufruf vom Modellklassenkonstruktor ein neues erstellt.</span><span class="sxs-lookup"><span data-stu-id="4374c-603">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-api-with-jquery"></a><span data-ttu-id="4374c-604">Aufrufen der API mit jQuery</span><span class="sxs-lookup"><span data-stu-id="4374c-604">Call the API with jQuery</span></span>

<span data-ttu-id="4374c-605">In diesem Abschnitt wird eine HTML-Seite hinzugefügt, die mithilfe von jQuery die Web-API aufruft.</span><span class="sxs-lookup"><span data-stu-id="4374c-605">In this section, an HTML page is added that uses jQuery to call the web api.</span></span> <span data-ttu-id="4374c-606">jQuery initiiert die Anforderung und aktualisiert die Seite mit den Informationen aus der Antwort der API.</span><span class="sxs-lookup"><span data-stu-id="4374c-606">jQuery initiates the request and updates the page with the details from the API's response.</span></span>

<span data-ttu-id="4374c-607">Konfigurieren Sie die App so, dass sie [statische Dateien bereitstellt](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_), und [aktivieren Sie die Standarddateizuordnung](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_), indem Sie *startup.cs* mit dem unten markierten Code aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="4374c-607">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="4374c-608">Erstellen Sie im Projektverzeichnis den Ordner *wwwwroot*.</span><span class="sxs-lookup"><span data-stu-id="4374c-608">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="4374c-609">Fügen Sie dem Verzeichnis *wwwroot* eine HTML-Datei namens *index.html* hinzu.</span><span class="sxs-lookup"><span data-stu-id="4374c-609">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="4374c-610">Ersetzen Sie den Inhalt durch folgendes Markup:</span><span class="sxs-lookup"><span data-stu-id="4374c-610">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="4374c-611">Fügen Sie dem Verzeichnis *wwwroot* eine JavaScript-Datei namens *site.js* hinzu.</span><span class="sxs-lookup"><span data-stu-id="4374c-611">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="4374c-612">Ersetzen Sie den Inhalt durch folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="4374c-612">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="4374c-613">Möglicherweise ist eine Änderung an den Starteinstellungen des ASP.NET Core-Projekts erforderlich, um die HTML-Seite lokal zu testen:</span><span class="sxs-lookup"><span data-stu-id="4374c-613">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="4374c-614">Öffnen Sie *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="4374c-614">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="4374c-615">Entfernen Sie die `launchUrl`-Eigenschaft, um zu erzwingen, dass die App mit *index.html* als Startseite geöffnet wird. Dies ist die Standarddatei des Projekts.</span><span class="sxs-lookup"><span data-stu-id="4374c-615">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="4374c-616">Es gibt mehrere Möglichkeiten, um jQuery herunterzuladen.</span><span class="sxs-lookup"><span data-stu-id="4374c-616">There are several ways to get jQuery.</span></span> <span data-ttu-id="4374c-617">Im vorherigen Codeausschnitt wurde die Bibliothek aus einem Content Delivery Network (CDN) geladen.</span><span class="sxs-lookup"><span data-stu-id="4374c-617">In the preceding snippet, the library is loaded from a CDN.</span></span>

<span data-ttu-id="4374c-618">Dieses Beispiel ruft alle CRUD-Methoden der API auf.</span><span class="sxs-lookup"><span data-stu-id="4374c-618">This sample calls all of the CRUD methods of the API.</span></span> <span data-ttu-id="4374c-619">Im Folgenden werden die API-Aufrufe erläutert.</span><span class="sxs-lookup"><span data-stu-id="4374c-619">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="4374c-620">Abrufen einer Liste von To-Do-Elementen</span><span class="sxs-lookup"><span data-stu-id="4374c-620">Get a list of to-do items</span></span>

<span data-ttu-id="4374c-621">Die jQuery-Funktion [ajax](https://api.jquery.com/jquery.ajax/) sendet eine `GET`-Anforderung an die API, die JSON-Code zurückgibt, der ein Aufgabenarray darstellt.</span><span class="sxs-lookup"><span data-stu-id="4374c-621">The jQuery [ajax](https://api.jquery.com/jquery.ajax/) function sends a `GET` request to the API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="4374c-622">Die Rückruffunktion `success` wird aufgerufen, wenn die Anforderung erfolgreich ist.</span><span class="sxs-lookup"><span data-stu-id="4374c-622">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="4374c-623">Im Rückruf wird DOM mit den To-Do-Informationen aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="4374c-623">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="4374c-624">Hinzufügen eines To-Do-Elements</span><span class="sxs-lookup"><span data-stu-id="4374c-624">Add a to-do item</span></span>

<span data-ttu-id="4374c-625">Die Funktion [ajax](https://api.jquery.com/jquery.ajax/) sendet eine `POST`-Anforderung mit der Aufgabe im Anforderungstext.</span><span class="sxs-lookup"><span data-stu-id="4374c-625">The [ajax](https://api.jquery.com/jquery.ajax/) function sends a `POST` request with the to-do item in the request body.</span></span> <span data-ttu-id="4374c-626">Die Optionen `accepts` und `contentType` werden auf `application/json` festgelegt, um den gesendeten und empfangenen Medientyp anzugeben.</span><span class="sxs-lookup"><span data-stu-id="4374c-626">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="4374c-627">Die Aufgabe wird mit [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) in JSON konvertiert.</span><span class="sxs-lookup"><span data-stu-id="4374c-627">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="4374c-628">Wenn die API den Statuscode „Erfolgreich“ zurückgibt, wird die Funktion `getData` aufgerufen, um die HTML-Tabelle zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="4374c-628">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="4374c-629">Aktualisieren eines To-Do-Elements</span><span class="sxs-lookup"><span data-stu-id="4374c-629">Update a to-do item</span></span>

<span data-ttu-id="4374c-630">Das Aktualisieren einer Aufgabe ist vergleichbar mit dem Hinzufügen einer Aufgabe.</span><span class="sxs-lookup"><span data-stu-id="4374c-630">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="4374c-631">Die `url` ändert sich, um den eindeutigen Bezeichner des Elements hinzuzufügen. Der `type` lautet `PUT`.</span><span class="sxs-lookup"><span data-stu-id="4374c-631">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="4374c-632">Löschen eines To-Do-Elements</span><span class="sxs-lookup"><span data-stu-id="4374c-632">Delete a to-do item</span></span>

<span data-ttu-id="4374c-633">Eine Aufgabe wird folgendermaßen gelöscht: im AJAX-Aufruf wird `type` auf `DELETE` festgelegt, und der eindeutige Bezeichner des Elements wird in der URL angegeben.</span><span class="sxs-lookup"><span data-stu-id="4374c-633">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="4374c-634">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="4374c-634">Additional resources</span></span>

<span data-ttu-id="4374c-635">[Sie können den Beispielcode für dieses Tutorial anzeigen oder herunterladen.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)</span><span class="sxs-lookup"><span data-stu-id="4374c-635">[View or download sample code for this tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="4374c-636">Informationen [zum Herunterladen](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="4374c-636">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="4374c-637">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="4374c-637">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/index>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="4374c-638">Dieses Tutorial auf YouTube</span><span class="sxs-lookup"><span data-stu-id="4374c-638">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)

---
title: 'Tutorial: Erstellen einer Web-API mit ASP.NET Core'
author: rick-anderson
description: Informationen zum Erstellen einer Web-API mit ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 2/25/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-web-api
ms.openlocfilehash: ddc14aba14e31c5530cda14b4792736da001246a
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767238"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="1e3c7-103">Tutorial: Erstellen einer Web-API mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1e3c7-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="1e3c7-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5) und [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="1e3c7-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="1e3c7-105">In diesem Tutorial lernen Sie die Grundlagen der Erstellung einer Web-API mit ASP.NET Core kennen.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1e3c7-106">In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="1e3c7-107">Erstellen eines Web-API-Projekts</span><span class="sxs-lookup"><span data-stu-id="1e3c7-107">Create a web API project.</span></span>
> * <span data-ttu-id="1e3c7-108">Hinzufügen einer Modellklasse und eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="1e3c7-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="1e3c7-109">Erstellen eines Controllergerüsts mit CRUD-Methoden</span><span class="sxs-lookup"><span data-stu-id="1e3c7-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="1e3c7-110">Konfigurieren des Routings, der URL-Pfade und der Rückgabewerte</span><span class="sxs-lookup"><span data-stu-id="1e3c7-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="1e3c7-111">Aufrufen der Web-API mit Postman</span><span class="sxs-lookup"><span data-stu-id="1e3c7-111">Call the web API with Postman.</span></span>

<span data-ttu-id="1e3c7-112">Abschließend steht Ihnen eine Web-API zur Verfügung, die in einer relationalen Datenbank gespeicherte To-do-Elemente verwalten kann.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="1e3c7-113">Übersicht</span><span class="sxs-lookup"><span data-stu-id="1e3c7-113">Overview</span></span>

<span data-ttu-id="1e3c7-114">In diesem Tutorial wird die folgende API erstellt:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="1e3c7-115">API</span><span class="sxs-lookup"><span data-stu-id="1e3c7-115">API</span></span> | <span data-ttu-id="1e3c7-116">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="1e3c7-116">Description</span></span> | <span data-ttu-id="1e3c7-117">Anforderungstext</span><span class="sxs-lookup"><span data-stu-id="1e3c7-117">Request body</span></span> | <span data-ttu-id="1e3c7-118">Antworttext</span><span class="sxs-lookup"><span data-stu-id="1e3c7-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="1e3c7-119">Alle To-do-Elemente abrufen</span><span class="sxs-lookup"><span data-stu-id="1e3c7-119">Get all to-do items</span></span> | <span data-ttu-id="1e3c7-120">Keine</span><span class="sxs-lookup"><span data-stu-id="1e3c7-120">None</span></span> | <span data-ttu-id="1e3c7-121">Array von To-do-Elementen</span><span class="sxs-lookup"><span data-stu-id="1e3c7-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="1e3c7-122">Ein Element nach ID abrufen</span><span class="sxs-lookup"><span data-stu-id="1e3c7-122">Get an item by ID</span></span> | <span data-ttu-id="1e3c7-123">Keine</span><span class="sxs-lookup"><span data-stu-id="1e3c7-123">None</span></span> | <span data-ttu-id="1e3c7-124">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="1e3c7-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="1e3c7-125">Neues Element hinzufügen</span><span class="sxs-lookup"><span data-stu-id="1e3c7-125">Add a new item</span></span> | <span data-ttu-id="1e3c7-126">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="1e3c7-126">To-do item</span></span> | <span data-ttu-id="1e3c7-127">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="1e3c7-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="1e3c7-128">Vorhandenes Element aktualisieren &nbsp;</span><span class="sxs-lookup"><span data-stu-id="1e3c7-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="1e3c7-129">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="1e3c7-129">To-do item</span></span> | <span data-ttu-id="1e3c7-130">Keine</span><span class="sxs-lookup"><span data-stu-id="1e3c7-130">None</span></span> |
|<span data-ttu-id="1e3c7-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="1e3c7-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="1e3c7-132">Löschen eines Elements &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="1e3c7-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="1e3c7-133">Keine</span><span class="sxs-lookup"><span data-stu-id="1e3c7-133">None</span></span> | <span data-ttu-id="1e3c7-134">Keine</span><span class="sxs-lookup"><span data-stu-id="1e3c7-134">None</span></span>|

<span data-ttu-id="1e3c7-135">Das folgende Diagramm zeigt den Entwurf der App.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-135">The following diagram shows the design of the app.</span></span>

![Der Client ist das Feld ganz links.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="1e3c7-141">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="1e3c7-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1e3c7-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1e3c7-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="1e3c7-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1e3c7-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1e3c7-144">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="1e3c7-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="1e3c7-145">Erstellen eines Webprojekts</span><span class="sxs-lookup"><span data-stu-id="1e3c7-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1e3c7-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1e3c7-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1e3c7-147">Klicken Sie im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="1e3c7-148">Wählen Sie die Vorlage **ASP.NET Core-Webanwendung** aus, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="1e3c7-149">Geben Sie dem Projekt den Namen *TodoApi*, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="1e3c7-150">Vergewissern Sie sich, dass im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Optionen **.NET Core** und **ASP.NET Core 3.1** ausgewählt sind.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="1e3c7-151">Wählen Sie die Vorlage **API** aus, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-151">Select the **API** template and click **Create**.</span></span>

![VS-Dialogfeld „Neues Projekt“](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="1e3c7-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1e3c7-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1e3c7-154">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="1e3c7-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="1e3c7-155">Wechseln Sie mit dem Befehl `cd`' zu dem Ordner, der den Projektordner enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="1e3c7-156">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="1e3c7-157">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem Projekt die erforderlichen Elemente hinzufügen möchten, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="1e3c7-158">Die obenstehenden Befehle haben folgende Konsequenzen:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-158">The preceding commands:</span></span>

  * <span data-ttu-id="1e3c7-159">Ein neues Web-API-Projekt wird erstellt und in Visual Studio Code geöffnet.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="1e3c7-160">Die NuGet-Pakete, die im nächsten Abschnitt erforderlich sind, werden hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1e3c7-161">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="1e3c7-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="1e3c7-162">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-162">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="1e3c7-164">Wählen Sie **.NET Core** > **App** > **API** > **Weiter** aus.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-164">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![Dialogfeld „Neue Projektmappe“ in macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="1e3c7-166">Wählen Sie im Dialogfeld **Neue ASP.NET Core-Web-API konfigurieren**für das **Zielframework** den Eintrag \* *.NET Core 3.1* aus.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-166">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.1*.</span></span>

* <span data-ttu-id="1e3c7-167">Geben Sie für **Projektname** *TodoApi* ein, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-167">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogfeld „Konfiguration“](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="1e3c7-169">Öffnen Sie im Projektordner ein Befehlsterminal, und führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-169">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="1e3c7-170">Testen der API</span><span class="sxs-lookup"><span data-stu-id="1e3c7-170">Test the API</span></span>

<span data-ttu-id="1e3c7-171">Die Projektvorlage erstellt eine `WeatherForecast`-API.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-171">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="1e3c7-172">Rufen Sie in einem Browser die `Get`-Methode zum Testen der App auf.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-172">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1e3c7-173">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1e3c7-173">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1e3c7-174">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-174">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="1e3c7-175">Visual Studio startet einen Browser und navigiert zu `https://localhost:<port>/WeatherForecast`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-175">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="1e3c7-176">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem IIS Express-Zertifikat vertrauen, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-176">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="1e3c7-177">Wählen Sie im folgenden Dialogfeld **Sicherheitswarnung** die Option **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-177">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1e3c7-178">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1e3c7-178">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1e3c7-179">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-179">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="1e3c7-180">Navigieren Sie in einem Browser zur folgenden URL: `https://localhost:5001/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-180">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1e3c7-181">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="1e3c7-181">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="1e3c7-182">Wählen Sie **Ausführen** > **Debugging starten** aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-182">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="1e3c7-183">Visual Studio für Mac startet einen Browser und navigiert zu `https://localhost:<port>`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-183">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="1e3c7-184">Der Fehler „HTTP 404: Nicht gefunden“ wird zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-184">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="1e3c7-185">Fügen Sie `/WeatherForecast` an die URL an, d. h., ändern Sie sie in `https://localhost:<port>/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-185">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="1e3c7-186">Der zurückgegebene JSON-Code sieht in etwa wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-186">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="1e3c7-187">Hinzufügen einer Modellklasse</span><span class="sxs-lookup"><span data-stu-id="1e3c7-187">Add a model class</span></span>

<span data-ttu-id="1e3c7-188">Ein *Modell* ist eine Gruppe von Klassen, die die Daten darstellen, die die App verwaltet.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-188">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="1e3c7-189">Das Modell für diese App ist eine einzelne `TodoItem`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-189">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1e3c7-190">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1e3c7-190">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1e3c7-191">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-191">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="1e3c7-192">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-192">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="1e3c7-193">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-193">Name the folder *Models*.</span></span>

* <span data-ttu-id="1e3c7-194">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-194">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="1e3c7-195">Nennen Sie die Klasse *TodoItem*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-195">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="1e3c7-196">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-196">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1e3c7-197">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1e3c7-197">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1e3c7-198">Fügen Sie einen Ordner namens *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-198">Add a folder named *Models*.</span></span>

* <span data-ttu-id="1e3c7-199">Fügen Sie dem Ordner *Modelle* mit dem folgenden Code eine `TodoItem`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-199">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1e3c7-200">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="1e3c7-200">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="1e3c7-201">Klicken Sie mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-201">Right-click the project.</span></span> <span data-ttu-id="1e3c7-202">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-202">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="1e3c7-203">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-203">Name the folder *Models*.</span></span>

  ![Neuer Ordner](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="1e3c7-205">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Neue Datei** > **Allgemein** > **Leere Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-205">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="1e3c7-206">Nennen Sie die Klasse *TodoItem*, und klicken Sie dann auf **Neu**.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-206">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="1e3c7-207">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-207">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="1e3c7-208">Die `Id`-Eigenschaft fungiert als eindeutiger Schlüssel in einer relationalen Datenbank.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-208">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="1e3c7-209">Modellklassen können überall im Projekt platziert werden, doch gemäß der Konvention wird der Ordner *Modelle* verwendet.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-209">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="1e3c7-210">Hinzufügen eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="1e3c7-210">Add a database context</span></span>

<span data-ttu-id="1e3c7-211">Der *Datenbankkontext* ist die Hauptklasse, die die Entity Framework-Funktionen für ein Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-211">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="1e3c7-212">Diese Klasse wird durch Ableiten von der `Microsoft.EntityFrameworkCore.DbContext`-Klasse erstellt.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-212">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1e3c7-213">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1e3c7-213">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="1e3c7-214">Hinzufügen von Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="1e3c7-214">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="1e3c7-215">Klicken Sie im Menü **Extras** auf **NuGet-Paket-Manager > NuGet-Pakete für Projektmappe verwalten...** .</span><span class="sxs-lookup"><span data-stu-id="1e3c7-215">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="1e3c7-216">Klicken Sie auf die Registerkarte **Durchsuchen**, und geben Sie dann **Microsoft.EntityFrameworkCore.SqlServer** in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-216">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="1e3c7-217">Wählen Sie im linken Bereich **Microsoft.EntityFrameworkCore.SqlServer** aus.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-217">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="1e3c7-218">Aktivieren Sie das Kontrollkästchen **Projekt** im rechten Bereich, und klicken Sie dann auf **Installieren**.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-218">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="1e3c7-219">Fügen Sie mithilfe der obigen Schritte das `Microsoft.EntityFrameworkCore.InMemory`-NuGet-Paket hinzu.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-219">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![NuGet-Paket-Manager](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="1e3c7-221">Hinzufügen des TodoContext-Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="1e3c7-221">Add the TodoContext database context</span></span>

* <span data-ttu-id="1e3c7-222">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-222">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="1e3c7-223">Nennen Sie die Klasse *TodoContext*, und klicken Sie auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-223">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="1e3c7-224">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="1e3c7-224">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="1e3c7-225">Fügen Sie eine `TodoContext`-Klasse zum Ordner *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-225">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="1e3c7-226">Geben Sie den folgenden Code ein:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-226">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="1e3c7-227">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="1e3c7-227">Register the database context</span></span>

<span data-ttu-id="1e3c7-228">In ASP.NET Core müssen Dienste wie der Datenbankkontext mit dem Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) registriert werden.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-228">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="1e3c7-229">Der Container stellt den Dienst für Controller bereit.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-229">The container provides the service to controllers.</span></span>

<span data-ttu-id="1e3c7-230">Aktualisieren Sie *Startup.cs* mit dem folgenden hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-230">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="1e3c7-231">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-231">The preceding code:</span></span>

* <span data-ttu-id="1e3c7-232">Entfernt nicht benötigte `using`-Deklarationen</span><span class="sxs-lookup"><span data-stu-id="1e3c7-232">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="1e3c7-233">Fügt dem Abhängigkeitscontainer den Datenbankkontext hinzu</span><span class="sxs-lookup"><span data-stu-id="1e3c7-233">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="1e3c7-234">Gibt an, dass der Datenbankkontext eine In-Memory Database verwendet</span><span class="sxs-lookup"><span data-stu-id="1e3c7-234">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="1e3c7-235">Erstellen eines Controllergerüsts</span><span class="sxs-lookup"><span data-stu-id="1e3c7-235">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1e3c7-236">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1e3c7-236">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1e3c7-237">Klicken Sie mit der rechten Maustaste auf den Ordner *Controller*.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-237">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="1e3c7-238">Wählen Sie **Hinzufügen** > **Neues Gerüstelement** aus.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-238">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="1e3c7-239">Klicken Sie auf **API-Controller mit Aktionen unter Verwendung von Entity Framework** und dann auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-239">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="1e3c7-240">Führen Sie im Dialogfeld **API-Controller mit Aktionen unter Verwendung von Entity Framework** folgende Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-240">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="1e3c7-241">Wählen Sie in der **Modellklasse** das Element **TodoItem (TodoApi.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-241">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="1e3c7-242">Wählen Sie in der **Datenkontextklasse** das Element **TodoContext (TodoApi.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-242">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="1e3c7-243">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-243">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="1e3c7-244">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="1e3c7-244">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="1e3c7-245">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-245">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="1e3c7-246">Die obenstehenden Befehle haben folgende Konsequenzen:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-246">The preceding commands:</span></span>

* <span data-ttu-id="1e3c7-247">Die NuGet-Pakete für den Gerüstbau werden hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-247">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="1e3c7-248">Die Gerüstbau-Engine (`dotnet-aspnet-codegenerator`) wird installiert.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-248">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="1e3c7-249">Das Gerüst für `TodoItemsController` wird erstellt.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-249">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="1e3c7-250">Der generierte Code hat folgende Auswirkungen:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-250">The generated code:</span></span>

* <span data-ttu-id="1e3c7-251">Markiert die Klasse mit dem [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute)-Attribut.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-251">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="1e3c7-252">Dieses Attribut gibt an, dass der Controller auf Web-API-Anforderungen reagiert.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-252">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="1e3c7-253">Weitere Informationen zu bestimmten Verhaltensweisen, die das Attribut ermöglicht, finden Sie unter <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-253">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="1e3c7-254">Verwendet die Abhängigkeitsinjektion, um den Datenbankkontext (`TodoContext`) dem Controller hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-254">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="1e3c7-255">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-255">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="1e3c7-256">Die ASP.NET Core-Vorlagen für:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-256">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="1e3c7-257">Controller mit Ansichten enthalten `[action]` in der Routenvorlage.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-257">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="1e3c7-258">API-Controller enthalten keine `[action]` in der Routenvorlage.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-258">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="1e3c7-259">Wenn sich das `[action]`-Token nicht in der Routenvorlage befindet, wird der [action](xref:mvc/controllers/routing#action)-Name von der Route ausgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-259">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="1e3c7-260">Dies bedeutet, dass der zugehörige Methodenname der Aktion nicht in der übereinstimmenden Route verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-260">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="1e3c7-261">Überblick über die PostTodoItem-Erstellungsmethode</span><span class="sxs-lookup"><span data-stu-id="1e3c7-261">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="1e3c7-262">Ersetzen Sie die Rückgabeanweisung in `PostTodoItem` durch eine Anweisung mit dem [nameof](/dotnet/csharp/language-reference/operators/nameof)-Operator:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-262">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="1e3c7-263">Der oben stehende Code ist eine HTTP POST-Methode, wie durch das [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute)-Attribut angegeben.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-263">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="1e3c7-264">Die Methode ruft den Wert der Aufgabe aus dem Text der HTTP-Anforderung ab.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-264">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="1e3c7-265">Die <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>-Methode:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-265">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="1e3c7-266">Gibt den HTTP-Statuscode 201 zurück, wenn der Vorgang erfolgreich ist.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-266">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="1e3c7-267">HTTP 201 ist die Standardantwort für eine HTTP POST-Methode, die eine neue Ressource auf dem Server erstellt.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-267">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="1e3c7-268">Fügt der Antwort einen [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location)-Header hinzu.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-268">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="1e3c7-269">Der `Location`-Header legt den [URI](https://developer.mozilla.org/docs/Glossary/URI) des neu erstellten To-do-Elements fest.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-269">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="1e3c7-270">Weitere Informationen finden Sie unter [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="1e3c7-270">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="1e3c7-271">Verweist auf die `GetTodoItem`-Aktion zum Erstellen des URIs des `Location`-Headers.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-271">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="1e3c7-272">Das `nameof`-Schlüsselwort von C# wird verwendet, um eine Hartcodierung des Aktionsnamens im `CreatedAtAction`-Aufruf zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-272">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="1e3c7-273">Installieren von Postman</span><span class="sxs-lookup"><span data-stu-id="1e3c7-273">Install Postman</span></span>

<span data-ttu-id="1e3c7-274">Dieses Tutorial verwendet Postman zum Testen der Web-API.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-274">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="1e3c7-275">Installieren Sie [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="1e3c7-275">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="1e3c7-276">Starten Sie die Web-App.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-276">Start the web app.</span></span>
* <span data-ttu-id="1e3c7-277">Starten Sie Postman.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-277">Start Postman.</span></span>
* <span data-ttu-id="1e3c7-278">Deaktivieren Sie **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="1e3c7-278">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="1e3c7-279">Deaktivieren Sie auf der Registerkarte **General** (Allgemein) unter File > Settings (**Datei** > **Einstellungen**) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="1e3c7-279">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="1e3c7-280">Aktivieren Sie die Verifizierung des SSL-Zertifikats wieder, nachdem Sie den Controller getestet haben.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-280">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="1e3c7-281">Testen von PostTodoItem mit Postman</span><span class="sxs-lookup"><span data-stu-id="1e3c7-281">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="1e3c7-282">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-282">Create a new request.</span></span>
* <span data-ttu-id="1e3c7-283">Legen Sie als HTTP-Methode `POST` fest.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-283">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="1e3c7-284">Wählen Sie die Registerkarte **Body** (Text) aus.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-284">Select the **Body** tab.</span></span>
* <span data-ttu-id="1e3c7-285">Wählen Sie das Optionsfeld **raw** (Unformatiert) aus.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-285">Select the **raw** radio button.</span></span>
* <span data-ttu-id="1e3c7-286">Legen Sie den Typ auf **JSON (application/json)** fest.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-286">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="1e3c7-287">Geben Sie für die Aufgabe den Anforderungstext im JSON-Format ein:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-287">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="1e3c7-288">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-288">Select **Send**.</span></span>

  ![Postman mit erstellter Anforderung](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="1e3c7-290">Testen des Adressheader-URIs</span><span class="sxs-lookup"><span data-stu-id="1e3c7-290">Test the location header URI</span></span>

* <span data-ttu-id="1e3c7-291">Wählen Sie auf der Registerkarte **Header** (Header) den Bereich **Response** (Antwort) aus.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-291">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="1e3c7-292">Kopieren Sie den den Headerwert von **Location** (Speicherort):</span><span class="sxs-lookup"><span data-stu-id="1e3c7-292">Copy the **Location** header value:</span></span>

  ![Registerkarte „Header“ in der Postman-Konsole](first-web-api/_static/3/create.png)

* <span data-ttu-id="1e3c7-294">Legen Sie die Methode auf „GET“ fest.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-294">Set the method to GET.</span></span>
* <span data-ttu-id="1e3c7-295">Fügen Sie den URI (z. B. `https://localhost:5001/api/TodoItems/1`) ein.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-295">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="1e3c7-296">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-296">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="1e3c7-297">Überblick über die GET-Methoden</span><span class="sxs-lookup"><span data-stu-id="1e3c7-297">Examine the GET methods</span></span>

<span data-ttu-id="1e3c7-298">Diese Methoden implementieren zwei GET-Endpunkte:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-298">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="1e3c7-299">Testen Sie die App, indem Sie die beiden Endpunkte in einem Browser oder über Postman aufrufen.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-299">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="1e3c7-300">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-300">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="1e3c7-301">Durch einen Aufruf von `GetTodoItems` wird eine Antwort gesendet, die der folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-301">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="1e3c7-302">Testen von GET mit Postman</span><span class="sxs-lookup"><span data-stu-id="1e3c7-302">Test Get with Postman</span></span>

* <span data-ttu-id="1e3c7-303">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-303">Create a new request.</span></span>
* <span data-ttu-id="1e3c7-304">Legen Sie die HTTP-Methode auf **GET** fest.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-304">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="1e3c7-305">Legen Sie die Anforderungs-URL auf `https://localhost:<port>/api/TodoItems` fest.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-305">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="1e3c7-306">Beispielsweise `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-306">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="1e3c7-307">Wählen Sie in Postman **Two pane view** (Ansicht mit zwei Bereichen) aus.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-307">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="1e3c7-308">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-308">Select **Send**.</span></span>

<span data-ttu-id="1e3c7-309">Diese App verwendet eine In-Memory-Datenbank.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-309">This app uses an in-memory database.</span></span> <span data-ttu-id="1e3c7-310">Wenn die App angehalten und dann wieder gestartet wird, werden durch die vorherige GET-Anforderung keine Daten zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-310">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="1e3c7-311">Wenn keine Daten zurückgegeben werden, senden Sie mit [POST](#post) Daten an die App.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-311">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="1e3c7-312">Routing und URL-Pfade</span><span class="sxs-lookup"><span data-stu-id="1e3c7-312">Routing and URL paths</span></span>

<span data-ttu-id="1e3c7-313">Das Attribut [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) gibt eine Methode an, die auf eine HTTP GET-Anforderung antwortet.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-313">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="1e3c7-314">Der URL-Pfad für jede Methode wird wie folgt erstellt:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-314">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="1e3c7-315">Beginnen Sie mit der Vorlagenzeichenfolge im `Route`-Attribut des Controllers:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-315">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="1e3c7-316">Ersetzen Sie `[controller]` durch den Namen des Controllers, bei dem es sich standardmäßig um den Namen der Controller-Klasse ohne das Suffix „Controller“ handelt.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-316">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="1e3c7-317">In diesem Beispiel ist der Klassenname des Controllers „**TodoItems**Controller“. Der Controllername lautet also „TodoItems“.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-317">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="1e3c7-318">Beim ASP.NET Core-[Routing](xref:mvc/controllers/routing) wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-318">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="1e3c7-319">Wenn das `[HttpGet]`-Attribut eine Routenvorlage (z. B. `[HttpGet("products")]`) hat, fügen Sie diese an den Pfad an.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-319">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="1e3c7-320">In diesem Beispiel wird keine Vorlage verwendet.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-320">This sample doesn't use a template.</span></span> <span data-ttu-id="1e3c7-321">Weitere Informationen finden Sie unter [Attributrouting mit Http[Verb]-Attributen](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="1e3c7-321">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="1e3c7-322">In der folgenden `GetTodoItem`-Methode ist `"{id}"` eine Platzhaltervariable für den eindeutigen Bezeichner des To-do-Elements.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-322">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="1e3c7-323">Wenn `GetTodoItem` aufgerufen wird, wird der Wert von `"{id}"` in der URL der Methode in ihrem Parameter `id` bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-323">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="1e3c7-324">Rückgabewerte</span><span class="sxs-lookup"><span data-stu-id="1e3c7-324">Return values</span></span>

<span data-ttu-id="1e3c7-325">Der Rückgabetyp der Methoden `GetTodoItems` und `GetTodoItem` ist [ActionResult\<T>-Typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="1e3c7-325">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="1e3c7-326">ASP.NET Core serialisiert automatisch das Objekt in [JSON](https://www.json.org/) und schreibt den JSON-Code in den Text der Antwortnachricht.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-326">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="1e3c7-327">Der Antwortcode für diesen Rückgabetyp ist 200, vorausgesetzt, es gibt keine Ausnahmefehler.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-327">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="1e3c7-328">Nicht behandelte Ausnahmen werden in 5xx-Fehler übersetzt.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-328">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="1e3c7-329">`ActionResult`-Rückgabetypen können eine Vielzahl von HTTP-Statuscodes darstellen.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-329">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="1e3c7-330">Beispielsweise kann `GetTodoItem` zwei verschiedene Statuswerte zurückgeben:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-330">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="1e3c7-331">Wenn kein Element mit der angeforderten ID übereinstimmt, gibt die Methode einen 404-Fehlercode [Nicht gefunden](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) zurück.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-331">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="1e3c7-332">Andernfalls gibt die Methode 200 mit einem JSON-Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-332">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="1e3c7-333">Die Rückgabe von `item` löst eine HTTP 200-Antwort aus.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-333">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="1e3c7-334">PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="1e3c7-334">The PutTodoItem method</span></span>

<span data-ttu-id="1e3c7-335">Untersuchen Sie die `PutTodoItem`-Methode.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-335">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="1e3c7-336">`PutTodoItem` ähnelt `PostTodoItem`, verwendet allerdings HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-336">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="1e3c7-337">Die Antwort ist [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="1e3c7-337">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="1e3c7-338">Gemäß der HTTP-Spezifikation erfordert eine PUT-Anforderung, dass der Client die gesamte aktualisierte Entität (nicht nur die Änderungen) sendet.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-338">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="1e3c7-339">Verwenden Sie [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute), um Teilupdates zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-339">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="1e3c7-340">Wenn beim Aufrufen von `PutTodoItem` ein Fehler zurückgegeben wird, rufen Sie `GET` auf, um sicherzustellen, dass die Datenbank ein Element enthält.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-340">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="1e3c7-341">Testen der PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="1e3c7-341">Test the PutTodoItem method</span></span>

<span data-ttu-id="1e3c7-342">In diesem Beispiel wird eine In-Memory-Datenbank verwendet, die jedes Mal initialisiert werden muss, wenn die App gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-342">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="1e3c7-343">Es muss ein Element in der Datenbank vorhanden sein, bevor Sie einen PUT-Aufruf durchführen.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-343">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="1e3c7-344">Rufen Sie vor einem PUT-Aufruf GET auf, um sicherzustellen, dass ein Element in der Datenbank vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-344">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="1e3c7-345">Aktualisieren Sie das To-do-Element, das über den ID-Wert 1 verfügt, und legen Sie als Namen „feed fish“ fest:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-345">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="1e3c7-346">Die folgende Abbildung zeigt das Postman-Update:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-346">The following image shows the Postman update:</span></span>

![Postman-Konsole mit der Antwort „204 (Kein Inhalt)“](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="1e3c7-348">DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="1e3c7-348">The DeleteTodoItem method</span></span>

<span data-ttu-id="1e3c7-349">Untersuchen Sie die `DeleteTodoItem`-Methode.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-349">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="1e3c7-350">Testen der DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="1e3c7-350">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="1e3c7-351">So löschen Sie mit Postman eine Aufgabe</span><span class="sxs-lookup"><span data-stu-id="1e3c7-351">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="1e3c7-352">Legen Sie die Methode auf `DELETE` fest.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-352">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="1e3c7-353">Legen Sie den URI des zu löschenden Objekts fest, z. B. `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-353">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="1e3c7-354">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-354">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="1e3c7-355">Vermeiden von Overposting</span><span class="sxs-lookup"><span data-stu-id="1e3c7-355">Prevent over-posting</span></span>

<span data-ttu-id="1e3c7-356">Derzeit macht die Beispiel-App das gesamte `TodoItem`-Objekt verfügbar.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-356">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="1e3c7-357">In den Produktions-Apps sind die Daten, die eingegeben und mithilfe einer Teilmenge des Modells zurückgegeben werden, in der Regel eingeschränkt.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-357">Productions apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="1e3c7-358">Hierfür gibt es mehrere Gründe, wobei die Sicherheit einer der Hauptgründe ist.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-358">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="1e3c7-359">Die Teilmenge eines Modells wird üblicherweise als Datenübertragungsobjekt (DTO, Data Transfer Object), Eingabemodell oder Anzeigemodell bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-359">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="1e3c7-360">In diesem Artikel wird das **DTO** verwendet.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-360">**DTO** is used in this article.</span></span>

<span data-ttu-id="1e3c7-361">Ein DTO kann für Folgendes verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-361">A DTO may be used to:</span></span>

* <span data-ttu-id="1e3c7-362">Vermeiden Sie Overposting.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-362">Prevent over-posting.</span></span>
* <span data-ttu-id="1e3c7-363">Ausblenden von Eigenschaften, die Clients nicht anzeigen sollen</span><span class="sxs-lookup"><span data-stu-id="1e3c7-363">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="1e3c7-364">Auslassen einiger Eigenschaften, um die Nutzlast zu verringern</span><span class="sxs-lookup"><span data-stu-id="1e3c7-364">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="1e3c7-365">Vereinfachen von Objektgraphen, die geschachtelte Objekte enthalten</span><span class="sxs-lookup"><span data-stu-id="1e3c7-365">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="1e3c7-366">Vereinfachte Objektgraphen können für Clients zweckmäßiger sein.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-366">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="1e3c7-367">Um den DTO-Ansatz zu veranschaulichen, aktualisieren Sie die `TodoItem`-Klasse, sodass sie ein geheimes Feld einschließt:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-367">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="1e3c7-368">Das geheime Feld muss in dieser App ausgeblendet werden, eine administrative App kann es jedoch verfügbar machen.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-368">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="1e3c7-369">Vergewissern Sie sich, dass Sie das geheime Feld veröffentlichen und abrufen können.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-369">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="1e3c7-370">Erstellen Sie ein DTO-Modell:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-370">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="1e3c7-371">Aktualisieren Sie `TodoItemsController`, sodass `TodoItemDTO` verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-371">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="1e3c7-372">Vergewissern Sie sich, dass Sie das geheime Feld weder veröffentlichen noch abrufen können.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-372">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="1e3c7-373">Aufrufen der Web-API mit JavaScript</span><span class="sxs-lookup"><span data-stu-id="1e3c7-373">Call the web API with JavaScript</span></span>

<span data-ttu-id="1e3c7-374">Mehr dazu finden Sie im [-Tutorial: Aufrufen einer ASP.NET Core-Web-API mit JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="1e3c7-374">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1e3c7-375">In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-375">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="1e3c7-376">Erstellen eines Web-API-Projekts</span><span class="sxs-lookup"><span data-stu-id="1e3c7-376">Create a web API project.</span></span>
> * <span data-ttu-id="1e3c7-377">Hinzufügen einer Modellklasse und eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="1e3c7-377">Add a model class and a database context.</span></span>
> * <span data-ttu-id="1e3c7-378">Hinzufügen eines Controllers</span><span class="sxs-lookup"><span data-stu-id="1e3c7-378">Add a controller.</span></span>
> * <span data-ttu-id="1e3c7-379">Hinzufügen von CRUD-Methoden</span><span class="sxs-lookup"><span data-stu-id="1e3c7-379">Add CRUD methods.</span></span>
> * <span data-ttu-id="1e3c7-380">Konfigurieren von Routing und URL-Pfaden</span><span class="sxs-lookup"><span data-stu-id="1e3c7-380">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="1e3c7-381">Angeben von Rückgabewerten</span><span class="sxs-lookup"><span data-stu-id="1e3c7-381">Specify return values.</span></span>
> * <span data-ttu-id="1e3c7-382">Aufrufen der Web-API mit Postman</span><span class="sxs-lookup"><span data-stu-id="1e3c7-382">Call the web API with Postman.</span></span>
> * <span data-ttu-id="1e3c7-383">Aufrufen der Web-API mit JavaScript</span><span class="sxs-lookup"><span data-stu-id="1e3c7-383">Call the web API with JavaScript.</span></span>

<span data-ttu-id="1e3c7-384">Am Ende haben Sie eine Web-API, die in einer relationalen Datenbank gespeicherte „Aufgaben“ verwalten kann.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-384">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="1e3c7-385">Übersicht</span><span class="sxs-lookup"><span data-stu-id="1e3c7-385">Overview</span></span>

<span data-ttu-id="1e3c7-386">In diesem Tutorial wird die folgende API erstellt:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-386">This tutorial creates the following API:</span></span>

|<span data-ttu-id="1e3c7-387">API</span><span class="sxs-lookup"><span data-stu-id="1e3c7-387">API</span></span> | <span data-ttu-id="1e3c7-388">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="1e3c7-388">Description</span></span> | <span data-ttu-id="1e3c7-389">Anforderungstext</span><span class="sxs-lookup"><span data-stu-id="1e3c7-389">Request body</span></span> | <span data-ttu-id="1e3c7-390">Antworttext</span><span class="sxs-lookup"><span data-stu-id="1e3c7-390">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="1e3c7-391">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="1e3c7-391">GET /api/TodoItems</span></span> | <span data-ttu-id="1e3c7-392">Alle To-do-Elemente abrufen</span><span class="sxs-lookup"><span data-stu-id="1e3c7-392">Get all to-do items</span></span> | <span data-ttu-id="1e3c7-393">Keine</span><span class="sxs-lookup"><span data-stu-id="1e3c7-393">None</span></span> | <span data-ttu-id="1e3c7-394">Array von To-do-Elementen</span><span class="sxs-lookup"><span data-stu-id="1e3c7-394">Array of to-do items</span></span>|
|<span data-ttu-id="1e3c7-395">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="1e3c7-395">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="1e3c7-396">Ein Element nach ID abrufen</span><span class="sxs-lookup"><span data-stu-id="1e3c7-396">Get an item by ID</span></span> | <span data-ttu-id="1e3c7-397">Keine</span><span class="sxs-lookup"><span data-stu-id="1e3c7-397">None</span></span> | <span data-ttu-id="1e3c7-398">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="1e3c7-398">To-do item</span></span>|
|<span data-ttu-id="1e3c7-399">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="1e3c7-399">POST /api/TodoItems</span></span> | <span data-ttu-id="1e3c7-400">Neues Element hinzufügen</span><span class="sxs-lookup"><span data-stu-id="1e3c7-400">Add a new item</span></span> | <span data-ttu-id="1e3c7-401">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="1e3c7-401">To-do item</span></span> | <span data-ttu-id="1e3c7-402">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="1e3c7-402">To-do item</span></span> |
|<span data-ttu-id="1e3c7-403">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="1e3c7-403">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="1e3c7-404">Vorhandenes Element aktualisieren &nbsp;</span><span class="sxs-lookup"><span data-stu-id="1e3c7-404">Update an existing item &nbsp;</span></span> | <span data-ttu-id="1e3c7-405">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="1e3c7-405">To-do item</span></span> | <span data-ttu-id="1e3c7-406">Keine</span><span class="sxs-lookup"><span data-stu-id="1e3c7-406">None</span></span> |
|<span data-ttu-id="1e3c7-407">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="1e3c7-407">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="1e3c7-408">Löschen eines Elements &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="1e3c7-408">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="1e3c7-409">Keine</span><span class="sxs-lookup"><span data-stu-id="1e3c7-409">None</span></span> | <span data-ttu-id="1e3c7-410">Keine</span><span class="sxs-lookup"><span data-stu-id="1e3c7-410">None</span></span>|

<span data-ttu-id="1e3c7-411">Das folgende Diagramm zeigt den Entwurf der App.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-411">The following diagram shows the design of the app.</span></span>

![Der Client ist das Feld ganz links.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="1e3c7-417">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="1e3c7-417">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1e3c7-418">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1e3c7-418">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="1e3c7-419">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1e3c7-419">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1e3c7-420">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="1e3c7-420">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="1e3c7-421">Erstellen eines Webprojekts</span><span class="sxs-lookup"><span data-stu-id="1e3c7-421">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1e3c7-422">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1e3c7-422">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1e3c7-423">Klicken Sie im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-423">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="1e3c7-424">Wählen Sie die Vorlage **ASP.NET Core-Webanwendung** aus, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-424">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="1e3c7-425">Geben Sie dem Projekt den Namen *TodoApi*, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-425">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="1e3c7-426">Vergewissern Sie sich, dass im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Optionen **.NET Core** und **ASP.NET Core 2.2** ausgewählt sind.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-426">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="1e3c7-427">Wählen Sie die Vorlage **API** aus, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-427">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="1e3c7-428">Wählen Sie **nicht** **Enable Docker Support** (Docker-Unterstützung aktivieren) aus.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-428">**Don't** select **Enable Docker Support**.</span></span>

![VS-Dialogfeld „Neues Projekt“](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="1e3c7-430">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1e3c7-430">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1e3c7-431">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="1e3c7-431">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="1e3c7-432">Wechseln Sie mit dem Befehl `cd`' zu dem Ordner, der den Projektordner enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-432">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="1e3c7-433">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-433">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="1e3c7-434">Diese Befehle erstellen ein neues Web-API-Projekt und öffnen eine neue Visual Studio Code-Instanz im neuen Projektordner.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-434">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="1e3c7-435">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem Projekt die erforderlichen Elemente hinzufügen möchten, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-435">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1e3c7-436">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="1e3c7-436">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="1e3c7-437">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-437">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="1e3c7-439">Wählen Sie **.NET Core** > **App** > **API** > **Weiter** aus.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-439">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![Dialogfeld „Neue Projektmappe“ in macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="1e3c7-441">Übernehmen Sie im Dialogfeld **Neue ASP.NET Core-Web-API konfigurieren** die Standardeinstellung **Zielframework** von \* *.NET Core 2.2*.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-441">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="1e3c7-442">Geben Sie für **Projektname** *TodoApi* ein, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-442">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogfeld „Konfiguration“](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="1e3c7-444">Testen der API</span><span class="sxs-lookup"><span data-stu-id="1e3c7-444">Test the API</span></span>

<span data-ttu-id="1e3c7-445">Die Projektvorlage erstellt eine `values`-API.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-445">The project template creates a `values` API.</span></span> <span data-ttu-id="1e3c7-446">Rufen Sie in einem Browser die `Get`-Methode zum Testen der App auf.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-446">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1e3c7-447">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1e3c7-447">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1e3c7-448">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-448">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="1e3c7-449">Visual Studio startet einen Browser und navigiert zu `https://localhost:<port>/api/values`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-449">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="1e3c7-450">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem IIS Express-Zertifikat vertrauen, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-450">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="1e3c7-451">Wählen Sie im folgenden Dialogfeld **Sicherheitswarnung** die Option **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-451">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1e3c7-452">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1e3c7-452">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1e3c7-453">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-453">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="1e3c7-454">Navigieren Sie in einem Browser zur folgenden URL: `https://localhost:5001/api/values`.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-454">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1e3c7-455">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="1e3c7-455">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="1e3c7-456">Wählen Sie **Ausführen** > **Debugging starten** aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-456">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="1e3c7-457">Visual Studio für Mac startet einen Browser und navigiert zu `https://localhost:<port>`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-457">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="1e3c7-458">Der Fehler „HTTP 404: Nicht gefunden“ wird zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-458">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="1e3c7-459">Fügen Sie der URL `/api/values` an, d.h. ändern Sie die URL in `https://localhost:<port>/api/values`.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-459">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="1e3c7-460">Die folgende JSON-Datei wird zurückgegeben:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-460">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="1e3c7-461">Hinzufügen einer Modellklasse</span><span class="sxs-lookup"><span data-stu-id="1e3c7-461">Add a model class</span></span>

<span data-ttu-id="1e3c7-462">Ein *Modell* ist eine Gruppe von Klassen, die die Daten darstellen, die die App verwaltet.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-462">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="1e3c7-463">Das Modell für diese App ist eine einzelne `TodoItem`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-463">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1e3c7-464">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1e3c7-464">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1e3c7-465">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-465">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="1e3c7-466">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-466">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="1e3c7-467">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-467">Name the folder *Models*.</span></span>

* <span data-ttu-id="1e3c7-468">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-468">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="1e3c7-469">Nennen Sie die Klasse *TodoItem*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-469">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="1e3c7-470">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-470">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1e3c7-471">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1e3c7-471">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1e3c7-472">Fügen Sie einen Ordner namens *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-472">Add a folder named *Models*.</span></span>

* <span data-ttu-id="1e3c7-473">Fügen Sie dem Ordner *Modelle* mit dem folgenden Code eine `TodoItem`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-473">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1e3c7-474">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="1e3c7-474">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="1e3c7-475">Klicken Sie mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-475">Right-click the project.</span></span> <span data-ttu-id="1e3c7-476">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-476">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="1e3c7-477">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-477">Name the folder *Models*.</span></span>

  ![Neuer Ordner](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="1e3c7-479">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Neue Datei** > **Allgemein** > **Leere Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-479">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="1e3c7-480">Nennen Sie die Klasse *TodoItem*, und klicken Sie dann auf **Neu**.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-480">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="1e3c7-481">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-481">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="1e3c7-482">Die `Id`-Eigenschaft fungiert als eindeutiger Schlüssel in einer relationalen Datenbank.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-482">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="1e3c7-483">Modellklassen können überall im Projekt platziert werden, doch gemäß der Konvention wird der Ordner *Modelle* verwendet.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-483">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="1e3c7-484">Hinzufügen eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="1e3c7-484">Add a database context</span></span>

<span data-ttu-id="1e3c7-485">Der *Datenbankkontext* ist die Hauptklasse, die die Entity Framework-Funktionen für ein Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-485">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="1e3c7-486">Diese Klasse wird durch Ableiten von der `Microsoft.EntityFrameworkCore.DbContext`-Klasse erstellt.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-486">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1e3c7-487">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1e3c7-487">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1e3c7-488">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-488">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="1e3c7-489">Nennen Sie die Klasse *TodoContext*, und klicken Sie auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-489">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="1e3c7-490">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="1e3c7-490">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="1e3c7-491">Fügen Sie eine `TodoContext`-Klasse zum Ordner *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-491">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="1e3c7-492">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-492">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="1e3c7-493">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="1e3c7-493">Register the database context</span></span>

<span data-ttu-id="1e3c7-494">In ASP.NET Core müssen Dienste wie der Datenbankkontext mit dem Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) registriert werden.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-494">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="1e3c7-495">Der Container stellt den Dienst für Controller bereit.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-495">The container provides the service to controllers.</span></span>

<span data-ttu-id="1e3c7-496">Aktualisieren Sie *Startup.cs* mit dem folgenden hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-496">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="1e3c7-497">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-497">The preceding code:</span></span>

* <span data-ttu-id="1e3c7-498">Entfernt nicht benötigte `using`-Deklarationen</span><span class="sxs-lookup"><span data-stu-id="1e3c7-498">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="1e3c7-499">Fügt dem Abhängigkeitscontainer den Datenbankkontext hinzu</span><span class="sxs-lookup"><span data-stu-id="1e3c7-499">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="1e3c7-500">Gibt an, dass der Datenbankkontext eine In-Memory Database verwendet</span><span class="sxs-lookup"><span data-stu-id="1e3c7-500">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="1e3c7-501">Hinzufügen eines Controllers</span><span class="sxs-lookup"><span data-stu-id="1e3c7-501">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1e3c7-502">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1e3c7-502">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1e3c7-503">Klicken Sie mit der rechten Maustaste auf den Ordner *Controller*.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-503">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="1e3c7-504">Wählen Sie **Hinzufügen** > **Neues Element** aus.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-504">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="1e3c7-505">Wählen Sie im Dialogfeld **Neues Element hinzufügen** die Vorlage **API-Controllerklasse** aus.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-505">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="1e3c7-506">Benennen Sie die Klasse *TodoController*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-506">Name the class *TodoController*, and select **Add**.</span></span>

  ![Dialogfeld „Neues Element hinzufügen“ mit Controller im Suchfeld und ausgewähltem Web-API-Controller](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="1e3c7-508">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="1e3c7-508">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="1e3c7-509">Erstellen Sie im Ordner *Controllers* eine Klasse namens `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-509">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="1e3c7-510">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-510">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="1e3c7-511">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-511">The preceding code:</span></span>

* <span data-ttu-id="1e3c7-512">Definiert eine API-Controllerklasse ohne Methoden.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-512">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="1e3c7-513">Markiert die Klasse mit dem [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute)-Attribut.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-513">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="1e3c7-514">Dieses Attribut gibt an, dass der Controller auf Web-API-Anforderungen reagiert.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-514">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="1e3c7-515">Weitere Informationen zu bestimmten Verhaltensweisen, die das Attribut ermöglicht, finden Sie unter <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-515">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="1e3c7-516">Verwendet die Abhängigkeitsinjektion, um den Datenbankkontext (`TodoContext`) dem Controller hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-516">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="1e3c7-517">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-517">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="1e3c7-518">Fügt der Datenbank, falls sie leer ist, ein Element mit dem Namen `Item1` hinzu.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-518">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="1e3c7-519">Dieser Code befindet sich im Konstruktor. Er wird bei jeder neuen HTTP-Anforderung ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-519">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="1e3c7-520">Wenn Sie alle Elemente löschen, erstellt der Konstruktor beim nächsten Aufruf einer API-Methode erneut `Item1`.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-520">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="1e3c7-521">So sieht es möglicherweise so aus, als sei die Löschung fehlgeschlagen, obwohl sie erfolgreich war.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-521">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="1e3c7-522">Hinzufügen von GET-Methoden</span><span class="sxs-lookup"><span data-stu-id="1e3c7-522">Add Get methods</span></span>

<span data-ttu-id="1e3c7-523">Fügen Sie der Klasse `TodoController` die folgenden Methoden hinzu, um eine API bereitzustellen, die Aufgaben abruft:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-523">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="1e3c7-524">Diese Methoden implementieren zwei GET-Endpunkte:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-524">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="1e3c7-525">Halten Sie die App an, falls diese noch ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-525">Stop the app if it's still running.</span></span> <span data-ttu-id="1e3c7-526">Führen Sie diese dann noch mal aus, damit die letzten Änderungen übernommen werden.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-526">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="1e3c7-527">Testen Sie die App, indem Sie die beiden Endpunkte in einem Browser aufrufen.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-527">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="1e3c7-528">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-528">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="1e3c7-529">Die folgende HTTP-Antwort wird durch den Aufruf von `GetTodoItems` erzeugt:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-529">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="1e3c7-530">Routing und URL-Pfade</span><span class="sxs-lookup"><span data-stu-id="1e3c7-530">Routing and URL paths</span></span>

<span data-ttu-id="1e3c7-531">Das Attribut [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) gibt eine Methode an, die auf eine HTTP GET-Anforderung antwortet.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-531">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="1e3c7-532">Der URL-Pfad für jede Methode wird wie folgt erstellt:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-532">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="1e3c7-533">Beginnen Sie mit der Vorlagenzeichenfolge im `Route`-Attribut des Controllers:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-533">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="1e3c7-534">Ersetzen Sie `[controller]` durch den Namen des Controllers, bei dem es sich standardmäßig um den Namen der Controller-Klasse ohne das Suffix „Controller“ handelt.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-534">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="1e3c7-535">Bei diesem Beispiel ist der Klassenname des Controllers „**Todo**Controller“, d.h. der Controllername lautet „todo“.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-535">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="1e3c7-536">Beim ASP.NET Core-[Routing](xref:mvc/controllers/routing) wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-536">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="1e3c7-537">Wenn das `[HttpGet]`-Attribut eine Routenvorlage (z. B. `[HttpGet("products")]`) hat, fügen Sie diese an den Pfad an.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-537">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="1e3c7-538">In diesem Beispiel wird keine Vorlage verwendet.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-538">This sample doesn't use a template.</span></span> <span data-ttu-id="1e3c7-539">Weitere Informationen finden Sie unter [Attributrouting mit Http[Verb]-Attributen](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="1e3c7-539">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="1e3c7-540">In der folgenden `GetTodoItem`-Methode ist `"{id}"` eine Platzhaltervariable für den eindeutigen Bezeichner des To-do-Elements.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-540">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="1e3c7-541">Wenn `GetTodoItem` aufgerufen wird, wird der Wert von `"{id}"` in der URL der Methode in ihrem Parameter `id` bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-541">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="1e3c7-542">Rückgabewerte</span><span class="sxs-lookup"><span data-stu-id="1e3c7-542">Return values</span></span>

<span data-ttu-id="1e3c7-543">Der Rückgabetyp der Methoden `GetTodoItems` und `GetTodoItem` ist [ActionResult\<T>-Typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="1e3c7-543">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="1e3c7-544">ASP.NET Core serialisiert automatisch das Objekt in [JSON](https://www.json.org/) und schreibt den JSON-Code in den Text der Antwortnachricht.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-544">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="1e3c7-545">Der Antwortcode für diesen Rückgabetyp ist 200, vorausgesetzt, es gibt keine Ausnahmefehler.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-545">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="1e3c7-546">Nicht behandelte Ausnahmen werden in 5xx-Fehler übersetzt.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-546">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="1e3c7-547">`ActionResult`-Rückgabetypen können eine Vielzahl von HTTP-Statuscodes darstellen.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-547">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="1e3c7-548">Beispielsweise kann `GetTodoItem` zwei verschiedene Statuswerte zurückgeben:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-548">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="1e3c7-549">Wenn kein Element mit der angeforderten ID übereinstimmt, gibt die Methode einen 404-Fehlercode [Nicht gefunden](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) zurück.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-549">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="1e3c7-550">Andernfalls gibt die Methode 200 mit einem JSON-Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-550">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="1e3c7-551">Die Rückgabe von `item` löst eine HTTP 200-Antwort aus.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-551">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="1e3c7-552">Testen der GetTodoItems-Methode</span><span class="sxs-lookup"><span data-stu-id="1e3c7-552">Test the GetTodoItems method</span></span>

<span data-ttu-id="1e3c7-553">Dieses Tutorial verwendet Postman zum Testen der Web-API.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-553">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="1e3c7-554">Installieren Sie [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="1e3c7-554">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="1e3c7-555">Starten Sie die Web-App.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-555">Start the web app.</span></span>
* <span data-ttu-id="1e3c7-556">Starten Sie Postman.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-556">Start Postman.</span></span>
* <span data-ttu-id="1e3c7-557">Deaktivieren Sie **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="1e3c7-557">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1e3c7-558">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1e3c7-558">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1e3c7-559">Deaktivieren Sie auf der Registerkarte **General** (Allgemein) unter File > Settings (**Datei** > **Einstellungen**) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="1e3c7-559">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="1e3c7-560">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="1e3c7-560">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="1e3c7-561">Deaktivieren Sie auf der Registerkarte **General** (Allgemein) unter **Postman** > **Preferences** (Postman > Einstellungen) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="1e3c7-561">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="1e3c7-562">Wählen Sie alternativ den Schraubenschlüssel und **Settings** (Einstellungen) aus, und deaktivieren Sie dann die Überprüfung des SSL-Zertifikats.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-562">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="1e3c7-563">Aktivieren Sie die Verifizierung des SSL-Zertifikats wieder, nachdem Sie den Controller getestet haben.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-563">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="1e3c7-564">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-564">Create a new request.</span></span>
  * <span data-ttu-id="1e3c7-565">Legen Sie die HTTP-Methode auf **GET** fest.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-565">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="1e3c7-566">Legen Sie die Anforderungs-URL auf `https://localhost:<port>/api/todo` fest.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-566">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="1e3c7-567">Beispielsweise `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-567">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="1e3c7-568">Wählen Sie in Postman **Two pane view** (Ansicht mit zwei Bereichen) aus.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-568">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="1e3c7-569">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-569">Select **Send**.</span></span>

![Postman mit GET-Anforderung](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="1e3c7-571">Hinzufügen einer Methode</span><span class="sxs-lookup"><span data-stu-id="1e3c7-571">Add a Create method</span></span>

<span data-ttu-id="1e3c7-572">Fügen Sie die folgende `PostTodoItem`-Methode in *Controllers/TodoController.cs* hinzu:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-572">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="1e3c7-573">Der oben stehende Code ist eine HTTP POST-Methode, wie durch das [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute)-Attribut angegeben.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-573">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="1e3c7-574">Die Methode ruft den Wert der Aufgabe aus dem Text der HTTP-Anforderung ab.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-574">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="1e3c7-575">Die `CreatedAtAction`-Methode:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-575">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="1e3c7-576">Gibt bei einer erfolgreichen Anforderung den Statuscode „HTTP 201“ zurück.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-576">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="1e3c7-577">HTTP 201 ist die Standardantwort für eine HTTP POST-Methode, die eine neue Ressource auf dem Server erstellt.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-577">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="1e3c7-578">Fügt der Antwort einen `Location`-Header hinzu.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-578">Adds a `Location` header to the response.</span></span> <span data-ttu-id="1e3c7-579">Der `Location`-Header gibt den URI des neu erstellten To-Do-Elements zurück.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-579">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="1e3c7-580">Weitere Informationen finden Sie unter [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="1e3c7-580">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="1e3c7-581">Verweist auf die `GetTodoItem`-Aktion zum Erstellen des URIs des `Location`-Headers.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-581">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="1e3c7-582">Das `nameof`-Schlüsselwort von C# wird verwendet, um eine Hartcodierung des Aktionsnamens im `CreatedAtAction`-Aufruf zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-582">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="1e3c7-583">Testen der PostTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="1e3c7-583">Test the PostTodoItem method</span></span>

* <span data-ttu-id="1e3c7-584">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-584">Build the project.</span></span>
* <span data-ttu-id="1e3c7-585">Legen Sie in Postman die HTTP-Methode auf `POST` fest.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-585">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="1e3c7-586">Wählen Sie die Registerkarte **Body** (Text) aus.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-586">Select the **Body** tab.</span></span>
* <span data-ttu-id="1e3c7-587">Wählen Sie das Optionsfeld **raw** (Unformatiert) aus.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-587">Select the **raw** radio button.</span></span>
* <span data-ttu-id="1e3c7-588">Legen Sie den Typ auf **JSON (application/json)** fest.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-588">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="1e3c7-589">Geben Sie für die Aufgabe den Anforderungstext im JSON-Format ein:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-589">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="1e3c7-590">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-590">Select **Send**.</span></span>

  ![Postman mit erstellter Anforderung](first-web-api/_static/create.png)

  <span data-ttu-id="1e3c7-592">Wenn Sie die Fehlermeldung „405: Methode nicht zulässig“ erhalten, wurde das Projekt wahrscheinlich nicht kompiliert, nachdem die Methode `PostTodoItem` hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-592">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="1e3c7-593">Testen des Adressheader-URIs</span><span class="sxs-lookup"><span data-stu-id="1e3c7-593">Test the location header URI</span></span>

* <span data-ttu-id="1e3c7-594">Wählen Sie auf der Registerkarte **Header** (Header) den Bereich **Response** (Antwort) aus.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-594">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="1e3c7-595">Kopieren Sie den den Headerwert von **Location** (Speicherort):</span><span class="sxs-lookup"><span data-stu-id="1e3c7-595">Copy the **Location** header value:</span></span>

  ![Registerkarte „Header“ in der Postman-Konsole](first-web-api/_static/pmc2.png)

* <span data-ttu-id="1e3c7-597">Legen Sie die Methode auf „GET“ fest.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-597">Set the method to GET.</span></span>
* <span data-ttu-id="1e3c7-598">Fügen Sie den URI (z. B. `https://localhost:5001/api/Todo/2`) ein.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-598">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="1e3c7-599">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-599">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="1e3c7-600">Hinzufügen einer PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="1e3c7-600">Add a PutTodoItem method</span></span>

<span data-ttu-id="1e3c7-601">Fügen Sie die folgende `PutTodoItem`-Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-601">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="1e3c7-602">`PutTodoItem` ähnelt `PostTodoItem`, verwendet allerdings HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-602">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="1e3c7-603">Die Antwort ist [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="1e3c7-603">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="1e3c7-604">Gemäß der HTTP-Spezifikation erfordert eine PUT-Anforderung, dass der Client die gesamte aktualisierte Entität (nicht nur die Änderungen) sendet.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-604">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="1e3c7-605">Verwenden Sie [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute), um Teilupdates zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-605">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="1e3c7-606">Wenn beim Aufrufen von `PutTodoItem` ein Fehler zurückgegeben wird, rufen Sie `GET` auf, um sicherzustellen, dass die Datenbank ein Element enthält.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-606">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="1e3c7-607">Testen der PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="1e3c7-607">Test the PutTodoItem method</span></span>

<span data-ttu-id="1e3c7-608">In diesem Beispiel wird eine In-Memory-Datenbank verwendet, die jedes Mal initialisiert werden muss, wenn die App gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-608">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="1e3c7-609">Es muss ein Element in der Datenbank vorhanden sein, bevor Sie einen PUT-Aufruf durchführen.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-609">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="1e3c7-610">Rufen Sie vor einem PUT-Aufruf GET auf, um sicherzustellen, dass ein Element in der Datenbank vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-610">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="1e3c7-611">Aktualisieren Sie die Aufgabe mit der ID = 1, und setzen Sie ihren Namen auf „feed fish“:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-611">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="1e3c7-612">Die folgende Abbildung zeigt das Postman-Update:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-612">The following image shows the Postman update:</span></span>

![Postman-Konsole mit der Antwort „204 (Kein Inhalt)“](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="1e3c7-614">Hinzufügen einer DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="1e3c7-614">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="1e3c7-615">Fügen Sie die folgende `DeleteTodoItem`-Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-615">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="1e3c7-616">Die `DeleteTodoItem`-Antwort lautet [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="1e3c7-616">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="1e3c7-617">Testen der DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="1e3c7-617">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="1e3c7-618">So löschen Sie mit Postman eine Aufgabe</span><span class="sxs-lookup"><span data-stu-id="1e3c7-618">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="1e3c7-619">Legen Sie die Methode auf `DELETE` fest.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-619">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="1e3c7-620">Legen Sie den URI des zu löschenden Objekts fest, z. B. `https://localhost:5001/api/todo/1`.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-620">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="1e3c7-621">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-621">Select **Send**.</span></span>

<span data-ttu-id="1e3c7-622">Sie können in der Beispiel-App alle Elemente löschen.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-622">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="1e3c7-623">Sobald das letzte Element gelöscht wurde, wird allerdings beim nächsten API-Aufruf vom Modellklassenkonstruktor ein neues erstellt.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-623">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="1e3c7-624">Aufrufen der Web-API mit JavaScript</span><span class="sxs-lookup"><span data-stu-id="1e3c7-624">Call the web API with JavaScript</span></span>

<span data-ttu-id="1e3c7-625">In diesem Abschnitt wird eine HTML-Seite hinzugefügt, die mithilfe von JavaScript die Web-API aufruft.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-625">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="1e3c7-626">jQuery initiiert die Anforderung.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-626">jQuery initiates the request.</span></span> <span data-ttu-id="1e3c7-627">JavaScript aktualisiert die Seite mit den Details aus der Antwort der Web-API.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-627">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="1e3c7-628">Konfigurieren Sie die App so, dass sie [statische Dateien bereitstellt](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_), und [aktivieren Sie die Standarddateizuordnung](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_), indem Sie *startup.cs* mit dem unten markierten Code aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-628">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="1e3c7-629">Erstellen Sie im Projektverzeichnis den Ordner *wwwwroot*.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-629">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="1e3c7-630">Fügen Sie dem Verzeichnis *wwwroot* eine HTML-Datei namens *index.html* hinzu.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-630">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="1e3c7-631">Ersetzen Sie den Inhalt durch folgendes Markup:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-631">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="1e3c7-632">Fügen Sie dem Verzeichnis *wwwroot* eine JavaScript-Datei namens *site.js* hinzu.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-632">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="1e3c7-633">Ersetzen Sie den Inhalt durch folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-633">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="1e3c7-634">Möglicherweise ist eine Änderung an den Starteinstellungen des ASP.NET Core-Projekts erforderlich, um die HTML-Seite lokal zu testen:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-634">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="1e3c7-635">Öffnen Sie *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-635">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="1e3c7-636">Entfernen Sie die `launchUrl`-Eigenschaft, um zu erzwingen, dass die App mit *index.html* als Startseite geöffnet wird. Dies ist die Standarddatei des Projekts.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-636">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="1e3c7-637">Dieses Beispiel ruft alle CRUD-Methoden der Web-API auf.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-637">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="1e3c7-638">Im Folgenden werden die API-Aufrufe erläutert.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-638">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="1e3c7-639">Abrufen einer Liste von To-Do-Elementen</span><span class="sxs-lookup"><span data-stu-id="1e3c7-639">Get a list of to-do items</span></span>

<span data-ttu-id="1e3c7-640">jQuery sendet eine HTTP GET-Anforderung an die Web-API, die JSON-Code mit einem Array aus To-Do-Elementen zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-640">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="1e3c7-641">Die Rückruffunktion `success` wird aufgerufen, wenn die Anforderung erfolgreich ist.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-641">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="1e3c7-642">Im Rückruf wird DOM mit den To-Do-Informationen aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-642">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="1e3c7-643">Hinzufügen eines To-Do-Elements</span><span class="sxs-lookup"><span data-stu-id="1e3c7-643">Add a to-do item</span></span>

<span data-ttu-id="1e3c7-644">jQuery sendet eine HTTP POST-Anforderung mit dem To-Do-Element im Anforderungstext.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-644">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="1e3c7-645">Die Optionen `accepts` und `contentType` werden auf `application/json` festgelegt, um den gesendeten und empfangenen Medientyp anzugeben.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-645">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="1e3c7-646">Die Aufgabe wird mit [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) in JSON konvertiert.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-646">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="1e3c7-647">Wenn die API den Statuscode „Erfolgreich“ zurückgibt, wird die Funktion `getData` aufgerufen, um die HTML-Tabelle zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-647">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="1e3c7-648">Aktualisieren eines To-Do-Elements</span><span class="sxs-lookup"><span data-stu-id="1e3c7-648">Update a to-do item</span></span>

<span data-ttu-id="1e3c7-649">Das Aktualisieren einer Aufgabe ist vergleichbar mit dem Hinzufügen einer Aufgabe.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-649">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="1e3c7-650">Die `url` ändert sich, um den eindeutigen Bezeichner des Elements hinzuzufügen. Der `type` lautet `PUT`.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-650">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="1e3c7-651">Löschen eines To-Do-Elements</span><span class="sxs-lookup"><span data-stu-id="1e3c7-651">Delete a to-do item</span></span>

<span data-ttu-id="1e3c7-652">Eine Aufgabe wird folgendermaßen gelöscht: im AJAX-Aufruf wird `type` auf `DELETE` festgelegt, und der eindeutige Bezeichner des Elements wird in der URL angegeben.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-652">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="1e3c7-653">Hinzufügen der Authentifizierungsunterstützung zu einer Web-API</span><span class="sxs-lookup"><span data-stu-id="1e3c7-653">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="1e3c7-654">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="1e3c7-654">Additional resources</span></span>

<span data-ttu-id="1e3c7-655">[Sie können den Beispielcode für dieses Tutorial anzeigen oder herunterladen.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)</span><span class="sxs-lookup"><span data-stu-id="1e3c7-655">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="1e3c7-656">[Informationen zum Herunterladen](xref:index#how-to-download-a-sample) finden Sie hier.</span><span class="sxs-lookup"><span data-stu-id="1e3c7-656">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="1e3c7-657">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="1e3c7-657">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="1e3c7-658">Dieses Tutorial auf YouTube</span><span class="sxs-lookup"><span data-stu-id="1e3c7-658">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)

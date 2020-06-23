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
ms.openlocfilehash: 71ab0fc0a679acf540fa53fa2a9c8ba893b34edf
ms.sourcegitcommit: d243fadeda20ad4f142ea60301ae5f5e0d41ed60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/12/2020
ms.locfileid: "84724353"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="f2343-103">Tutorial: Erstellen einer Web-API mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f2343-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="f2343-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5) und [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="f2343-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="f2343-105">In diesem Tutorial lernen Sie die Grundlagen der Erstellung einer Web-API mit ASP.NET Core kennen.</span><span class="sxs-lookup"><span data-stu-id="f2343-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f2343-106">In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="f2343-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f2343-107">Erstellen eines Web-API-Projekts</span><span class="sxs-lookup"><span data-stu-id="f2343-107">Create a web API project.</span></span>
> * <span data-ttu-id="f2343-108">Hinzufügen einer Modellklasse und eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="f2343-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="f2343-109">Erstellen eines Controllergerüsts mit CRUD-Methoden</span><span class="sxs-lookup"><span data-stu-id="f2343-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="f2343-110">Konfigurieren des Routings, der URL-Pfade und der Rückgabewerte</span><span class="sxs-lookup"><span data-stu-id="f2343-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="f2343-111">Aufrufen der Web-API mit Postman</span><span class="sxs-lookup"><span data-stu-id="f2343-111">Call the web API with Postman.</span></span>

<span data-ttu-id="f2343-112">Abschließend steht Ihnen eine Web-API zur Verfügung, die in einer relationalen Datenbank gespeicherte To-do-Elemente verwalten kann.</span><span class="sxs-lookup"><span data-stu-id="f2343-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="f2343-113">Übersicht</span><span class="sxs-lookup"><span data-stu-id="f2343-113">Overview</span></span>

<span data-ttu-id="f2343-114">In diesem Tutorial wird die folgende API erstellt:</span><span class="sxs-lookup"><span data-stu-id="f2343-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="f2343-115">API</span><span class="sxs-lookup"><span data-stu-id="f2343-115">API</span></span> | <span data-ttu-id="f2343-116">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f2343-116">Description</span></span> | <span data-ttu-id="f2343-117">Anforderungstext</span><span class="sxs-lookup"><span data-stu-id="f2343-117">Request body</span></span> | <span data-ttu-id="f2343-118">Antworttext</span><span class="sxs-lookup"><span data-stu-id="f2343-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="f2343-119">Alle To-do-Elemente abrufen</span><span class="sxs-lookup"><span data-stu-id="f2343-119">Get all to-do items</span></span> | <span data-ttu-id="f2343-120">Keine</span><span class="sxs-lookup"><span data-stu-id="f2343-120">None</span></span> | <span data-ttu-id="f2343-121">Array von To-do-Elementen</span><span class="sxs-lookup"><span data-stu-id="f2343-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="f2343-122">Ein Element nach ID abrufen</span><span class="sxs-lookup"><span data-stu-id="f2343-122">Get an item by ID</span></span> | <span data-ttu-id="f2343-123">Keine</span><span class="sxs-lookup"><span data-stu-id="f2343-123">None</span></span> | <span data-ttu-id="f2343-124">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="f2343-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="f2343-125">Neues Element hinzufügen</span><span class="sxs-lookup"><span data-stu-id="f2343-125">Add a new item</span></span> | <span data-ttu-id="f2343-126">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="f2343-126">To-do item</span></span> | <span data-ttu-id="f2343-127">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="f2343-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="f2343-128">Vorhandenes Element aktualisieren &nbsp;</span><span class="sxs-lookup"><span data-stu-id="f2343-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="f2343-129">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="f2343-129">To-do item</span></span> | <span data-ttu-id="f2343-130">Keine</span><span class="sxs-lookup"><span data-stu-id="f2343-130">None</span></span> |
|<span data-ttu-id="f2343-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="f2343-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="f2343-132">Löschen eines Elements &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="f2343-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="f2343-133">Keine</span><span class="sxs-lookup"><span data-stu-id="f2343-133">None</span></span> | <span data-ttu-id="f2343-134">Keine</span><span class="sxs-lookup"><span data-stu-id="f2343-134">None</span></span>|

<span data-ttu-id="f2343-135">Das folgende Diagramm zeigt den Entwurf der App.</span><span class="sxs-lookup"><span data-stu-id="f2343-135">The following diagram shows the design of the app.</span></span>

![Der Client ist das Feld ganz links.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="f2343-141">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="f2343-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2343-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2343-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="f2343-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f2343-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f2343-144">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="f2343-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="f2343-145">Erstellen eines Webprojekts</span><span class="sxs-lookup"><span data-stu-id="f2343-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2343-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2343-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f2343-147">Klicken Sie im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="f2343-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="f2343-148">Wählen Sie die Vorlage **ASP.NET Core-Webanwendung** aus, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="f2343-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="f2343-149">Geben Sie dem Projekt den Namen *TodoApi*, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="f2343-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="f2343-150">Vergewissern Sie sich, dass im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Optionen **.NET Core** und **ASP.NET Core 3.1** ausgewählt sind.</span><span class="sxs-lookup"><span data-stu-id="f2343-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="f2343-151">Wählen Sie die Vorlage **API** aus, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="f2343-151">Select the **API** template and click **Create**.</span></span>

![VS-Dialogfeld „Neues Projekt“](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="f2343-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f2343-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f2343-154">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="f2343-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="f2343-155">Wechseln Sie mit dem Befehl `cd`' zu dem Ordner, der den Projektordner enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="f2343-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="f2343-156">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="f2343-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="f2343-157">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem Projekt die erforderlichen Elemente hinzufügen möchten, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="f2343-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="f2343-158">Die obenstehenden Befehle haben folgende Konsequenzen:</span><span class="sxs-lookup"><span data-stu-id="f2343-158">The preceding commands:</span></span>

  * <span data-ttu-id="f2343-159">Ein neues Web-API-Projekt wird erstellt und in Visual Studio Code geöffnet.</span><span class="sxs-lookup"><span data-stu-id="f2343-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="f2343-160">Die NuGet-Pakete, die im nächsten Abschnitt erforderlich sind, werden hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="f2343-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f2343-161">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="f2343-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f2343-162">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="f2343-162">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="f2343-164">Navigieren Sie in Visual Studio für Mac-Versionen vor Version 8.6 zu **.NET Core** > **App** > **API** > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="f2343-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="f2343-165">Bei Version 8.6 oder höher klicken Sie auf **Web and Console** > **App** > **API** > **Weiter** (Web und Konsole).</span><span class="sxs-lookup"><span data-stu-id="f2343-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next** .</span></span>

  ![Auswählen von macOS-API-Vorlagen](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="f2343-167">Vergewissern Sie sich, dass das **Zielframework** auf **.NET Core 3.1** festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="f2343-167">Confirm the **Target Framework** is set to **.NET Core 3.1**.</span></span> <span data-ttu-id="f2343-168">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="f2343-168">Select **Next**.</span></span>

  ![Auswahl für .NET Core 3.1 in macOS](first-web-api-mac/_static/api_31_config.png)

* <span data-ttu-id="f2343-170">Geben Sie für **Projektname** *TodoApi* ein, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="f2343-170">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogfeld „Konfiguration“](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="f2343-172">Öffnen Sie im Projektordner ein Befehlsterminal, und führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="f2343-172">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="f2343-173">Testen der API</span><span class="sxs-lookup"><span data-stu-id="f2343-173">Test the API</span></span>

<span data-ttu-id="f2343-174">Die Projektvorlage erstellt eine `WeatherForecast`-API.</span><span class="sxs-lookup"><span data-stu-id="f2343-174">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="f2343-175">Rufen Sie in einem Browser die `Get`-Methode zum Testen der App auf.</span><span class="sxs-lookup"><span data-stu-id="f2343-175">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2343-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2343-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f2343-177">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="f2343-177">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="f2343-178">Visual Studio startet einen Browser und navigiert zu `https://localhost:<port>/WeatherForecast`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="f2343-178">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="f2343-179">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem IIS Express-Zertifikat vertrauen, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="f2343-179">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="f2343-180">Wählen Sie im folgenden Dialogfeld **Sicherheitswarnung** die Option **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="f2343-180">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f2343-181">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f2343-181">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f2343-182">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="f2343-182">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="f2343-183">Navigieren Sie in einem Browser zur folgenden URL: `https://localhost:5001/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="f2343-183">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f2343-184">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="f2343-184">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f2343-185">Wählen Sie **Ausführen** > **Debugging starten** aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="f2343-185">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="f2343-186">Visual Studio für Mac startet einen Browser und navigiert zu `https://localhost:<port>`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="f2343-186">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="f2343-187">Der Fehler „HTTP 404: Nicht gefunden“ wird zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="f2343-187">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="f2343-188">Fügen Sie `/WeatherForecast` an die URL an, d. h., ändern Sie sie in `https://localhost:<port>/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="f2343-188">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="f2343-189">Der zurückgegebene JSON-Code sieht in etwa wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="f2343-189">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="f2343-190">Hinzufügen einer Modellklasse</span><span class="sxs-lookup"><span data-stu-id="f2343-190">Add a model class</span></span>

<span data-ttu-id="f2343-191">Ein *Modell* ist eine Gruppe von Klassen, die die Daten darstellen, die die App verwaltet.</span><span class="sxs-lookup"><span data-stu-id="f2343-191">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="f2343-192">Das Modell für diese App ist eine einzelne `TodoItem`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="f2343-192">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2343-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2343-193">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f2343-194">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="f2343-194">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="f2343-195">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="f2343-195">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="f2343-196">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="f2343-196">Name the folder *Models*.</span></span>

* <span data-ttu-id="f2343-197">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="f2343-197">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="f2343-198">Nennen Sie die Klasse *TodoItem*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="f2343-198">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="f2343-199">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="f2343-199">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f2343-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f2343-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f2343-201">Fügen Sie einen Ordner namens *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="f2343-201">Add a folder named *Models*.</span></span>

* <span data-ttu-id="f2343-202">Fügen Sie dem Ordner *Modelle* mit dem folgenden Code eine `TodoItem`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="f2343-202">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f2343-203">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="f2343-203">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f2343-204">Klicken Sie mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="f2343-204">Right-click the project.</span></span> <span data-ttu-id="f2343-205">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="f2343-205">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="f2343-206">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="f2343-206">Name the folder *Models*.</span></span>

  ![Neuer Ordner](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="f2343-208">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Neue Datei** > **Allgemein** > **Leere Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="f2343-208">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="f2343-209">Nennen Sie die Klasse *TodoItem*, und klicken Sie dann auf **Neu**.</span><span class="sxs-lookup"><span data-stu-id="f2343-209">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="f2343-210">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="f2343-210">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="f2343-211">Die `Id`-Eigenschaft fungiert als eindeutiger Schlüssel in einer relationalen Datenbank.</span><span class="sxs-lookup"><span data-stu-id="f2343-211">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="f2343-212">Modellklassen können überall im Projekt platziert werden, doch gemäß der Konvention wird der Ordner *Modelle* verwendet.</span><span class="sxs-lookup"><span data-stu-id="f2343-212">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="f2343-213">Hinzufügen eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="f2343-213">Add a database context</span></span>

<span data-ttu-id="f2343-214">Der *Datenbankkontext* ist die Hauptklasse, die die Entity Framework-Funktionen für ein Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="f2343-214">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="f2343-215">Diese Klasse wird durch Ableiten von der `Microsoft.EntityFrameworkCore.DbContext`-Klasse erstellt.</span><span class="sxs-lookup"><span data-stu-id="f2343-215">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2343-216">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2343-216">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="f2343-217">Hinzufügen von Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="f2343-217">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="f2343-218">Klicken Sie im Menü **Extras** auf **NuGet-Paket-Manager > NuGet-Pakete für Projektmappe verwalten...** .</span><span class="sxs-lookup"><span data-stu-id="f2343-218">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="f2343-219">Klicken Sie auf die Registerkarte **Durchsuchen**, und geben Sie dann **Microsoft.EntityFrameworkCore.SqlServer** in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="f2343-219">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="f2343-220">Wählen Sie im linken Bereich **Microsoft.EntityFrameworkCore.SqlServer** aus.</span><span class="sxs-lookup"><span data-stu-id="f2343-220">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="f2343-221">Aktivieren Sie das Kontrollkästchen **Projekt** im rechten Bereich, und klicken Sie dann auf **Installieren**.</span><span class="sxs-lookup"><span data-stu-id="f2343-221">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="f2343-222">Fügen Sie mithilfe der obigen Schritte das `Microsoft.EntityFrameworkCore.InMemory`-NuGet-Paket hinzu.</span><span class="sxs-lookup"><span data-stu-id="f2343-222">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![NuGet-Paket-Manager](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="f2343-224">Hinzufügen des TodoContext-Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="f2343-224">Add the TodoContext database context</span></span>

* <span data-ttu-id="f2343-225">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="f2343-225">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="f2343-226">Nennen Sie die Klasse *TodoContext*, und klicken Sie auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="f2343-226">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f2343-227">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="f2343-227">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="f2343-228">Fügen Sie eine `TodoContext`-Klasse zum Ordner *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="f2343-228">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="f2343-229">Geben Sie den folgenden Code ein:</span><span class="sxs-lookup"><span data-stu-id="f2343-229">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="f2343-230">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="f2343-230">Register the database context</span></span>

<span data-ttu-id="f2343-231">In ASP.NET Core müssen Dienste wie der Datenbankkontext mit dem Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) registriert werden.</span><span class="sxs-lookup"><span data-stu-id="f2343-231">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="f2343-232">Der Container stellt den Dienst für Controller bereit.</span><span class="sxs-lookup"><span data-stu-id="f2343-232">The container provides the service to controllers.</span></span>

<span data-ttu-id="f2343-233">Aktualisieren Sie *Startup.cs* mit dem folgenden hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="f2343-233">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="f2343-234">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="f2343-234">The preceding code:</span></span>

* <span data-ttu-id="f2343-235">Entfernt nicht benötigte `using`-Deklarationen</span><span class="sxs-lookup"><span data-stu-id="f2343-235">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="f2343-236">Fügt dem Abhängigkeitscontainer den Datenbankkontext hinzu</span><span class="sxs-lookup"><span data-stu-id="f2343-236">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="f2343-237">Gibt an, dass der Datenbankkontext eine In-Memory Database verwendet</span><span class="sxs-lookup"><span data-stu-id="f2343-237">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="f2343-238">Erstellen eines Controllergerüsts</span><span class="sxs-lookup"><span data-stu-id="f2343-238">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2343-239">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2343-239">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f2343-240">Klicken Sie mit der rechten Maustaste auf den Ordner *Controller*.</span><span class="sxs-lookup"><span data-stu-id="f2343-240">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="f2343-241">Wählen Sie **Hinzufügen** > **Neues Gerüstelement** aus.</span><span class="sxs-lookup"><span data-stu-id="f2343-241">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="f2343-242">Klicken Sie auf **API-Controller mit Aktionen unter Verwendung von Entity Framework** und dann auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="f2343-242">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="f2343-243">Führen Sie im Dialogfeld **API-Controller mit Aktionen unter Verwendung von Entity Framework** folgende Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="f2343-243">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="f2343-244">Wählen Sie in der **Modellklasse** das Element **TodoItem (TodoApi.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="f2343-244">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="f2343-245">Wählen Sie in der **Datenkontextklasse** das Element **TodoContext (TodoApi.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="f2343-245">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="f2343-246">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="f2343-246">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f2343-247">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="f2343-247">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="f2343-248">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="f2343-248">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="f2343-249">Die obenstehenden Befehle haben folgende Konsequenzen:</span><span class="sxs-lookup"><span data-stu-id="f2343-249">The preceding commands:</span></span>

* <span data-ttu-id="f2343-250">Die NuGet-Pakete für den Gerüstbau werden hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="f2343-250">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="f2343-251">Die Gerüstbau-Engine (`dotnet-aspnet-codegenerator`) wird installiert.</span><span class="sxs-lookup"><span data-stu-id="f2343-251">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="f2343-252">Das Gerüst für `TodoItemsController` wird erstellt.</span><span class="sxs-lookup"><span data-stu-id="f2343-252">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="f2343-253">Der generierte Code hat folgende Auswirkungen:</span><span class="sxs-lookup"><span data-stu-id="f2343-253">The generated code:</span></span>

* <span data-ttu-id="f2343-254">Markiert die Klasse mit dem [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute)-Attribut.</span><span class="sxs-lookup"><span data-stu-id="f2343-254">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="f2343-255">Dieses Attribut gibt an, dass der Controller auf Web-API-Anforderungen reagiert.</span><span class="sxs-lookup"><span data-stu-id="f2343-255">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="f2343-256">Weitere Informationen zu bestimmten Verhaltensweisen, die das Attribut ermöglicht, finden Sie unter <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="f2343-256">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="f2343-257">Verwendet die Abhängigkeitsinjektion, um den Datenbankkontext (`TodoContext`) dem Controller hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="f2343-257">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="f2343-258">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="f2343-258">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="f2343-259">Die ASP.NET Core-Vorlagen für:</span><span class="sxs-lookup"><span data-stu-id="f2343-259">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="f2343-260">Controller mit Ansichten enthalten `[action]` in der Routenvorlage.</span><span class="sxs-lookup"><span data-stu-id="f2343-260">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="f2343-261">API-Controller enthalten keine `[action]` in der Routenvorlage.</span><span class="sxs-lookup"><span data-stu-id="f2343-261">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="f2343-262">Wenn sich das `[action]`-Token nicht in der Routenvorlage befindet, wird der [action](xref:mvc/controllers/routing#action)-Name von der Route ausgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="f2343-262">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="f2343-263">Dies bedeutet, dass der zugehörige Methodenname der Aktion nicht in der übereinstimmenden Route verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="f2343-263">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="f2343-264">Überblick über die PostTodoItem-Erstellungsmethode</span><span class="sxs-lookup"><span data-stu-id="f2343-264">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="f2343-265">Ersetzen Sie die Rückgabeanweisung in `PostTodoItem` durch eine Anweisung mit dem [nameof](/dotnet/csharp/language-reference/operators/nameof)-Operator:</span><span class="sxs-lookup"><span data-stu-id="f2343-265">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="f2343-266">Der oben stehende Code ist eine HTTP POST-Methode, wie durch das [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute)-Attribut angegeben.</span><span class="sxs-lookup"><span data-stu-id="f2343-266">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="f2343-267">Die Methode ruft den Wert der Aufgabe aus dem Text der HTTP-Anforderung ab.</span><span class="sxs-lookup"><span data-stu-id="f2343-267">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="f2343-268">Die <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>-Methode:</span><span class="sxs-lookup"><span data-stu-id="f2343-268">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="f2343-269">Gibt den HTTP-Statuscode 201 zurück, wenn der Vorgang erfolgreich ist.</span><span class="sxs-lookup"><span data-stu-id="f2343-269">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="f2343-270">HTTP 201 ist die Standardantwort für eine HTTP POST-Methode, die eine neue Ressource auf dem Server erstellt.</span><span class="sxs-lookup"><span data-stu-id="f2343-270">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="f2343-271">Fügt der Antwort einen [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location)-Header hinzu.</span><span class="sxs-lookup"><span data-stu-id="f2343-271">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="f2343-272">Der `Location`-Header legt den [URI](https://developer.mozilla.org/docs/Glossary/URI) des neu erstellten To-do-Elements fest.</span><span class="sxs-lookup"><span data-stu-id="f2343-272">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="f2343-273">Weitere Informationen finden Sie unter [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="f2343-273">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="f2343-274">Verweist auf die `GetTodoItem`-Aktion zum Erstellen des URIs des `Location`-Headers.</span><span class="sxs-lookup"><span data-stu-id="f2343-274">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="f2343-275">Das `nameof`-Schlüsselwort von C# wird verwendet, um eine Hartcodierung des Aktionsnamens im `CreatedAtAction`-Aufruf zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="f2343-275">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="f2343-276">Installieren von Postman</span><span class="sxs-lookup"><span data-stu-id="f2343-276">Install Postman</span></span>

<span data-ttu-id="f2343-277">Dieses Tutorial verwendet Postman zum Testen der Web-API.</span><span class="sxs-lookup"><span data-stu-id="f2343-277">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="f2343-278">Installieren Sie [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="f2343-278">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="f2343-279">Starten Sie die Web-App.</span><span class="sxs-lookup"><span data-stu-id="f2343-279">Start the web app.</span></span>
* <span data-ttu-id="f2343-280">Starten Sie Postman.</span><span class="sxs-lookup"><span data-stu-id="f2343-280">Start Postman.</span></span>
* <span data-ttu-id="f2343-281">Deaktivieren Sie **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="f2343-281">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="f2343-282">Deaktivieren Sie auf der Registerkarte **General** (Allgemein) unter File > Settings (**Datei** > **Einstellungen**) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="f2343-282">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="f2343-283">Aktivieren Sie die Verifizierung des SSL-Zertifikats wieder, nachdem Sie den Controller getestet haben.</span><span class="sxs-lookup"><span data-stu-id="f2343-283">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="f2343-284">Testen von PostTodoItem mit Postman</span><span class="sxs-lookup"><span data-stu-id="f2343-284">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="f2343-285">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="f2343-285">Create a new request.</span></span>
* <span data-ttu-id="f2343-286">Legen Sie als HTTP-Methode `POST` fest.</span><span class="sxs-lookup"><span data-stu-id="f2343-286">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="f2343-287">Wählen Sie die Registerkarte **Body** (Text) aus.</span><span class="sxs-lookup"><span data-stu-id="f2343-287">Select the **Body** tab.</span></span>
* <span data-ttu-id="f2343-288">Wählen Sie das Optionsfeld **raw** (Unformatiert) aus.</span><span class="sxs-lookup"><span data-stu-id="f2343-288">Select the **raw** radio button.</span></span>
* <span data-ttu-id="f2343-289">Legen Sie den Typ auf **JSON (application/json)** fest.</span><span class="sxs-lookup"><span data-stu-id="f2343-289">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="f2343-290">Geben Sie für die Aufgabe den Anforderungstext im JSON-Format ein:</span><span class="sxs-lookup"><span data-stu-id="f2343-290">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="f2343-291">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="f2343-291">Select **Send**.</span></span>

  ![Postman mit erstellter Anforderung](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="f2343-293">Testen des Adressheader-URIs</span><span class="sxs-lookup"><span data-stu-id="f2343-293">Test the location header URI</span></span>

* <span data-ttu-id="f2343-294">Wählen Sie auf der Registerkarte **Header** (Header) den Bereich **Response** (Antwort) aus.</span><span class="sxs-lookup"><span data-stu-id="f2343-294">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="f2343-295">Kopieren Sie den den Headerwert von **Location** (Speicherort):</span><span class="sxs-lookup"><span data-stu-id="f2343-295">Copy the **Location** header value:</span></span>

  ![Registerkarte „Header“ in der Postman-Konsole](first-web-api/_static/3/create.png)

* <span data-ttu-id="f2343-297">Legen Sie die Methode auf „GET“ fest.</span><span class="sxs-lookup"><span data-stu-id="f2343-297">Set the method to GET.</span></span>
* <span data-ttu-id="f2343-298">Fügen Sie den URI (z. B. `https://localhost:5001/api/TodoItems/1`) ein.</span><span class="sxs-lookup"><span data-stu-id="f2343-298">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="f2343-299">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="f2343-299">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="f2343-300">Überblick über die GET-Methoden</span><span class="sxs-lookup"><span data-stu-id="f2343-300">Examine the GET methods</span></span>

<span data-ttu-id="f2343-301">Diese Methoden implementieren zwei GET-Endpunkte:</span><span class="sxs-lookup"><span data-stu-id="f2343-301">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="f2343-302">Testen Sie die App, indem Sie die beiden Endpunkte in einem Browser oder über Postman aufrufen.</span><span class="sxs-lookup"><span data-stu-id="f2343-302">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="f2343-303">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f2343-303">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="f2343-304">Durch einen Aufruf von `GetTodoItems` wird eine Antwort gesendet, die der folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="f2343-304">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="f2343-305">Testen von GET mit Postman</span><span class="sxs-lookup"><span data-stu-id="f2343-305">Test Get with Postman</span></span>

* <span data-ttu-id="f2343-306">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="f2343-306">Create a new request.</span></span>
* <span data-ttu-id="f2343-307">Legen Sie die HTTP-Methode auf **GET** fest.</span><span class="sxs-lookup"><span data-stu-id="f2343-307">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="f2343-308">Legen Sie die Anforderungs-URL auf `https://localhost:<port>/api/TodoItems` fest.</span><span class="sxs-lookup"><span data-stu-id="f2343-308">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="f2343-309">Beispielsweise `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="f2343-309">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="f2343-310">Wählen Sie in Postman **Two pane view** (Ansicht mit zwei Bereichen) aus.</span><span class="sxs-lookup"><span data-stu-id="f2343-310">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="f2343-311">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="f2343-311">Select **Send**.</span></span>

<span data-ttu-id="f2343-312">Diese App verwendet eine In-Memory-Datenbank.</span><span class="sxs-lookup"><span data-stu-id="f2343-312">This app uses an in-memory database.</span></span> <span data-ttu-id="f2343-313">Wenn die App angehalten und dann wieder gestartet wird, werden durch die vorherige GET-Anforderung keine Daten zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="f2343-313">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="f2343-314">Wenn keine Daten zurückgegeben werden, senden Sie mit [POST](#post) Daten an die App.</span><span class="sxs-lookup"><span data-stu-id="f2343-314">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="f2343-315">Routing und URL-Pfade</span><span class="sxs-lookup"><span data-stu-id="f2343-315">Routing and URL paths</span></span>

<span data-ttu-id="f2343-316">Das Attribut [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) gibt eine Methode an, die auf eine HTTP GET-Anforderung antwortet.</span><span class="sxs-lookup"><span data-stu-id="f2343-316">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="f2343-317">Der URL-Pfad für jede Methode wird wie folgt erstellt:</span><span class="sxs-lookup"><span data-stu-id="f2343-317">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="f2343-318">Beginnen Sie mit der Vorlagenzeichenfolge im `Route`-Attribut des Controllers:</span><span class="sxs-lookup"><span data-stu-id="f2343-318">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="f2343-319">Ersetzen Sie `[controller]` durch den Namen des Controllers, bei dem es sich standardmäßig um den Namen der Controller-Klasse ohne das Suffix „Controller“ handelt.</span><span class="sxs-lookup"><span data-stu-id="f2343-319">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="f2343-320">In diesem Beispiel ist der Klassenname des Controllers „**TodoItems**Controller“. Der Controllername lautet also „TodoItems“.</span><span class="sxs-lookup"><span data-stu-id="f2343-320">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="f2343-321">Beim ASP.NET Core-[Routing](xref:mvc/controllers/routing) wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="f2343-321">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="f2343-322">Wenn das `[HttpGet]`-Attribut eine Routenvorlage (z. B. `[HttpGet("products")]`) hat, fügen Sie diese an den Pfad an.</span><span class="sxs-lookup"><span data-stu-id="f2343-322">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="f2343-323">In diesem Beispiel wird keine Vorlage verwendet.</span><span class="sxs-lookup"><span data-stu-id="f2343-323">This sample doesn't use a template.</span></span> <span data-ttu-id="f2343-324">Weitere Informationen finden Sie unter [Attributrouting mit Http[Verb]-Attributen](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="f2343-324">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="f2343-325">In der folgenden `GetTodoItem`-Methode ist `"{id}"` eine Platzhaltervariable für den eindeutigen Bezeichner des To-do-Elements.</span><span class="sxs-lookup"><span data-stu-id="f2343-325">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="f2343-326">Wenn `GetTodoItem` aufgerufen wird, wird der Wert von `"{id}"` in der URL der Methode in ihrem Parameter `id` bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="f2343-326">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="f2343-327">Rückgabewerte</span><span class="sxs-lookup"><span data-stu-id="f2343-327">Return values</span></span>

<span data-ttu-id="f2343-328">Der Rückgabetyp der Methoden `GetTodoItems` und `GetTodoItem` ist der [ActionResult\<T>-Typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="f2343-328">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="f2343-329">ASP.NET Core serialisiert automatisch das Objekt in [JSON](https://www.json.org/) und schreibt den JSON-Code in den Text der Antwortnachricht.</span><span class="sxs-lookup"><span data-stu-id="f2343-329">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="f2343-330">Der Antwortcode für diesen Rückgabetyp ist 200, vorausgesetzt, es gibt keine Ausnahmefehler.</span><span class="sxs-lookup"><span data-stu-id="f2343-330">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="f2343-331">Nicht behandelte Ausnahmen werden in 5xx-Fehler übersetzt.</span><span class="sxs-lookup"><span data-stu-id="f2343-331">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="f2343-332">`ActionResult`-Rückgabetypen können eine Vielzahl von HTTP-Statuscodes darstellen.</span><span class="sxs-lookup"><span data-stu-id="f2343-332">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="f2343-333">Beispielsweise kann `GetTodoItem` zwei verschiedene Statuswerte zurückgeben:</span><span class="sxs-lookup"><span data-stu-id="f2343-333">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="f2343-334">Wenn kein Element mit der angeforderten ID übereinstimmt, gibt die Methode einen 404-Fehlercode [Nicht gefunden](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) zurück.</span><span class="sxs-lookup"><span data-stu-id="f2343-334">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="f2343-335">Andernfalls gibt die Methode 200 mit einem JSON-Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="f2343-335">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="f2343-336">Die Rückgabe von `item` löst eine HTTP 200-Antwort aus.</span><span class="sxs-lookup"><span data-stu-id="f2343-336">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="f2343-337">PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="f2343-337">The PutTodoItem method</span></span>

<span data-ttu-id="f2343-338">Untersuchen Sie die `PutTodoItem`-Methode.</span><span class="sxs-lookup"><span data-stu-id="f2343-338">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="f2343-339">`PutTodoItem` ähnelt `PostTodoItem`, verwendet allerdings HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="f2343-339">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="f2343-340">Die Antwort ist [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="f2343-340">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="f2343-341">Gemäß der HTTP-Spezifikation erfordert eine PUT-Anforderung, dass der Client die gesamte aktualisierte Entität (nicht nur die Änderungen) sendet.</span><span class="sxs-lookup"><span data-stu-id="f2343-341">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="f2343-342">Verwenden Sie [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute), um Teilupdates zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="f2343-342">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="f2343-343">Wenn beim Aufrufen von `PutTodoItem` ein Fehler zurückgegeben wird, rufen Sie `GET` auf, um sicherzustellen, dass die Datenbank ein Element enthält.</span><span class="sxs-lookup"><span data-stu-id="f2343-343">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="f2343-344">Testen der PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="f2343-344">Test the PutTodoItem method</span></span>

<span data-ttu-id="f2343-345">In diesem Beispiel wird eine In-Memory-Datenbank verwendet, die jedes Mal initialisiert werden muss, wenn die App gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="f2343-345">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="f2343-346">Es muss ein Element in der Datenbank vorhanden sein, bevor Sie einen PUT-Aufruf durchführen.</span><span class="sxs-lookup"><span data-stu-id="f2343-346">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="f2343-347">Rufen Sie vor einem PUT-Aufruf GET auf, um sicherzustellen, dass ein Element in der Datenbank vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="f2343-347">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="f2343-348">Aktualisieren Sie das To-do-Element, das über den ID-Wert 1 verfügt, und legen Sie als Namen „feed fish“ fest:</span><span class="sxs-lookup"><span data-stu-id="f2343-348">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="f2343-349">Die folgende Abbildung zeigt das Postman-Update:</span><span class="sxs-lookup"><span data-stu-id="f2343-349">The following image shows the Postman update:</span></span>

![Postman-Konsole mit der Antwort „204 (Kein Inhalt)“](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="f2343-351">DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="f2343-351">The DeleteTodoItem method</span></span>

<span data-ttu-id="f2343-352">Untersuchen Sie die `DeleteTodoItem`-Methode.</span><span class="sxs-lookup"><span data-stu-id="f2343-352">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="f2343-353">Testen der DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="f2343-353">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="f2343-354">So löschen Sie mit Postman eine Aufgabe</span><span class="sxs-lookup"><span data-stu-id="f2343-354">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="f2343-355">Legen Sie die Methode auf `DELETE` fest.</span><span class="sxs-lookup"><span data-stu-id="f2343-355">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="f2343-356">Legen Sie den URI des zu löschenden Objekts fest, z. B. `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="f2343-356">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="f2343-357">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="f2343-357">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="f2343-358">Vermeiden von Overposting</span><span class="sxs-lookup"><span data-stu-id="f2343-358">Prevent over-posting</span></span>

<span data-ttu-id="f2343-359">Derzeit macht die Beispiel-App das gesamte `TodoItem`-Objekt verfügbar.</span><span class="sxs-lookup"><span data-stu-id="f2343-359">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="f2343-360">In den Produktions-Apps sind die Daten, die eingegeben und mithilfe einer Teilmenge des Modells zurückgegeben werden, in der Regel eingeschränkt.</span><span class="sxs-lookup"><span data-stu-id="f2343-360">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="f2343-361">Hierfür gibt es mehrere Gründe, wobei die Sicherheit einer der Hauptgründe ist.</span><span class="sxs-lookup"><span data-stu-id="f2343-361">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="f2343-362">Die Teilmenge eines Modells wird üblicherweise als Datenübertragungsobjekt (DTO, Data Transfer Object), Eingabemodell oder Anzeigemodell bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="f2343-362">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="f2343-363">In diesem Artikel wird das **DTO** verwendet.</span><span class="sxs-lookup"><span data-stu-id="f2343-363">**DTO** is used in this article.</span></span>

<span data-ttu-id="f2343-364">Ein DTO kann für Folgendes verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="f2343-364">A DTO may be used to:</span></span>

* <span data-ttu-id="f2343-365">Vermeiden Sie Overposting.</span><span class="sxs-lookup"><span data-stu-id="f2343-365">Prevent over-posting.</span></span>
* <span data-ttu-id="f2343-366">Ausblenden von Eigenschaften, die Clients nicht anzeigen sollen</span><span class="sxs-lookup"><span data-stu-id="f2343-366">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="f2343-367">Auslassen einiger Eigenschaften, um die Nutzlast zu verringern</span><span class="sxs-lookup"><span data-stu-id="f2343-367">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="f2343-368">Vereinfachen von Objektgraphen, die geschachtelte Objekte enthalten</span><span class="sxs-lookup"><span data-stu-id="f2343-368">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="f2343-369">Vereinfachte Objektgraphen können für Clients zweckmäßiger sein.</span><span class="sxs-lookup"><span data-stu-id="f2343-369">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="f2343-370">Um den DTO-Ansatz zu veranschaulichen, aktualisieren Sie die `TodoItem`-Klasse, sodass sie ein geheimes Feld einschließt:</span><span class="sxs-lookup"><span data-stu-id="f2343-370">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="f2343-371">Das geheime Feld muss in dieser App ausgeblendet werden, eine administrative App kann es jedoch verfügbar machen.</span><span class="sxs-lookup"><span data-stu-id="f2343-371">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="f2343-372">Vergewissern Sie sich, dass Sie das geheime Feld veröffentlichen und abrufen können.</span><span class="sxs-lookup"><span data-stu-id="f2343-372">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="f2343-373">Erstellen Sie ein DTO-Modell:</span><span class="sxs-lookup"><span data-stu-id="f2343-373">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="f2343-374">Aktualisieren Sie `TodoItemsController`, sodass `TodoItemDTO` verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="f2343-374">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="f2343-375">Vergewissern Sie sich, dass Sie das geheime Feld weder veröffentlichen noch abrufen können.</span><span class="sxs-lookup"><span data-stu-id="f2343-375">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="f2343-376">Aufrufen der Web-API mit JavaScript</span><span class="sxs-lookup"><span data-stu-id="f2343-376">Call the web API with JavaScript</span></span>

<span data-ttu-id="f2343-377">Mehr dazu finden Sie im [-Tutorial: Aufrufen einer ASP.NET Core-Web-API mit JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="f2343-377">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f2343-378">In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="f2343-378">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f2343-379">Erstellen eines Web-API-Projekts</span><span class="sxs-lookup"><span data-stu-id="f2343-379">Create a web API project.</span></span>
> * <span data-ttu-id="f2343-380">Hinzufügen einer Modellklasse und eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="f2343-380">Add a model class and a database context.</span></span>
> * <span data-ttu-id="f2343-381">Hinzufügen eines Controllers</span><span class="sxs-lookup"><span data-stu-id="f2343-381">Add a controller.</span></span>
> * <span data-ttu-id="f2343-382">Hinzufügen von CRUD-Methoden</span><span class="sxs-lookup"><span data-stu-id="f2343-382">Add CRUD methods.</span></span>
> * <span data-ttu-id="f2343-383">Konfigurieren von Routing und URL-Pfaden</span><span class="sxs-lookup"><span data-stu-id="f2343-383">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="f2343-384">Angeben von Rückgabewerten</span><span class="sxs-lookup"><span data-stu-id="f2343-384">Specify return values.</span></span>
> * <span data-ttu-id="f2343-385">Aufrufen der Web-API mit Postman</span><span class="sxs-lookup"><span data-stu-id="f2343-385">Call the web API with Postman.</span></span>
> * <span data-ttu-id="f2343-386">Aufrufen der Web-API mit JavaScript</span><span class="sxs-lookup"><span data-stu-id="f2343-386">Call the web API with JavaScript.</span></span>

<span data-ttu-id="f2343-387">Am Ende haben Sie eine Web-API, die in einer relationalen Datenbank gespeicherte „Aufgaben“ verwalten kann.</span><span class="sxs-lookup"><span data-stu-id="f2343-387">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="f2343-388">Übersicht</span><span class="sxs-lookup"><span data-stu-id="f2343-388">Overview</span></span>

<span data-ttu-id="f2343-389">In diesem Tutorial wird die folgende API erstellt:</span><span class="sxs-lookup"><span data-stu-id="f2343-389">This tutorial creates the following API:</span></span>

|<span data-ttu-id="f2343-390">API</span><span class="sxs-lookup"><span data-stu-id="f2343-390">API</span></span> | <span data-ttu-id="f2343-391">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="f2343-391">Description</span></span> | <span data-ttu-id="f2343-392">Anforderungstext</span><span class="sxs-lookup"><span data-stu-id="f2343-392">Request body</span></span> | <span data-ttu-id="f2343-393">Antworttext</span><span class="sxs-lookup"><span data-stu-id="f2343-393">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="f2343-394">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="f2343-394">GET /api/TodoItems</span></span> | <span data-ttu-id="f2343-395">Alle To-do-Elemente abrufen</span><span class="sxs-lookup"><span data-stu-id="f2343-395">Get all to-do items</span></span> | <span data-ttu-id="f2343-396">Keine</span><span class="sxs-lookup"><span data-stu-id="f2343-396">None</span></span> | <span data-ttu-id="f2343-397">Array von To-do-Elementen</span><span class="sxs-lookup"><span data-stu-id="f2343-397">Array of to-do items</span></span>|
|<span data-ttu-id="f2343-398">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="f2343-398">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="f2343-399">Ein Element nach ID abrufen</span><span class="sxs-lookup"><span data-stu-id="f2343-399">Get an item by ID</span></span> | <span data-ttu-id="f2343-400">Keine</span><span class="sxs-lookup"><span data-stu-id="f2343-400">None</span></span> | <span data-ttu-id="f2343-401">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="f2343-401">To-do item</span></span>|
|<span data-ttu-id="f2343-402">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="f2343-402">POST /api/TodoItems</span></span> | <span data-ttu-id="f2343-403">Neues Element hinzufügen</span><span class="sxs-lookup"><span data-stu-id="f2343-403">Add a new item</span></span> | <span data-ttu-id="f2343-404">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="f2343-404">To-do item</span></span> | <span data-ttu-id="f2343-405">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="f2343-405">To-do item</span></span> |
|<span data-ttu-id="f2343-406">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="f2343-406">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="f2343-407">Vorhandenes Element aktualisieren &nbsp;</span><span class="sxs-lookup"><span data-stu-id="f2343-407">Update an existing item &nbsp;</span></span> | <span data-ttu-id="f2343-408">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="f2343-408">To-do item</span></span> | <span data-ttu-id="f2343-409">Keine</span><span class="sxs-lookup"><span data-stu-id="f2343-409">None</span></span> |
|<span data-ttu-id="f2343-410">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="f2343-410">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="f2343-411">Löschen eines Elements &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="f2343-411">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="f2343-412">Keine</span><span class="sxs-lookup"><span data-stu-id="f2343-412">None</span></span> | <span data-ttu-id="f2343-413">Keine</span><span class="sxs-lookup"><span data-stu-id="f2343-413">None</span></span>|

<span data-ttu-id="f2343-414">Das folgende Diagramm zeigt den Entwurf der App.</span><span class="sxs-lookup"><span data-stu-id="f2343-414">The following diagram shows the design of the app.</span></span>

![Der Client ist das Feld ganz links.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="f2343-420">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="f2343-420">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2343-421">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2343-421">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="f2343-422">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f2343-422">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f2343-423">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="f2343-423">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="f2343-424">Erstellen eines Webprojekts</span><span class="sxs-lookup"><span data-stu-id="f2343-424">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2343-425">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2343-425">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f2343-426">Klicken Sie im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="f2343-426">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="f2343-427">Wählen Sie die Vorlage **ASP.NET Core-Webanwendung** aus, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="f2343-427">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="f2343-428">Geben Sie dem Projekt den Namen *TodoApi*, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="f2343-428">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="f2343-429">Vergewissern Sie sich, dass im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Optionen **.NET Core** und **ASP.NET Core 2.2** ausgewählt sind.</span><span class="sxs-lookup"><span data-stu-id="f2343-429">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="f2343-430">Wählen Sie die Vorlage **API** aus, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="f2343-430">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="f2343-431">Wählen Sie **nicht** **Enable Docker Support** (Docker-Unterstützung aktivieren) aus.</span><span class="sxs-lookup"><span data-stu-id="f2343-431">**Don't** select **Enable Docker Support**.</span></span>

![VS-Dialogfeld „Neues Projekt“](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="f2343-433">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f2343-433">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f2343-434">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="f2343-434">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="f2343-435">Wechseln Sie mit dem Befehl `cd`' zu dem Ordner, der den Projektordner enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="f2343-435">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="f2343-436">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="f2343-436">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="f2343-437">Diese Befehle erstellen ein neues Web-API-Projekt und öffnen eine neue Visual Studio Code-Instanz im neuen Projektordner.</span><span class="sxs-lookup"><span data-stu-id="f2343-437">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="f2343-438">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem Projekt die erforderlichen Elemente hinzufügen möchten, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="f2343-438">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f2343-439">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="f2343-439">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f2343-440">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="f2343-440">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="f2343-442">Navigieren Sie in Visual Studio für Mac-Versionen vor Version 8.6 zu **.NET Core** > **App** > **API** > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="f2343-442">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="f2343-443">Bei Version 8.6 oder höher klicken Sie auf **Web and Console** > **App** > **API** > **Weiter** (Web und Konsole).</span><span class="sxs-lookup"><span data-stu-id="f2343-443">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="f2343-444">Übernehmen Sie im Dialogfeld **Neue ASP.NET Core-Web-API konfigurieren** die Standardeinstellung **Zielframework** von \* *.NET Core 2.2*.</span><span class="sxs-lookup"><span data-stu-id="f2343-444">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="f2343-445">Geben Sie für **Projektname** *TodoApi* ein, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="f2343-445">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogfeld „Konfiguration“](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="f2343-447">Testen der API</span><span class="sxs-lookup"><span data-stu-id="f2343-447">Test the API</span></span>

<span data-ttu-id="f2343-448">Die Projektvorlage erstellt eine `values`-API.</span><span class="sxs-lookup"><span data-stu-id="f2343-448">The project template creates a `values` API.</span></span> <span data-ttu-id="f2343-449">Rufen Sie in einem Browser die `Get`-Methode zum Testen der App auf.</span><span class="sxs-lookup"><span data-stu-id="f2343-449">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2343-450">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2343-450">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f2343-451">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="f2343-451">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="f2343-452">Visual Studio startet einen Browser und navigiert zu `https://localhost:<port>/api/values`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="f2343-452">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="f2343-453">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem IIS Express-Zertifikat vertrauen, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="f2343-453">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="f2343-454">Wählen Sie im folgenden Dialogfeld **Sicherheitswarnung** die Option **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="f2343-454">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f2343-455">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f2343-455">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f2343-456">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="f2343-456">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="f2343-457">Navigieren Sie in einem Browser zur folgenden URL: `https://localhost:5001/api/values`.</span><span class="sxs-lookup"><span data-stu-id="f2343-457">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f2343-458">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="f2343-458">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f2343-459">Wählen Sie **Ausführen** > **Debugging starten** aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="f2343-459">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="f2343-460">Visual Studio für Mac startet einen Browser und navigiert zu `https://localhost:<port>`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="f2343-460">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="f2343-461">Der Fehler „HTTP 404: Nicht gefunden“ wird zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="f2343-461">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="f2343-462">Fügen Sie der URL `/api/values` an, d.h. ändern Sie die URL in `https://localhost:<port>/api/values`.</span><span class="sxs-lookup"><span data-stu-id="f2343-462">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="f2343-463">Die folgende JSON-Datei wird zurückgegeben:</span><span class="sxs-lookup"><span data-stu-id="f2343-463">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="f2343-464">Hinzufügen einer Modellklasse</span><span class="sxs-lookup"><span data-stu-id="f2343-464">Add a model class</span></span>

<span data-ttu-id="f2343-465">Ein *Modell* ist eine Gruppe von Klassen, die die Daten darstellen, die die App verwaltet.</span><span class="sxs-lookup"><span data-stu-id="f2343-465">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="f2343-466">Das Modell für diese App ist eine einzelne `TodoItem`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="f2343-466">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2343-467">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2343-467">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f2343-468">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="f2343-468">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="f2343-469">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="f2343-469">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="f2343-470">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="f2343-470">Name the folder *Models*.</span></span>

* <span data-ttu-id="f2343-471">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="f2343-471">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="f2343-472">Nennen Sie die Klasse *TodoItem*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="f2343-472">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="f2343-473">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="f2343-473">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f2343-474">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f2343-474">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f2343-475">Fügen Sie einen Ordner namens *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="f2343-475">Add a folder named *Models*.</span></span>

* <span data-ttu-id="f2343-476">Fügen Sie dem Ordner *Modelle* mit dem folgenden Code eine `TodoItem`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="f2343-476">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f2343-477">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="f2343-477">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f2343-478">Klicken Sie mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="f2343-478">Right-click the project.</span></span> <span data-ttu-id="f2343-479">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="f2343-479">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="f2343-480">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="f2343-480">Name the folder *Models*.</span></span>

  ![Neuer Ordner](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="f2343-482">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Neue Datei** > **Allgemein** > **Leere Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="f2343-482">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="f2343-483">Nennen Sie die Klasse *TodoItem*, und klicken Sie dann auf **Neu**.</span><span class="sxs-lookup"><span data-stu-id="f2343-483">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="f2343-484">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="f2343-484">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="f2343-485">Die `Id`-Eigenschaft fungiert als eindeutiger Schlüssel in einer relationalen Datenbank.</span><span class="sxs-lookup"><span data-stu-id="f2343-485">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="f2343-486">Modellklassen können überall im Projekt platziert werden, doch gemäß der Konvention wird der Ordner *Modelle* verwendet.</span><span class="sxs-lookup"><span data-stu-id="f2343-486">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="f2343-487">Hinzufügen eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="f2343-487">Add a database context</span></span>

<span data-ttu-id="f2343-488">Der *Datenbankkontext* ist die Hauptklasse, die die Entity Framework-Funktionen für ein Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="f2343-488">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="f2343-489">Diese Klasse wird durch Ableiten von der `Microsoft.EntityFrameworkCore.DbContext`-Klasse erstellt.</span><span class="sxs-lookup"><span data-stu-id="f2343-489">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2343-490">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2343-490">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f2343-491">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="f2343-491">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="f2343-492">Nennen Sie die Klasse *TodoContext*, und klicken Sie auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="f2343-492">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f2343-493">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="f2343-493">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="f2343-494">Fügen Sie eine `TodoContext`-Klasse zum Ordner *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="f2343-494">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="f2343-495">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="f2343-495">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="f2343-496">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="f2343-496">Register the database context</span></span>

<span data-ttu-id="f2343-497">In ASP.NET Core müssen Dienste wie der Datenbankkontext mit dem Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) registriert werden.</span><span class="sxs-lookup"><span data-stu-id="f2343-497">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="f2343-498">Der Container stellt den Dienst für Controller bereit.</span><span class="sxs-lookup"><span data-stu-id="f2343-498">The container provides the service to controllers.</span></span>

<span data-ttu-id="f2343-499">Aktualisieren Sie *Startup.cs* mit dem folgenden hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="f2343-499">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="f2343-500">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="f2343-500">The preceding code:</span></span>

* <span data-ttu-id="f2343-501">Entfernt nicht benötigte `using`-Deklarationen</span><span class="sxs-lookup"><span data-stu-id="f2343-501">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="f2343-502">Fügt dem Abhängigkeitscontainer den Datenbankkontext hinzu</span><span class="sxs-lookup"><span data-stu-id="f2343-502">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="f2343-503">Gibt an, dass der Datenbankkontext eine In-Memory Database verwendet</span><span class="sxs-lookup"><span data-stu-id="f2343-503">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="f2343-504">Hinzufügen eines Controllers</span><span class="sxs-lookup"><span data-stu-id="f2343-504">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2343-505">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2343-505">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f2343-506">Klicken Sie mit der rechten Maustaste auf den Ordner *Controller*.</span><span class="sxs-lookup"><span data-stu-id="f2343-506">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="f2343-507">Wählen Sie **Hinzufügen** > **Neues Element** aus.</span><span class="sxs-lookup"><span data-stu-id="f2343-507">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="f2343-508">Wählen Sie im Dialogfeld **Neues Element hinzufügen** die Vorlage **API-Controllerklasse** aus.</span><span class="sxs-lookup"><span data-stu-id="f2343-508">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="f2343-509">Benennen Sie die Klasse *TodoController*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="f2343-509">Name the class *TodoController*, and select **Add**.</span></span>

  ![Dialogfeld „Neues Element hinzufügen“ mit Controller im Suchfeld und ausgewähltem Web-API-Controller](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f2343-511">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="f2343-511">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="f2343-512">Erstellen Sie im Ordner *Controllers* eine Klasse namens `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="f2343-512">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="f2343-513">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="f2343-513">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="f2343-514">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="f2343-514">The preceding code:</span></span>

* <span data-ttu-id="f2343-515">Definiert eine API-Controllerklasse ohne Methoden.</span><span class="sxs-lookup"><span data-stu-id="f2343-515">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="f2343-516">Markiert die Klasse mit dem [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute)-Attribut.</span><span class="sxs-lookup"><span data-stu-id="f2343-516">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="f2343-517">Dieses Attribut gibt an, dass der Controller auf Web-API-Anforderungen reagiert.</span><span class="sxs-lookup"><span data-stu-id="f2343-517">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="f2343-518">Weitere Informationen zu bestimmten Verhaltensweisen, die das Attribut ermöglicht, finden Sie unter <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="f2343-518">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="f2343-519">Verwendet die Abhängigkeitsinjektion, um den Datenbankkontext (`TodoContext`) dem Controller hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="f2343-519">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="f2343-520">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="f2343-520">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="f2343-521">Fügt der Datenbank, falls sie leer ist, ein Element mit dem Namen `Item1` hinzu.</span><span class="sxs-lookup"><span data-stu-id="f2343-521">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="f2343-522">Dieser Code befindet sich im Konstruktor. Er wird bei jeder neuen HTTP-Anforderung ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="f2343-522">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="f2343-523">Wenn Sie alle Elemente löschen, erstellt der Konstruktor beim nächsten Aufruf einer API-Methode erneut `Item1`.</span><span class="sxs-lookup"><span data-stu-id="f2343-523">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="f2343-524">So sieht es möglicherweise so aus, als sei die Löschung fehlgeschlagen, obwohl sie erfolgreich war.</span><span class="sxs-lookup"><span data-stu-id="f2343-524">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="f2343-525">Hinzufügen von GET-Methoden</span><span class="sxs-lookup"><span data-stu-id="f2343-525">Add Get methods</span></span>

<span data-ttu-id="f2343-526">Fügen Sie der Klasse `TodoController` die folgenden Methoden hinzu, um eine API bereitzustellen, die Aufgaben abruft:</span><span class="sxs-lookup"><span data-stu-id="f2343-526">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="f2343-527">Diese Methoden implementieren zwei GET-Endpunkte:</span><span class="sxs-lookup"><span data-stu-id="f2343-527">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="f2343-528">Halten Sie die App an, falls diese noch ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="f2343-528">Stop the app if it's still running.</span></span> <span data-ttu-id="f2343-529">Führen Sie diese dann noch mal aus, damit die letzten Änderungen übernommen werden.</span><span class="sxs-lookup"><span data-stu-id="f2343-529">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="f2343-530">Testen Sie die App, indem Sie die beiden Endpunkte in einem Browser aufrufen.</span><span class="sxs-lookup"><span data-stu-id="f2343-530">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="f2343-531">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f2343-531">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="f2343-532">Die folgende HTTP-Antwort wird durch den Aufruf von `GetTodoItems` erzeugt:</span><span class="sxs-lookup"><span data-stu-id="f2343-532">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="f2343-533">Routing und URL-Pfade</span><span class="sxs-lookup"><span data-stu-id="f2343-533">Routing and URL paths</span></span>

<span data-ttu-id="f2343-534">Das Attribut [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) gibt eine Methode an, die auf eine HTTP GET-Anforderung antwortet.</span><span class="sxs-lookup"><span data-stu-id="f2343-534">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="f2343-535">Der URL-Pfad für jede Methode wird wie folgt erstellt:</span><span class="sxs-lookup"><span data-stu-id="f2343-535">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="f2343-536">Beginnen Sie mit der Vorlagenzeichenfolge im `Route`-Attribut des Controllers:</span><span class="sxs-lookup"><span data-stu-id="f2343-536">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="f2343-537">Ersetzen Sie `[controller]` durch den Namen des Controllers, bei dem es sich standardmäßig um den Namen der Controller-Klasse ohne das Suffix „Controller“ handelt.</span><span class="sxs-lookup"><span data-stu-id="f2343-537">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="f2343-538">Bei diesem Beispiel ist der Klassenname des Controllers „**Todo**Controller“, d.h. der Controllername lautet „todo“.</span><span class="sxs-lookup"><span data-stu-id="f2343-538">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="f2343-539">Beim ASP.NET Core-[Routing](xref:mvc/controllers/routing) wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="f2343-539">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="f2343-540">Wenn das `[HttpGet]`-Attribut eine Routenvorlage (z. B. `[HttpGet("products")]`) hat, fügen Sie diese an den Pfad an.</span><span class="sxs-lookup"><span data-stu-id="f2343-540">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="f2343-541">In diesem Beispiel wird keine Vorlage verwendet.</span><span class="sxs-lookup"><span data-stu-id="f2343-541">This sample doesn't use a template.</span></span> <span data-ttu-id="f2343-542">Weitere Informationen finden Sie unter [Attributrouting mit Http[Verb]-Attributen](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="f2343-542">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="f2343-543">In der folgenden `GetTodoItem`-Methode ist `"{id}"` eine Platzhaltervariable für den eindeutigen Bezeichner des To-do-Elements.</span><span class="sxs-lookup"><span data-stu-id="f2343-543">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="f2343-544">Wenn `GetTodoItem` aufgerufen wird, wird der Wert von `"{id}"` in der URL der Methode in ihrem Parameter `id` bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="f2343-544">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="f2343-545">Rückgabewerte</span><span class="sxs-lookup"><span data-stu-id="f2343-545">Return values</span></span>

<span data-ttu-id="f2343-546">Der Rückgabetyp der Methoden `GetTodoItems` und `GetTodoItem` ist der [ActionResult\<T>-Typ](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="f2343-546">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="f2343-547">ASP.NET Core serialisiert automatisch das Objekt in [JSON](https://www.json.org/) und schreibt den JSON-Code in den Text der Antwortnachricht.</span><span class="sxs-lookup"><span data-stu-id="f2343-547">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="f2343-548">Der Antwortcode für diesen Rückgabetyp ist 200, vorausgesetzt, es gibt keine Ausnahmefehler.</span><span class="sxs-lookup"><span data-stu-id="f2343-548">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="f2343-549">Nicht behandelte Ausnahmen werden in 5xx-Fehler übersetzt.</span><span class="sxs-lookup"><span data-stu-id="f2343-549">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="f2343-550">`ActionResult`-Rückgabetypen können eine Vielzahl von HTTP-Statuscodes darstellen.</span><span class="sxs-lookup"><span data-stu-id="f2343-550">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="f2343-551">Beispielsweise kann `GetTodoItem` zwei verschiedene Statuswerte zurückgeben:</span><span class="sxs-lookup"><span data-stu-id="f2343-551">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="f2343-552">Wenn kein Element mit der angeforderten ID übereinstimmt, gibt die Methode einen 404-Fehlercode [Nicht gefunden](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) zurück.</span><span class="sxs-lookup"><span data-stu-id="f2343-552">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="f2343-553">Andernfalls gibt die Methode 200 mit einem JSON-Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="f2343-553">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="f2343-554">Die Rückgabe von `item` löst eine HTTP 200-Antwort aus.</span><span class="sxs-lookup"><span data-stu-id="f2343-554">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="f2343-555">Testen der GetTodoItems-Methode</span><span class="sxs-lookup"><span data-stu-id="f2343-555">Test the GetTodoItems method</span></span>

<span data-ttu-id="f2343-556">Dieses Tutorial verwendet Postman zum Testen der Web-API.</span><span class="sxs-lookup"><span data-stu-id="f2343-556">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="f2343-557">Installieren Sie [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="f2343-557">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="f2343-558">Starten Sie die Web-App.</span><span class="sxs-lookup"><span data-stu-id="f2343-558">Start the web app.</span></span>
* <span data-ttu-id="f2343-559">Starten Sie Postman.</span><span class="sxs-lookup"><span data-stu-id="f2343-559">Start Postman.</span></span>
* <span data-ttu-id="f2343-560">Deaktivieren Sie **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="f2343-560">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2343-561">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2343-561">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f2343-562">Deaktivieren Sie auf der Registerkarte **General** (Allgemein) unter File > Settings (**Datei** > **Einstellungen**) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="f2343-562">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f2343-563">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="f2343-563">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="f2343-564">Deaktivieren Sie auf der Registerkarte **General** (Allgemein) unter **Postman** > **Preferences** (Postman > Einstellungen) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="f2343-564">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="f2343-565">Wählen Sie alternativ den Schraubenschlüssel und **Settings** (Einstellungen) aus, und deaktivieren Sie dann die Überprüfung des SSL-Zertifikats.</span><span class="sxs-lookup"><span data-stu-id="f2343-565">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="f2343-566">Aktivieren Sie die Verifizierung des SSL-Zertifikats wieder, nachdem Sie den Controller getestet haben.</span><span class="sxs-lookup"><span data-stu-id="f2343-566">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="f2343-567">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="f2343-567">Create a new request.</span></span>
  * <span data-ttu-id="f2343-568">Legen Sie die HTTP-Methode auf **GET** fest.</span><span class="sxs-lookup"><span data-stu-id="f2343-568">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="f2343-569">Legen Sie die Anforderungs-URL auf `https://localhost:<port>/api/todo` fest.</span><span class="sxs-lookup"><span data-stu-id="f2343-569">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="f2343-570">Beispielsweise `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="f2343-570">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="f2343-571">Wählen Sie in Postman **Two pane view** (Ansicht mit zwei Bereichen) aus.</span><span class="sxs-lookup"><span data-stu-id="f2343-571">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="f2343-572">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="f2343-572">Select **Send**.</span></span>

![Postman mit GET-Anforderung](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="f2343-574">Hinzufügen einer Methode</span><span class="sxs-lookup"><span data-stu-id="f2343-574">Add a Create method</span></span>

<span data-ttu-id="f2343-575">Fügen Sie die folgende `PostTodoItem`-Methode in *Controllers/TodoController.cs* hinzu:</span><span class="sxs-lookup"><span data-stu-id="f2343-575">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="f2343-576">Der oben stehende Code ist eine HTTP POST-Methode, wie durch das [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute)-Attribut angegeben.</span><span class="sxs-lookup"><span data-stu-id="f2343-576">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="f2343-577">Die Methode ruft den Wert der Aufgabe aus dem Text der HTTP-Anforderung ab.</span><span class="sxs-lookup"><span data-stu-id="f2343-577">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="f2343-578">Die `CreatedAtAction`-Methode:</span><span class="sxs-lookup"><span data-stu-id="f2343-578">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="f2343-579">Gibt bei einer erfolgreichen Anforderung den Statuscode „HTTP 201“ zurück.</span><span class="sxs-lookup"><span data-stu-id="f2343-579">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="f2343-580">HTTP 201 ist die Standardantwort für eine HTTP POST-Methode, die eine neue Ressource auf dem Server erstellt.</span><span class="sxs-lookup"><span data-stu-id="f2343-580">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="f2343-581">Fügt der Antwort einen `Location`-Header hinzu.</span><span class="sxs-lookup"><span data-stu-id="f2343-581">Adds a `Location` header to the response.</span></span> <span data-ttu-id="f2343-582">Der `Location`-Header gibt den URI des neu erstellten To-Do-Elements zurück.</span><span class="sxs-lookup"><span data-stu-id="f2343-582">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="f2343-583">Weitere Informationen finden Sie unter [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="f2343-583">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="f2343-584">Verweist auf die `GetTodoItem`-Aktion zum Erstellen des URIs des `Location`-Headers.</span><span class="sxs-lookup"><span data-stu-id="f2343-584">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="f2343-585">Das `nameof`-Schlüsselwort von C# wird verwendet, um eine Hartcodierung des Aktionsnamens im `CreatedAtAction`-Aufruf zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="f2343-585">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="f2343-586">Testen der PostTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="f2343-586">Test the PostTodoItem method</span></span>

* <span data-ttu-id="f2343-587">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="f2343-587">Build the project.</span></span>
* <span data-ttu-id="f2343-588">Legen Sie in Postman die HTTP-Methode auf `POST` fest.</span><span class="sxs-lookup"><span data-stu-id="f2343-588">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="f2343-589">Wählen Sie die Registerkarte **Body** (Text) aus.</span><span class="sxs-lookup"><span data-stu-id="f2343-589">Select the **Body** tab.</span></span>
* <span data-ttu-id="f2343-590">Wählen Sie das Optionsfeld **raw** (Unformatiert) aus.</span><span class="sxs-lookup"><span data-stu-id="f2343-590">Select the **raw** radio button.</span></span>
* <span data-ttu-id="f2343-591">Legen Sie den Typ auf **JSON (application/json)** fest.</span><span class="sxs-lookup"><span data-stu-id="f2343-591">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="f2343-592">Geben Sie für die Aufgabe den Anforderungstext im JSON-Format ein:</span><span class="sxs-lookup"><span data-stu-id="f2343-592">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="f2343-593">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="f2343-593">Select **Send**.</span></span>

  ![Postman mit erstellter Anforderung](first-web-api/_static/create.png)

  <span data-ttu-id="f2343-595">Wenn Sie die Fehlermeldung „405: Methode nicht zulässig“ erhalten, wurde das Projekt wahrscheinlich nicht kompiliert, nachdem die Methode `PostTodoItem` hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="f2343-595">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="f2343-596">Testen des Adressheader-URIs</span><span class="sxs-lookup"><span data-stu-id="f2343-596">Test the location header URI</span></span>

* <span data-ttu-id="f2343-597">Wählen Sie auf der Registerkarte **Header** (Header) den Bereich **Response** (Antwort) aus.</span><span class="sxs-lookup"><span data-stu-id="f2343-597">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="f2343-598">Kopieren Sie den den Headerwert von **Location** (Speicherort):</span><span class="sxs-lookup"><span data-stu-id="f2343-598">Copy the **Location** header value:</span></span>

  ![Registerkarte „Header“ in der Postman-Konsole](first-web-api/_static/pmc2.png)

* <span data-ttu-id="f2343-600">Legen Sie die Methode auf „GET“ fest.</span><span class="sxs-lookup"><span data-stu-id="f2343-600">Set the method to GET.</span></span>
* <span data-ttu-id="f2343-601">Fügen Sie den URI (z. B. `https://localhost:5001/api/Todo/2`) ein.</span><span class="sxs-lookup"><span data-stu-id="f2343-601">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="f2343-602">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="f2343-602">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="f2343-603">Hinzufügen einer PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="f2343-603">Add a PutTodoItem method</span></span>

<span data-ttu-id="f2343-604">Fügen Sie die folgende `PutTodoItem`-Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="f2343-604">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="f2343-605">`PutTodoItem` ähnelt `PostTodoItem`, verwendet allerdings HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="f2343-605">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="f2343-606">Die Antwort ist [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="f2343-606">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="f2343-607">Gemäß der HTTP-Spezifikation erfordert eine PUT-Anforderung, dass der Client die gesamte aktualisierte Entität (nicht nur die Änderungen) sendet.</span><span class="sxs-lookup"><span data-stu-id="f2343-607">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="f2343-608">Verwenden Sie [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute), um Teilupdates zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="f2343-608">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="f2343-609">Wenn beim Aufrufen von `PutTodoItem` ein Fehler zurückgegeben wird, rufen Sie `GET` auf, um sicherzustellen, dass die Datenbank ein Element enthält.</span><span class="sxs-lookup"><span data-stu-id="f2343-609">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="f2343-610">Testen der PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="f2343-610">Test the PutTodoItem method</span></span>

<span data-ttu-id="f2343-611">In diesem Beispiel wird eine In-Memory-Datenbank verwendet, die jedes Mal initialisiert werden muss, wenn die App gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="f2343-611">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="f2343-612">Es muss ein Element in der Datenbank vorhanden sein, bevor Sie einen PUT-Aufruf durchführen.</span><span class="sxs-lookup"><span data-stu-id="f2343-612">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="f2343-613">Rufen Sie vor einem PUT-Aufruf GET auf, um sicherzustellen, dass ein Element in der Datenbank vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="f2343-613">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="f2343-614">Aktualisieren Sie die Aufgabe mit der ID = 1, und setzen Sie ihren Namen auf „feed fish“:</span><span class="sxs-lookup"><span data-stu-id="f2343-614">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="f2343-615">Die folgende Abbildung zeigt das Postman-Update:</span><span class="sxs-lookup"><span data-stu-id="f2343-615">The following image shows the Postman update:</span></span>

![Postman-Konsole mit der Antwort „204 (Kein Inhalt)“](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="f2343-617">Hinzufügen einer DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="f2343-617">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="f2343-618">Fügen Sie die folgende `DeleteTodoItem`-Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="f2343-618">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="f2343-619">Die `DeleteTodoItem`-Antwort lautet [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="f2343-619">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="f2343-620">Testen der DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="f2343-620">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="f2343-621">So löschen Sie mit Postman eine Aufgabe</span><span class="sxs-lookup"><span data-stu-id="f2343-621">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="f2343-622">Legen Sie die Methode auf `DELETE` fest.</span><span class="sxs-lookup"><span data-stu-id="f2343-622">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="f2343-623">Legen Sie den URI des zu löschenden Objekts fest, z. B. `https://localhost:5001/api/todo/1`.</span><span class="sxs-lookup"><span data-stu-id="f2343-623">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="f2343-624">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="f2343-624">Select **Send**.</span></span>

<span data-ttu-id="f2343-625">Sie können in der Beispiel-App alle Elemente löschen.</span><span class="sxs-lookup"><span data-stu-id="f2343-625">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="f2343-626">Sobald das letzte Element gelöscht wurde, wird allerdings beim nächsten API-Aufruf vom Modellklassenkonstruktor ein neues erstellt.</span><span class="sxs-lookup"><span data-stu-id="f2343-626">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="f2343-627">Aufrufen der Web-API mit JavaScript</span><span class="sxs-lookup"><span data-stu-id="f2343-627">Call the web API with JavaScript</span></span>

<span data-ttu-id="f2343-628">In diesem Abschnitt wird eine HTML-Seite hinzugefügt, die mithilfe von JavaScript die Web-API aufruft.</span><span class="sxs-lookup"><span data-stu-id="f2343-628">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="f2343-629">jQuery initiiert die Anforderung.</span><span class="sxs-lookup"><span data-stu-id="f2343-629">jQuery initiates the request.</span></span> <span data-ttu-id="f2343-630">JavaScript aktualisiert die Seite mit den Details aus der Antwort der Web-API.</span><span class="sxs-lookup"><span data-stu-id="f2343-630">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="f2343-631">Konfigurieren Sie die App so, dass sie [statische Dateien bereitstellt](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_), und [aktivieren Sie die Standarddateizuordnung](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_), indem Sie *startup.cs* mit dem unten markierten Code aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="f2343-631">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="f2343-632">Erstellen Sie im Projektverzeichnis den Ordner *wwwwroot*.</span><span class="sxs-lookup"><span data-stu-id="f2343-632">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="f2343-633">Fügen Sie dem Verzeichnis *wwwroot* eine HTML-Datei namens *index.html* hinzu.</span><span class="sxs-lookup"><span data-stu-id="f2343-633">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="f2343-634">Ersetzen Sie den Inhalt durch folgendes Markup:</span><span class="sxs-lookup"><span data-stu-id="f2343-634">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="f2343-635">Fügen Sie dem Verzeichnis *wwwroot* eine JavaScript-Datei namens *site.js* hinzu.</span><span class="sxs-lookup"><span data-stu-id="f2343-635">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="f2343-636">Ersetzen Sie den Inhalt durch folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="f2343-636">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="f2343-637">Möglicherweise ist eine Änderung an den Starteinstellungen des ASP.NET Core-Projekts erforderlich, um die HTML-Seite lokal zu testen:</span><span class="sxs-lookup"><span data-stu-id="f2343-637">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="f2343-638">Öffnen Sie *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="f2343-638">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="f2343-639">Entfernen Sie die `launchUrl`-Eigenschaft, um zu erzwingen, dass die App mit *index.html* als Startseite geöffnet wird. Dies ist die Standarddatei des Projekts.</span><span class="sxs-lookup"><span data-stu-id="f2343-639">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="f2343-640">Dieses Beispiel ruft alle CRUD-Methoden der Web-API auf.</span><span class="sxs-lookup"><span data-stu-id="f2343-640">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="f2343-641">Im Folgenden werden die API-Aufrufe erläutert.</span><span class="sxs-lookup"><span data-stu-id="f2343-641">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="f2343-642">Abrufen einer Liste von To-Do-Elementen</span><span class="sxs-lookup"><span data-stu-id="f2343-642">Get a list of to-do items</span></span>

<span data-ttu-id="f2343-643">jQuery sendet eine HTTP GET-Anforderung an die Web-API, die JSON-Code mit einem Array aus To-Do-Elementen zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="f2343-643">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="f2343-644">Die Rückruffunktion `success` wird aufgerufen, wenn die Anforderung erfolgreich ist.</span><span class="sxs-lookup"><span data-stu-id="f2343-644">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="f2343-645">Im Rückruf wird DOM mit den To-Do-Informationen aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="f2343-645">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="f2343-646">Hinzufügen eines To-Do-Elements</span><span class="sxs-lookup"><span data-stu-id="f2343-646">Add a to-do item</span></span>

<span data-ttu-id="f2343-647">jQuery sendet eine HTTP POST-Anforderung mit dem To-Do-Element im Anforderungstext.</span><span class="sxs-lookup"><span data-stu-id="f2343-647">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="f2343-648">Die Optionen `accepts` und `contentType` werden auf `application/json` festgelegt, um den gesendeten und empfangenen Medientyp anzugeben.</span><span class="sxs-lookup"><span data-stu-id="f2343-648">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="f2343-649">Die Aufgabe wird mit [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) in JSON konvertiert.</span><span class="sxs-lookup"><span data-stu-id="f2343-649">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="f2343-650">Wenn die API den Statuscode „Erfolgreich“ zurückgibt, wird die Funktion `getData` aufgerufen, um die HTML-Tabelle zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="f2343-650">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="f2343-651">Aktualisieren eines To-Do-Elements</span><span class="sxs-lookup"><span data-stu-id="f2343-651">Update a to-do item</span></span>

<span data-ttu-id="f2343-652">Das Aktualisieren einer Aufgabe ist vergleichbar mit dem Hinzufügen einer Aufgabe.</span><span class="sxs-lookup"><span data-stu-id="f2343-652">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="f2343-653">Die `url` ändert sich, um den eindeutigen Bezeichner des Elements hinzuzufügen. Der `type` lautet `PUT`.</span><span class="sxs-lookup"><span data-stu-id="f2343-653">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="f2343-654">Löschen eines To-Do-Elements</span><span class="sxs-lookup"><span data-stu-id="f2343-654">Delete a to-do item</span></span>

<span data-ttu-id="f2343-655">Eine Aufgabe wird folgendermaßen gelöscht: im AJAX-Aufruf wird `type` auf `DELETE` festgelegt, und der eindeutige Bezeichner des Elements wird in der URL angegeben.</span><span class="sxs-lookup"><span data-stu-id="f2343-655">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="f2343-656">Hinzufügen der Authentifizierungsunterstützung zu einer Web-API</span><span class="sxs-lookup"><span data-stu-id="f2343-656">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="f2343-657">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="f2343-657">Additional resources</span></span>

<span data-ttu-id="f2343-658">[Sie können den Beispielcode für dieses Tutorial anzeigen oder herunterladen.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)</span><span class="sxs-lookup"><span data-stu-id="f2343-658">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="f2343-659">[Informationen zum Herunterladen](xref:index#how-to-download-a-sample) finden Sie hier.</span><span class="sxs-lookup"><span data-stu-id="f2343-659">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="f2343-660">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="f2343-660">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="f2343-661">Dieses Tutorial auf YouTube</span><span class="sxs-lookup"><span data-stu-id="f2343-661">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)

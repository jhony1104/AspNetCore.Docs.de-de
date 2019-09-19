---
title: 'Tutorial: Erstellen einer Web-API mit ASP.NET Core'
author: rick-anderson
description: Informationen zum Erstellen einer Web-API mit ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 08/27/2019
uid: tutorials/first-web-api
ms.openlocfilehash: 1cc4fffc50978a3a958a96e1eb250cb85a8d2879
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082067"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="4e8cc-103">Tutorial: Erstellen einer Web-API mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4e8cc-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="4e8cc-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="4e8cc-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="4e8cc-105">In diesem Tutorial lernen Sie die Grundlagen der Erstellung einer Web-API mit ASP.NET Core kennen.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4e8cc-106">In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4e8cc-107">Erstellen eines Web-API-Projekts</span><span class="sxs-lookup"><span data-stu-id="4e8cc-107">Create a web API project.</span></span>
> * <span data-ttu-id="4e8cc-108">Hinzufügen einer Modellklasse und eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="4e8cc-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="4e8cc-109">Erstellen eines Controllergerüsts mit CRUD-Methoden</span><span class="sxs-lookup"><span data-stu-id="4e8cc-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="4e8cc-110">Konfigurieren des Routings, der URL-Pfade und der Rückgabewerte</span><span class="sxs-lookup"><span data-stu-id="4e8cc-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="4e8cc-111">Aufrufen der Web-API mit Postman</span><span class="sxs-lookup"><span data-stu-id="4e8cc-111">Call the web API with Postman.</span></span>

<span data-ttu-id="4e8cc-112">Abschließend steht Ihnen eine Web-API zur Verfügung, die in einer relationalen Datenbank gespeicherte To-do-Elemente verwalten kann.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="4e8cc-113">Übersicht</span><span class="sxs-lookup"><span data-stu-id="4e8cc-113">Overview</span></span>

<span data-ttu-id="4e8cc-114">In diesem Tutorial wird die folgende API erstellt:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="4e8cc-115">API</span><span class="sxs-lookup"><span data-stu-id="4e8cc-115">API</span></span> | <span data-ttu-id="4e8cc-116">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="4e8cc-116">Description</span></span> | <span data-ttu-id="4e8cc-117">Anforderungstext</span><span class="sxs-lookup"><span data-stu-id="4e8cc-117">Request body</span></span> | <span data-ttu-id="4e8cc-118">Antworttext</span><span class="sxs-lookup"><span data-stu-id="4e8cc-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="4e8cc-119">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="4e8cc-119">GET /api/TodoItems</span></span> | <span data-ttu-id="4e8cc-120">Alle To-do-Elemente abrufen</span><span class="sxs-lookup"><span data-stu-id="4e8cc-120">Get all to-do items</span></span> | <span data-ttu-id="4e8cc-121">Keine</span><span class="sxs-lookup"><span data-stu-id="4e8cc-121">None</span></span> | <span data-ttu-id="4e8cc-122">Array von To-do-Elementen</span><span class="sxs-lookup"><span data-stu-id="4e8cc-122">Array of to-do items</span></span>|
|<span data-ttu-id="4e8cc-123">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="4e8cc-123">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="4e8cc-124">Ein Element nach ID abrufen</span><span class="sxs-lookup"><span data-stu-id="4e8cc-124">Get an item by ID</span></span> | <span data-ttu-id="4e8cc-125">Keine</span><span class="sxs-lookup"><span data-stu-id="4e8cc-125">None</span></span> | <span data-ttu-id="4e8cc-126">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="4e8cc-126">To-do item</span></span>|
|<span data-ttu-id="4e8cc-127">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="4e8cc-127">POST /api/TodoItems</span></span> | <span data-ttu-id="4e8cc-128">Neues Element hinzufügen</span><span class="sxs-lookup"><span data-stu-id="4e8cc-128">Add a new item</span></span> | <span data-ttu-id="4e8cc-129">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="4e8cc-129">To-do item</span></span> | <span data-ttu-id="4e8cc-130">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="4e8cc-130">To-do item</span></span> |
|<span data-ttu-id="4e8cc-131">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="4e8cc-131">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="4e8cc-132">Vorhandenes Element aktualisieren &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4e8cc-132">Update an existing item &nbsp;</span></span> | <span data-ttu-id="4e8cc-133">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="4e8cc-133">To-do item</span></span> | <span data-ttu-id="4e8cc-134">Keine</span><span class="sxs-lookup"><span data-stu-id="4e8cc-134">None</span></span> |
|<span data-ttu-id="4e8cc-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4e8cc-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="4e8cc-136">Löschen eines Elements &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4e8cc-136">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="4e8cc-137">Keine</span><span class="sxs-lookup"><span data-stu-id="4e8cc-137">None</span></span> | <span data-ttu-id="4e8cc-138">Keine</span><span class="sxs-lookup"><span data-stu-id="4e8cc-138">None</span></span>|

<span data-ttu-id="4e8cc-139">Das folgende Diagramm zeigt den Entwurf der App.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-139">The following diagram shows the design of the app.</span></span>

![Der Client ist das Feld ganz links.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="4e8cc-145">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="4e8cc-145">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4e8cc-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4e8cc-146">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4e8cc-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4e8cc-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4e8cc-148">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4e8cc-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="4e8cc-149">Erstellen eines Webprojekts</span><span class="sxs-lookup"><span data-stu-id="4e8cc-149">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4e8cc-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4e8cc-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4e8cc-151">Klicken Sie im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="4e8cc-152">Wählen Sie die Vorlage **ASP.NET Core-Webanwendung** aus, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-152">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="4e8cc-153">Geben Sie dem Projekt den Namen *TodoApi*, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-153">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="4e8cc-154">Vergewissern Sie sich, dass im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Optionen **.NET Core** und **ASP.NET Core 3.0** ausgewählt sind.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-154">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span> <span data-ttu-id="4e8cc-155">Wählen Sie die Vorlage **API** aus, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-155">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="4e8cc-156">Wählen Sie **nicht** **Enable Docker Support** (Docker-Unterstützung aktivieren) aus.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-156">**Don't** select **Enable Docker Support**.</span></span>

![VS-Dialogfeld „Neues Projekt“](first-web-api/_static/vs3.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4e8cc-158">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4e8cc-158">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4e8cc-159">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="4e8cc-159">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="4e8cc-160">Wechseln Sie vom Verzeichnis (`cd`) zum Ordner, der den Projektordner enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-160">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="4e8cc-161">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-161">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoAPI
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.0.0-*
   dotnet add package Microsoft.EntityFrameworkCore.InMemory --version 3.0.0-*
   code -r ../TodoApi
   ```

* <span data-ttu-id="4e8cc-162">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem Projekt die erforderlichen Elemente hinzufügen möchten, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-162">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="4e8cc-163">Die obenstehenden Befehle haben folgende Konsequenzen:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-163">The preceding commands:</span></span>

  * <span data-ttu-id="4e8cc-164">Ein neues Web-API-Projekt wird erstellt und in Visual Studio Code geöffnet.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-164">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="4e8cc-165">Die NuGet-Pakete, die im nächsten Abschnitt erforderlich sind, werden hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-165">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4e8cc-166">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4e8cc-166">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4e8cc-167">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-167">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="4e8cc-169">Klicken Sie auf **.NET Core** > **App** > **API** > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-169">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![Dialogfeld „Neue Projektmappe“ in macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="4e8cc-171">Wählen Sie im Dialogfeld **Configure your new ASP.NET Core Web API** (Neue ASP.NET Core-Web-API konfigurieren) für das **Zielframework** den Eintrag \* *.NET Core 3.0* aus.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-171">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.0*.</span></span>

* <span data-ttu-id="4e8cc-172">Geben Sie für **Projektname** *TodoApi* ein, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-172">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogfeld „Konfiguration“](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="4e8cc-174">Öffnen Sie im Projektordner ein Befehlsterminal, und führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-174">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.0.0-*
   dotnet add package Microsoft.EntityFrameworkCore.InMemory --version 3.0.0-*
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="4e8cc-175">Testen der API</span><span class="sxs-lookup"><span data-stu-id="4e8cc-175">Test the API</span></span>

<span data-ttu-id="4e8cc-176">Die Projektvorlage erstellt eine `WeatherForecast`-API.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-176">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="4e8cc-177">Rufen Sie in einem Browser die `Get`-Methode zum Testen der App auf.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-177">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4e8cc-178">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4e8cc-178">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4e8cc-179">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-179">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="4e8cc-180">Visual Studio startet einen Browser und navigiert zu `https://localhost:<port>/WeatherForecast`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-180">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="4e8cc-181">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem IIS Express-Zertifikat vertrauen, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-181">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="4e8cc-182">Wählen Sie im folgenden Dialogfeld **Sicherheitswarnung** die Option **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-182">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4e8cc-183">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4e8cc-183">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4e8cc-184">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-184">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="4e8cc-185">Rufen Sie in einem Browser die folgende URL auf: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span><span class="sxs-lookup"><span data-stu-id="4e8cc-185">In a browser, go to following URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4e8cc-186">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4e8cc-186">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4e8cc-187">Wählen Sie **Run** > **Start Debugging** (Ausführen > Debugging starten) aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-187">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="4e8cc-188">Visual Studio für Mac startet einen Browser und navigiert zu `https://localhost:<port>`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-188">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="4e8cc-189">Der Fehler „HTTP 404: Nicht gefunden“ wird zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-189">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="4e8cc-190">Fügen Sie der URL `/WeatherForecast` an, d.h. ändern Sie die URL in `https://localhost:<port>/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-190">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="4e8cc-191">Der zurückgegebene JSON-Code sieht in etwa wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-191">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="4e8cc-192">Hinzufügen einer Modellklasse</span><span class="sxs-lookup"><span data-stu-id="4e8cc-192">Add a model class</span></span>

<span data-ttu-id="4e8cc-193">Ein *Modell* ist eine Gruppe von Klassen, die die Daten darstellen, die die App verwaltet.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-193">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="4e8cc-194">Das Modell für diese App ist eine einzelne `TodoItem`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-194">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4e8cc-195">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4e8cc-195">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4e8cc-196">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-196">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="4e8cc-197">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-197">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="4e8cc-198">Geben Sie dem Ordner den Namen *Models*.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-198">Name the folder *Models*.</span></span>

* <span data-ttu-id="4e8cc-199">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-199">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="4e8cc-200">Nennen Sie die Klasse *TodoItem*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-200">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="4e8cc-201">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-201">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4e8cc-202">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4e8cc-202">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4e8cc-203">Fügen Sie einen Ordner namens *Models* hinzu.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-203">Add a folder named *Models*.</span></span>

* <span data-ttu-id="4e8cc-204">Fügen Sie dem Ordner *Modelle* mit dem folgenden Code eine `TodoItem`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-204">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4e8cc-205">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4e8cc-205">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4e8cc-206">Klicken Sie mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-206">Right-click the project.</span></span> <span data-ttu-id="4e8cc-207">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-207">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="4e8cc-208">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-208">Name the folder *Models*.</span></span>

  ![Neuer Ordner](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="4e8cc-210">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und klicken Sie auf **Hinzufügen** > **Neue Datei** > **Allgemein** > **Leere Klasse**.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-210">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="4e8cc-211">Nennen Sie die Klasse *TodoItem*, und klicken Sie dann auf **Neu**.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-211">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="4e8cc-212">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-212">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="4e8cc-213">Die `Id`-Eigenschaft fungiert als eindeutiger Schlüssel in einer relationalen Datenbank.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-213">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="4e8cc-214">Modellklassen können überall im Projekt platziert werden, doch gemäß der Konvention wird der Ordner *Modelle* verwendet.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-214">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="4e8cc-215">Hinzufügen eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="4e8cc-215">Add a database context</span></span>

<span data-ttu-id="4e8cc-216">Der *Datenbankkontext* ist die Hauptklasse, die die Entity Framework-Funktionen für ein Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-216">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="4e8cc-217">Diese Klasse wird durch Ableiten von der `Microsoft.EntityFrameworkCore.DbContext`-Klasse erstellt.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-217">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4e8cc-218">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4e8cc-218">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="4e8cc-219">Hinzufügen von Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="4e8cc-219">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="4e8cc-220">Klicken Sie im Menü **Extras** auf **NuGet-Paket-Manager > NuGet-Pakete für Projektmappe verwalten...** .</span><span class="sxs-lookup"><span data-stu-id="4e8cc-220">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="4e8cc-221">Aktivieren Sie das Kontrollkästchen **Vorabversion einbeziehen**.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-221">Select the **Include prerelease** checkbox.</span></span>
* <span data-ttu-id="4e8cc-222">Klicken Sie auf die Registerkarte **Durchsuchen**, und geben Sie dann **Microsoft.EntityFrameworkCore.SqlServer** in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-222">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="4e8cc-223">Wählen Sie im linken Bereich **Microsoft.EntityFrameworkCore.SqlServer V3.0.0-preview** aus.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-223">Select  **Microsoft.EntityFrameworkCore.SqlServer V3.0.0-preview** in the left pane.</span></span>
* <span data-ttu-id="4e8cc-224">Aktivieren Sie das Kontrollkästchen **Projekt** im rechten Bereich, und klicken Sie dann auf **Installieren**.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-224">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="4e8cc-225">Fügen Sie mithilfe der obigen Schritte das `Microsoft.EntityFrameworkCore.InMemory`-NuGet-Paket hinzu.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-225">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![NuGet-Paket-Manager](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="4e8cc-227">Hinzufügen des TodoContext-Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="4e8cc-227">Add the TodoContext database context</span></span>

* <span data-ttu-id="4e8cc-228">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-228">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="4e8cc-229">Nennen Sie die Klasse *TodoContext*, und klicken Sie auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-229">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="4e8cc-230">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4e8cc-230">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="4e8cc-231">Fügen Sie eine `TodoContext`-Klasse zum Ordner *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-231">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="4e8cc-232">Geben Sie den folgenden Code ein:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-232">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="4e8cc-233">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="4e8cc-233">Register the database context</span></span>

<span data-ttu-id="4e8cc-234">In ASP.NET Core müssen Dienste wie der Datenbankkontext mit dem Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) registriert werden.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-234">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="4e8cc-235">Der Container stellt den Dienst für Controller bereit.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-235">The container provides the service to controllers.</span></span>

<span data-ttu-id="4e8cc-236">Aktualisieren Sie *Startup.cs* mit dem folgenden hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-236">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="4e8cc-237">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-237">The preceding code:</span></span>

* <span data-ttu-id="4e8cc-238">Entfernt nicht benötigte `using`-Deklarationen</span><span class="sxs-lookup"><span data-stu-id="4e8cc-238">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="4e8cc-239">Fügt dem Abhängigkeitscontainer den Datenbankkontext hinzu</span><span class="sxs-lookup"><span data-stu-id="4e8cc-239">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="4e8cc-240">Gibt an, dass der Datenbankkontext eine In-Memory Database verwendet</span><span class="sxs-lookup"><span data-stu-id="4e8cc-240">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="4e8cc-241">Erstellen eines Controllergerüsts</span><span class="sxs-lookup"><span data-stu-id="4e8cc-241">Scaffold a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4e8cc-242">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4e8cc-242">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4e8cc-243">Klicken Sie mit der rechten Maustaste auf den Ordner *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-243">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="4e8cc-244">Klicken Sie auf **Hinzufügen** > **New Scaffolded Item** (Neues Gerüstelement).</span><span class="sxs-lookup"><span data-stu-id="4e8cc-244">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="4e8cc-245">Klicken Sie auf **API-Controller mit Aktionen unter Verwendung von Entity Framework** und dann auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-245">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="4e8cc-246">Führen Sie im Dialogfeld **API-Controller mit Aktionen unter Verwendung von Entity Framework** folgende Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-246">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="4e8cc-247">Wählen Sie in der **Modellklasse** das Element **TodoItem (TodoAPI.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-247">Select **TodoItem (TodoAPI.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="4e8cc-248">Wählen Sie in der **Datenkontextklasse** das Element **TodoContext (TodoAPI.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-248">Select **TodoContext (TodoAPI.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="4e8cc-249">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-249">Select **Add**</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="4e8cc-250">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4e8cc-250">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="4e8cc-251">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-251">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.0.0-*
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext  -outDir Controllers
```

<span data-ttu-id="4e8cc-252">Die obenstehenden Befehle haben folgende Konsequenzen:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-252">The preceding commands:</span></span>

* <span data-ttu-id="4e8cc-253">Die NuGet-Pakete für den Gerüstbau werden hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-253">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="4e8cc-254">Die Gerüstbau-Engine (`dotnet-aspnet-codegenerator`) wird installiert.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-254">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="4e8cc-255">Das Gerüst für `TodoItemsController` wird erstellt.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-255">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="4e8cc-256">Der generierte Code hat folgende Auswirkungen:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-256">The generated code:</span></span>

* <span data-ttu-id="4e8cc-257">Er definiert eine API-Controllerklasse ohne Methoden.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-257">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="4e8cc-258">Ergänzt die Klasse mit dem Attribut [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute).</span><span class="sxs-lookup"><span data-stu-id="4e8cc-258">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="4e8cc-259">Dieses Attribut gibt an, dass der Controller auf Web-API-Anforderungen reagiert.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-259">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="4e8cc-260">Weitere Informationen zu bestimmten Verhaltensweisen, die das Attribut ermöglicht, finden Sie unter <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-260">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="4e8cc-261">Verwendet die Abhängigkeitsinjektion, um den Datenbankkontext (`TodoContext`) dem Controller hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-261">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="4e8cc-262">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-262">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="4e8cc-263">Überblick über die PostTodoItem-Erstellungsmethode</span><span class="sxs-lookup"><span data-stu-id="4e8cc-263">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="4e8cc-264">Ersetzen Sie die Rückgabeanweisung in `PostTodoItem` durch eine Anweisung mit dem [nameof](/dotnet/csharp/language-reference/operators/nameof)-Operator:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-264">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="4e8cc-265">Der vorherige Code ist eine HTTP POST-Methode, die durch das Attribut [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-265">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="4e8cc-266">Die Methode ruft den Wert der Aufgabe aus dem Text der HTTP-Anforderung ab.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-266">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="4e8cc-267">Die <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>-Methode:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-267">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="4e8cc-268">Sie gibt den HTTP-Statuscode 201 zurück, wenn der Vorgang erfolgreich ist.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-268">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="4e8cc-269">HTTP 201 ist die Standardantwort für eine HTTP POST-Methode, die eine neue Ressource auf dem Server erstellt.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-269">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="4e8cc-270">Sie fügt der Antwort einen [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location)-Header hinzu.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-270">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="4e8cc-271">Der `Location`-Header legt den [URI](https://developer.mozilla.org/docs/Glossary/URI) des neu erstellten To-do-Elements fest.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-271">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="4e8cc-272">Weitere Informationen finden Sie unter [10.2.2 201 Erstellt](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="4e8cc-272">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="4e8cc-273">Verweist auf die `GetTodoItem`-Aktion zum Erstellen des URIs des `Location`-Headers.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-273">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="4e8cc-274">Das `nameof`-Schlüsselwort von C# wird verwendet, um eine Hartcodierung des Aktionsnamens im `CreatedAtAction`-Aufruf zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-274">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="4e8cc-275">Installieren von Postman</span><span class="sxs-lookup"><span data-stu-id="4e8cc-275">Install Postman</span></span>

<span data-ttu-id="4e8cc-276">Dieses Tutorial verwendet Postman zum Testen der Web-API.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-276">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="4e8cc-277">Installieren Sie [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="4e8cc-277">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="4e8cc-278">Starten Sie die Web-App.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-278">Start the web app.</span></span>
* <span data-ttu-id="4e8cc-279">Starten Sie Postman.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-279">Start Postman.</span></span>
* <span data-ttu-id="4e8cc-280">Deaktivieren Sie **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="4e8cc-280">Disable **SSL certificate verification**</span></span>
* <span data-ttu-id="4e8cc-281">Deaktivieren Sie auf der Registerkarte \**General* (Allgemein) unter **File > Settings** (Datei > Einstellungen) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="4e8cc-281">From  **File > Settings** (\**General* tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="4e8cc-282">Aktivieren Sie die Verifizierung des SSL-Zertifikats wieder, nachdem Sie den Controller getestet haben.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-282">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="4e8cc-283">Testen von PostTodoItem mit Postman</span><span class="sxs-lookup"><span data-stu-id="4e8cc-283">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="4e8cc-284">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-284">Create a new request.</span></span>
* <span data-ttu-id="4e8cc-285">Legen Sie als HTTP-Methode `POST` fest.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-285">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="4e8cc-286">Wählen Sie die Registerkarte **Body** (Text) aus.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-286">Select the **Body** tab.</span></span>
* <span data-ttu-id="4e8cc-287">Wählen Sie das Optionsfeld **raw** (Unformatiert) aus.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-287">Select the **raw** radio button.</span></span>
* <span data-ttu-id="4e8cc-288">Legen Sie den Typ auf **JSON (application/json)** fest.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-288">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="4e8cc-289">Geben Sie für die Aufgabe den Anforderungstext im JSON-Format ein:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-289">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="4e8cc-290">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-290">Select **Send**.</span></span>

  ![Postman mit erstellter Anforderung](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="4e8cc-292">Testen des Adressheader-URIs</span><span class="sxs-lookup"><span data-stu-id="4e8cc-292">Test the location header URI</span></span>

* <span data-ttu-id="4e8cc-293">Wählen Sie auf der Registerkarte **Header** (Header) den Bereich **Response** (Antwort) aus.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-293">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="4e8cc-294">Kopieren Sie den den Headerwert von **Location** (Speicherort):</span><span class="sxs-lookup"><span data-stu-id="4e8cc-294">Copy the **Location** header value:</span></span>

  ![Registerkarte „Header“ in der Postman-Konsole](first-web-api/_static/3/create.png)

* <span data-ttu-id="4e8cc-296">Legen Sie die Methode auf „GET“ fest.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-296">Set the method to GET.</span></span>
* <span data-ttu-id="4e8cc-297">Fügen Sie den URI (z. B. `https://localhost:5001/api/TodoItems/1`) ein.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-297">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`)</span></span>
* <span data-ttu-id="4e8cc-298">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-298">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="4e8cc-299">Überblick über die GET-Methoden</span><span class="sxs-lookup"><span data-stu-id="4e8cc-299">Examine the GET methods</span></span>

<span data-ttu-id="4e8cc-300">Diese Methoden implementieren zwei GET-Endpunkte:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-300">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="4e8cc-301">Testen Sie die App, indem Sie die beiden Endpunkte in einem Browser oder über Postman aufrufen.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-301">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="4e8cc-302">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-302">For example:</span></span>

* [https://localhost:5001/api/TodoItems](https://localhost:5001/api/TodoItems)
* [https://localhost:5001/api/TodoItems/1](https://localhost:5001/api/TodoItems/1)

<span data-ttu-id="4e8cc-303">Durch einen Aufruf von `GetTodoItems` wird eine Antwort gesendet, die der folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-303">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="4e8cc-304">Testen von GET mit Postman</span><span class="sxs-lookup"><span data-stu-id="4e8cc-304">Test Get with Postman</span></span>

* <span data-ttu-id="4e8cc-305">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-305">Create a new request.</span></span>
* <span data-ttu-id="4e8cc-306">Legen Sie die HTTP-Methode auf **GET** fest.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-306">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="4e8cc-307">Legen Sie die Anforderungs-URL auf `https://localhost:<port>/api/TodoItems` fest.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-307">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="4e8cc-308">Beispielsweise `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-308">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="4e8cc-309">Wählen Sie in Postman **Two pane view** (Ansicht mit zwei Bereichen) aus.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-309">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="4e8cc-310">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-310">Select **Send**.</span></span>

<span data-ttu-id="4e8cc-311">Diese App verwendet eine In-Memory-Datenbank.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-311">This app uses an in-memory database.</span></span> <span data-ttu-id="4e8cc-312">Wenn die App angehalten und dann wieder gestartet wird, werden durch die vorherige GET-Anforderung keine Daten zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-312">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="4e8cc-313">Wenn keine Daten zurückgegeben werden, senden Sie mit [POST](#post) Daten an die App.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-313">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="4e8cc-314">Routing und URL-Pfade</span><span class="sxs-lookup"><span data-stu-id="4e8cc-314">Routing and URL paths</span></span>

<span data-ttu-id="4e8cc-315">Das Attribut [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) gibt eine Methode an, die auf eine HTTP GET-Anforderung antwortet.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-315">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="4e8cc-316">Der URL-Pfad für jede Methode wird wie folgt erstellt:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-316">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="4e8cc-317">Beginnen Sie mit der Vorlagenzeichenfolge im `Route`-Attribut des Controllers:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-317">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="4e8cc-318">Ersetzen Sie `[controller]` durch den Namen des Controllers, bei dem es sich standardmäßig um den Namen der Controller-Klasse ohne das Suffix „Controller“ handelt.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-318">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="4e8cc-319">In diesem Beispiel ist der Klassenname des Controllers „**TodoItems**Controller“. Der Controllername lautet also „TodoItems“.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-319">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="4e8cc-320">Beim ASP.NET Core-[Routing](xref:mvc/controllers/routing) wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-320">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="4e8cc-321">Wenn das `[HttpGet]`-Attribut eine Routenvorlage (z.B. `[HttpGet("products")]`) hat, fügen Sie diese an den Pfad an.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-321">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="4e8cc-322">In diesem Beispiel wird keine Vorlage verwendet.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-322">This sample doesn't use a template.</span></span> <span data-ttu-id="4e8cc-323">Weitere Informationen finden Sie unter [Attributrouting mit Http[Verb]-Attributen](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="4e8cc-323">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="4e8cc-324">In der folgenden `GetTodoItem`-Methode ist `"{id}"` eine Platzhaltervariable für den eindeutigen Bezeichners des To-do-Elements.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-324">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="4e8cc-325">Wenn `GetTodoItem` aufgerufen wird, wird der Wert von `"{id}"` in der URL der Methode in ihrem Parameter `id` bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-325">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="4e8cc-326">Rückgabewert</span><span class="sxs-lookup"><span data-stu-id="4e8cc-326">Return values</span></span>

<span data-ttu-id="4e8cc-327">Der Rückgabetyp der Methoden `GetTodoItems` und `GetTodoItem` ist [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="4e8cc-327">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="4e8cc-328">ASP.NET Core serialisiert automatisch das Objekt in [JSON](https://www.json.org/) und schreibt den JSON-Code in den Text der Antwortnachricht.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-328">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="4e8cc-329">Der Antwortcode für diesen Rückgabetyp ist 200, vorausgesetzt, es gibt keine Ausnahmefehler.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-329">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="4e8cc-330">Nicht behandelte Ausnahmen werden in 5xx-Fehler übersetzt.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-330">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="4e8cc-331">`ActionResult`-Rückgabetypen können eine Vielzahl von HTTP-Statuscodes darstellen.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-331">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="4e8cc-332">Beispielsweise kann `GetTodoItem` zwei verschiedene Statuswerte zurückgeben:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-332">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="4e8cc-333">Wenn kein Element mit der angeforderten ID übereinstimmt, gibt die Methode einen 404-Fehlercode [Nicht gefunden](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) zurück.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-333">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="4e8cc-334">Andernfalls gibt die Methode 200 mit einem JSON-Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-334">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="4e8cc-335">Die Rückgabe von `item` löst eine HTTP 200-Antwort aus.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-335">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="4e8cc-336">PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="4e8cc-336">The PutTodoItem method</span></span>

<span data-ttu-id="4e8cc-337">Untersuchen Sie die `PutTodoItem`-Methode.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-337">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="4e8cc-338">`PutTodoItem` ähnelt `PostTodoItem`, verwendet allerdings HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-338">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="4e8cc-339">Die Antwort ist [204 (Kein Inhalt)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="4e8cc-339">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="4e8cc-340">Gemäß der HTTP-Spezifikation erfordert eine PUT-Anforderung, dass der Client die gesamte aktualisierte Entität (nicht nur die Änderungen) sendet.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-340">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="4e8cc-341">Verwenden Sie [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute), um Teilupdates zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-341">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="4e8cc-342">Wenn beim Aufrufen von `PutTodoItem` ein Fehler zurückgegeben wird, rufen Sie `GET` auf, um sicherzustellen, dass die Datenbank ein Element enthält.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-342">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="4e8cc-343">Testen der PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="4e8cc-343">Test the PutTodoItem method</span></span>

<span data-ttu-id="4e8cc-344">In diesem Beispiel wird eine In-Memory Database verwendet, die jedes Mal initialisiert werden muss, wenn die App gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-344">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="4e8cc-345">Es muss ein Element in der Datenbank vorhanden sein, bevor Sie einen PUT-Aufruf durchführen.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-345">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="4e8cc-346">Rufen Sie vor einem PUT-Aufruf GET auf, um sicherzustellen, dass ein Element in der Datenbank vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-346">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="4e8cc-347">Aktualisieren Sie das To-do-Element, das über den ID-Wert 1 verfügt, und legen Sie als Namen „feed fish“ fest:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-347">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="4e8cc-348">Die folgende Abbildung zeigt das Postman-Update:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-348">The following image shows the Postman update:</span></span>

![Postman-Konsole mit der Antwort „204 (Kein Inhalt)“](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="4e8cc-350">DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="4e8cc-350">The DeleteTodoItem method</span></span>

<span data-ttu-id="4e8cc-351">Untersuchen Sie die `DeleteTodoItem`-Methode.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-351">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

<span data-ttu-id="4e8cc-352">Die `DeleteTodoItem`-Antwort lautet [204 (Kein Inhalt)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="4e8cc-352">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="4e8cc-353">Testen der DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="4e8cc-353">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="4e8cc-354">So löschen Sie mit Postman eine Aufgabe</span><span class="sxs-lookup"><span data-stu-id="4e8cc-354">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="4e8cc-355">Legen Sie die Methode auf `DELETE` fest.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-355">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="4e8cc-356">Legen Sie den URI des zu löschenden Objekts fest, z. B. `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-356">Set the URI of the object to delete, for example `https://localhost:5001/api/TodoItems/1`</span></span>
* <span data-ttu-id="4e8cc-357">Klicken Sie auf **Send**.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-357">Select **Send**</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="4e8cc-358">Aufrufen der Web-API mit JavaScript</span><span class="sxs-lookup"><span data-stu-id="4e8cc-358">Call the web API with JavaScript</span></span>

<span data-ttu-id="4e8cc-359">Eine Schritt-für-Schritt-Anleitung finden Sie im [Tutorial: Aufrufen einer ASP.NET Core-Web-API mit JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="4e8cc-359">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4e8cc-360">In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-360">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4e8cc-361">Erstellen eines Web-API-Projekts</span><span class="sxs-lookup"><span data-stu-id="4e8cc-361">Create a web API project.</span></span>
> * <span data-ttu-id="4e8cc-362">Hinzufügen einer Modellklasse und eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="4e8cc-362">Add a model class and a database context.</span></span>
> * <span data-ttu-id="4e8cc-363">Hinzufügen eines Controllers</span><span class="sxs-lookup"><span data-stu-id="4e8cc-363">Add a controller.</span></span>
> * <span data-ttu-id="4e8cc-364">Hinzufügen von CRUD-Methoden</span><span class="sxs-lookup"><span data-stu-id="4e8cc-364">Add CRUD methods.</span></span>
> * <span data-ttu-id="4e8cc-365">Konfigurieren von Routing und URL-Pfaden</span><span class="sxs-lookup"><span data-stu-id="4e8cc-365">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="4e8cc-366">Angeben von Rückgabewerten</span><span class="sxs-lookup"><span data-stu-id="4e8cc-366">Specify return values.</span></span>
> * <span data-ttu-id="4e8cc-367">Aufrufen der Web-API mit Postman</span><span class="sxs-lookup"><span data-stu-id="4e8cc-367">Call the web API with Postman.</span></span>
> * <span data-ttu-id="4e8cc-368">Aufrufen der Web-API mit JavaScript</span><span class="sxs-lookup"><span data-stu-id="4e8cc-368">Call the web API with JavaScript.</span></span>

<span data-ttu-id="4e8cc-369">Am Ende haben Sie eine Web-API, die in einer relationalen Datenbank gespeicherte „Aufgaben“ verwalten kann.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-369">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="4e8cc-370">Übersicht</span><span class="sxs-lookup"><span data-stu-id="4e8cc-370">Overview</span></span>

<span data-ttu-id="4e8cc-371">In diesem Tutorial wird die folgende API erstellt:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-371">This tutorial creates the following API:</span></span>

|<span data-ttu-id="4e8cc-372">API</span><span class="sxs-lookup"><span data-stu-id="4e8cc-372">API</span></span> | <span data-ttu-id="4e8cc-373">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="4e8cc-373">Description</span></span> | <span data-ttu-id="4e8cc-374">Anforderungstext</span><span class="sxs-lookup"><span data-stu-id="4e8cc-374">Request body</span></span> | <span data-ttu-id="4e8cc-375">Antworttext</span><span class="sxs-lookup"><span data-stu-id="4e8cc-375">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="4e8cc-376">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="4e8cc-376">GET /api/TodoItems</span></span> | <span data-ttu-id="4e8cc-377">Alle To-do-Elemente abrufen</span><span class="sxs-lookup"><span data-stu-id="4e8cc-377">Get all to-do items</span></span> | <span data-ttu-id="4e8cc-378">Keine</span><span class="sxs-lookup"><span data-stu-id="4e8cc-378">None</span></span> | <span data-ttu-id="4e8cc-379">Array von To-do-Elementen</span><span class="sxs-lookup"><span data-stu-id="4e8cc-379">Array of to-do items</span></span>|
|<span data-ttu-id="4e8cc-380">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="4e8cc-380">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="4e8cc-381">Ein Element nach ID abrufen</span><span class="sxs-lookup"><span data-stu-id="4e8cc-381">Get an item by ID</span></span> | <span data-ttu-id="4e8cc-382">Keine</span><span class="sxs-lookup"><span data-stu-id="4e8cc-382">None</span></span> | <span data-ttu-id="4e8cc-383">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="4e8cc-383">To-do item</span></span>|
|<span data-ttu-id="4e8cc-384">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="4e8cc-384">POST /api/TodoItems</span></span> | <span data-ttu-id="4e8cc-385">Neues Element hinzufügen</span><span class="sxs-lookup"><span data-stu-id="4e8cc-385">Add a new item</span></span> | <span data-ttu-id="4e8cc-386">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="4e8cc-386">To-do item</span></span> | <span data-ttu-id="4e8cc-387">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="4e8cc-387">To-do item</span></span> |
|<span data-ttu-id="4e8cc-388">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="4e8cc-388">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="4e8cc-389">Vorhandenes Element aktualisieren &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4e8cc-389">Update an existing item &nbsp;</span></span> | <span data-ttu-id="4e8cc-390">To-do-Element</span><span class="sxs-lookup"><span data-stu-id="4e8cc-390">To-do item</span></span> | <span data-ttu-id="4e8cc-391">Keine</span><span class="sxs-lookup"><span data-stu-id="4e8cc-391">None</span></span> |
|<span data-ttu-id="4e8cc-392">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4e8cc-392">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="4e8cc-393">Löschen eines Elements &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4e8cc-393">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="4e8cc-394">Keine</span><span class="sxs-lookup"><span data-stu-id="4e8cc-394">None</span></span> | <span data-ttu-id="4e8cc-395">Keine</span><span class="sxs-lookup"><span data-stu-id="4e8cc-395">None</span></span>|

<span data-ttu-id="4e8cc-396">Das folgende Diagramm zeigt den Entwurf der App.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-396">The following diagram shows the design of the app.</span></span>

![Der Client ist das Feld ganz links.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="4e8cc-402">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="4e8cc-402">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4e8cc-403">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4e8cc-403">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4e8cc-404">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4e8cc-404">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4e8cc-405">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4e8cc-405">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="4e8cc-406">Erstellen eines Webprojekts</span><span class="sxs-lookup"><span data-stu-id="4e8cc-406">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4e8cc-407">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4e8cc-407">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4e8cc-408">Klicken Sie im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-408">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="4e8cc-409">Wählen Sie die Vorlage **ASP.NET Core-Webanwendung** aus, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-409">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="4e8cc-410">Geben Sie dem Projekt den Namen *TodoApi*, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-410">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="4e8cc-411">Vergewissern Sie sich, dass im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Optionen **.NET Core** und **ASP.NET Core 2.2** ausgewählt sind.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-411">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="4e8cc-412">Wählen Sie die Vorlage **API** aus, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-412">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="4e8cc-413">Wählen Sie **nicht** **Enable Docker Support** (Docker-Unterstützung aktivieren) aus.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-413">**Don't** select **Enable Docker Support**.</span></span>

![VS-Dialogfeld „Neues Projekt“](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4e8cc-415">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4e8cc-415">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4e8cc-416">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="4e8cc-416">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="4e8cc-417">Wechseln Sie vom Verzeichnis (`cd`) zum Ordner, der den Projektordner enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-417">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="4e8cc-418">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-418">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="4e8cc-419">Diese Befehle erstellen ein neues Web-API-Projekt und öffnen eine neue Visual Studio Code-Instanz im neuen Projektordner.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-419">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="4e8cc-420">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem Projekt die erforderlichen Elemente hinzufügen möchten, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-420">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4e8cc-421">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4e8cc-421">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4e8cc-422">Klicken Sie auf **Datei** > **Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-422">Select **File** > **New Solution**.</span></span>

  ![Neue Projektmappe in macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="4e8cc-424">Klicken Sie auf **.NET Core** > **App** > **API** > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-424">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![Dialogfeld „Neue Projektmappe“ in macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="4e8cc-426">Übernehmen Sie im Dialogfeld **Neue ASP.NET Core-Web-API konfigurieren** die Standardeinstellung **Zielframework** von \* *.NET Core 2.2*.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-426">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="4e8cc-427">Geben Sie für **Projektname** *TodoApi* ein, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-427">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Dialogfeld „Konfiguration“](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="4e8cc-429">Testen der API</span><span class="sxs-lookup"><span data-stu-id="4e8cc-429">Test the API</span></span>

<span data-ttu-id="4e8cc-430">Die Projektvorlage erstellt eine `values`-API.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-430">The project template creates a `values` API.</span></span> <span data-ttu-id="4e8cc-431">Rufen Sie in einem Browser die `Get`-Methode zum Testen der App auf.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-431">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4e8cc-432">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4e8cc-432">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4e8cc-433">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-433">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="4e8cc-434">Visual Studio startet einen Browser und navigiert zu `https://localhost:<port>/api/values`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-434">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="4e8cc-435">Wenn Sie in einem Dialogfeld angeben müssen, ob Sie dem IIS Express-Zertifikat vertrauen, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-435">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="4e8cc-436">Wählen Sie im folgenden Dialogfeld **Sicherheitswarnung** die Option **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-436">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4e8cc-437">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4e8cc-437">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4e8cc-438">Drücken Sie STRG+F5, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-438">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="4e8cc-439">Rufen Sie in einem Browser die folgende URL auf: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span><span class="sxs-lookup"><span data-stu-id="4e8cc-439">In a browser, go to following URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4e8cc-440">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4e8cc-440">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4e8cc-441">Wählen Sie **Run** > **Start Debugging** (Ausführen > Debugging starten) aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-441">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="4e8cc-442">Visual Studio für Mac startet einen Browser und navigiert zu `https://localhost:<port>`, wobei es sich bei `<port>` um eine zufällig ausgewählte Portnummer handelt.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-442">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="4e8cc-443">Der Fehler „HTTP 404: Nicht gefunden“ wird zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-443">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="4e8cc-444">Fügen Sie der URL `/api/values` an, d.h. ändern Sie die URL in `https://localhost:<port>/api/values`.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-444">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="4e8cc-445">Die folgende JSON-Datei wird zurückgegeben:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-445">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="4e8cc-446">Hinzufügen einer Modellklasse</span><span class="sxs-lookup"><span data-stu-id="4e8cc-446">Add a model class</span></span>

<span data-ttu-id="4e8cc-447">Ein *Modell* ist eine Gruppe von Klassen, die die Daten darstellen, die die App verwaltet.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-447">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="4e8cc-448">Das Modell für diese App ist eine einzelne `TodoItem`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-448">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4e8cc-449">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4e8cc-449">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4e8cc-450">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-450">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="4e8cc-451">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-451">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="4e8cc-452">Geben Sie dem Ordner den Namen *Models*.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-452">Name the folder *Models*.</span></span>

* <span data-ttu-id="4e8cc-453">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-453">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="4e8cc-454">Nennen Sie die Klasse *TodoItem*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-454">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="4e8cc-455">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-455">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4e8cc-456">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4e8cc-456">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4e8cc-457">Fügen Sie einen Ordner namens *Models* hinzu.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-457">Add a folder named *Models*.</span></span>

* <span data-ttu-id="4e8cc-458">Fügen Sie dem Ordner *Modelle* mit dem folgenden Code eine `TodoItem`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-458">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4e8cc-459">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4e8cc-459">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4e8cc-460">Klicken Sie mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-460">Right-click the project.</span></span> <span data-ttu-id="4e8cc-461">Klicken Sie auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-461">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="4e8cc-462">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-462">Name the folder *Models*.</span></span>

  ![Neuer Ordner](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="4e8cc-464">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und klicken Sie auf **Hinzufügen** > **Neue Datei** > **Allgemein** > **Leere Klasse**.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-464">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="4e8cc-465">Nennen Sie die Klasse *TodoItem*, und klicken Sie dann auf **Neu**.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-465">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="4e8cc-466">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-466">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="4e8cc-467">Die `Id`-Eigenschaft fungiert als eindeutiger Schlüssel in einer relationalen Datenbank.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-467">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="4e8cc-468">Modellklassen können überall im Projekt platziert werden, doch gemäß der Konvention wird der Ordner *Modelle* verwendet.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-468">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="4e8cc-469">Hinzufügen eines Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="4e8cc-469">Add a database context</span></span>

<span data-ttu-id="4e8cc-470">Der *Datenbankkontext* ist die Hauptklasse, die die Entity Framework-Funktionen für ein Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-470">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="4e8cc-471">Diese Klasse wird durch Ableiten von der `Microsoft.EntityFrameworkCore.DbContext`-Klasse erstellt.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-471">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4e8cc-472">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4e8cc-472">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4e8cc-473">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-473">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="4e8cc-474">Nennen Sie die Klasse *TodoContext*, und klicken Sie auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-474">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="4e8cc-475">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4e8cc-475">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="4e8cc-476">Fügen Sie eine `TodoContext`-Klasse zum Ordner *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-476">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="4e8cc-477">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-477">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="4e8cc-478">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="4e8cc-478">Register the database context</span></span>

<span data-ttu-id="4e8cc-479">In ASP.NET Core müssen Dienste wie der Datenbankkontext mit dem Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) registriert werden.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-479">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="4e8cc-480">Der Container stellt den Dienst für Controller bereit.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-480">The container provides the service to controllers.</span></span>

<span data-ttu-id="4e8cc-481">Aktualisieren Sie *Startup.cs* mit dem folgenden hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-481">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="4e8cc-482">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-482">The preceding code:</span></span>

* <span data-ttu-id="4e8cc-483">Entfernt nicht benötigte `using`-Deklarationen</span><span class="sxs-lookup"><span data-stu-id="4e8cc-483">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="4e8cc-484">Fügt dem Abhängigkeitscontainer den Datenbankkontext hinzu</span><span class="sxs-lookup"><span data-stu-id="4e8cc-484">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="4e8cc-485">Gibt an, dass der Datenbankkontext eine In-Memory Database verwendet</span><span class="sxs-lookup"><span data-stu-id="4e8cc-485">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="4e8cc-486">Hinzufügen eines Controllers</span><span class="sxs-lookup"><span data-stu-id="4e8cc-486">Add a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4e8cc-487">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4e8cc-487">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4e8cc-488">Klicken Sie mit der rechten Maustaste auf den Ordner *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-488">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="4e8cc-489">Klicken Sie auf **Hinzufügen** > **Neues Element**.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-489">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="4e8cc-490">Wählen Sie im Dialogfeld **Neues Element hinzufügen** die Vorlage **API-Controllerklasse** aus.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-490">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="4e8cc-491">Benennen Sie die Klasse *TodoController*, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-491">Name the class *TodoController*, and select **Add**.</span></span>

  ![Dialogfeld „Neues Element hinzufügen“ mit Controller im Suchfeld und ausgewähltem Web-API-Controller](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="4e8cc-493">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4e8cc-493">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="4e8cc-494">Erstellen Sie im Ordner *Controllers* eine Klasse namens `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-494">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="4e8cc-495">Ersetzen Sie den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-495">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="4e8cc-496">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-496">The preceding code:</span></span>

* <span data-ttu-id="4e8cc-497">Er definiert eine API-Controllerklasse ohne Methoden.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-497">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="4e8cc-498">Ergänzt die Klasse mit dem Attribut [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute).</span><span class="sxs-lookup"><span data-stu-id="4e8cc-498">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="4e8cc-499">Dieses Attribut gibt an, dass der Controller auf Web-API-Anforderungen reagiert.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-499">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="4e8cc-500">Weitere Informationen zu bestimmten Verhaltensweisen, die das Attribut ermöglicht, finden Sie unter <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-500">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="4e8cc-501">Verwendet die Abhängigkeitsinjektion, um den Datenbankkontext (`TodoContext`) dem Controller hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-501">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="4e8cc-502">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-502">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="4e8cc-503">Fügt der Datenbank, falls sie leer ist, ein Element mit dem Namen `Item1` hinzu.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-503">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="4e8cc-504">Dieser Code befindet sich im Konstruktor. Er wird bei jeder neuen HTTP-Anforderung ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-504">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="4e8cc-505">Wenn Sie alle Elemente löschen, erstellt der Konstruktor beim nächsten Aufruf einer API-Methode erneut `Item1`.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-505">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="4e8cc-506">So sieht es möglicherweise so aus, als sei die Löschung fehlgeschlagen, obwohl sie erfolgreich war.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-506">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="4e8cc-507">Hinzufügen von GET-Methoden</span><span class="sxs-lookup"><span data-stu-id="4e8cc-507">Add Get methods</span></span>

<span data-ttu-id="4e8cc-508">Fügen Sie der Klasse `TodoController` die folgenden Methoden hinzu, um eine API bereitzustellen, die Aufgaben abruft:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-508">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="4e8cc-509">Diese Methoden implementieren zwei GET-Endpunkte:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-509">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="4e8cc-510">Halten Sie die App an, falls diese noch ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-510">Stop the app if it's still running.</span></span> <span data-ttu-id="4e8cc-511">Führen Sie diese dann noch mal aus, damit die letzten Änderungen übernommen werden.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-511">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="4e8cc-512">Testen Sie die App, indem Sie die beiden Endpunkte in einem Browser aufrufen.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-512">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="4e8cc-513">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-513">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="4e8cc-514">Die folgende HTTP-Antwort wird durch den Aufruf von `GetTodoItems` erzeugt:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-514">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="4e8cc-515">Routing und URL-Pfade</span><span class="sxs-lookup"><span data-stu-id="4e8cc-515">Routing and URL paths</span></span>

<span data-ttu-id="4e8cc-516">Das Attribut [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) gibt eine Methode an, die auf eine HTTP GET-Anforderung antwortet.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-516">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="4e8cc-517">Der URL-Pfad für jede Methode wird wie folgt erstellt:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-517">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="4e8cc-518">Beginnen Sie mit der Vorlagenzeichenfolge im `Route`-Attribut des Controllers:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-518">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="4e8cc-519">Ersetzen Sie `[controller]` durch den Namen des Controllers, bei dem es sich standardmäßig um den Namen der Controller-Klasse ohne das Suffix „Controller“ handelt.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-519">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="4e8cc-520">Bei diesem Beispiel ist der Klassenname des Controllers „**Todo**Controller“, d.h. der Controllername lautet „todo“.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-520">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="4e8cc-521">Beim ASP.NET Core-[Routing](xref:mvc/controllers/routing) wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-521">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="4e8cc-522">Wenn das `[HttpGet]`-Attribut eine Routenvorlage (z.B. `[HttpGet("products")]`) hat, fügen Sie diese an den Pfad an.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-522">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="4e8cc-523">In diesem Beispiel wird keine Vorlage verwendet.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-523">This sample doesn't use a template.</span></span> <span data-ttu-id="4e8cc-524">Weitere Informationen finden Sie unter [Attributrouting mit Http[Verb]-Attributen](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="4e8cc-524">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="4e8cc-525">In der folgenden `GetTodoItem`-Methode ist `"{id}"` eine Platzhaltervariable für den eindeutigen Bezeichners des To-do-Elements.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-525">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="4e8cc-526">Wenn `GetTodoItem` aufgerufen wird, wird der Wert von `"{id}"` in der URL der Methode in ihrem Parameter `id` bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-526">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="4e8cc-527">Rückgabewert</span><span class="sxs-lookup"><span data-stu-id="4e8cc-527">Return values</span></span>

<span data-ttu-id="4e8cc-528">Der Rückgabetyp der Methoden `GetTodoItems` und `GetTodoItem` ist [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="4e8cc-528">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="4e8cc-529">ASP.NET Core serialisiert automatisch das Objekt in [JSON](https://www.json.org/) und schreibt den JSON-Code in den Text der Antwortnachricht.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-529">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="4e8cc-530">Der Antwortcode für diesen Rückgabetyp ist 200, vorausgesetzt, es gibt keine Ausnahmefehler.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-530">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="4e8cc-531">Nicht behandelte Ausnahmen werden in 5xx-Fehler übersetzt.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-531">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="4e8cc-532">`ActionResult`-Rückgabetypen können eine Vielzahl von HTTP-Statuscodes darstellen.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-532">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="4e8cc-533">Beispielsweise kann `GetTodoItem` zwei verschiedene Statuswerte zurückgeben:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-533">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="4e8cc-534">Wenn kein Element mit der angeforderten ID übereinstimmt, gibt die Methode einen 404-Fehlercode [Nicht gefunden](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) zurück.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-534">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="4e8cc-535">Andernfalls gibt die Methode 200 mit einem JSON-Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-535">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="4e8cc-536">Die Rückgabe von `item` löst eine HTTP 200-Antwort aus.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-536">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="4e8cc-537">Testen der GetTodoItems-Methode</span><span class="sxs-lookup"><span data-stu-id="4e8cc-537">Test the GetTodoItems method</span></span>

<span data-ttu-id="4e8cc-538">Dieses Tutorial verwendet Postman zum Testen der Web-API.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-538">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="4e8cc-539">Installieren Sie [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="4e8cc-539">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="4e8cc-540">Starten Sie die Web-App.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-540">Start the web app.</span></span>
* <span data-ttu-id="4e8cc-541">Starten Sie Postman.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-541">Start Postman.</span></span>
* <span data-ttu-id="4e8cc-542">Deaktivieren Sie **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="4e8cc-542">Disable **SSL certificate verification**</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4e8cc-543">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4e8cc-543">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4e8cc-544">Deaktivieren Sie auf der Registerkarte **General** (Allgemein) unter **File**>**Settings** (Datei > Einstellungen) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="4e8cc-544">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="4e8cc-545">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4e8cc-545">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="4e8cc-546">Deaktivieren Sie auf der Registerkarte **General** (Allgemein) unter **Postman** > **Preferences** (Postman > Einstellungen) **SSL certificate verification** (Verifizierung des SSL-Zertifikats).</span><span class="sxs-lookup"><span data-stu-id="4e8cc-546">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="4e8cc-547">Wählen Sie alternativ den Schraubenschlüssel und **Settings** (Einstellungen) aus, und deaktivieren Sie dann die Überprüfung des SSL-Zertifikats.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-547">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="4e8cc-548">Aktivieren Sie die Verifizierung des SSL-Zertifikats wieder, nachdem Sie den Controller getestet haben.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-548">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="4e8cc-549">Erstellen Sie eine neue Anforderung.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-549">Create a new request.</span></span>
  * <span data-ttu-id="4e8cc-550">Legen Sie die HTTP-Methode auf **GET** fest.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-550">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="4e8cc-551">Legen Sie die Anforderungs-URL auf `https://localhost:<port>/api/todo` fest.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-551">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="4e8cc-552">Beispielsweise `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-552">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="4e8cc-553">Wählen Sie in Postman **Two pane view** (Ansicht mit zwei Bereichen) aus.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-553">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="4e8cc-554">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-554">Select **Send**.</span></span>

![Postman mit GET-Anforderung](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="4e8cc-556">Hinzufügen einer Methode</span><span class="sxs-lookup"><span data-stu-id="4e8cc-556">Add a Create method</span></span>

<span data-ttu-id="4e8cc-557">Fügen Sie die folgende `PostTodoItem`-Methode in *Controllers/TodoController.cs* hinzu:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-557">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="4e8cc-558">Der vorherige Code ist eine HTTP POST-Methode, die durch das Attribut [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-558">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="4e8cc-559">Die Methode ruft den Wert der Aufgabe aus dem Text der HTTP-Anforderung ab.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-559">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="4e8cc-560">Die `CreatedAtAction`-Methode:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-560">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="4e8cc-561">Gibt bei einer erfolgreichen Anforderung den Statuscode „HTTP 201“ zurück.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-561">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="4e8cc-562">HTTP 201 ist die Standardantwort für eine HTTP POST-Methode, die eine neue Ressource auf dem Server erstellt.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-562">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="4e8cc-563">Fügt der Antwort einen `Location`-Header hinzu.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-563">Adds a `Location` header to the response.</span></span> <span data-ttu-id="4e8cc-564">Der `Location`-Header gibt den URI des neu erstellten To-Do-Elements zurück.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-564">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="4e8cc-565">Weitere Informationen finden Sie unter [10.2.2 201 Erstellt](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="4e8cc-565">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="4e8cc-566">Verweist auf die `GetTodoItem`-Aktion zum Erstellen des URIs des `Location`-Headers.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-566">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="4e8cc-567">Das `nameof`-Schlüsselwort von C# wird verwendet, um eine Hartcodierung des Aktionsnamens im `CreatedAtAction`-Aufruf zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-567">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="4e8cc-568">Testen der PostTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="4e8cc-568">Test the PostTodoItem method</span></span>

* <span data-ttu-id="4e8cc-569">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-569">Build the project.</span></span>
* <span data-ttu-id="4e8cc-570">Legen Sie in Postman die HTTP-Methode auf `POST` fest.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-570">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="4e8cc-571">Wählen Sie die Registerkarte **Body** (Text) aus.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-571">Select the **Body** tab.</span></span>
* <span data-ttu-id="4e8cc-572">Wählen Sie das Optionsfeld **raw** (Unformatiert) aus.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-572">Select the **raw** radio button.</span></span>
* <span data-ttu-id="4e8cc-573">Legen Sie den Typ auf **JSON (application/json)** fest.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-573">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="4e8cc-574">Geben Sie für die Aufgabe den Anforderungstext im JSON-Format ein:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-574">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="4e8cc-575">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-575">Select **Send**.</span></span>

  ![Postman mit erstellter Anforderung](first-web-api/_static/create.png)

  <span data-ttu-id="4e8cc-577">Wenn Sie die Fehlermeldung „405: Methode nicht zulässig“ erhalten, wurde das Projekt wahrscheinlich nicht kompiliert, nachdem die Methode `PostTodoItem` hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-577">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="4e8cc-578">Testen des Adressheader-URIs</span><span class="sxs-lookup"><span data-stu-id="4e8cc-578">Test the location header URI</span></span>

* <span data-ttu-id="4e8cc-579">Wählen Sie auf der Registerkarte **Header** (Header) den Bereich **Response** (Antwort) aus.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-579">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="4e8cc-580">Kopieren Sie den den Headerwert von **Location** (Speicherort):</span><span class="sxs-lookup"><span data-stu-id="4e8cc-580">Copy the **Location** header value:</span></span>

  ![Registerkarte „Header“ in der Postman-Konsole](first-web-api/_static/pmc2.png)

* <span data-ttu-id="4e8cc-582">Legen Sie die Methode auf „GET“ fest.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-582">Set the method to GET.</span></span>
* <span data-ttu-id="4e8cc-583">Fügen Sie den URI (z. B. `https://localhost:5001/api/Todo/2`) ein.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-583">Paste the URI (for example, `https://localhost:5001/api/Todo/2`)</span></span>
* <span data-ttu-id="4e8cc-584">Wählen Sie **Send** (Senden) aus.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-584">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="4e8cc-585">Hinzufügen einer PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="4e8cc-585">Add a PutTodoItem method</span></span>

<span data-ttu-id="4e8cc-586">Fügen Sie die folgende `PutTodoItem`-Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-586">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="4e8cc-587">`PutTodoItem` ähnelt `PostTodoItem`, verwendet allerdings HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-587">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="4e8cc-588">Die Antwort ist [204 (Kein Inhalt)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="4e8cc-588">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="4e8cc-589">Gemäß der HTTP-Spezifikation erfordert eine PUT-Anforderung, dass der Client die gesamte aktualisierte Entität (nicht nur die Änderungen) sendet.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-589">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="4e8cc-590">Verwenden Sie [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute), um Teilupdates zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-590">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="4e8cc-591">Wenn beim Aufrufen von `PutTodoItem` ein Fehler zurückgegeben wird, rufen Sie `GET` auf, um sicherzustellen, dass die Datenbank ein Element enthält.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-591">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="4e8cc-592">Testen der PutTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="4e8cc-592">Test the PutTodoItem method</span></span>

<span data-ttu-id="4e8cc-593">In diesem Beispiel wird eine In-Memory Database verwendet, die jedes Mal initialisiert werden muss, wenn die App gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-593">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="4e8cc-594">Es muss ein Element in der Datenbank vorhanden sein, bevor Sie einen PUT-Aufruf durchführen.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-594">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="4e8cc-595">Rufen Sie vor einem PUT-Aufruf GET auf, um sicherzustellen, dass ein Element in der Datenbank vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-595">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="4e8cc-596">Aktualisieren Sie die Aufgabe mit der ID = 1, und setzen Sie ihren Namen auf „feed fish“:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-596">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="4e8cc-597">Die folgende Abbildung zeigt das Postman-Update:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-597">The following image shows the Postman update:</span></span>

![Postman-Konsole mit der Antwort „204 (Kein Inhalt)“](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="4e8cc-599">Hinzufügen einer DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="4e8cc-599">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="4e8cc-600">Fügen Sie die folgende `DeleteTodoItem`-Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-600">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="4e8cc-601">Die `DeleteTodoItem`-Antwort lautet [204 (Kein Inhalt)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="4e8cc-601">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="4e8cc-602">Testen der DeleteTodoItem-Methode</span><span class="sxs-lookup"><span data-stu-id="4e8cc-602">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="4e8cc-603">So löschen Sie mit Postman eine Aufgabe</span><span class="sxs-lookup"><span data-stu-id="4e8cc-603">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="4e8cc-604">Legen Sie die Methode auf `DELETE` fest.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-604">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="4e8cc-605">Legen Sie den URI des zu löschenden Objekts fest, z. B. `https://localhost:5001/api/todo/1`.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-605">Set the URI of the object to delete, for example `https://localhost:5001/api/todo/1`</span></span>
* <span data-ttu-id="4e8cc-606">Klicken Sie auf **Send**.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-606">Select **Send**</span></span>

<span data-ttu-id="4e8cc-607">Sie können in der Beispiel-App alle Elemente löschen.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-607">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="4e8cc-608">Sobald das letzte Element gelöscht wurde, wird allerdings beim nächsten API-Aufruf vom Modellklassenkonstruktor ein neues erstellt.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-608">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="4e8cc-609">Aufrufen der Web-API mit JavaScript</span><span class="sxs-lookup"><span data-stu-id="4e8cc-609">Call the web API with JavaScript</span></span>

<span data-ttu-id="4e8cc-610">In diesem Abschnitt wird eine HTML-Seite hinzugefügt, die mithilfe von JavaScript die Web-API aufruft.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-610">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="4e8cc-611">Die Fetch-API initiiert die Anforderung.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-611">The Fetch API initiates the request.</span></span> <span data-ttu-id="4e8cc-612">JavaScript aktualisiert die Seite mit den Details aus der Antwort der Web-API.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-612">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="4e8cc-613">Konfigurieren Sie die App so, dass sie [statische Dateien bereitstellt](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_), und [aktivieren Sie die Standarddateizuordnung](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_), indem Sie *startup.cs* mit dem unten markierten Code aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-613">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="4e8cc-614">Erstellen Sie im Projektverzeichnis den Ordner *wwwwroot*.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-614">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="4e8cc-615">Fügen Sie dem Verzeichnis *wwwroot* eine HTML-Datei namens *index.html* hinzu.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-615">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="4e8cc-616">Ersetzen Sie den Inhalt durch folgendes Markup:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-616">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="4e8cc-617">Fügen Sie dem Verzeichnis *wwwroot* eine JavaScript-Datei namens *site.js* hinzu.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-617">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="4e8cc-618">Ersetzen Sie den Inhalt durch folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-618">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="4e8cc-619">Möglicherweise ist eine Änderung an den Starteinstellungen des ASP.NET Core-Projekts erforderlich, um die HTML-Seite lokal zu testen:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-619">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="4e8cc-620">Öffnen Sie *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-620">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="4e8cc-621">Entfernen Sie die `launchUrl`-Eigenschaft, um zu erzwingen, dass die App mit *index.html* als Startseite geöffnet wird. Dies ist die Standarddatei des Projekts.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-621">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="4e8cc-622">Dieses Beispiel ruft alle CRUD-Methoden der Web-API auf.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-622">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="4e8cc-623">Im Folgenden werden die API-Aufrufe erläutert.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-623">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="4e8cc-624">Abrufen einer Liste von To-Do-Elementen</span><span class="sxs-lookup"><span data-stu-id="4e8cc-624">Get a list of to-do items</span></span>

<span data-ttu-id="4e8cc-625">Fetch sendet eine HTTP GET-Anforderung an die Web-API, die JSON-Code mit einem Array aus To-Do-Elementen zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-625">Fetch sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="4e8cc-626">Die Rückruffunktion `success` wird aufgerufen, wenn die Anforderung erfolgreich ist.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-626">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="4e8cc-627">Im Rückruf wird DOM mit den To-Do-Informationen aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-627">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="4e8cc-628">Hinzufügen eines To-Do-Elements</span><span class="sxs-lookup"><span data-stu-id="4e8cc-628">Add a to-do item</span></span>

<span data-ttu-id="4e8cc-629">Fetch sendet eine HTTP POST-Anforderung mit dem To-Do-Element im Anforderungstext.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-629">Fetch sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="4e8cc-630">Die Optionen `accepts` und `contentType` werden auf `application/json` festgelegt, um den gesendeten und empfangenen Medientyp anzugeben.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-630">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="4e8cc-631">Die Aufgabe wird mit [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) in JSON konvertiert.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-631">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="4e8cc-632">Wenn die API den Statuscode „Erfolgreich“ zurückgibt, wird die Funktion `getData` aufgerufen, um die HTML-Tabelle zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-632">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="4e8cc-633">Aktualisieren eines To-Do-Elements</span><span class="sxs-lookup"><span data-stu-id="4e8cc-633">Update a to-do item</span></span>

<span data-ttu-id="4e8cc-634">Das Aktualisieren einer Aufgabe ist vergleichbar mit dem Hinzufügen einer Aufgabe.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-634">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="4e8cc-635">Die `url` ändert sich, um den eindeutigen Bezeichner des Elements hinzuzufügen. Der `type` lautet `PUT`.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-635">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="4e8cc-636">Löschen eines To-Do-Elements</span><span class="sxs-lookup"><span data-stu-id="4e8cc-636">Delete a to-do item</span></span>

<span data-ttu-id="4e8cc-637">Eine Aufgabe wird folgendermaßen gelöscht: im AJAX-Aufruf wird `type` auf `DELETE` festgelegt, und der eindeutige Bezeichner des Elements wird in der URL angegeben.</span><span class="sxs-lookup"><span data-stu-id="4e8cc-637">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="4e8cc-638">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="4e8cc-638">Additional resources</span></span>

<span data-ttu-id="4e8cc-639">[Sie können den Beispielcode für dieses Tutorial anzeigen oder herunterladen.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)</span><span class="sxs-lookup"><span data-stu-id="4e8cc-639">[View or download sample code for this tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="4e8cc-640">Informationen [zum Herunterladen](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="4e8cc-640">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="4e8cc-641">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="4e8cc-641">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="4e8cc-642">Dieses Tutorial auf YouTube</span><span class="sxs-lookup"><span data-stu-id="4e8cc-642">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)

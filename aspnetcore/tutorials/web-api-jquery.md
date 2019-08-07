---
title: 'Tutorial: Aufrufen einer ASP.NET Core-Web-API mit jQuery'
author: rick-anderson
description: Erfahren Sie, wie Sie eine ASP.NET Core-Web-API mit jQuery aufrufen.
ms.author: riande
ms.custom: mvc
ms.date: 07/20/2019
uid: tutorials/web-api-jquery
ms.openlocfilehash: eb8b2453fd037170a49f531fea4c3ef1c056292d
ms.sourcegitcommit: 0efb9e219fef481dee35f7b763165e488aa6cf9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68602569"
---
# <a name="tutorial-call-an-aspnet-core-web-api-with-jquery"></a><span data-ttu-id="9d78c-103">Tutorial: Aufrufen einer ASP.NET Core-Web-API mit jQuery</span><span class="sxs-lookup"><span data-stu-id="9d78c-103">Tutorial: Call an ASP.NET Core web API with jQuery</span></span>

<span data-ttu-id="9d78c-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9d78c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="9d78c-105">In diesem Tutorial erfahren Sie, wie Sie eine ASP.NET Core-Web-API mit jQuery aufrufen.</span><span class="sxs-lookup"><span data-stu-id="9d78c-105">This tutorial shows how to call an ASP.NET Core web API with jQuery</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9d78c-106">Informationen zur Verwendung von ASP.NET Core 2.2 finden Sie unter [Aufrufen einer Web-API mit jQuery](xref:tutorials/first-web-api#call-the-api-with-jquery).</span><span class="sxs-lookup"><span data-stu-id="9d78c-106">For ASP.NET Core 2.2, see the 2.2 version of [Call the Web API with jQuery](xref:tutorials/first-web-api#call-the-api-with-jquery).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="9d78c-107">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="9d78c-107">Prerequisites</span></span>

<span data-ttu-id="9d78c-108">Sie haben [Tutorial: Erstellen einer Web-API](xref:tutorials/first-web-api) abgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="9d78c-108">Complete [Tutorial: Create a web API](xref:tutorials/first-web-api)</span></span>

## <a name="call-the-api-with-jquery"></a><span data-ttu-id="9d78c-109">Aufrufen der API mit jQuery</span><span class="sxs-lookup"><span data-stu-id="9d78c-109">Call the API with jQuery</span></span>

<span data-ttu-id="9d78c-110">In diesem Abschnitt wird eine HTML-Seite hinzugefügt, die mithilfe von jQuery die Web-API aufruft.</span><span class="sxs-lookup"><span data-stu-id="9d78c-110">In this section, an HTML page is added that uses jQuery to call the web api.</span></span> <span data-ttu-id="9d78c-111">jQuery initiiert die Anforderung und aktualisiert die Seite mit den Informationen aus der Antwort der API.</span><span class="sxs-lookup"><span data-stu-id="9d78c-111">jQuery initiates the request and updates the page with the details from the API's response.</span></span>

<span data-ttu-id="9d78c-112">Konfigurieren Sie die App so, dass sie [statische Dateien bereitstellt](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_), und [aktivieren Sie die Standarddateizuordnung](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_), indem Sie *startup.cs* mit dem unten markierten Code aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="9d78c-112">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJquery.cs?highlight=8-9&name=snippet_configure)]

<span data-ttu-id="9d78c-113">Erstellen Sie im Projektverzeichnis den Ordner *wwwwroot*.</span><span class="sxs-lookup"><span data-stu-id="9d78c-113">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="9d78c-114">Fügen Sie dem Verzeichnis *wwwroot* eine HTML-Datei namens *index.html* hinzu.</span><span class="sxs-lookup"><span data-stu-id="9d78c-114">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="9d78c-115">Ersetzen Sie den Inhalt durch folgendes Markup:</span><span class="sxs-lookup"><span data-stu-id="9d78c-115">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

<span data-ttu-id="9d78c-116">Fügen Sie dem Verzeichnis *wwwroot* eine JavaScript-Datei namens *site.js* hinzu.</span><span class="sxs-lookup"><span data-stu-id="9d78c-116">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="9d78c-117">Ersetzen Sie den Inhalt durch folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="9d78c-117">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="9d78c-118">Möglicherweise ist eine Änderung an den Starteinstellungen des ASP.NET Core-Projekts erforderlich, um die HTML-Seite lokal zu testen:</span><span class="sxs-lookup"><span data-stu-id="9d78c-118">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="9d78c-119">Öffnen Sie *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="9d78c-119">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="9d78c-120">Entfernen Sie die `launchUrl`-Eigenschaft, um zu erzwingen, dass die App mit *index.html* als Startseite geöffnet wird. Dies ist die Standarddatei des Projekts.</span><span class="sxs-lookup"><span data-stu-id="9d78c-120">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="9d78c-121">Es gibt mehrere Möglichkeiten, um jQuery herunterzuladen.</span><span class="sxs-lookup"><span data-stu-id="9d78c-121">There are several ways to get jQuery.</span></span> <span data-ttu-id="9d78c-122">Im vorherigen Codeausschnitt wurde die Bibliothek aus einem Content Delivery Network (CDN) geladen.</span><span class="sxs-lookup"><span data-stu-id="9d78c-122">In the preceding snippet, the library is loaded from a CDN.</span></span>

<span data-ttu-id="9d78c-123">Dieses Beispiel ruft alle CRUD-Methoden der API auf.</span><span class="sxs-lookup"><span data-stu-id="9d78c-123">This sample calls all of the CRUD methods of the API.</span></span> <span data-ttu-id="9d78c-124">Im Folgenden werden die API-Aufrufe erläutert.</span><span class="sxs-lookup"><span data-stu-id="9d78c-124">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="9d78c-125">Abrufen einer Liste von To-Do-Elementen</span><span class="sxs-lookup"><span data-stu-id="9d78c-125">Get a list of to-do items</span></span>

<span data-ttu-id="9d78c-126">Die jQuery-Funktion [ajax](https://api.jquery.com/jquery.ajax/) sendet eine `GET`-Anforderung an die API, die JSON-Code zurückgibt, der ein Aufgabenarray darstellt.</span><span class="sxs-lookup"><span data-stu-id="9d78c-126">The jQuery [ajax](https://api.jquery.com/jquery.ajax/) function sends a `GET` request to the API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="9d78c-127">Die Rückruffunktion `success` wird aufgerufen, wenn die Anforderung erfolgreich ist.</span><span class="sxs-lookup"><span data-stu-id="9d78c-127">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="9d78c-128">Im Rückruf wird DOM mit den To-Do-Informationen aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="9d78c-128">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="9d78c-129">Hinzufügen eines To-Do-Elements</span><span class="sxs-lookup"><span data-stu-id="9d78c-129">Add a to-do item</span></span>

<span data-ttu-id="9d78c-130">Die Funktion [ajax](https://api.jquery.com/jquery.ajax/) sendet eine `POST`-Anforderung mit der Aufgabe im Anforderungstext.</span><span class="sxs-lookup"><span data-stu-id="9d78c-130">The [ajax](https://api.jquery.com/jquery.ajax/) function sends a `POST` request with the to-do item in the request body.</span></span> <span data-ttu-id="9d78c-131">Die Optionen `accepts` und `contentType` werden auf `application/json` festgelegt, um den gesendeten und empfangenen Medientyp anzugeben.</span><span class="sxs-lookup"><span data-stu-id="9d78c-131">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="9d78c-132">Die Aufgabe wird mit [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) in JSON konvertiert.</span><span class="sxs-lookup"><span data-stu-id="9d78c-132">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="9d78c-133">Wenn die API den Statuscode „Erfolgreich“ zurückgibt, wird die Funktion `getData` aufgerufen, um die HTML-Tabelle zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="9d78c-133">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="9d78c-134">Aktualisieren eines To-Do-Elements</span><span class="sxs-lookup"><span data-stu-id="9d78c-134">Update a to-do item</span></span>

<span data-ttu-id="9d78c-135">Das Aktualisieren einer Aufgabe ist vergleichbar mit dem Hinzufügen einer Aufgabe.</span><span class="sxs-lookup"><span data-stu-id="9d78c-135">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="9d78c-136">Die `url` ändert sich, um den eindeutigen Bezeichner des Elements hinzuzufügen. Der `type` lautet `PUT`.</span><span class="sxs-lookup"><span data-stu-id="9d78c-136">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="9d78c-137">Löschen eines To-Do-Elements</span><span class="sxs-lookup"><span data-stu-id="9d78c-137">Delete a to-do item</span></span>

<span data-ttu-id="9d78c-138">Eine Aufgabe wird folgendermaßen gelöscht: im AJAX-Aufruf wird `type` auf `DELETE` festgelegt, und der eindeutige Bezeichner des Elements wird in der URL angegeben.</span><span class="sxs-lookup"><span data-stu-id="9d78c-138">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

<span data-ttu-id="9d78c-139">Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie API-Hilfeseiten generieren:</span><span class="sxs-lookup"><span data-stu-id="9d78c-139">Advance to the next tutorial to learn how to generate API help pages:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>
::: moniker-end
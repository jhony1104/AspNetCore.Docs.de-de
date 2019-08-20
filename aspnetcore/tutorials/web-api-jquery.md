---
title: 'Tutorial: Aufrufen einer ASP.NET Core-Web-API mit jQuery'
author: rick-anderson
description: Erfahren Sie, wie Sie eine ASP.NET Core-Web-API mit jQuery aufrufen.
ms.author: riande
ms.custom: mvc
ms.date: 07/20/2019
uid: tutorials/web-api-jquery
ms.openlocfilehash: a319e4b4ce09e9b09afeaff065d5740276deb115
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2019
ms.locfileid: "69022563"
---
# <a name="tutorial-call-an-aspnet-core-web-api-with-jquery"></a>Tutorial: Aufrufen einer ASP.NET Core-Web-API mit jQuery

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

In diesem Tutorial erfahren Sie, wie Sie eine ASP.NET Core-Web-API mit jQuery aufrufen.

::: moniker range="< aspnetcore-3.0"

Informationen zur Verwendung von ASP.NET Core 2.2 finden Sie unter [Aufrufen einer Web-API mit jQuery](xref:tutorials/first-web-api#call-the-api-with-jquery).

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>Erforderliche Komponenten

* Sie haben [Tutorial: Erstellen einer Web-API](xref:tutorials/first-web-api) abgeschlossen.
* Vertrautheit mit CSS, HTML, JavaScript und jQuery

## <a name="call-the-api-with-jquery"></a>Aufrufen der API mit jQuery

In diesem Abschnitt wird eine HTML-Seite hinzugefügt, die mithilfe von jQuery die Web-API aufruft. jQuery initiiert die Anforderung und aktualisiert die Seite mit den Informationen aus der Antwort der API.

Konfigurieren Sie die App so, dass sie [statische Dateien bereitstellt](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_), und [aktivieren Sie die Standarddateizuordnung](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_), indem Sie *startup.cs* mit dem unten markierten Code aktualisieren.

[!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJquery.cs?highlight=8-9&name=snippet_configure)]

Erstellen Sie im Projektverzeichnis den Ordner *wwwwroot*.

Fügen Sie dem Verzeichnis *wwwroot* eine HTML-Datei namens *index.html* hinzu. Ersetzen Sie den Inhalt durch folgendes Markup:

[!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

Fügen Sie dem Verzeichnis *wwwroot* eine JavaScript-Datei namens *site.js* hinzu. Ersetzen Sie den Inhalt durch folgenden Code:

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

Möglicherweise ist eine Änderung an den Starteinstellungen des ASP.NET Core-Projekts erforderlich, um die HTML-Seite lokal zu testen:

* Öffnen Sie *Properties\launchSettings.json*.
* Entfernen Sie die `launchUrl`-Eigenschaft, um zu erzwingen, dass die App mit *index.html* als Startseite geöffnet wird. Dies ist die Standarddatei des Projekts.

Es gibt mehrere Möglichkeiten, um jQuery herunterzuladen. Im vorherigen Codeausschnitt wurde die Bibliothek aus einem Content Delivery Network (CDN) geladen.

Dieses Beispiel ruft alle CRUD-Methoden der API auf. Im Folgenden werden die API-Aufrufe erläutert.

### <a name="get-a-list-of-to-do-items"></a>Abrufen einer Liste von To-Do-Elementen

Die jQuery-Funktion [ajax](https://api.jquery.com/jquery.ajax/) sendet eine `GET`-Anforderung an die API, die JSON-Code zurückgibt, der ein Aufgabenarray darstellt. Die Rückruffunktion `success` wird aufgerufen, wenn die Anforderung erfolgreich ist. Im Rückruf wird DOM mit den To-Do-Informationen aktualisiert.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a>Hinzufügen eines To-Do-Elements

Die Funktion [ajax](https://api.jquery.com/jquery.ajax/) sendet eine `POST`-Anforderung mit der Aufgabe im Anforderungstext. Die Optionen `accepts` und `contentType` werden auf `application/json` festgelegt, um den gesendeten und empfangenen Medientyp anzugeben. Die Aufgabe wird mit [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) in JSON konvertiert. Wenn die API den Statuscode „Erfolgreich“ zurückgibt, wird die Funktion `getData` aufgerufen, um die HTML-Tabelle zu aktualisieren.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a>Aktualisieren eines To-Do-Elements

Das Aktualisieren einer Aufgabe ist vergleichbar mit dem Hinzufügen einer Aufgabe. Die `url` ändert sich, um den eindeutigen Bezeichner des Elements hinzuzufügen. Der `type` lautet `PUT`.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a>Löschen eines To-Do-Elements

Eine Aufgabe wird folgendermaßen gelöscht: im AJAX-Aufruf wird `type` auf `DELETE` festgelegt, und der eindeutige Bezeichner des Elements wird in der URL angegeben.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie API-Hilfeseiten generieren:

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>
::: moniker-end

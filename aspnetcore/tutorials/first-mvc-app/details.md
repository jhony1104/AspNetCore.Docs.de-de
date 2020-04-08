---
title: Untersuchen der Methoden „Details“ und „Delete“ einer ASP.NET Core-App
author: rick-anderson
description: Erfahren Sie mehr über die Controllermethode und Ansicht „Details“ in einer einfachen ASP.NET Core MVC-App.
ms.author: riande
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/details
ms.openlocfilehash: 04eb2efa4e67d84e575580a6248d0b5b567064af
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/06/2020
ms.locfileid: "78648865"
---
# <a name="examine-the-details-and-delete-methods-of-an-aspnet-core-app"></a>Untersuchen der Methoden „Details“ und „Delete“ einer ASP.NET Core-App

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

Öffnen Sie den „Movie“-Controller, und untersuchen Sie die `Details`-Methode.

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_details)]

Die MVC-Gerüstbau-Engine, die diese Aktionsmethode erstellt hat, fügt einen Kommentar mit einer HTTP-Anforderung hinzu, die die Methode aufruft. In diesem Fall ist dies eine GET-Anforderung mit drei URL-Segmenten: dem Controller `Movies`, der Methode `Details` und dem Wert von `id`. Wie bereits erwähnt, werden diese Segmente in *Startup.cs* definiert.

[!code-csharp[](start-mvc/sample/MvcMovie3/Startup.cs?highlight=5&name=snippet_1)]

EF erleichtert das Suchen nach Daten mithilfe der `FirstOrDefaultAsync`-Methode. Ein wichtiges in die Methode integriertes Sicherheitsmerkmal ist, dass der Code überprüft, ob die Suchmethode einen Film gefunden hat, bevor sie versucht, etwas damit zu tun. Ein Hacker kann beispielsweise Fehler in die Website einschleusen, indem die von den Links erstellte URL von `http://localhost:{PORT}/Movies/Details/1` in etwas wie `http://localhost:{PORT}/Movies/Details/12345` geändert wird (oder einen anderen Wert, der keinen tatsächlichen Film darstellt). Wenn Sie nicht nach einem NULL-Movie gesucht haben, löst die App keine Ausnahme aus.

Untersuchen Sie die Methoden `Delete` und `DeleteConfirmed`.

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_delete)]

Beachten Sie, dass die `HTTP GET Delete`-Methode den angegebenen Film nicht löscht, sondern eine Ansicht des Films zurückgibt, in der Sie den Löschvorgang (HttpPost) auslösen können. Das Ausführen eines Löschvorgangs als Reaktion auf eine GET-Anforderung (oder eigentlich eines Bearbeitungs-, Erstellungs- oder sonstigen Vorgangs, der Daten ändern) stellt eine Sicherheitslücke dar.

Die `[HttpPost]`-Methode, die die Daten löscht, heißt `DeleteConfirmed`, um der HTTP-POST-Methode eine eindeutige Signatur bzw. einen eindeutigen Namen zu geben. Die beiden Methodensignaturen werden nachstehend gezeigt:

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete2)]

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete3)]

Die Common Language Runtime (CLR) erfordert überladene Methoden, um eine eindeutige Parametersignatur zu erhalten (selber Methodenname, aber unterschiedliche Liste von Parametern). Hier benötigen Sie jedoch zwei `Delete`-Methoden (eine für GET und eine für POST), die beide die gleiche Parametersignatur haben. (Beide müssen eine einzelne ganze Zahl als Parameter akzeptieren.)

Es gibt zwei Ansätze zur Lösung dieses Problems. Eine besteht darin, die Methoden unterschiedlich zu benennen. Dafür das der Gerüstbaumechanismus im vorherigen Beispiel gesorgt. Dies bringt jedoch ein kleines Problem mit sich: ASP.NET ordnet Segmente einer URL anhand des Namens zu Aktionsmethoden zu. Wenn Sie die Methode umbenennen sollten, ist das Routing normalerweise nicht in der Lage, diese Methode zu finden. Die Lösung besteht (wie im Beispiel) im Hinzufügen des `ActionName("Delete")`-Attributs zur `DeleteConfirmed`-Methode. Dieses Attribut führt die Zuordnung für das Routingsystem durch, sodass eine URL, die „/Delete/“ für eine POST-Anforderung enthält, die `DeleteConfirmed`-Methode findet.

Eine weitere gängige Behelfslösung für Methoden mit identischen Namen und Signaturen ist das künstliche Ändern der POST-Methode durch Hinzufügen eines zusätzlichen (nicht verwendeten) Parameters. Siehe dazu die vorherige POST-Anforderung, der wir den `notUsed`-Parameter hinzugefügt haben. Sie können dasselbe hier für die `[HttpPost] Delete`-Methode ausführen:

```csharp
// POST: Movies/Delete/6
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Delete(int id, bool notUsed)
```

### <a name="publish-to-azure"></a>Veröffentlichen in Azure

Informationen zur Bereitstellung in Azure finden Sie unter [Tutorial: Erstellen einer .NET Core- und SQL-Datenbank-Web-App in Azure App Service](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).

> [!div class="step-by-step"]
> [Zurück](validation.md)

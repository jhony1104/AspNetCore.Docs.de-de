---
uid: mvc/overview/getting-started/introduction/adding-search
title: Suche | Microsoft-Dokumentation
author: Rick-Anderson
description: ''
ms.author: riande
ms.date: 05/22/2015
ms.assetid: df001954-18bf-4550-b03d-43911a0ea186
msc.legacyurl: /mvc/overview/getting-started/introduction/adding-search
msc.type: authoredcontent
ms.openlocfilehash: 31fd35ac63f3eb31d824e1710833ad83a0852ac9
ms.sourcegitcommit: a91e8dd2f4b788114c8bc834507277f4b5e8d6c5
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2019
ms.locfileid: "55712262"
---
<a name="search"></a>Suchen
====================
durch [Rick Anderson]((https://twitter.com/RickAndMSFT))

[!INCLUDE [Tutorial Note](sample/code-location.md)]

## <a name="adding-a-search-method-and-search-view"></a>Hinzufügen eines Search-Methode und des Anzeigebereichs

In diesem Abschnitt fügen Sie die Suchfunktion auf der `Index` Aktionsmethode, mit der Sie, suchen, Filme nach Genre oder Namen.

## <a name="updating-the-index-form"></a>Aktualisieren des Index-Formulars

Aktualisieren Sie zunächst die `Index` Aktionsmethode mit dem vorhandenen `MoviesController` Klasse. Hier ist der Code ein:

[!code-csharp[Main](adding-search/samples/sample1.cs?highlight=1,6-9)]

Die erste Zeile der `Index` Methode erstellt die folgenden [LINQ](https://msdn.microsoft.com/library/bb397926.aspx) Abfrage zum Auswählen der Filme:

[!code-csharp[Main](adding-search/samples/sample2.cs)]

Die Abfrage wird an diesem Punkt definiert, aber noch nicht für die Datenbank noch ausgeführt wurde.

Wenn die `searchString` Parameter eine Zeichenfolge enthält, wird die filmabfrage geändert, um den Wert der Suchzeichenfolge, mit dem folgenden Code zu filtern:

[!code-csharp[Main](adding-search/samples/sample3.cs)]

Der Code `s => s.Title` oben ist ein [Lambdaausdruck](https://msdn.microsoft.com/library/bb397687.aspx). Lambdas werden in methodenbasierten verwendet [LINQ](https://msdn.microsoft.com/library/bb397926.aspx) -Abfragen als Argumente für Standardabfrageoperator-Methoden wie z. B. die [, in denen](https://msdn.microsoft.com/library/system.linq.enumerable.where.aspx) im obigen Code verwendete Methode. LINQ-Abfragen werden nicht ausgeführt werden, wenn sie definiert werden, oder wenn sie geändert werden, durch Aufrufen einer Methode wie z. B. `Where` oder `OrderBy`. Stattdessen wird die abfrageausführung verzögert, was bedeutet, dass die Auswertung eines Ausdrucks hinausgezögert wird, bis dessen realisierte Wert tatsächlich durchlaufen oder die [ `ToList` ](https://msdn.microsoft.com/library/bb342261.aspx) Methode wird aufgerufen. In der `Search` Beispiel wird die Abfrage wird ausgeführt, der *"Index.cshtml"* anzeigen. Weitere Informationen zur verzögerten Abfrageausführung finden Sie unter [Abfrageausführung](https://msdn.microsoft.com/library/bb738633.aspx).

> [!NOTE]
> Die [Contains](https://msdn.microsoft.com/library/bb155125.aspx) Methode für die Datenbank, die nicht den c#-Code oben ausgeführt wird. In der Datenbank [Contains](https://msdn.microsoft.com/library/bb155125.aspx) ordnet [SQL LIKE](https://msdn.microsoft.com/library/ms179859.aspx), dies ist die Groß-/Kleinschreibung.

Nun können Sie aktualisieren die `Index` anzeigen, die das Formular für den Benutzer angezeigt werden.

Führen Sie die Anwendung, und navigieren Sie zu */Filme/Index*. Fügen Sie eine Abfragezeichenfolge wie `?searchString=ghost` an die URL an. Die gefilterten Filme werden angezeigt.

![SearchQryStr](adding-search/_static/image1.png)

Wenn Sie die Signatur des Ändern der `Index` Methode zu einem Parameter mit dem Namen `id`, wird die `id` Parameter entspricht der `{id}` Platzhalter für den standardmäßigen leitet Menge in der *App\_Start RouteConfig.cs* Datei.

[!code-json[Main](adding-search/samples/sample4.json)]

Die ursprüngliche `Index` Methode sieht wie folgt aus:

[!code-csharp[Main](adding-search/samples/sample5.cs)]

Die geänderte `Index` Methode würde wie folgt aussehen:

[!code-csharp[Main](adding-search/samples/sample6.cs?highlight=1,3)]

Sie können nun den Suchtitel als Routendaten (ein URL-Segment) anstatt als Wert einer Abfragezeichenfolge übergeben.

![](adding-search/_static/image2.png)

Sie können jedoch von den Benutzern nicht erwarten, dass sie jedes Mal die URL ändern, wenn sie nach einem Film suchen möchten. Nun Sie Benutzeroberfläche helfen hinzufügen, diese Filme zu filtern. Wenn Sie die Signatur des geändert haben die `Index` Methode zum Testen den routengebundenen ID-Parameter übergeben. ändern Sie ihn, damit Ihre `Index` Methode nimmt einen Zeichenfolgenparameter namens `searchString`:

[!code-csharp[Main](adding-search/samples/sample7.cs)]

Öffnen der *Views\Movies\Index.cshtml* , und stellen Sie direkt nach `@Html.ActionLink("Create New", "Create")`, fügen Sie das Formularmarkup im folgenden aufgeführt:

[!code-cshtml[Main](adding-search/samples/sample8.cshtml?highlight=12-15)]

Die `Html.BeginForm` Hilfsprogramm erstellt ein öffnendes `<form>` Tag. Die `Html.BeginForm` Helper wird das Formular an sich selbst zurückgesendet werden soll, wenn der Benutzer das Formular durch Klicken auf sendet die **Filter** Schaltfläche.

Visual Studio 2013 bietet eine erfreuliche Verbesserung bei der Anzeige und Bearbeitung von Dateien anzeigen. Wenn Sie die Anwendung mit einer Ansichtsdatei Öffnen ausführen, ruft Visual Studio 2013 die richtigen Controlleraktionsmethode, um die Ansicht anzuzeigen.

![](adding-search/_static/image3.png)

Klicken Sie mit Ansicht "Index" Öffnen in Visual Studio (wie in der Abbildung oben gezeigt), und tippen Sie auf Ctr F5 oder F5, um die Anwendung auszuführen, und wiederholen dann nach einem Film suchen.

![](adding-search/_static/image4.png)

Es gibt keine `HttpPost` Überladung von der `Index` Methode. Sie erforderlich nicht, da die Methode den Zustand der Anwendung ändern, ist nicht nur das Filtern von Daten.

Sie können die folgende `HttpPost Index`-Methode hinzufügen. In diesem Fall die Instanz zum Aufrufen der Aktion entsprechen der `HttpPost Index` -Methode, und die `HttpPost Index` Methode wird ausgeführt, wie in der folgenden Abbildung dargestellt.

[!code-csharp[Main](adding-search/samples/sample9.cs)]

![SearchPostGhost](adding-search/_static/image5.png)

Doch selbst wenn Sie diese `HttpPost`-Version der `Index`-Methode hinzufügen, gibt es eine Einschränkung für die gesamte Implementierung. Stellen Sie sich vor, Sie möchten eine bestimmte Suche als Favorit speichern oder einen Link an Freunde senden, auf den diese klicken können, um dieselbe gefilterte Liste von Filmen anzuzeigen. Beachten Sie, dass die URL für die HTTP-POST-Anforderung die URL für die GET-Anforderung (localhost: xxxxx/Filme/Index) identisch ist – es keine Suchinformationen in der URL selbst sind. Rechts wird Informationen in der Suchzeichenfolge nun als einen Formularfeldwert an den Server gesendet. Dies bedeutet, dass Sie diese Suchinformationen Lesezeichen oder Senden an Freunde, in einer URL nicht erfassen können.

Die Lösung besteht darin, eine Überladung verwenden `BeginForm` , der angibt, dass die POST-Anforderung sollte der URL die Suchinformationen hinzufügen und ihn weitergeleitet werden soll die `HttpGet` Version der `Index` Methode. Ersetzen Sie die vorhandene parameterlosen `BeginForm` Methode mit folgendem Markup:

[!code-cshtml[Main](adding-search/samples/sample10.cshtml)]

![BeginFormPost_SM](adding-search/_static/image6.png)

Wenn Sie eine Suche übermitteln, enthält die URL jetzt eine Suchzeichenfolge für die Abfrage an. Das Suchen wird auch an Aktionsmethode `HttpGet Index` übertragen, auch wenn Sie eine `HttpPost Index`-Methode haben.

![IndexWithGetURL](adding-search/_static/image7.png)

## <a name="adding-search-by-genre"></a>Hinzufügen der Suche nach Genre

Wenn Sie hinzugefügt haben die `HttpPost` Version der `Index` -Methode, löschen Sie sie jetzt.

Als Nächstes fügen Sie eine Funktion, damit Benutzer, die für Filme nach Genre suchen können. Ersetzen Sie die `Index`-Methode durch folgenden Code:

[!code-csharp[Main](adding-search/samples/sample11.cs)]

Diese Version von der `Index` Methode nimmt einen zusätzlichen Parameter, nämlich `movieGenre`. Erstellen Sie die ersten Zeilen des Codes eine `List` Objekt zum Speichern der Filmgenres aus der Datenbank.

Der folgende Code ist eine LINQ-Abfrage, die alle Genres aus der Datenbank abruft.

[!code-csharp[Main](adding-search/samples/sample12.cs)]

Der Code verwendet die `AddRange` Methode der generischen `List` Auflistung, die alle der unterschiedlichen Genres zur Liste hinzugefügt. (Ohne die `Distinct` Modifizierer, doppelte Genres hinzugefügt – zweimal in unserem Beispiel würde z. B. Komödie hinzugefügt werden). Der Code speichert dann die Liste der Genres in die `ViewBag.MovieGenre` Objekt. Speichern von Kategoriedaten (solche eine Filmgenre des) als eine [SelectList](https://msdn.microsoft.cus/library/system.web.mvc.selectlist(v=vs.108).aspx) -Objekt in ein `ViewBag`, und klicken Sie dann den Zugriff auf die Kategoriedaten in einem Dropdown-Listenfeld ein typischer Ansatz für MVC-Anwendungen ist.

Der folgende Code zeigt, wie Sie überprüfen die `movieGenre` Parameter. Wenn es nicht leer ist, schränkt der Code weiter die filmabfrage, um die ausgewählten Filme, die bestimmten Genre zu beschränken.

[!code-csharp[Main](adding-search/samples/sample13.cs)]

Wie bereits erwähnt, die Abfrage wird nicht ausgeführt für die Datenbank bis die Filmliste durchlaufen (das geschieht in der Ansicht nach der `Index` Aktionsmethode zurückgegeben).

## <a name="adding-markup-to-the-index-view-to-support-search-by-genre"></a>Hinzufügen von Markup für die Ansicht "Index", um die Suche nach Genre zu unterstützen

Hinzufügen einer `Html.DropDownList` Hilfsmethode zum die *Views\Movies\Index.cshtml* Datei kurz vor dem Ausführen der `TextBox` Helper. Das vollständige Markup wird unten gezeigt:

[!code-cshtml[Main](adding-search/samples/sample14.cshtml?highlight=11)]

Im folgenden Code:

[!code-cshtml[Main](adding-search/samples/sample15.cshtml)]

Der Parameter "MovieGenre" enthält den Schlüssel für die `DropDownList` Hilfsmethode zum Suchen einer `IEnumerable<SelectListItem>` in die `ViewBag`. Die `ViewBag` in der Aktionsmethode aufgefüllt wurde:

[!code-csharp[Main](adding-search/samples/sample16.cs?highlight=10)]

Der Parameter "All" stellt eine optionsbezeichnung bereit. Wenn Sie diese Auswahl in Ihrem Browser untersuchen, sehen Sie sich, dass das Attribut "Value" leer ist. Da unser Controller nur zum Filtern `if` die Zeichenfolge ist kein `null` oder leer ist, senden einen leeren Wert `movieGenre` zeigt alle Genres.

Sie können auch eine Option aus, um die standardmäßig ausgewählt festlegen. Wenn Sie "Komödie" als Standardwert verwenden möchten, ändern Sie den Code im Controller wie folgt:

[!code-cshtml[Main](adding-search/samples/sample17.cshtml)]

Führen Sie die Anwendung, und navigieren Sie zu */Filme/Index*. Versuchen Sie eine Suche nach Genre, Filmname und von beiden Kriterien.

![](adding-search/_static/image8.png)

In diesem Abschnitt haben Sie erstellt eine Suchmethode für Aktion und Ansicht, die Benutzern das Suchen von Filmtitel und Genre zu ermöglichen. Im nächsten Abschnitt, betrachten Sie eine Eigenschaft zum Hinzufügen der `Movie` Modell und einen Initialisierer hinzufügen, die automatisch eine Testdatenbank erstellen.

> [!div class="step-by-step"]
> [Zurück](examining-the-edit-methods-and-edit-view.md)
> [Weiter](adding-a-new-field.md)

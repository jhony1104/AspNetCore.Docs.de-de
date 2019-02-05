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
<a name="search"></a><span data-ttu-id="7c83b-102">Suchen</span><span class="sxs-lookup"><span data-stu-id="7c83b-102">Search</span></span>
====================
<span data-ttu-id="7c83b-103">durch [Rick Anderson]((https://twitter.com/RickAndMSFT))</span><span class="sxs-lookup"><span data-stu-id="7c83b-103">by [Rick Anderson]((https://twitter.com/RickAndMSFT))</span></span>

[!INCLUDE [Tutorial Note](sample/code-location.md)]

## <a name="adding-a-search-method-and-search-view"></a><span data-ttu-id="7c83b-104">Hinzufügen eines Search-Methode und des Anzeigebereichs</span><span class="sxs-lookup"><span data-stu-id="7c83b-104">Adding a Search Method and Search View</span></span>

<span data-ttu-id="7c83b-105">In diesem Abschnitt fügen Sie die Suchfunktion auf der `Index` Aktionsmethode, mit der Sie, suchen, Filme nach Genre oder Namen.</span><span class="sxs-lookup"><span data-stu-id="7c83b-105">In this section you'll add search capability to the `Index` action method that lets you search movies by genre or name.</span></span>

## <a name="updating-the-index-form"></a><span data-ttu-id="7c83b-106">Aktualisieren des Index-Formulars</span><span class="sxs-lookup"><span data-stu-id="7c83b-106">Updating the Index Form</span></span>

<span data-ttu-id="7c83b-107">Aktualisieren Sie zunächst die `Index` Aktionsmethode mit dem vorhandenen `MoviesController` Klasse.</span><span class="sxs-lookup"><span data-stu-id="7c83b-107">Start by updating the `Index` action method to the existing `MoviesController` class.</span></span> <span data-ttu-id="7c83b-108">Hier ist der Code ein:</span><span class="sxs-lookup"><span data-stu-id="7c83b-108">Here's the code:</span></span>

[!code-csharp[Main](adding-search/samples/sample1.cs?highlight=1,6-9)]

<span data-ttu-id="7c83b-109">Die erste Zeile der `Index` Methode erstellt die folgenden [LINQ](https://msdn.microsoft.com/library/bb397926.aspx) Abfrage zum Auswählen der Filme:</span><span class="sxs-lookup"><span data-stu-id="7c83b-109">The first line of the `Index` method creates the following [LINQ](https://msdn.microsoft.com/library/bb397926.aspx) query to select the movies:</span></span>

[!code-csharp[Main](adding-search/samples/sample2.cs)]

<span data-ttu-id="7c83b-110">Die Abfrage wird an diesem Punkt definiert, aber noch nicht für die Datenbank noch ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="7c83b-110">The query is defined at this point, but hasn't yet been run against the database.</span></span>

<span data-ttu-id="7c83b-111">Wenn die `searchString` Parameter eine Zeichenfolge enthält, wird die filmabfrage geändert, um den Wert der Suchzeichenfolge, mit dem folgenden Code zu filtern:</span><span class="sxs-lookup"><span data-stu-id="7c83b-111">If the `searchString` parameter contains a string, the movies query is modified to filter on the value of the search string, using the following code:</span></span>

[!code-csharp[Main](adding-search/samples/sample3.cs)]

<span data-ttu-id="7c83b-112">Der Code `s => s.Title` oben ist ein [Lambdaausdruck](https://msdn.microsoft.com/library/bb397687.aspx).</span><span class="sxs-lookup"><span data-stu-id="7c83b-112">The `s => s.Title` code above is a [Lambda Expression](https://msdn.microsoft.com/library/bb397687.aspx).</span></span> <span data-ttu-id="7c83b-113">Lambdas werden in methodenbasierten verwendet [LINQ](https://msdn.microsoft.com/library/bb397926.aspx) -Abfragen als Argumente für Standardabfrageoperator-Methoden wie z. B. die [, in denen](https://msdn.microsoft.com/library/system.linq.enumerable.where.aspx) im obigen Code verwendete Methode.</span><span class="sxs-lookup"><span data-stu-id="7c83b-113">Lambdas are used in method-based [LINQ](https://msdn.microsoft.com/library/bb397926.aspx) queries as arguments to standard query operator methods such as the [Where](https://msdn.microsoft.com/library/system.linq.enumerable.where.aspx) method used in the above code.</span></span> <span data-ttu-id="7c83b-114">LINQ-Abfragen werden nicht ausgeführt werden, wenn sie definiert werden, oder wenn sie geändert werden, durch Aufrufen einer Methode wie z. B. `Where` oder `OrderBy`.</span><span class="sxs-lookup"><span data-stu-id="7c83b-114">LINQ queries are not executed when they are defined or when they are modified by calling a method such as `Where` or `OrderBy`.</span></span> <span data-ttu-id="7c83b-115">Stattdessen wird die abfrageausführung verzögert, was bedeutet, dass die Auswertung eines Ausdrucks hinausgezögert wird, bis dessen realisierte Wert tatsächlich durchlaufen oder die [ `ToList` ](https://msdn.microsoft.com/library/bb342261.aspx) Methode wird aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="7c83b-115">Instead, query execution is deferred, which means that the evaluation of an expression is delayed until its realized value is actually iterated over or the [`ToList`](https://msdn.microsoft.com/library/bb342261.aspx) method is called.</span></span> <span data-ttu-id="7c83b-116">In der `Search` Beispiel wird die Abfrage wird ausgeführt, der *"Index.cshtml"* anzeigen.</span><span class="sxs-lookup"><span data-stu-id="7c83b-116">In the `Search` sample, the query is executed in the *Index.cshtml* view.</span></span> <span data-ttu-id="7c83b-117">Weitere Informationen zur verzögerten Abfrageausführung finden Sie unter [Abfrageausführung](https://msdn.microsoft.com/library/bb738633.aspx).</span><span class="sxs-lookup"><span data-stu-id="7c83b-117">For more information about deferred query execution, see [Query Execution](https://msdn.microsoft.com/library/bb738633.aspx).</span></span>

> [!NOTE]
> <span data-ttu-id="7c83b-118">Die [Contains](https://msdn.microsoft.com/library/bb155125.aspx) Methode für die Datenbank, die nicht den c#-Code oben ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="7c83b-118">The [Contains](https://msdn.microsoft.com/library/bb155125.aspx) method is run on the database, not the c# code above.</span></span> <span data-ttu-id="7c83b-119">In der Datenbank [Contains](https://msdn.microsoft.com/library/bb155125.aspx) ordnet [SQL LIKE](https://msdn.microsoft.com/library/ms179859.aspx), dies ist die Groß-/Kleinschreibung.</span><span class="sxs-lookup"><span data-stu-id="7c83b-119">On the database, [Contains](https://msdn.microsoft.com/library/bb155125.aspx) maps to [SQL LIKE](https://msdn.microsoft.com/library/ms179859.aspx), which is case insensitive.</span></span>

<span data-ttu-id="7c83b-120">Nun können Sie aktualisieren die `Index` anzeigen, die das Formular für den Benutzer angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="7c83b-120">Now you can update the `Index` view that will display the form to the user.</span></span>

<span data-ttu-id="7c83b-121">Führen Sie die Anwendung, und navigieren Sie zu */Filme/Index*.</span><span class="sxs-lookup"><span data-stu-id="7c83b-121">Run the application and navigate to */Movies/Index*.</span></span> <span data-ttu-id="7c83b-122">Fügen Sie eine Abfragezeichenfolge wie `?searchString=ghost` an die URL an.</span><span class="sxs-lookup"><span data-stu-id="7c83b-122">Append a query string such as `?searchString=ghost` to the URL.</span></span> <span data-ttu-id="7c83b-123">Die gefilterten Filme werden angezeigt.</span><span class="sxs-lookup"><span data-stu-id="7c83b-123">The filtered movies are displayed.</span></span>

![SearchQryStr](adding-search/_static/image1.png)

<span data-ttu-id="7c83b-125">Wenn Sie die Signatur des Ändern der `Index` Methode zu einem Parameter mit dem Namen `id`, wird die `id` Parameter entspricht der `{id}` Platzhalter für den standardmäßigen leitet Menge in der *App\_Start RouteConfig.cs* Datei.</span><span class="sxs-lookup"><span data-stu-id="7c83b-125">If you change the signature of the `Index` method to have a parameter named `id`, the `id` parameter will match the `{id}` placeholder for the default routes set in the *App\_Start\RouteConfig.cs* file.</span></span>

[!code-json[Main](adding-search/samples/sample4.json)]

<span data-ttu-id="7c83b-126">Die ursprüngliche `Index` Methode sieht wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="7c83b-126">The original `Index` method looks like this::</span></span>

[!code-csharp[Main](adding-search/samples/sample5.cs)]

<span data-ttu-id="7c83b-127">Die geänderte `Index` Methode würde wie folgt aussehen:</span><span class="sxs-lookup"><span data-stu-id="7c83b-127">The modified `Index` method would look as follows:</span></span>

[!code-csharp[Main](adding-search/samples/sample6.cs?highlight=1,3)]

<span data-ttu-id="7c83b-128">Sie können nun den Suchtitel als Routendaten (ein URL-Segment) anstatt als Wert einer Abfragezeichenfolge übergeben.</span><span class="sxs-lookup"><span data-stu-id="7c83b-128">You can now pass the search title as route data (a URL segment) instead of as a query string value.</span></span>

![](adding-search/_static/image2.png)

<span data-ttu-id="7c83b-129">Sie können jedoch von den Benutzern nicht erwarten, dass sie jedes Mal die URL ändern, wenn sie nach einem Film suchen möchten.</span><span class="sxs-lookup"><span data-stu-id="7c83b-129">However, you can't expect users to modify the URL every time they want to search for a movie.</span></span> <span data-ttu-id="7c83b-130">Nun Sie Benutzeroberfläche helfen hinzufügen, diese Filme zu filtern.</span><span class="sxs-lookup"><span data-stu-id="7c83b-130">So now you you'll add UI to help them filter movies.</span></span> <span data-ttu-id="7c83b-131">Wenn Sie die Signatur des geändert haben die `Index` Methode zum Testen den routengebundenen ID-Parameter übergeben. ändern Sie ihn, damit Ihre `Index` Methode nimmt einen Zeichenfolgenparameter namens `searchString`:</span><span class="sxs-lookup"><span data-stu-id="7c83b-131">If you changed the signature of the `Index` method to test how to pass the route-bound ID parameter, change it back so that your `Index` method takes a string parameter named `searchString`:</span></span>

[!code-csharp[Main](adding-search/samples/sample7.cs)]

<span data-ttu-id="7c83b-132">Öffnen der *Views\Movies\Index.cshtml* , und stellen Sie direkt nach `@Html.ActionLink("Create New", "Create")`, fügen Sie das Formularmarkup im folgenden aufgeführt:</span><span class="sxs-lookup"><span data-stu-id="7c83b-132">Open the *Views\Movies\Index.cshtml* file, and just after `@Html.ActionLink("Create New", "Create")`, add the form markup highlighted below:</span></span>

[!code-cshtml[Main](adding-search/samples/sample8.cshtml?highlight=12-15)]

<span data-ttu-id="7c83b-133">Die `Html.BeginForm` Hilfsprogramm erstellt ein öffnendes `<form>` Tag.</span><span class="sxs-lookup"><span data-stu-id="7c83b-133">The `Html.BeginForm` helper creates an opening `<form>` tag.</span></span> <span data-ttu-id="7c83b-134">Die `Html.BeginForm` Helper wird das Formular an sich selbst zurückgesendet werden soll, wenn der Benutzer das Formular durch Klicken auf sendet die **Filter** Schaltfläche.</span><span class="sxs-lookup"><span data-stu-id="7c83b-134">The `Html.BeginForm` helper causes the form to post to itself when the user submits the form by clicking the **Filter** button.</span></span>

<span data-ttu-id="7c83b-135">Visual Studio 2013 bietet eine erfreuliche Verbesserung bei der Anzeige und Bearbeitung von Dateien anzeigen.</span><span class="sxs-lookup"><span data-stu-id="7c83b-135">Visual Studio 2013 has a nice improvement when displaying and editing View files.</span></span> <span data-ttu-id="7c83b-136">Wenn Sie die Anwendung mit einer Ansichtsdatei Öffnen ausführen, ruft Visual Studio 2013 die richtigen Controlleraktionsmethode, um die Ansicht anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="7c83b-136">When you run the application with a view file open, Visual Studio 2013 invokes the correct controller action method to display the view.</span></span>

![](adding-search/_static/image3.png)

<span data-ttu-id="7c83b-137">Klicken Sie mit Ansicht "Index" Öffnen in Visual Studio (wie in der Abbildung oben gezeigt), und tippen Sie auf Ctr F5 oder F5, um die Anwendung auszuführen, und wiederholen dann nach einem Film suchen.</span><span class="sxs-lookup"><span data-stu-id="7c83b-137">With the Index view open in Visual Studio (as shown in the image above), tap Ctr F5 or F5 to run the application and then try searching for a movie.</span></span>

![](adding-search/_static/image4.png)

<span data-ttu-id="7c83b-138">Es gibt keine `HttpPost` Überladung von der `Index` Methode.</span><span class="sxs-lookup"><span data-stu-id="7c83b-138">There's no `HttpPost` overload of the `Index` method.</span></span> <span data-ttu-id="7c83b-139">Sie erforderlich nicht, da die Methode den Zustand der Anwendung ändern, ist nicht nur das Filtern von Daten.</span><span class="sxs-lookup"><span data-stu-id="7c83b-139">You don't need it, because the method isn't changing the state of the application, just filtering data.</span></span>

<span data-ttu-id="7c83b-140">Sie können die folgende `HttpPost Index`-Methode hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="7c83b-140">You could add the following `HttpPost Index` method.</span></span> <span data-ttu-id="7c83b-141">In diesem Fall die Instanz zum Aufrufen der Aktion entsprechen der `HttpPost Index` -Methode, und die `HttpPost Index` Methode wird ausgeführt, wie in der folgenden Abbildung dargestellt.</span><span class="sxs-lookup"><span data-stu-id="7c83b-141">In that case, the action invoker would match the `HttpPost Index` method, and the `HttpPost Index` method would run as shown in the image below.</span></span>

[!code-csharp[Main](adding-search/samples/sample9.cs)]

![SearchPostGhost](adding-search/_static/image5.png)

<span data-ttu-id="7c83b-143">Doch selbst wenn Sie diese `HttpPost`-Version der `Index`-Methode hinzufügen, gibt es eine Einschränkung für die gesamte Implementierung.</span><span class="sxs-lookup"><span data-stu-id="7c83b-143">However, even if you add this `HttpPost` version of the `Index` method, there's a limitation in how this has all been implemented.</span></span> <span data-ttu-id="7c83b-144">Stellen Sie sich vor, Sie möchten eine bestimmte Suche als Favorit speichern oder einen Link an Freunde senden, auf den diese klicken können, um dieselbe gefilterte Liste von Filmen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="7c83b-144">Imagine that you want to bookmark a particular search or you want to send a link to friends that they can click in order to see the same filtered list of movies.</span></span> <span data-ttu-id="7c83b-145">Beachten Sie, dass die URL für die HTTP-POST-Anforderung die URL für die GET-Anforderung (localhost: xxxxx/Filme/Index) identisch ist – es keine Suchinformationen in der URL selbst sind.</span><span class="sxs-lookup"><span data-stu-id="7c83b-145">Notice that the URL for the HTTP POST request is the same as the URL for the GET request (localhost:xxxxx/Movies/Index) -- there's no search information in the URL itself.</span></span> <span data-ttu-id="7c83b-146">Rechts wird Informationen in der Suchzeichenfolge nun als einen Formularfeldwert an den Server gesendet.</span><span class="sxs-lookup"><span data-stu-id="7c83b-146">Right now, the search string information is sent to the server as a form field value.</span></span> <span data-ttu-id="7c83b-147">Dies bedeutet, dass Sie diese Suchinformationen Lesezeichen oder Senden an Freunde, in einer URL nicht erfassen können.</span><span class="sxs-lookup"><span data-stu-id="7c83b-147">This means you can't capture that search information to bookmark or send to friends in a URL.</span></span>

<span data-ttu-id="7c83b-148">Die Lösung besteht darin, eine Überladung verwenden `BeginForm` , der angibt, dass die POST-Anforderung sollte der URL die Suchinformationen hinzufügen und ihn weitergeleitet werden soll die `HttpGet` Version der `Index` Methode.</span><span class="sxs-lookup"><span data-stu-id="7c83b-148">The solution is to use an overload of `BeginForm` that specifies that the POST request should add the search information to the URL and that it should be routed to the `HttpGet` version of the `Index` method.</span></span> <span data-ttu-id="7c83b-149">Ersetzen Sie die vorhandene parameterlosen `BeginForm` Methode mit folgendem Markup:</span><span class="sxs-lookup"><span data-stu-id="7c83b-149">Replace the existing parameterless `BeginForm` method with the following markup:</span></span>

[!code-cshtml[Main](adding-search/samples/sample10.cshtml)]

![BeginFormPost_SM](adding-search/_static/image6.png)

<span data-ttu-id="7c83b-151">Wenn Sie eine Suche übermitteln, enthält die URL jetzt eine Suchzeichenfolge für die Abfrage an.</span><span class="sxs-lookup"><span data-stu-id="7c83b-151">Now when you submit a search, the URL contains a search query string.</span></span> <span data-ttu-id="7c83b-152">Das Suchen wird auch an Aktionsmethode `HttpGet Index` übertragen, auch wenn Sie eine `HttpPost Index`-Methode haben.</span><span class="sxs-lookup"><span data-stu-id="7c83b-152">Searching will also go to the `HttpGet Index` action method, even if you have a `HttpPost Index` method.</span></span>

![IndexWithGetURL](adding-search/_static/image7.png)

## <a name="adding-search-by-genre"></a><span data-ttu-id="7c83b-154">Hinzufügen der Suche nach Genre</span><span class="sxs-lookup"><span data-stu-id="7c83b-154">Adding Search by Genre</span></span>

<span data-ttu-id="7c83b-155">Wenn Sie hinzugefügt haben die `HttpPost` Version der `Index` -Methode, löschen Sie sie jetzt.</span><span class="sxs-lookup"><span data-stu-id="7c83b-155">If you added the `HttpPost` version of the `Index` method, delete it now.</span></span>

<span data-ttu-id="7c83b-156">Als Nächstes fügen Sie eine Funktion, damit Benutzer, die für Filme nach Genre suchen können.</span><span class="sxs-lookup"><span data-stu-id="7c83b-156">Next, you'll add a feature to let users search for movies by genre.</span></span> <span data-ttu-id="7c83b-157">Ersetzen Sie die `Index`-Methode durch folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="7c83b-157">Replace the `Index` method with the following code:</span></span>

[!code-csharp[Main](adding-search/samples/sample11.cs)]

<span data-ttu-id="7c83b-158">Diese Version von der `Index` Methode nimmt einen zusätzlichen Parameter, nämlich `movieGenre`.</span><span class="sxs-lookup"><span data-stu-id="7c83b-158">This version of the `Index` method takes an additional parameter, namely `movieGenre`.</span></span> <span data-ttu-id="7c83b-159">Erstellen Sie die ersten Zeilen des Codes eine `List` Objekt zum Speichern der Filmgenres aus der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="7c83b-159">The first few lines of code create a `List` object to hold movie genres from the database.</span></span>

<span data-ttu-id="7c83b-160">Der folgende Code ist eine LINQ-Abfrage, die alle Genres aus der Datenbank abruft.</span><span class="sxs-lookup"><span data-stu-id="7c83b-160">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[Main](adding-search/samples/sample12.cs)]

<span data-ttu-id="7c83b-161">Der Code verwendet die `AddRange` Methode der generischen `List` Auflistung, die alle der unterschiedlichen Genres zur Liste hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="7c83b-161">The code uses the `AddRange` method of the generic `List` collection to add all the distinct genres to the list.</span></span> <span data-ttu-id="7c83b-162">(Ohne die `Distinct` Modifizierer, doppelte Genres hinzugefügt – zweimal in unserem Beispiel würde z. B. Komödie hinzugefügt werden).</span><span class="sxs-lookup"><span data-stu-id="7c83b-162">(Without the `Distinct` modifier, duplicate genres would be added — for example, comedy would be added twice in our sample).</span></span> <span data-ttu-id="7c83b-163">Der Code speichert dann die Liste der Genres in die `ViewBag.MovieGenre` Objekt.</span><span class="sxs-lookup"><span data-stu-id="7c83b-163">The code then stores the list of genres in the `ViewBag.MovieGenre` object.</span></span> <span data-ttu-id="7c83b-164">Speichern von Kategoriedaten (solche eine Filmgenre des) als eine [SelectList](https://msdn.microsoft.cus/library/system.web.mvc.selectlist(v=vs.108).aspx) -Objekt in ein `ViewBag`, und klicken Sie dann den Zugriff auf die Kategoriedaten in einem Dropdown-Listenfeld ein typischer Ansatz für MVC-Anwendungen ist.</span><span class="sxs-lookup"><span data-stu-id="7c83b-164">Storing category data (such a movie genre's) as a [SelectList](https://msdn.microsoft.cus/library/system.web.mvc.selectlist(v=vs.108).aspx) object in a `ViewBag`, then accessing the category data in a dropdown list box is a typical approach for MVC applications.</span></span>

<span data-ttu-id="7c83b-165">Der folgende Code zeigt, wie Sie überprüfen die `movieGenre` Parameter.</span><span class="sxs-lookup"><span data-stu-id="7c83b-165">The following code shows how to check the `movieGenre` parameter.</span></span> <span data-ttu-id="7c83b-166">Wenn es nicht leer ist, schränkt der Code weiter die filmabfrage, um die ausgewählten Filme, die bestimmten Genre zu beschränken.</span><span class="sxs-lookup"><span data-stu-id="7c83b-166">If it's not empty, the code further constrains the movies query to limit the selected movies to the specified genre.</span></span>

[!code-csharp[Main](adding-search/samples/sample13.cs)]

<span data-ttu-id="7c83b-167">Wie bereits erwähnt, die Abfrage wird nicht ausgeführt für die Datenbank bis die Filmliste durchlaufen (das geschieht in der Ansicht nach der `Index` Aktionsmethode zurückgegeben).</span><span class="sxs-lookup"><span data-stu-id="7c83b-167">As stated previously, the query is not run on the database until the movie list is iterated over (which happens in the View, after the `Index` action method returns).</span></span>

## <a name="adding-markup-to-the-index-view-to-support-search-by-genre"></a><span data-ttu-id="7c83b-168">Hinzufügen von Markup für die Ansicht "Index", um die Suche nach Genre zu unterstützen</span><span class="sxs-lookup"><span data-stu-id="7c83b-168">Adding Markup to the Index View to Support Search by Genre</span></span>

<span data-ttu-id="7c83b-169">Hinzufügen einer `Html.DropDownList` Hilfsmethode zum die *Views\Movies\Index.cshtml* Datei kurz vor dem Ausführen der `TextBox` Helper.</span><span class="sxs-lookup"><span data-stu-id="7c83b-169">Add an `Html.DropDownList` helper to the *Views\Movies\Index.cshtml* file, just before the `TextBox` helper.</span></span> <span data-ttu-id="7c83b-170">Das vollständige Markup wird unten gezeigt:</span><span class="sxs-lookup"><span data-stu-id="7c83b-170">The completed markup is shown below:</span></span>

[!code-cshtml[Main](adding-search/samples/sample14.cshtml?highlight=11)]

<span data-ttu-id="7c83b-171">Im folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="7c83b-171">In the following code:</span></span>

[!code-cshtml[Main](adding-search/samples/sample15.cshtml)]

<span data-ttu-id="7c83b-172">Der Parameter "MovieGenre" enthält den Schlüssel für die `DropDownList` Hilfsmethode zum Suchen einer `IEnumerable<SelectListItem>` in die `ViewBag`.</span><span class="sxs-lookup"><span data-stu-id="7c83b-172">The parameter "MovieGenre" provides the key for the `DropDownList` helper to find a `IEnumerable<SelectListItem>` in the `ViewBag`.</span></span> <span data-ttu-id="7c83b-173">Die `ViewBag` in der Aktionsmethode aufgefüllt wurde:</span><span class="sxs-lookup"><span data-stu-id="7c83b-173">The `ViewBag` was populated in the action method:</span></span>

[!code-csharp[Main](adding-search/samples/sample16.cs?highlight=10)]

<span data-ttu-id="7c83b-174">Der Parameter "All" stellt eine optionsbezeichnung bereit.</span><span class="sxs-lookup"><span data-stu-id="7c83b-174">The parameter "All" provides an option label.</span></span> <span data-ttu-id="7c83b-175">Wenn Sie diese Auswahl in Ihrem Browser untersuchen, sehen Sie sich, dass das Attribut "Value" leer ist.</span><span class="sxs-lookup"><span data-stu-id="7c83b-175">If you inspect that choice in your browser, you'll see that its "value" attribute is empty.</span></span> <span data-ttu-id="7c83b-176">Da unser Controller nur zum Filtern `if` die Zeichenfolge ist kein `null` oder leer ist, senden einen leeren Wert `movieGenre` zeigt alle Genres.</span><span class="sxs-lookup"><span data-stu-id="7c83b-176">Since our controller only filters `if` the string is not `null` or empty, submitting an empty value for `movieGenre` shows all genres.</span></span>

<span data-ttu-id="7c83b-177">Sie können auch eine Option aus, um die standardmäßig ausgewählt festlegen.</span><span class="sxs-lookup"><span data-stu-id="7c83b-177">You can also set an option to be selected by default.</span></span> <span data-ttu-id="7c83b-178">Wenn Sie "Komödie" als Standardwert verwenden möchten, ändern Sie den Code im Controller wie folgt:</span><span class="sxs-lookup"><span data-stu-id="7c83b-178">If you wanted "Comedy" as your default option, you would change the code in the Controller like so:</span></span>

[!code-cshtml[Main](adding-search/samples/sample17.cshtml)]

<span data-ttu-id="7c83b-179">Führen Sie die Anwendung, und navigieren Sie zu */Filme/Index*.</span><span class="sxs-lookup"><span data-stu-id="7c83b-179">Run the application and browse to */Movies/Index*.</span></span> <span data-ttu-id="7c83b-180">Versuchen Sie eine Suche nach Genre, Filmname und von beiden Kriterien.</span><span class="sxs-lookup"><span data-stu-id="7c83b-180">Try a search by genre, by movie name, and by both criteria.</span></span>

![](adding-search/_static/image8.png)

<span data-ttu-id="7c83b-181">In diesem Abschnitt haben Sie erstellt eine Suchmethode für Aktion und Ansicht, die Benutzern das Suchen von Filmtitel und Genre zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="7c83b-181">In this section you created a search action method and view that let users search by movie title and genre.</span></span> <span data-ttu-id="7c83b-182">Im nächsten Abschnitt, betrachten Sie eine Eigenschaft zum Hinzufügen der `Movie` Modell und einen Initialisierer hinzufügen, die automatisch eine Testdatenbank erstellen.</span><span class="sxs-lookup"><span data-stu-id="7c83b-182">In the next section, you'll look at how to add a property to the `Movie` model and how to add an initializer that will automatically create a test database.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="7c83b-183">[Zurück](examining-the-edit-methods-and-edit-view.md)
> [Weiter](adding-a-new-field.md)</span><span class="sxs-lookup"><span data-stu-id="7c83b-183">[Previous](examining-the-edit-methods-and-edit-view.md)
[Next](adding-a-new-field.md)</span></span>

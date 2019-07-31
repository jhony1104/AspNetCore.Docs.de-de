---
title: Aktualisieren der generierten Seiten in einer ASP.NET Core-App
author: rick-anderson
description: Erfahren Sie mehr über das Aktualisieren der generierten Seiten in einer ASP.NET Core-App.
ms.author: riande
ms.date: 12/20/2018
uid: tutorials/razor-pages/da1
ms.openlocfilehash: f1f69b7facf584d46248405c808e75bdd8448d2b
ms.sourcegitcommit: 051f068c78931432e030b60094c38376d64d013e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68440325"
---
# <a name="update-the-generated-pages-in-an-aspnet-core-app"></a><span data-ttu-id="94826-103">Aktualisieren der generierten Seiten in einer ASP.NET Core-App</span><span class="sxs-lookup"><span data-stu-id="94826-103">Update the generated pages in an ASP.NET Core app</span></span>

<span data-ttu-id="94826-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="94826-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="94826-105">Für den Anfang ist die mit einem Gerüst erstellte Movie-App schon recht ansprechend, doch es gibt Raum für Verbesserungen.</span><span class="sxs-lookup"><span data-stu-id="94826-105">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="94826-106">**ReleaseDate** sollte **Release Date** heißen (zwei Wörter).</span><span class="sxs-lookup"><span data-stu-id="94826-106">**ReleaseDate** should be **Release Date** (two words).</span></span>

![In Chrome geöffnete Movie-Anwendung](sql/_static/m55.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="94826-108">Aktualisieren des generierten Codes</span><span class="sxs-lookup"><span data-stu-id="94826-108">Update the generated code</span></span>

<span data-ttu-id="94826-109">Öffnen Sie die Datei *Models/Movie.cs*, und fügen Sie die im folgenden Code gezeigten markierten Zeilen hinzu:</span><span class="sxs-lookup"><span data-stu-id="94826-109">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="94826-110">Mit der Datenanmerkung `[Column(TypeName = "decimal(18, 2)")]` kann Entity Framework Core `Price` ordnungsgemäß einer Währung in der Datenbank zuordnen.</span><span class="sxs-lookup"><span data-stu-id="94826-110">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="94826-111">Weitere Informationen finden Sie unter [Datentypen](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="94826-111">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="94826-112">[Datenanmerkungen](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) werden im nächsten Tutorial behandelt.</span><span class="sxs-lookup"><span data-stu-id="94826-112">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span> <span data-ttu-id="94826-113">Das [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata)-Attribut gibt an, was für den Namen eines Felds angezeigt werden soll (in diesem Fall „Release Date“ anstatt „ReleaseDate“).</span><span class="sxs-lookup"><span data-stu-id="94826-113">The [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) attribute specifies what to display for the name of a field (in this case "Release Date" instead of "ReleaseDate").</span></span> <span data-ttu-id="94826-114">Das [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter)-Attribut gibt den Typ der Daten (Datum) an, damit die im Feld gespeicherten Informationen zur Uhrzeit nicht angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="94826-114">The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (Date), so the time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="94826-115">Navigieren Sie zu „Pages/Movies“, und bewegen Sie den Mauszeiger über dem Link **Bearbeiten**, um die Ziel-URL anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="94826-115">Browse to Pages/Movies and  hover over an **Edit** link to see the target URL.</span></span>

![Browserfenster mit Maus über dem Link „Bearbeiten“ und der angezeigten Link-URL von http://localhost:1234/Movies/Edit/5](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="94826-117">Die Links **Edit**, **Details** und **Delete** werden mithilfe des [Hilfsprogramms für Ankertags](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in der Datei *Pages/Movies/Index.cshtml* generiert.</span><span class="sxs-lookup"><span data-stu-id="94826-117">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="94826-118">[Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) ermöglichen serverseitigem Code das Mitwirken am Erstellen und Rendern von HTML-Elementen in Razor-Dateien.</span><span class="sxs-lookup"><span data-stu-id="94826-118">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="94826-119">Im vorangehenden Code generiert das `AnchorTagHelper` dynamisch den Wert des HTML-Attributs `href` auf der Razor Page (die Route ist relativ), das `asp-page`-Element und die Routen-ID (`asp-route-id`).</span><span class="sxs-lookup"><span data-stu-id="94826-119">In the preceding code, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`,  and the route id (`asp-route-id`).</span></span> <span data-ttu-id="94826-120">Weitere Informationen finden Sie unter [URL-Generierung für Seiten](xref:razor-pages/index#url-generation-for-pages).</span><span class="sxs-lookup"><span data-stu-id="94826-120">See [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages) for more information.</span></span>

<span data-ttu-id="94826-121">Rufen Sie in Ihrem bevorzugten Browser **Quelltext anzeigen** auf, um das generierte Markup zu untersuchen.</span><span class="sxs-lookup"><span data-stu-id="94826-121">Use **View Source** from your favorite browser to examine the generated markup.</span></span> <span data-ttu-id="94826-122">Ein Teil des generierten HTML-Codes wird unten gezeigt:</span><span class="sxs-lookup"><span data-stu-id="94826-122">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

<span data-ttu-id="94826-123">Die dynamisch generierten Links übergeben die Film-ID mit einer Abfragezeichenfolge (z.B. den Teil `?id=1` in `https://localhost:5001/Movies/Details?id=1`).</span><span class="sxs-lookup"><span data-stu-id="94826-123">The dynamically-generated links pass the movie ID with a query string (for example, the `?id=1` in  `https://localhost:5001/Movies/Details?id=1`).</span></span>

<span data-ttu-id="94826-124">Aktualisieren Sie die Razor Pages „Edit“, „Details“ und „Delete“ so, dass die Routenvorlage „{id:int}“ verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="94826-124">Update the Edit, Details, and Delete Razor Pages to use the "{id:int}" route template.</span></span> <span data-ttu-id="94826-125">Ändern Sie die „page“-Direktive für jede dieser Seiten aus `@page` in `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="94826-125">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="94826-126">Führen Sie die App aus, und zeigen Sie dann den Quelltext an.</span><span class="sxs-lookup"><span data-stu-id="94826-126">Run the app and then view source.</span></span> <span data-ttu-id="94826-127">Der generierte HTML-Code fügt die ID dem Pfadteil der URL hinzu:</span><span class="sxs-lookup"><span data-stu-id="94826-127">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="94826-128">Eine Anforderung an die Seite mit der Routenvorlage „{id:int}“, die **nicht** die ganze Zahl enthält, gibt den HTTP-Fehler 404 (Nicht gefunden) zurück.</span><span class="sxs-lookup"><span data-stu-id="94826-128">A request to the page with the "{id:int}" route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="94826-129">`http://localhost:5000/Movies/Details` gibt beispielsweise den Fehler 404 zurück.</span><span class="sxs-lookup"><span data-stu-id="94826-129">For example, `http://localhost:5000/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="94826-130">Um die ID optional zu machen, fügen Sie `?` an die Routeneinschränkung an:</span><span class="sxs-lookup"><span data-stu-id="94826-130">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="94826-131">So testen Sie das Verhalten von `@page "{id:int?}"`:</span><span class="sxs-lookup"><span data-stu-id="94826-131">To test the behavior of `@page "{id:int?}"`:</span></span>

* <span data-ttu-id="94826-132">Legen Sie die page-Anweisung in *Pages/Movies/Details.cshtml* auf `@page "{id:int?}"` fest.</span><span class="sxs-lookup"><span data-stu-id="94826-132">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
* <span data-ttu-id="94826-133">Legen Sie eine Haltepunkt in `public async Task<IActionResult> OnGetAsync(int? id)` fest (in *Pages/Movies/Details.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="94826-133">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)` (in *Pages/Movies/Details.cshtml.cs*).</span></span>
* <span data-ttu-id="94826-134">Navigieren Sie zu `https://localhost:5001/Movies/Details/`.</span><span class="sxs-lookup"><span data-stu-id="94826-134">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="94826-135">Mit der `@page "{id:int}"`-Direktive wird der Haltepunkt nie erreicht.</span><span class="sxs-lookup"><span data-stu-id="94826-135">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="94826-136">Die Routing-Engine gibt den HTTP-Fehler 404 zurück.</span><span class="sxs-lookup"><span data-stu-id="94826-136">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="94826-137">Bei Verwendung von `@page "{id:int?}"` gibt die `OnGetAsync`-Methode `NotFound` zurück (HTTP 404).</span><span class="sxs-lookup"><span data-stu-id="94826-137">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404).</span></span>

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="94826-138">Überprüfen der Behandlung von Ausnahmen bei Parallelität</span><span class="sxs-lookup"><span data-stu-id="94826-138">Review concurrency exception handling</span></span>

<span data-ttu-id="94826-139">Überprüfen Sie die `OnPostAsync`-Methode in der Datei *Pages/Movies/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="94826-139">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="94826-140">Der obige Code erkennt Parallelitätsausnahmen, wenn ein Client den Film löscht und der andere Client Änderungen am Film übermittelt.</span><span class="sxs-lookup"><span data-stu-id="94826-140">The previous code detects concurrency exceptions when the one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="94826-141">So testen Sie den Block `catch`</span><span class="sxs-lookup"><span data-stu-id="94826-141">To test the `catch` block:</span></span>

* <span data-ttu-id="94826-142">Legen Sie einen Haltepunkt auf `catch (DbUpdateConcurrencyException)`fest.</span><span class="sxs-lookup"><span data-stu-id="94826-142">Set a breakpoint on `catch (DbUpdateConcurrencyException)`</span></span>
* <span data-ttu-id="94826-143">Wählen Sie **Edit** für einen Film aus, nehmen Sie Änderungen vor, aber geben Sie nicht **Save** ein.</span><span class="sxs-lookup"><span data-stu-id="94826-143">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
* <span data-ttu-id="94826-144">Klicken Sie in einem anderen Browserfenster auf den Link **Delete** für denselben Film, und löschen Sie dann den Film.</span><span class="sxs-lookup"><span data-stu-id="94826-144">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
* <span data-ttu-id="94826-145">Übermitteln Sie im vorherigen Browserfenster Änderungen am Film.</span><span class="sxs-lookup"><span data-stu-id="94826-145">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="94826-146">Der Produktionscode sollte Nebenläufigkeitskonflikte erkennen.</span><span class="sxs-lookup"><span data-stu-id="94826-146">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="94826-147">Weitere Informationen finden Sie unter [Verarbeiten von Nebenläufigkeitskonflikten](xref:data/ef-rp/concurrency).</span><span class="sxs-lookup"><span data-stu-id="94826-147">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="94826-148">Überprüfen der Bereitstellung und Bindung</span><span class="sxs-lookup"><span data-stu-id="94826-148">Posting and binding review</span></span>

<span data-ttu-id="94826-149">Sehen Sie sich die Datei *Pages/Movies/Edit.cshtml.cs* an:</span><span class="sxs-lookup"><span data-stu-id="94826-149">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/SnapShots/Edit.cshtml.cs?name=snippet2)]

<span data-ttu-id="94826-150">Wenn eine HTTP GET-Anforderung an die Seite „Movies/Edit“ gerichtet wird (z.B. `http://localhost:5000/Movies/Edit/2`):</span><span class="sxs-lookup"><span data-stu-id="94826-150">When an HTTP GET request is made to the Movies/Edit page (for example, `http://localhost:5000/Movies/Edit/2`):</span></span>

* <span data-ttu-id="94826-151">Die `OnGetAsync`-Methode ruft den Film aus der Datenbank ab und gibt die `Page`-Methode zurück.</span><span class="sxs-lookup"><span data-stu-id="94826-151">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span>
* <span data-ttu-id="94826-152">Die `Page`-Methode rendert die Razor Page *Pages/Movies/Edit.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="94826-152">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="94826-153">Die Datei *Pages/Movies/Edit.cshtml* enthält die Modelldirektive (`@model RazorPagesMovie.Pages.Movies.EditModel`), die das Filmmodell auf der Seite verfügbar macht.</span><span class="sxs-lookup"><span data-stu-id="94826-153">The *Pages/Movies/Edit.cshtml* file contains the model directive (`@model RazorPagesMovie.Pages.Movies.EditModel`), which makes the movie model available on the page.</span></span>
* <span data-ttu-id="94826-154">Das Bearbeitungsformular wird mit den Werten aus dem Film angezeigt.</span><span class="sxs-lookup"><span data-stu-id="94826-154">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="94826-155">Wenn die Seite „Filme/Bearbeiten“ bereitgestellt wird:</span><span class="sxs-lookup"><span data-stu-id="94826-155">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="94826-156">Die Formularwerte auf der Seite sind an die `Movie`-Eigenschaft gebunden.</span><span class="sxs-lookup"><span data-stu-id="94826-156">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="94826-157">Das `[BindProperty]`-Attribut ermöglicht die [Modellbindung](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="94826-157">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="94826-158">Bei Fehlern beim Modellstatus (Beispiel: `ReleaseDate` kann nicht in ein Datum konvertiert werden) wird das Formular mit den übermittelten Werten erneut angezeigt.</span><span class="sxs-lookup"><span data-stu-id="94826-158">If there are errors in the model state (for example, `ReleaseDate` cannot be converted to a date), the form is redisplayed with the submitted values.</span></span>
* <span data-ttu-id="94826-159">Wenn keine Modellfehler vorhanden sind, wird der Film gespeichert.</span><span class="sxs-lookup"><span data-stu-id="94826-159">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="94826-160">Die HTTP GET-Methoden auf den Razor Pages „Index“, „Create“ und „Delete“ folgen einem ähnlichen Muster.</span><span class="sxs-lookup"><span data-stu-id="94826-160">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="94826-161">Die HTTP-POST-Methode `OnPostAsync` auf der Razor Page „Create“ folgt einem ähnlichen Muster wie die `OnPostAsync`-Methode auf der Razor Page „Edit“.</span><span class="sxs-lookup"><span data-stu-id="94826-161">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="94826-162">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="94826-162">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="94826-163">[Zurück: Arbeiten mit einer Datenbank](xref:tutorials/razor-pages/sql)
> [Weiter: Hinzufügen der Suche](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="94826-163">[Previous: Working with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="94826-164">Für den Anfang ist die mit einem Gerüst erstellte Movie-App schon recht ansprechend, doch es gibt Raum für Verbesserungen.</span><span class="sxs-lookup"><span data-stu-id="94826-164">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="94826-165">**ReleaseDate** sollte **Release Date** heißen (zwei Wörter).</span><span class="sxs-lookup"><span data-stu-id="94826-165">**ReleaseDate** should be **Release Date** (two words).</span></span>

![In Chrome geöffnete Movie-Anwendung](sql/_static/m55.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="94826-167">Aktualisieren des generierten Codes</span><span class="sxs-lookup"><span data-stu-id="94826-167">Update the generated code</span></span>

<span data-ttu-id="94826-168">Öffnen Sie die Datei *Models/Movie.cs*, und fügen Sie die im folgenden Code gezeigten markierten Zeilen hinzu:</span><span class="sxs-lookup"><span data-stu-id="94826-168">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="94826-169">Mit der Datenanmerkung `[Column(TypeName = "decimal(18, 2)")]` kann Entity Framework Core `Price` ordnungsgemäß einer Währung in der Datenbank zuordnen.</span><span class="sxs-lookup"><span data-stu-id="94826-169">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="94826-170">Weitere Informationen finden Sie unter [Datentypen](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="94826-170">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="94826-171">[Datenanmerkungen](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) werden im nächsten Tutorial behandelt.</span><span class="sxs-lookup"><span data-stu-id="94826-171">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span> <span data-ttu-id="94826-172">Das [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata)-Attribut gibt an, was für den Namen eines Felds angezeigt werden soll (in diesem Fall „Release Date“ anstatt „ReleaseDate“).</span><span class="sxs-lookup"><span data-stu-id="94826-172">The [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) attribute specifies what to display for the name of a field (in this case "Release Date" instead of "ReleaseDate").</span></span> <span data-ttu-id="94826-173">Das [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter)-Attribut gibt den Typ der Daten (Datum) an, damit die im Feld gespeicherten Informationen zur Uhrzeit nicht angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="94826-173">The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (Date), so the time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="94826-174">Navigieren Sie zu „Pages/Movies“, und bewegen Sie den Mauszeiger über dem Link **Bearbeiten**, um die Ziel-URL anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="94826-174">Browse to Pages/Movies and  hover over an **Edit** link to see the target URL.</span></span>

![Browserfenster mit Maus über dem Link „Bearbeiten“ und der angezeigten Link-URL von http://localhost:1234/Movies/Edit/5](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="94826-176">Die Links **Edit**, **Details** und **Delete** werden mithilfe des [Hilfsprogramms für Ankertags](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in der Datei *Pages/Movies/Index.cshtml* generiert.</span><span class="sxs-lookup"><span data-stu-id="94826-176">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="94826-177">[Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) ermöglichen serverseitigem Code das Mitwirken am Erstellen und Rendern von HTML-Elementen in Razor-Dateien.</span><span class="sxs-lookup"><span data-stu-id="94826-177">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="94826-178">Im vorangehenden Code generiert das `AnchorTagHelper` dynamisch den Wert des HTML-Attributs `href` auf der Razor Page (die Route ist relativ), das `asp-page`-Element und die Routen-ID (`asp-route-id`).</span><span class="sxs-lookup"><span data-stu-id="94826-178">In the preceding code, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`,  and the route id (`asp-route-id`).</span></span> <span data-ttu-id="94826-179">Weitere Informationen finden Sie unter [URL-Generierung für Seiten](xref:razor-pages/index#url-generation-for-pages).</span><span class="sxs-lookup"><span data-stu-id="94826-179">See [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages) for more information.</span></span>

<span data-ttu-id="94826-180">Rufen Sie in Ihrem bevorzugten Browser **Quelltext anzeigen** auf, um das generierte Markup zu untersuchen.</span><span class="sxs-lookup"><span data-stu-id="94826-180">Use **View Source** from your favorite browser to examine the generated markup.</span></span> <span data-ttu-id="94826-181">Ein Teil des generierten HTML-Codes wird unten gezeigt:</span><span class="sxs-lookup"><span data-stu-id="94826-181">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

<span data-ttu-id="94826-182">Die dynamisch generierten Links übergeben die Film-ID mit einer Abfragezeichenfolge (z.B. den Teil `?id=1` in `https://localhost:5001/Movies/Details?id=1`).</span><span class="sxs-lookup"><span data-stu-id="94826-182">The dynamically-generated links pass the movie ID with a query string (for example, the `?id=1` in  `https://localhost:5001/Movies/Details?id=1`).</span></span>

<span data-ttu-id="94826-183">Aktualisieren Sie die Razor Pages „Edit“, „Details“ und „Delete“ so, dass die Routenvorlage „{id:int}“ verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="94826-183">Update the Edit, Details, and Delete Razor Pages to use the "{id:int}" route template.</span></span> <span data-ttu-id="94826-184">Ändern Sie die „page“-Direktive für jede dieser Seiten aus `@page` in `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="94826-184">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="94826-185">Führen Sie die App aus, und zeigen Sie dann den Quelltext an.</span><span class="sxs-lookup"><span data-stu-id="94826-185">Run the app and then view source.</span></span> <span data-ttu-id="94826-186">Der generierte HTML-Code fügt die ID dem Pfadteil der URL hinzu:</span><span class="sxs-lookup"><span data-stu-id="94826-186">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="94826-187">Eine Anforderung an die Seite mit der Routenvorlage „{id:int}“, die **nicht** die ganze Zahl enthält, gibt den HTTP-Fehler 404 (Nicht gefunden) zurück.</span><span class="sxs-lookup"><span data-stu-id="94826-187">A request to the page with the "{id:int}" route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="94826-188">`http://localhost:5000/Movies/Details` gibt beispielsweise den Fehler 404 zurück.</span><span class="sxs-lookup"><span data-stu-id="94826-188">For example, `http://localhost:5000/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="94826-189">Um die ID optional zu machen, fügen Sie `?` an die Routeneinschränkung an:</span><span class="sxs-lookup"><span data-stu-id="94826-189">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="94826-190">So testen Sie das Verhalten von `@page "{id:int?}"`:</span><span class="sxs-lookup"><span data-stu-id="94826-190">To test the behavior of `@page "{id:int?}"`:</span></span>

* <span data-ttu-id="94826-191">Legen Sie die page-Anweisung in *Pages/Movies/Details.cshtml* auf `@page "{id:int?}"` fest.</span><span class="sxs-lookup"><span data-stu-id="94826-191">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
* <span data-ttu-id="94826-192">Legen Sie eine Haltepunkt in `public async Task<IActionResult> OnGetAsync(int? id)` fest (in *Pages/Movies/Details.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="94826-192">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)` (in *Pages/Movies/Details.cshtml.cs*).</span></span>
* <span data-ttu-id="94826-193">Navigieren Sie zu `https://localhost:5001/Movies/Details/`.</span><span class="sxs-lookup"><span data-stu-id="94826-193">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="94826-194">Mit der `@page "{id:int}"`-Direktive wird der Haltepunkt nie erreicht.</span><span class="sxs-lookup"><span data-stu-id="94826-194">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="94826-195">Die Routing-Engine gibt den HTTP-Fehler 404 zurück.</span><span class="sxs-lookup"><span data-stu-id="94826-195">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="94826-196">Bei Verwendung von `@page "{id:int?}"` gibt die `OnGetAsync`-Methode `NotFound` zurück (HTTP 404).</span><span class="sxs-lookup"><span data-stu-id="94826-196">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404).</span></span>

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="94826-197">Überprüfen der Behandlung von Ausnahmen bei Parallelität</span><span class="sxs-lookup"><span data-stu-id="94826-197">Review concurrency exception handling</span></span>

<span data-ttu-id="94826-198">Überprüfen Sie die `OnPostAsync`-Methode in der Datei *Pages/Movies/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="94826-198">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="94826-199">Der obige Code erkennt Parallelitätsausnahmen, wenn ein Client den Film löscht und der andere Client Änderungen am Film übermittelt.</span><span class="sxs-lookup"><span data-stu-id="94826-199">The previous code detects concurrency exceptions when the one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="94826-200">So testen Sie den Block `catch`</span><span class="sxs-lookup"><span data-stu-id="94826-200">To test the `catch` block:</span></span>

* <span data-ttu-id="94826-201">Legen Sie einen Haltepunkt auf `catch (DbUpdateConcurrencyException)`fest.</span><span class="sxs-lookup"><span data-stu-id="94826-201">Set a breakpoint on `catch (DbUpdateConcurrencyException)`</span></span>
* <span data-ttu-id="94826-202">Wählen Sie **Edit** für einen Film aus, nehmen Sie Änderungen vor, aber geben Sie nicht **Save** ein.</span><span class="sxs-lookup"><span data-stu-id="94826-202">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
* <span data-ttu-id="94826-203">Klicken Sie in einem anderen Browserfenster auf den Link **Delete** für denselben Film, und löschen Sie dann den Film.</span><span class="sxs-lookup"><span data-stu-id="94826-203">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
* <span data-ttu-id="94826-204">Übermitteln Sie im vorherigen Browserfenster Änderungen am Film.</span><span class="sxs-lookup"><span data-stu-id="94826-204">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="94826-205">Der Produktionscode sollte Nebenläufigkeitskonflikte erkennen.</span><span class="sxs-lookup"><span data-stu-id="94826-205">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="94826-206">Weitere Informationen finden Sie unter [Verarbeiten von Nebenläufigkeitskonflikten](xref:data/ef-rp/concurrency).</span><span class="sxs-lookup"><span data-stu-id="94826-206">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="94826-207">Überprüfen der Bereitstellung und Bindung</span><span class="sxs-lookup"><span data-stu-id="94826-207">Posting and binding review</span></span>

<span data-ttu-id="94826-208">Sehen Sie sich die Datei *Pages/Movies/Edit.cshtml.cs* an:</span><span class="sxs-lookup"><span data-stu-id="94826-208">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit21.cshtml.cs?name=snippet2)]

<span data-ttu-id="94826-209">Wenn eine HTTP GET-Anforderung an die Seite „Movies/Edit“ gerichtet wird (z.B. `http://localhost:5000/Movies/Edit/2`):</span><span class="sxs-lookup"><span data-stu-id="94826-209">When an HTTP GET request is made to the Movies/Edit page (for example, `http://localhost:5000/Movies/Edit/2`):</span></span>

* <span data-ttu-id="94826-210">Die `OnGetAsync`-Methode ruft den Film aus der Datenbank ab und gibt die `Page`-Methode zurück.</span><span class="sxs-lookup"><span data-stu-id="94826-210">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span> 
* <span data-ttu-id="94826-211">Die `Page`-Methode rendert die Razor Page *Pages/Movies/Edit.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="94826-211">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="94826-212">Die Datei *Pages/Movies/Edit.cshtml* enthält die Modelldirektive (`@model RazorPagesMovie.Pages.Movies.EditModel`), die das Filmmodell auf der Seite verfügbar macht.</span><span class="sxs-lookup"><span data-stu-id="94826-212">The *Pages/Movies/Edit.cshtml* file contains the model directive (`@model RazorPagesMovie.Pages.Movies.EditModel`), which makes the movie model available on the page.</span></span>
* <span data-ttu-id="94826-213">Das Bearbeitungsformular wird mit den Werten aus dem Film angezeigt.</span><span class="sxs-lookup"><span data-stu-id="94826-213">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="94826-214">Wenn die Seite „Filme/Bearbeiten“ bereitgestellt wird:</span><span class="sxs-lookup"><span data-stu-id="94826-214">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="94826-215">Die Formularwerte auf der Seite sind an die `Movie`-Eigenschaft gebunden.</span><span class="sxs-lookup"><span data-stu-id="94826-215">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="94826-216">Das `[BindProperty]`-Attribut ermöglicht die [Modellbindung](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="94826-216">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="94826-217">Bei Fehlern beim Modellstatus (Beispiel: `ReleaseDate` kann nicht in ein Datum konvertiert werden) wird das Formular mit den übermittelten Werten angezeigt.</span><span class="sxs-lookup"><span data-stu-id="94826-217">If there are errors in the model state (for example, `ReleaseDate` cannot be converted to a date), the form is displayed with the submitted values.</span></span>
* <span data-ttu-id="94826-218">Wenn keine Modellfehler vorhanden sind, wird der Film gespeichert.</span><span class="sxs-lookup"><span data-stu-id="94826-218">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="94826-219">Die HTTP GET-Methoden auf den Razor Pages „Index“, „Create“ und „Delete“ folgen einem ähnlichen Muster.</span><span class="sxs-lookup"><span data-stu-id="94826-219">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="94826-220">Die HTTP-POST-Methode `OnPostAsync` auf der Razor Page „Create“ folgt einem ähnlichen Muster wie die `OnPostAsync`-Methode auf der Razor Page „Edit“.</span><span class="sxs-lookup"><span data-stu-id="94826-220">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

<span data-ttu-id="94826-221">Die Suche wird im nächsten Tutorial hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="94826-221">Search is added in the next tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="94826-222">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="94826-222">Additional resources</span></span>

* [<span data-ttu-id="94826-223">Dieses Tutorial auf YouTube</span><span class="sxs-lookup"><span data-stu-id="94826-223">YouTube version of this tutorial</span></span>](https://youtu.be/yLnnleREMtQ)

> [!div class="step-by-step"]
> <span data-ttu-id="94826-224">[Zurück: Arbeiten mit einer Datenbank](xref:tutorials/razor-pages/sql)
> [Weiter: Hinzufügen der Suche](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="94826-224">[Previous: Working with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end

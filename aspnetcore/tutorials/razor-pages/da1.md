---
title: Aktualisieren der generierten Seiten in einer ASP.NET Core-App
author: rick-anderson
description: Erfahren Sie mehr über das Aktualisieren der generierten Seiten in einer ASP.NET Core-App.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.date: 12/20/2018
uid: tutorials/razor-pages/da1
ms.openlocfilehash: c950d1654591fe3c78daae0e2cb7a9b37472284f
ms.sourcegitcommit: a91e8dd2f4b788114c8bc834507277f4b5e8d6c5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2019
ms.locfileid: "55712249"
---
# <a name="update-the-generated-pages-in-an-aspnet-core-app"></a><span data-ttu-id="3dd9f-103">Aktualisieren der generierten Seiten in einer ASP.NET Core-App</span><span class="sxs-lookup"><span data-stu-id="3dd9f-103">Update the generated pages in an ASP.NET Core app</span></span>

<span data-ttu-id="3dd9f-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3dd9f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="3dd9f-105">Für den Anfang ist die mit einem Gerüst erstellte Movie-App schon recht ansprechend, doch es gibt Raum für Verbesserungen.</span><span class="sxs-lookup"><span data-stu-id="3dd9f-105">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="3dd9f-106">**ReleaseDate** sollte **Release Date** heißen (zwei Wörter).</span><span class="sxs-lookup"><span data-stu-id="3dd9f-106">**ReleaseDate** should be **Release Date** (two words).</span></span>

![In Chrome geöffnete Movie-Anwendung](sql/_static/m55.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="3dd9f-108">Aktualisieren des generierten Codes</span><span class="sxs-lookup"><span data-stu-id="3dd9f-108">Update the generated code</span></span>

<span data-ttu-id="3dd9f-109">Öffnen Sie die Datei *Models/Movie.cs*, und fügen Sie die im folgenden Code gezeigten markierten Zeilen hinzu:</span><span class="sxs-lookup"><span data-stu-id="3dd9f-109">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=12,17)]

<span data-ttu-id="3dd9f-110">Mit der Datenanmerkung `[Column(TypeName = "decimal(18, 2)")]` kann Entity Framework Core `Price` ordnungsgemäß einer Währung in der Datenbank zuordnen.</span><span class="sxs-lookup"><span data-stu-id="3dd9f-110">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="3dd9f-111">Weitere Informationen finden Sie unter [Datentypen](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="3dd9f-111">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="3dd9f-112">[Datenanmerkungen](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) werden im nächsten Tutorial behandelt.</span><span class="sxs-lookup"><span data-stu-id="3dd9f-112">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span> <span data-ttu-id="3dd9f-113">Das [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata)-Attribut gibt an, was für den Namen eines Felds angezeigt werden soll (in diesem Fall „Release Date“ anstatt „ReleaseDate“).</span><span class="sxs-lookup"><span data-stu-id="3dd9f-113">The [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) attribute specifies what to display for the name of a field (in this case "Release Date" instead of "ReleaseDate").</span></span> <span data-ttu-id="3dd9f-114">Das [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter)-Attribut gibt den Typ der Daten (Datum) an, damit die im Feld gespeicherten Informationen zur Uhrzeit nicht angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="3dd9f-114">The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (Date), so the time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="3dd9f-115">Navigieren Sie zu „Pages/Movies“, und bewegen Sie den Mauszeiger über dem Link **Bearbeiten**, um die Ziel-URL anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="3dd9f-115">Browse to Pages/Movies and  hover over an **Edit** link to see the target URL.</span></span>

![Browserfenster mit Maus über dem Link „Bearbeiten“ und der angezeigten Link-URL von http://localhost:1234/Movies/Edit/5](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="3dd9f-117">Die Links **Edit**, **Details** und **Delete** werden mithilfe des [Hilfsprogramms für Ankertags](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in der Datei *Pages/Movies/Index.cshtml* generiert.</span><span class="sxs-lookup"><span data-stu-id="3dd9f-117">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="3dd9f-118">[Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) ermöglichen serverseitigem Code das Mitwirken am Erstellen und Rendern von HTML-Elementen in Razor-Dateien.</span><span class="sxs-lookup"><span data-stu-id="3dd9f-118">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="3dd9f-119">Im vorangehenden Code generiert das `AnchorTagHelper` dynamisch den Wert des HTML-Attributs `href` auf der Razor Page (die Route ist relativ), das `asp-page`-Element und die Routen-ID (`asp-route-id`).</span><span class="sxs-lookup"><span data-stu-id="3dd9f-119">In the preceding code, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`,  and the route id (`asp-route-id`).</span></span> <span data-ttu-id="3dd9f-120">Weitere Informationen finden Sie unter [URL-Generierung für Seiten](xref:razor-pages/index#url-generation-for-pages).</span><span class="sxs-lookup"><span data-stu-id="3dd9f-120">See [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages) for more information.</span></span>

<span data-ttu-id="3dd9f-121">Rufen Sie in Ihrem bevorzugten Browser **Quelltext anzeigen** auf, um das generierte Markup zu untersuchen.</span><span class="sxs-lookup"><span data-stu-id="3dd9f-121">Use **View Source** from your favorite browser to examine the generated markup.</span></span> <span data-ttu-id="3dd9f-122">Ein Teil des generierten HTML-Codes wird unten gezeigt:</span><span class="sxs-lookup"><span data-stu-id="3dd9f-122">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

<span data-ttu-id="3dd9f-123">Die dynamisch generierten Links übergeben die Film-ID mit einer Abfragezeichenfolge (z.B. den Teil `?id=1` in `https://localhost:5001/Movies/Details?id=1`).</span><span class="sxs-lookup"><span data-stu-id="3dd9f-123">The dynamically-generated links pass the movie ID with a query string (for example, the `?id=1` in  `https://localhost:5001/Movies/Details?id=1`).</span></span>

<span data-ttu-id="3dd9f-124">Aktualisieren Sie die Razor Pages „Edit“, „Details“ und „Delete“ so, dass die Routenvorlage „{id:int}“ verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="3dd9f-124">Update the Edit, Details, and Delete Razor Pages to use the "{id:int}" route template.</span></span> <span data-ttu-id="3dd9f-125">Ändern Sie die „page“-Direktive für jede dieser Seiten aus `@page` in `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="3dd9f-125">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="3dd9f-126">Führen Sie die App aus, und zeigen Sie dann den Quelltext an.</span><span class="sxs-lookup"><span data-stu-id="3dd9f-126">Run the app and then view source.</span></span> <span data-ttu-id="3dd9f-127">Der generierte HTML-Code fügt die ID dem Pfadteil der URL hinzu:</span><span class="sxs-lookup"><span data-stu-id="3dd9f-127">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="3dd9f-128">Eine Anforderung an die Seite mit der Routenvorlage „{id:int}“, die **nicht** die ganze Zahl enthält, gibt den HTTP-Fehler 404 (Nicht gefunden) zurück.</span><span class="sxs-lookup"><span data-stu-id="3dd9f-128">A request to the page with the "{id:int}" route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="3dd9f-129">`http://localhost:5000/Movies/Details` gibt beispielsweise den Fehler 404 zurück.</span><span class="sxs-lookup"><span data-stu-id="3dd9f-129">For example, `http://localhost:5000/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="3dd9f-130">Um die ID optional zu machen, fügen Sie `?` an die Routeneinschränkung an:</span><span class="sxs-lookup"><span data-stu-id="3dd9f-130">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="3dd9f-131">So testen Sie das Verhalten von `@page "{id:int?}"`:</span><span class="sxs-lookup"><span data-stu-id="3dd9f-131">To test the behavior of `@page "{id:int?}"`:</span></span>

* <span data-ttu-id="3dd9f-132">Legen Sie die page-Anweisung in *Pages/Movies/Details.cshtml* auf `@page "{id:int?}"` fest.</span><span class="sxs-lookup"><span data-stu-id="3dd9f-132">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
* <span data-ttu-id="3dd9f-133">Legen Sie eine Haltepunkt in `public async Task<IActionResult> OnGetAsync(int? id)` fest (in *Pages/Movies/Details.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="3dd9f-133">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)` (in *Pages/Movies/Details.cshtml.cs*).</span></span>
* <span data-ttu-id="3dd9f-134">Navigieren Sie zu `https://localhost:5001/Movies/Details/`.</span><span class="sxs-lookup"><span data-stu-id="3dd9f-134">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="3dd9f-135">Mit der `@page "{id:int}"`-Direktive wird der Haltepunkt nie erreicht.</span><span class="sxs-lookup"><span data-stu-id="3dd9f-135">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="3dd9f-136">Die Routing-Engine gibt den HTTP-Fehler 404 zurück.</span><span class="sxs-lookup"><span data-stu-id="3dd9f-136">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="3dd9f-137">Bei Verwendung von `@page "{id:int?}"` gibt die `OnGetAsync`-Methode `NotFound` zurück (HTTP 404).</span><span class="sxs-lookup"><span data-stu-id="3dd9f-137">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404).</span></span>

<span data-ttu-id="3dd9f-138">Obwohl nicht zu empfehlen, könnten Sie die `OnGetAsync`-Methode (in *Pages/Movies/Delete.cshtml.cs*) wie folgt schreiben:</span><span class="sxs-lookup"><span data-stu-id="3dd9f-138">Although not recommended, you could write the `OnGetAsync` method (in *Pages/Movies/Delete.cshtml.cs*) as:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Delete.cshtml.cs?name=snippet)]

<span data-ttu-id="3dd9f-139">Testen Sie den oben stehenden Code:</span><span class="sxs-lookup"><span data-stu-id="3dd9f-139">Test the preceding code:</span></span>

* <span data-ttu-id="3dd9f-140">Wählen Sie einen **Löschen**-Link aus.</span><span class="sxs-lookup"><span data-stu-id="3dd9f-140">Select a **Delete** link.</span></span>
* <span data-ttu-id="3dd9f-141">Entfernen Sie die ID aus der URL.</span><span class="sxs-lookup"><span data-stu-id="3dd9f-141">Remove the ID from the URL.</span></span> <span data-ttu-id="3dd9f-142">Ändern Sie beispielsweise `https://localhost:5001/Movies/Delete/8` zu `https://localhost:5001/Movies/Delete`.</span><span class="sxs-lookup"><span data-stu-id="3dd9f-142">For example, change `https://localhost:5001/Movies/Delete/8` to `https://localhost:5001/Movies/Delete`.</span></span>
* <span data-ttu-id="3dd9f-143">Führen Sie den Code im Debugger schrittweise aus.</span><span class="sxs-lookup"><span data-stu-id="3dd9f-143">Step through the code in the debugger.</span></span>

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="3dd9f-144">Überprüfen der Behandlung von Ausnahmen bei Parallelität</span><span class="sxs-lookup"><span data-stu-id="3dd9f-144">Review concurrency exception handling</span></span>

<span data-ttu-id="3dd9f-145">Überprüfen Sie die `OnPostAsync`-Methode in der Datei *Pages/Movies/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="3dd9f-145">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="3dd9f-146">Der obige Code erkennt Parallelitätsausnahmen, wenn ein Client den Film löscht und der andere Client Änderungen am Film übermittelt.</span><span class="sxs-lookup"><span data-stu-id="3dd9f-146">The previous code detects concurrency exceptions when the one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="3dd9f-147">So testen Sie den Block `catch`</span><span class="sxs-lookup"><span data-stu-id="3dd9f-147">To test the `catch` block:</span></span>

* <span data-ttu-id="3dd9f-148">Legen Sie einen Haltepunkt auf `catch (DbUpdateConcurrencyException)`fest.</span><span class="sxs-lookup"><span data-stu-id="3dd9f-148">Set a breakpoint on `catch (DbUpdateConcurrencyException)`</span></span>
* <span data-ttu-id="3dd9f-149">Wählen Sie **Edit** für einen Film aus, nehmen Sie Änderungen vor, aber geben Sie nicht **Save** ein.</span><span class="sxs-lookup"><span data-stu-id="3dd9f-149">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
* <span data-ttu-id="3dd9f-150">Klicken Sie in einem anderen Browserfenster auf den Link **Delete** für denselben Film, und löschen Sie dann den Film.</span><span class="sxs-lookup"><span data-stu-id="3dd9f-150">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
* <span data-ttu-id="3dd9f-151">Übermitteln Sie im vorherigen Browserfenster Änderungen am Film.</span><span class="sxs-lookup"><span data-stu-id="3dd9f-151">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="3dd9f-152">Der Produktionscode sollte Nebenläufigkeitskonflikte erkennen.</span><span class="sxs-lookup"><span data-stu-id="3dd9f-152">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="3dd9f-153">Weitere Informationen finden Sie unter [Verarbeiten von Nebenläufigkeitskonflikten](xref:data/ef-rp/concurrency).</span><span class="sxs-lookup"><span data-stu-id="3dd9f-153">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="3dd9f-154">Überprüfen der Bereitstellung und Bindung</span><span class="sxs-lookup"><span data-stu-id="3dd9f-154">Posting and binding review</span></span>

<span data-ttu-id="3dd9f-155">Sehen Sie sich die Datei *Pages/Movies/Edit.cshtml.cs* an:</span><span class="sxs-lookup"><span data-stu-id="3dd9f-155">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit21.cshtml.cs?name=snippet2)]

<span data-ttu-id="3dd9f-156">Wenn eine HTTP GET-Anforderung an die Seite „Movies/Edit“ gerichtet wird (z.B. `http://localhost:5000/Movies/Edit/2`):</span><span class="sxs-lookup"><span data-stu-id="3dd9f-156">When an HTTP GET request is made to the Movies/Edit page (for example, `http://localhost:5000/Movies/Edit/2`):</span></span>

* <span data-ttu-id="3dd9f-157">Die `OnGetAsync`-Methode ruft den Film aus der Datenbank ab und gibt die `Page`-Methode zurück.</span><span class="sxs-lookup"><span data-stu-id="3dd9f-157">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span> 
* <span data-ttu-id="3dd9f-158">Die `Page`-Methode rendert die Razor Page *Pages/Movies/Edit.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="3dd9f-158">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="3dd9f-159">Die Datei *Pages/Movies/Edit.cshtml* enthält die Modelldirektive (`@model RazorPagesMovie.Pages.Movies.EditModel`), die das Filmmodell auf der Seite verfügbar macht.</span><span class="sxs-lookup"><span data-stu-id="3dd9f-159">The *Pages/Movies/Edit.cshtml* file contains the model directive (`@model RazorPagesMovie.Pages.Movies.EditModel`), which makes the movie model available on the page.</span></span>
* <span data-ttu-id="3dd9f-160">Das Bearbeitungsformular wird mit den Werten aus dem Film angezeigt.</span><span class="sxs-lookup"><span data-stu-id="3dd9f-160">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="3dd9f-161">Wenn die Seite „Filme/Bearbeiten“ bereitgestellt wird:</span><span class="sxs-lookup"><span data-stu-id="3dd9f-161">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="3dd9f-162">Die Formularwerte auf der Seite sind an die `Movie`-Eigenschaft gebunden.</span><span class="sxs-lookup"><span data-stu-id="3dd9f-162">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="3dd9f-163">Das `[BindProperty]`-Attribut ermöglicht die [Modellbindung](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="3dd9f-163">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="3dd9f-164">Bei Fehlern beim Modellstatus (Beispiel: `ReleaseDate` kann nicht in ein Datum konvertiert werden) wird das Formular mit den übermittelten Werten angezeigt.</span><span class="sxs-lookup"><span data-stu-id="3dd9f-164">If there are errors in the model state (for example, `ReleaseDate` cannot be converted to a date), the form is displayed with the submitted values.</span></span>
* <span data-ttu-id="3dd9f-165">Wenn keine Modellfehler vorhanden sind, wird der Film gespeichert.</span><span class="sxs-lookup"><span data-stu-id="3dd9f-165">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="3dd9f-166">Die HTTP GET-Methoden auf den Razor Pages „Index“, „Create“ und „Delete“ folgen einem ähnlichen Muster.</span><span class="sxs-lookup"><span data-stu-id="3dd9f-166">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="3dd9f-167">Die HTTP-POST-Methode `OnPostAsync` auf der Razor Page „Create“ folgt einem ähnlichen Muster wie die `OnPostAsync`-Methode auf der Razor Page „Edit“.</span><span class="sxs-lookup"><span data-stu-id="3dd9f-167">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

<span data-ttu-id="3dd9f-168">Die Suche wird im nächsten Tutorial hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="3dd9f-168">Search is added in the next tutorial.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="3dd9f-169">[Zurück: Arbeiten mit einer Datenbank](xref:tutorials/razor-pages/sql)
> [Weiter: Hinzufügen der Suche](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="3dd9f-169">[Previous: Working with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

---
title: Gerüstbau mit Razor Pages in ASP.NET Core
author: rick-anderson
description: Gibt nähere Informationen über durch Gerüstbau erstellte Razor Pages.
ms.author: riande
ms.date: 08/17/2019
uid: tutorials/razor-pages/page
ms.openlocfilehash: 00a8458b9bee4d30c5774a980ff5c23fb8872737
ms.sourcegitcommit: 38cac2552029fc19428722bb204ff9e16eb94225
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/18/2019
ms.locfileid: "69573147"
---
# <a name="scaffolded-razor-pages-in-aspnet-core"></a><span data-ttu-id="e0588-103">Gerüstbau mit Razor Pages in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e0588-103">Scaffolded Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e0588-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e0588-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e0588-105">In diesem Tutorial werden die Razor Pages näher untersucht, die durch Gerüstbau im [vorherigen Tutorial](xref:tutorials/razor-pages/model) erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="e0588-105">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="e0588-106">Die Seiten „Create“, „Delete“, „Details“ und „Edit“</span><span class="sxs-lookup"><span data-stu-id="e0588-106">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="e0588-107">Sehen Sie sich das Seitenmodell *Pages/Movies/Index.cshtml.cs* an:</span><span class="sxs-lookup"><span data-stu-id="e0588-107">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="e0588-108">Razor Pages werden von `PageModel` abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="e0588-108">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="e0588-109">Gemäß der Konvention wird die von `PageModel` abgeleitete Klasse `<PageName>Model` genannt.</span><span class="sxs-lookup"><span data-stu-id="e0588-109">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="e0588-110">Der Konstruktor verwendet [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection), um `RazorPagesMovieContext` zur Seite hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="e0588-110">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="e0588-111">Alle per Gerüstbau erstellten Seiten folgen diesem Muster.</span><span class="sxs-lookup"><span data-stu-id="e0588-111">All the scaffolded pages follow this pattern.</span></span> <span data-ttu-id="e0588-112">Weitere Informationen zum asynchronen Programmieren mit Entity Framework finden Sie unter [Asynchroner Code](xref:data/ef-rp/intro#asynchronous-code).</span><span class="sxs-lookup"><span data-stu-id="e0588-112">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="e0588-113">Wenn eine Anforderung an die Seite erfolgt, gibt die Methode `OnGetAsync` eine Filmliste an die Razor Page zurück.</span><span class="sxs-lookup"><span data-stu-id="e0588-113">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="e0588-114">`OnGetAsync` oder `OnGet` werden für eine Razor Page aufgerufen, um den Zustand der Seite zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="e0588-114">`OnGetAsync` or `OnGet` is called on a Razor Page to initialize the state for the page.</span></span> <span data-ttu-id="e0588-115">In diesem Fall ruft `OnGetAsync` eine Liste von Filmen ab und zeigt diese an.</span><span class="sxs-lookup"><span data-stu-id="e0588-115">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="e0588-116">Wenn `OnGet` `void` und `OnGetAsync` `Task` zurückgibt, wird keine Rückgabemethode verwendet.</span><span class="sxs-lookup"><span data-stu-id="e0588-116">When `OnGet` returns `void` or `OnGetAsync` returns`Task`, no return method is used.</span></span> <span data-ttu-id="e0588-117">Wenn der Rückgabetyp `IActionResult` oder `Task<IActionResult>` ist, muss eine Rückgabeanweisung bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="e0588-117">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="e0588-118">Die kann z.B. die `OnPostAsync`-Methode *Pages/Movies/Create.cshtml.cs* sein:</span><span class="sxs-lookup"><span data-stu-id="e0588-118">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> <span data-ttu-id="e0588-119">Sehen Sie sich die Razor-Seite *Pages/Movies/Index.cshtml* an:</span><span class="sxs-lookup"><span data-stu-id="e0588-119">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml)]

<span data-ttu-id="e0588-120">Razor kann aus HTML in C# oder Razor-spezifisches Markup übergehen.</span><span class="sxs-lookup"><span data-stu-id="e0588-120">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="e0588-121">Wenn auf ein `@`-Symbol ein für Razor reserviertes Schlüsselwort ([Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords)) folgt, wechselt es in Razor-spezifisches Markup. Andernfalls geht es in C# über.</span><span class="sxs-lookup"><span data-stu-id="e0588-121">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

<span data-ttu-id="e0588-122">Die Razor-Anweisung `@page` wandelt die Datei in eine MVC-Aktion um. Das bedeutet, dass sie Anforderungen verarbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="e0588-122">The `@page` Razor directive makes the file into an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="e0588-123">`@page` muss die erste Razor-Anweisung auf einer Seite sein.</span><span class="sxs-lookup"><span data-stu-id="e0588-123">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="e0588-124">`@page` ist ein Beispiel für einen Übergang in Razor-spezifisches Markup.</span><span class="sxs-lookup"><span data-stu-id="e0588-124">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="e0588-125">Unter [Razor-Syntax](xref:mvc/views/razor#razor-syntax) finden Sie weitere Informationen.</span><span class="sxs-lookup"><span data-stu-id="e0588-125">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="e0588-126">Überprüfen Sie den Lambdaausdruck, der in der folgenden HTML-Hilfsfunktion verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="e0588-126">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title))
```

<span data-ttu-id="e0588-127">Das HTML-Hilfsprogramm `DisplayNameFor` überprüft die Eigenschaft `Title`, auf die im Lambdaausdruck verwiesen wird, um den Anzeigenamen zu bestimmen.</span><span class="sxs-lookup"><span data-stu-id="e0588-127">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="e0588-128">Der Lambdaausdruck wird überprüft und nicht ausgewertet.</span><span class="sxs-lookup"><span data-stu-id="e0588-128">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="e0588-129">Das bedeutet, dass keine Zugriffsverletzung auftritt, wenn `model`, `model.Movie` oder `model.Movie[0]` `null` oder leer sind.</span><span class="sxs-lookup"><span data-stu-id="e0588-129">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` are `null` or empty.</span></span> <span data-ttu-id="e0588-130">Wenn der Lambdaausdruck ausgewertet wird, (z.B. mit `@Html.DisplayFor(modelItem => item.Title)`), werden die Eigenschaftswerte ausgewertet.</span><span class="sxs-lookup"><span data-stu-id="e0588-130">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="e0588-131">Die @model -Direktive</span><span class="sxs-lookup"><span data-stu-id="e0588-131">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="e0588-132">Die `@model`-Anweisung gibt den Typ des Modells an, das an die Razor Page weitergegeben wird.</span><span class="sxs-lookup"><span data-stu-id="e0588-132">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="e0588-133">Im vorherigen Beispiel macht die `@model`-Linie die von `PageModel` abgeleitete Klasse der Razor Page verfügbar.</span><span class="sxs-lookup"><span data-stu-id="e0588-133">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="e0588-134">Das Modell wird in den [HTML Helpers (HTML-Hilfsprogrammen)](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) `@Html.DisplayNameFor` und `@Html.DisplayFor` auf der Seite verwendet.</span><span class="sxs-lookup"><span data-stu-id="e0588-134">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="e0588-135">Die Seite „Layout“</span><span class="sxs-lookup"><span data-stu-id="e0588-135">The layout page</span></span>

<span data-ttu-id="e0588-136">Wählen Sie die Menülinks aus (**RazorPagesMovie**, **Home**, **Datenschutz**).</span><span class="sxs-lookup"><span data-stu-id="e0588-136">Select the menu links (**RazorPagesMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="e0588-137">Auf jeder Seite wird dasselbe Menülayout gezeigt.</span><span class="sxs-lookup"><span data-stu-id="e0588-137">Each page shows the same menu layout.</span></span> <span data-ttu-id="e0588-138">Das Menülayout wird mithilfe der Datei *Pages/Shared/_Layout.cshtml* implementiert.</span><span class="sxs-lookup"><span data-stu-id="e0588-138">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="e0588-139">Öffnen Sie die Datei *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="e0588-139">Open the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="e0588-140">Durch [Layout](xref:mvc/views/layout)-Vorlagen kann das HTML-Containerlayout:</span><span class="sxs-lookup"><span data-stu-id="e0588-140">[Layout](xref:mvc/views/layout) templates allow the HTML container layout to be:</span></span>

* <span data-ttu-id="e0588-141">An einem Ort angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="e0588-141">Specified in one place.</span></span>
* <span data-ttu-id="e0588-142">Auf mehreren Seiten der Website angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="e0588-142">Applied in multiple pages in the site.</span></span>

<span data-ttu-id="e0588-143">Suchen Sie die Zeile `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="e0588-143">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="e0588-144">`RenderBody` ist ein Platzhalter, bei dem alle seitenspezifischen Ansichten von der Layoutseite *umschlossen* angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="e0588-144">`RenderBody` is a placeholder where all the page-specific views show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="e0588-145">Wenn Sie beispielsweise den Link **Privacy** auswählen, wird die Ansicht *Pages/Privacy.cshtml* in der `RenderBody`-Methode gerendert.</span><span class="sxs-lookup"><span data-stu-id="e0588-145">For example, select the **Privacy** link and the *Pages/Privacy.cshtml* view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="e0588-146">ViewData und Layout</span><span class="sxs-lookup"><span data-stu-id="e0588-146">ViewData and layout</span></span>

<span data-ttu-id="e0588-147">Sehen Sie sich das folgenden Markup aus der Datei *Pages/Movies/Index.cshtml* an:</span><span class="sxs-lookup"><span data-stu-id="e0588-147">Consider the following markup from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="e0588-148">Das oben markierte Markup ist ein Beispiel vom Übergang von Razor in C#.</span><span class="sxs-lookup"><span data-stu-id="e0588-148">The preceding highlighted markup is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="e0588-149">Die Zeichen `{` und `}` schließen einen Block mit C#-Code ein.</span><span class="sxs-lookup"><span data-stu-id="e0588-149">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="e0588-150">Die Basisklasse `PageModel` verfügt über eine `ViewData`-Wörterbucheigenschaft, die zum Hinzufügen von Daten verwendet werden kann, die an eine Ansicht übergeben werden sollen.</span><span class="sxs-lookup"><span data-stu-id="e0588-150">The `PageModel` base class contains a `ViewData` dictionary property that can be used to add data that and pass it to a View.</span></span> <span data-ttu-id="e0588-151">Mithilfe eines Schlüssel-Wert-Musters können dem `ViewData`-Wörterbuch Objekte hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="e0588-151">Objects are added to the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="e0588-152">Im vorherigen Beispiel wurde dem `"Title"`-Wörterbuch die Eigenschaft `ViewData` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="e0588-152">In the preceding sample, the `"Title"` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="e0588-153">Die Eigenschaft `"Title"` wird in der Datei *Pages/Shared/_Layout.cshtml* verwendet.</span><span class="sxs-lookup"><span data-stu-id="e0588-153">The `"Title"` property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="e0588-154">Das folgende Markup zeigt die ersten Zeilen der Datei *Pages/_Layout.cshtml* an.</span><span class="sxs-lookup"><span data-stu-id="e0588-154">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

<span data-ttu-id="e0588-155">Die Zeile `@*Markup removed for brevity.*@` ist ein Razor-Kommentar.</span><span class="sxs-lookup"><span data-stu-id="e0588-155">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="e0588-156">Im Gegensatz zu HTML-Kommentaren (`<!-- -->`) werden Razor-Kommentare nicht an den Client gesendet.</span><span class="sxs-lookup"><span data-stu-id="e0588-156">Unlike HTML comments (`<!-- -->`), Razor comments are not sent to the client.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="e0588-157">Aktualisieren des Layouts</span><span class="sxs-lookup"><span data-stu-id="e0588-157">Update the layout</span></span>

<span data-ttu-id="e0588-158">Ändern Sie das `<title>`-Element in der Datei *Pages/Shared/_Layout.cshtml*, um **Movie** anstelle von **RazorPagesMovie** anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="e0588-158">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="e0588-159">Suchen Sie in der Datei *Pages/Shared/_Layout.cshtml* das folgende Ankerelement.</span><span class="sxs-lookup"><span data-stu-id="e0588-159">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="e0588-160">Ersetzen Sie das vorhergehende Element durch das folgende Markup.</span><span class="sxs-lookup"><span data-stu-id="e0588-160">Replace the preceding element with the following markup:</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="e0588-161">Das vorangehende Ankerelement ist ein [Tag Helper (Taghilfsprogramm)](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="e0588-161">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="e0588-162">In diesem Fall handelt es sich um ein [Anchor Tag Helper (Anchor-Taghilfsprogramm)](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="e0588-162">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="e0588-163">Das Taghilfsattribut und der -wert `asp-page="/Movies/Index"` erstellt einen Link zur Razor Page `/Movies/Index`.</span><span class="sxs-lookup"><span data-stu-id="e0588-163">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="e0588-164">Das `asp-area`-Attribut ist leer, daher wird der Bereich im Link nicht verwendet.</span><span class="sxs-lookup"><span data-stu-id="e0588-164">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="e0588-165">Weitere Informationen finden Sie unter [Bereiche](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="e0588-165">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="e0588-166">Speichern Sie Ihre Änderungen, und testen Sie die App, indem Sie auf den Link **RpMovie** klicken.</span><span class="sxs-lookup"><span data-stu-id="e0588-166">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="e0588-167">Wenn Probleme auftreten, sehen Sie sich die Datei [_Layout.cshtml](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) in GitHub an.</span><span class="sxs-lookup"><span data-stu-id="e0588-167">See the [_Layout.cshtml](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="e0588-168">Testen Sie die anderen Links – **Home** (Startseite), **RpMovie** (Razor Pages-Film), **Create** (Erstellen), **Edit** (Bearbeiten) und **Delete** (Löschen).</span><span class="sxs-lookup"><span data-stu-id="e0588-168">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="e0588-169">Jede Seite legt den Titel fest, der in der Browserregisterkarte angezeigt wird. Wenn Sie eine Seite mit einem Lesezeichen versehen, wird dieser Titel für das Lesezeichen verwendet.</span><span class="sxs-lookup"><span data-stu-id="e0588-169">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="e0588-170">Sie können unter Umständen in das Feld `Price` keine Kommas als Dezimaltrennzeichen eingeben.</span><span class="sxs-lookup"><span data-stu-id="e0588-170">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="e0588-171">Zur Unterstützung der [jQuery-Validierung](https://jqueryvalidation.org/) für nicht englische Gebietsschemas, in denen ein Komma („,“) als Dezimaltrennzeichen verwendet wird, und Nicht-US-englische Datums- und Uhrzeitformate müssen Sie Schritte zur Globalisierung Ihrer App ausführen.</span><span class="sxs-lookup"><span data-stu-id="e0588-171">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="e0588-172">In diesem [GitHub-Problem 4076](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) finden Sie Anweisungen zum Hinzufügen von Kommas als Dezimaltrennzeichen.</span><span class="sxs-lookup"><span data-stu-id="e0588-172">See this [GitHub issue 4076](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="e0588-173">Die Eigenschaft `Layout` wird in der Datei *Pages/_ViewStart.cshtml* festgelegt:</span><span class="sxs-lookup"><span data-stu-id="e0588-173">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/_ViewStart.cshtml)]

<span data-ttu-id="e0588-174">Das vorhergehende Markup legt die Layoutdatei für alle Razor-Dateien unter dem Ordner *Pages* auf *Pages/Shared/_Layout.cshtml* fest.</span><span class="sxs-lookup"><span data-stu-id="e0588-174">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="e0588-175">Weitere Informationen finden Sie unter [Layout](xref:razor-pages/index#layout).</span><span class="sxs-lookup"><span data-stu-id="e0588-175">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="e0588-176">Das Seitenmodell „Create“</span><span class="sxs-lookup"><span data-stu-id="e0588-176">The Create page model</span></span>

<span data-ttu-id="e0588-177">Überprüfen Sie das Seitenmodell *Pages/Movies/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="e0588-177">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="e0588-178">Die Methode `OnGet` initialisiert einen beliebigen Zustand, der für die Seite erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="e0588-178">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="e0588-179">Die Seite „Create“ (Erstellen) verfügt nicht über einen initialisierbaren Zustand. Deshalb wird `Page` zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="e0588-179">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="e0588-180">Später in diesem Tutorial wird ein Beispiel für `OnGet` zur Initialisierung des Zustands gezeigt.</span><span class="sxs-lookup"><span data-stu-id="e0588-180">Later in the tutorial, an example of `OnGet` initializing state is shown.</span></span> <span data-ttu-id="e0588-181">Die Methode `Page` erstellt ein `PageResult`-Objekt, das die Seite *Create.cshtml* rendert.</span><span class="sxs-lookup"><span data-stu-id="e0588-181">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="e0588-182">Die Eigenschaft `Movie` verwendet das `[BindProperty]`-Attribut, um die [Modellbindung](xref:mvc/models/model-binding) zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="e0588-182">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="e0588-183">Wenn das Formular „Create“ die Formularwerte bereitstellt, bindet die ASP.NET Core-Laufzeit die bereitgestellten Werte an das `Movie`-Modell.</span><span class="sxs-lookup"><span data-stu-id="e0588-183">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="e0588-184">Die Methode `OnPostAsync` wird ausgeführt, wenn die Seite Formulardaten bereitstellt:</span><span class="sxs-lookup"><span data-stu-id="e0588-184">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="e0588-185">Wenn es zu Modellfehlern kommt, wird das Formular mit allen bereitgestellten Formulardaten erneut angezeigt.</span><span class="sxs-lookup"><span data-stu-id="e0588-185">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="e0588-186">Die meisten Modellfehler können clientseitig abgefangen werden, bevor das Formular bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="e0588-186">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="e0588-187">Ein Beispiel für einen Modellfehler ist die Bereitstellung eines Werts für das Datumsfeld, der nicht in ein Datum konvertiert werden kann.</span><span class="sxs-lookup"><span data-stu-id="e0588-187">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="e0588-188">Die clientseitige Validierung und die Modellvalidierung werden später in diesem Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="e0588-188">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="e0588-189">Wenn keine Modellfehler auftreten, werden die Daten gespeichert und der Browser zur Indexseite umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="e0588-189">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-razor-page"></a><span data-ttu-id="e0588-190">Die Razor Page „Create“</span><span class="sxs-lookup"><span data-stu-id="e0588-190">The Create Razor Page</span></span>

<span data-ttu-id="e0588-191">Betrachten Sie die Datei *Pages/Movies/Create.cshtml* der Razor Page:</span><span class="sxs-lookup"><span data-stu-id="e0588-191">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e0588-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e0588-192">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e0588-193">Visual Studio zeigt die folgenden Tags in einer bestimmten Schriftart an, die für Taghilfsprogramme verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="e0588-193">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![VS17-Ansicht der Create.cshtml-Seite](page/_static/th3.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e0588-195">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e0588-195">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e0588-196">Die folgenden Taghilfsprogramme werden im vorangehenden Markup angezeigt:</span><span class="sxs-lookup"><span data-stu-id="e0588-196">The following Tag Helpers are shown in the preceding markup:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e0588-197">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="e0588-197">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e0588-198">Visual Studio zeigt die folgenden Tags in einer bestimmten Schriftart an, die für Taghilfsprogramme verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="e0588-198">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

<span data-ttu-id="e0588-199">Das Element `<form method="post">` ist ein [Form Tag Helper (Hilfsprogramm für Formulartags)](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="e0588-199">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="e0588-200">Das Hilfsprogramm für Formulartags enthält automatisch ein [antiforgery token (Antifälschungstoken)](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="e0588-200">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="e0588-201">Die Gerüstbau-Engine erstellt Razor-Markup für jedes Feld im Modell (ausgenommen die ID) ähnlich wie folgt:</span><span class="sxs-lookup"><span data-stu-id="e0588-201">The scaffolding engine creates Razor markup for each field in the model (except the ID) similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="e0588-202">Das [Hilfsprogramm für Validierungstags](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` und `<span asp-validation-for`) zeigt Validierungsfehler.</span><span class="sxs-lookup"><span data-stu-id="e0588-202">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="e0588-203">Die Validierung wird an späterer Stelle in dieser Reihe detaillierter beschrieben.</span><span class="sxs-lookup"><span data-stu-id="e0588-203">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="e0588-204">Das [Label Tag Helper (Taghilfsprogramm für Label)](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generiert den Titel des Labels und das `for`-Attribut für die Eigenschaft `Title`.</span><span class="sxs-lookup"><span data-stu-id="e0588-204">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="e0588-205">Das [Hilfsprogramm für Eingabetags](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) verwendet die Attribute von [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) und generiert HTML-Attribute, die auf der Clientseite für die jQuery-Validierung erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="e0588-205">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

<span data-ttu-id="e0588-206">Weitere Informationen zu Taghilfsprogrammen wie `<form method="post">` finden Sie unter [Taghilfsprogramme in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="e0588-206">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e0588-207">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="e0588-207">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="e0588-208">[Zurück: Hinzufügen eines Modells](xref:tutorials/razor-pages/model)
> [Nächster Schritt: Datenbank](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="e0588-208">[Previous: Adding a model](xref:tutorials/razor-pages/model)
[Next: Database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e0588-209">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e0588-209">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e0588-210">In diesem Tutorial werden die Razor Pages näher untersucht, die durch Gerüstbau im [vorherigen Tutorial](xref:tutorials/razor-pages/model) erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="e0588-210">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

<span data-ttu-id="e0588-211">Beispiel [Anzeigen oder Herunterladen](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22).</span><span class="sxs-lookup"><span data-stu-id="e0588-211">[View or download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22) sample.</span></span>

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="e0588-212">Die Seiten „Create“, „Delete“, „Details“ und „Edit“</span><span class="sxs-lookup"><span data-stu-id="e0588-212">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="e0588-213">Sehen Sie sich das Seitenmodell *Pages/Movies/Index.cshtml.cs* an:</span><span class="sxs-lookup"><span data-stu-id="e0588-213">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="e0588-214">Razor Pages werden von `PageModel` abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="e0588-214">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="e0588-215">Gemäß der Konvention wird die von `PageModel` abgeleitete Klasse `<PageName>Model` genannt.</span><span class="sxs-lookup"><span data-stu-id="e0588-215">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="e0588-216">Der Konstruktor verwendet [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection), um `RazorPagesMovieContext` zur Seite hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="e0588-216">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="e0588-217">Alle per Gerüstbau erstellten Seiten folgen diesem Muster.</span><span class="sxs-lookup"><span data-stu-id="e0588-217">All the scaffolded pages follow this pattern.</span></span> <span data-ttu-id="e0588-218">Weitere Informationen zum asynchronen Programmieren mit Entity Framework finden Sie unter [Asynchroner Code](xref:data/ef-rp/intro#asynchronous-code).</span><span class="sxs-lookup"><span data-stu-id="e0588-218">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="e0588-219">Wenn eine Anforderung an die Seite erfolgt, gibt die Methode `OnGetAsync` eine Filmliste an die Razor Page zurück.</span><span class="sxs-lookup"><span data-stu-id="e0588-219">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="e0588-220">`OnGetAsync` oder `OnGet` werden für eine Razor Page aufgerufen, um den Zustand der Seite zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="e0588-220">`OnGetAsync` or `OnGet` is called on a Razor Page to initialize the state for the page.</span></span> <span data-ttu-id="e0588-221">In diesem Fall ruft `OnGetAsync` eine Liste von Filmen ab und zeigt diese an.</span><span class="sxs-lookup"><span data-stu-id="e0588-221">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="e0588-222">Wenn `OnGet` `void` und `OnGetAsync` `Task` zurückgibt, wird keine Rückgabemethode verwendet.</span><span class="sxs-lookup"><span data-stu-id="e0588-222">When `OnGet` returns `void` or `OnGetAsync` returns`Task`, no return method is used.</span></span> <span data-ttu-id="e0588-223">Wenn der Rückgabetyp `IActionResult` oder `Task<IActionResult>` ist, muss eine Rückgabeanweisung bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="e0588-223">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="e0588-224">Die kann z.B. die `OnPostAsync`-Methode *Pages/Movies/Create.cshtml.cs* sein:</span><span class="sxs-lookup"><span data-stu-id="e0588-224">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> <span data-ttu-id="e0588-225">Sehen Sie sich die Razor-Seite *Pages/Movies/Index.cshtml* an:</span><span class="sxs-lookup"><span data-stu-id="e0588-225">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

<span data-ttu-id="e0588-226">Razor kann aus HTML in C# oder Razor-spezifisches Markup übergehen.</span><span class="sxs-lookup"><span data-stu-id="e0588-226">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="e0588-227">Wenn auf ein `@`-Symbol ein für Razor reserviertes Schlüsselwort ([Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords)) folgt, wechselt es in Razor-spezifisches Markup. Andernfalls geht es in C# über.</span><span class="sxs-lookup"><span data-stu-id="e0588-227">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

<span data-ttu-id="e0588-228">Die Razor-Anweisung `@page` wandelt die Datei in eine MVC-Aktion um. Das bedeutet, dass sie Anforderungen verarbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="e0588-228">The `@page` Razor directive makes the file into an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="e0588-229">`@page` muss die erste Razor-Anweisung auf einer Seite sein.</span><span class="sxs-lookup"><span data-stu-id="e0588-229">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="e0588-230">`@page` ist ein Beispiel für einen Übergang in Razor-spezifisches Markup.</span><span class="sxs-lookup"><span data-stu-id="e0588-230">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="e0588-231">Unter [Razor-Syntax](xref:mvc/views/razor#razor-syntax) finden Sie weitere Informationen.</span><span class="sxs-lookup"><span data-stu-id="e0588-231">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="e0588-232">Überprüfen Sie den Lambdaausdruck, der in der folgenden HTML-Hilfsfunktion verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="e0588-232">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title))
```

<span data-ttu-id="e0588-233">Das HTML-Hilfsprogramm `DisplayNameFor` überprüft die Eigenschaft `Title`, auf die im Lambdaausdruck verwiesen wird, um den Anzeigenamen zu bestimmen.</span><span class="sxs-lookup"><span data-stu-id="e0588-233">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="e0588-234">Der Lambdaausdruck wird überprüft und nicht ausgewertet.</span><span class="sxs-lookup"><span data-stu-id="e0588-234">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="e0588-235">Das bedeutet, dass keine Zugriffsverletzung auftritt, wenn `model`, `model.Movie` oder `model.Movie[0]` `null` oder leer sind.</span><span class="sxs-lookup"><span data-stu-id="e0588-235">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` are `null` or empty.</span></span> <span data-ttu-id="e0588-236">Wenn der Lambdaausdruck ausgewertet wird, (z.B. mit `@Html.DisplayFor(modelItem => item.Title)`), werden die Eigenschaftswerte ausgewertet.</span><span class="sxs-lookup"><span data-stu-id="e0588-236">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="e0588-237">Die @model -Direktive</span><span class="sxs-lookup"><span data-stu-id="e0588-237">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="e0588-238">Die `@model`-Anweisung gibt den Typ des Modells an, das an die Razor Page weitergegeben wird.</span><span class="sxs-lookup"><span data-stu-id="e0588-238">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="e0588-239">Im vorherigen Beispiel macht die `@model`-Linie die von `PageModel` abgeleitete Klasse der Razor Page verfügbar.</span><span class="sxs-lookup"><span data-stu-id="e0588-239">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="e0588-240">Das Modell wird in den [HTML Helpers (HTML-Hilfsprogrammen)](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) `@Html.DisplayNameFor` und `@Html.DisplayFor` auf der Seite verwendet.</span><span class="sxs-lookup"><span data-stu-id="e0588-240">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="e0588-241">Die Seite „Layout“</span><span class="sxs-lookup"><span data-stu-id="e0588-241">The layout page</span></span>

<span data-ttu-id="e0588-242">Wählen Sie die Menülinks aus (**RazorPagesMovie**, **Home**, **Datenschutz**).</span><span class="sxs-lookup"><span data-stu-id="e0588-242">Select the menu links (**RazorPagesMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="e0588-243">Auf jeder Seite wird dasselbe Menülayout gezeigt.</span><span class="sxs-lookup"><span data-stu-id="e0588-243">Each page shows the same menu layout.</span></span> <span data-ttu-id="e0588-244">Das Menülayout wird mithilfe der Datei *Pages/Shared/_Layout.cshtml* implementiert.</span><span class="sxs-lookup"><span data-stu-id="e0588-244">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="e0588-245">Öffnen Sie die Datei *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="e0588-245">Open the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="e0588-246">[Layout](xref:mvc/views/layout)-Vorlagen ermöglichen Ihnen, das HTML-Containerlayout Ihrer Website zentral anzugeben und dann auf mehrere Seiten Ihrer Website anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="e0588-246">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="e0588-247">Suchen Sie die Zeile `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="e0588-247">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="e0588-248">`RenderBody` ist ein Platzhalter, bei dem alle seitenspezifischen Ansichten, die Sie erstellen, von der Layoutseite *umschlossen* angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="e0588-248">`RenderBody` is a placeholder where all the page-specific views you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="e0588-249">Wenn Sie beispielsweise den Link **Privacy** auswählen, wird die Ansicht **Pages/Privacy.cshtml** in der `RenderBody`-Methode gerendert.</span><span class="sxs-lookup"><span data-stu-id="e0588-249">For example, if you select the **Privacy** link, the **Pages/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="e0588-250">ViewData und Layout</span><span class="sxs-lookup"><span data-stu-id="e0588-250">ViewData and layout</span></span>

<span data-ttu-id="e0588-251">Sehen Sie sich den folgenden Code aus der Datei *Pages/Movies/Index.cshtml* an:</span><span class="sxs-lookup"><span data-stu-id="e0588-251">Consider the following code from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="e0588-252">Der oben markierte Code ist ein Beispiel vom Übergang von Razor in C#.</span><span class="sxs-lookup"><span data-stu-id="e0588-252">The preceding highlighted code is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="e0588-253">Die Zeichen `{` und `}` schließen einen Block mit C#-Code ein.</span><span class="sxs-lookup"><span data-stu-id="e0588-253">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="e0588-254">Die Basisklasse `PageModel` verfügt über eine `ViewData`-Wörterbucheigenschaft, die zum Hinzufügen von Daten verwendet werden kann, die an eine Ansicht übergeben werden sollen.</span><span class="sxs-lookup"><span data-stu-id="e0588-254">The `PageModel` base class has a `ViewData` dictionary property that can be used to add data that you want to pass to a View.</span></span> <span data-ttu-id="e0588-255">Mithilfe eines Schlüssel-Wert-Musters können dem `ViewData`-Wörterbuch Objekte hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="e0588-255">You add objects into the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="e0588-256">Im vorherigen Beispiel wurde dem `ViewData`-Wörterbuch die Eigenschaft „Title“ hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="e0588-256">In the preceding sample, the "Title" property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="e0588-257">Die Eigenschaft „Title“ wird in der Datei *Pages/Shared/_Layout.cshtml* verwendet.</span><span class="sxs-lookup"><span data-stu-id="e0588-257">The "Title" property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="e0588-258">Das folgende Markup zeigt die ersten Zeilen der Datei *Pages/_Layout.cshtml* an.</span><span class="sxs-lookup"><span data-stu-id="e0588-258">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

<span data-ttu-id="e0588-259">Die Zeile `@*Markup removed for brevity.*@` ist ein Razor-Kommentar, der in Ihrer Layoutdatei nicht angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="e0588-259">The line `@*Markup removed for brevity.*@` is a Razor comment which doesn't appear in your layout file.</span></span> <span data-ttu-id="e0588-260">Im Gegensatz zu HTML-Kommentaren (`<!-- -->`) werden Razor-Kommentare nicht an den Client gesendet.</span><span class="sxs-lookup"><span data-stu-id="e0588-260">Unlike HTML comments (`<!-- -->`), Razor comments are not sent to the client.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="e0588-261">Aktualisieren des Layouts</span><span class="sxs-lookup"><span data-stu-id="e0588-261">Update the layout</span></span>

<span data-ttu-id="e0588-262">Ändern Sie das `<title>`-Element in der Datei *Pages/Shared/_Layout.cshtml*, um **Movie** anstelle von **RazorPagesMovie** anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="e0588-262">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="e0588-263">Suchen Sie in der Datei *Pages/Shared/_Layout.cshtml* das folgende Ankerelement.</span><span class="sxs-lookup"><span data-stu-id="e0588-263">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="e0588-264">Ersetzen Sie das vorhergehende Element durch das folgende Markup.</span><span class="sxs-lookup"><span data-stu-id="e0588-264">Replace the preceding element with the following markup.</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="e0588-265">Das vorangehende Ankerelement ist ein [Tag Helper (Taghilfsprogramm)](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="e0588-265">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="e0588-266">In diesem Fall handelt es sich um ein [Anchor Tag Helper (Anchor-Taghilfsprogramm)](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="e0588-266">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="e0588-267">Das Taghilfsattribut und der -wert `asp-page="/Movies/Index"` erstellt einen Link zur Razor Page `/Movies/Index`.</span><span class="sxs-lookup"><span data-stu-id="e0588-267">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="e0588-268">Das `asp-area`-Attribut ist leer, daher wird der Bereich im Link nicht verwendet.</span><span class="sxs-lookup"><span data-stu-id="e0588-268">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="e0588-269">Weitere Informationen finden Sie unter [Bereiche](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="e0588-269">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="e0588-270">Speichern Sie Ihre Änderungen, und testen Sie die App, indem Sie auf den Link **RpMovie** klicken.</span><span class="sxs-lookup"><span data-stu-id="e0588-270">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="e0588-271">Wenn Probleme auftreten, sehen Sie sich die Datei [_Layout.cshtml](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) in GitHub an.</span><span class="sxs-lookup"><span data-stu-id="e0588-271">See the [_Layout.cshtml](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="e0588-272">Testen Sie die anderen Links – **Home** (Startseite), **RpMovie** (Razor Pages-Film), **Create** (Erstellen), **Edit** (Bearbeiten) und **Delete** (Löschen).</span><span class="sxs-lookup"><span data-stu-id="e0588-272">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="e0588-273">Jede Seite legt den Titel fest, der in der Browserregisterkarte angezeigt wird. Wenn Sie eine Seite mit einem Lesezeichen versehen, wird dieser Titel für das Lesezeichen verwendet.</span><span class="sxs-lookup"><span data-stu-id="e0588-273">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="e0588-274">Sie können unter Umständen in das Feld `Price` keine Kommas als Dezimaltrennzeichen eingeben.</span><span class="sxs-lookup"><span data-stu-id="e0588-274">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="e0588-275">Zur Unterstützung der [jQuery-Validierung](https://jqueryvalidation.org/) für nicht englische Gebietsschemas, in denen ein Komma („,“) als Dezimaltrennzeichen verwendet wird, und Nicht-US-englische Datums- und Uhrzeitformate müssen Sie Schritte zur Globalisierung Ihrer App ausführen.</span><span class="sxs-lookup"><span data-stu-id="e0588-275">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="e0588-276">In diesem [GitHub-Problem 4076](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) finden Sie Anweisungen zum Hinzufügen von Kommas als Dezimaltrennzeichen.</span><span class="sxs-lookup"><span data-stu-id="e0588-276">This [GitHub issue 4076](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="e0588-277">Die Eigenschaft `Layout` wird in der Datei *Pages/_ViewStart.cshtml* festgelegt:</span><span class="sxs-lookup"><span data-stu-id="e0588-277">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

<span data-ttu-id="e0588-278">Das vorhergehende Markup legt die Layoutdatei für alle Razor-Dateien unter dem Ordner *Pages* auf *Pages/Shared/_Layout.cshtml* fest.</span><span class="sxs-lookup"><span data-stu-id="e0588-278">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="e0588-279">Weitere Informationen finden Sie unter [Layout](xref:razor-pages/index#layout).</span><span class="sxs-lookup"><span data-stu-id="e0588-279">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="e0588-280">Das Seitenmodell „Create“</span><span class="sxs-lookup"><span data-stu-id="e0588-280">The Create page model</span></span>

<span data-ttu-id="e0588-281">Überprüfen Sie das Seitenmodell *Pages/Movies/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="e0588-281">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="e0588-282">Die Methode `OnGet` initialisiert einen beliebigen Zustand, der für die Seite erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="e0588-282">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="e0588-283">Die Seite „Create“ (Erstellen) verfügt nicht über einen initialisierbaren Zustand. Deshalb wird `Page` zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="e0588-283">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="e0588-284">Im späteren Verlauf des Tutorials initialisiert die Methode `OnGet` einen Zustand.</span><span class="sxs-lookup"><span data-stu-id="e0588-284">Later in the tutorial you see `OnGet` method initialize state.</span></span> <span data-ttu-id="e0588-285">Die Methode `Page` erstellt ein `PageResult`-Objekt, das die Seite *Create.cshtml* rendert.</span><span class="sxs-lookup"><span data-stu-id="e0588-285">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="e0588-286">Die Eigenschaft `Movie` verwendet das `[BindProperty]`-Attribut, um die [Modellbindung](xref:mvc/models/model-binding) zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="e0588-286">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="e0588-287">Wenn das Formular „Create“ die Formularwerte bereitstellt, bindet die ASP.NET Core-Laufzeit die bereitgestellten Werte an das `Movie`-Modell.</span><span class="sxs-lookup"><span data-stu-id="e0588-287">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="e0588-288">Die Methode `OnPostAsync` wird ausgeführt, wenn die Seite Formulardaten bereitstellt:</span><span class="sxs-lookup"><span data-stu-id="e0588-288">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="e0588-289">Wenn es zu Modellfehlern kommt, wird das Formular mit allen bereitgestellten Formulardaten erneut angezeigt.</span><span class="sxs-lookup"><span data-stu-id="e0588-289">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="e0588-290">Die meisten Modellfehler können clientseitig abgefangen werden, bevor das Formular bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="e0588-290">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="e0588-291">Ein Beispiel für einen Modellfehler ist die Bereitstellung eines Werts für das Datumsfeld, der nicht in ein Datum konvertiert werden kann.</span><span class="sxs-lookup"><span data-stu-id="e0588-291">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="e0588-292">Die clientseitige Validierung und die Modellvalidierung werden später in diesem Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="e0588-292">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="e0588-293">Wenn keine Modellfehler auftreten, werden die Daten gespeichert und der Browser zur Indexseite umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="e0588-293">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-razor-page"></a><span data-ttu-id="e0588-294">Die Razor Page „Create“</span><span class="sxs-lookup"><span data-stu-id="e0588-294">The Create Razor Page</span></span>

<span data-ttu-id="e0588-295">Betrachten Sie die Datei *Pages/Movies/Create.cshtml* der Razor Page:</span><span class="sxs-lookup"><span data-stu-id="e0588-295">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e0588-296">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e0588-296">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e0588-297">Visual Studio zeigt das `<form method="post">`-Tag in einer bestimmten Schriftart an, die für Taghilfsprogramme verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="e0588-297">Visual Studio displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers:</span></span>

![VS17-Ansicht der Create.cshtml-Seite](page/_static/th.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e0588-299">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e0588-299">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e0588-300">Weitere Informationen zu Taghilfsprogrammen wie `<form method="post">` finden Sie unter [Taghilfsprogramme in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="e0588-300">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e0588-301">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="e0588-301">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e0588-302">Visual Studio für Mac zeigt das `<form method="post">`-Tag in einer bestimmten Schriftart an, die für Taghilfsprogramme verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="e0588-302">Visual Studio for Mac displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers.</span></span>

---

<span data-ttu-id="e0588-303">Das Element `<form method="post">` ist ein [Form Tag Helper (Hilfsprogramm für Formulartags)](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="e0588-303">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="e0588-304">Das Hilfsprogramm für Formulartags enthält automatisch ein [antiforgery token (Antifälschungstoken)](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="e0588-304">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="e0588-305">Die Gerüstbau-Engine erstellt Razor-Markup für jedes Feld im Modell (ausgenommen die ID) ähnlich wie folgt:</span><span class="sxs-lookup"><span data-stu-id="e0588-305">The scaffolding engine creates Razor markup for each field in the model (except the ID) similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="e0588-306">Das [Hilfsprogramm für Validierungstags](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` und `<span asp-validation-for`) zeigt Validierungsfehler.</span><span class="sxs-lookup"><span data-stu-id="e0588-306">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="e0588-307">Die Validierung wird an späterer Stelle in dieser Reihe detaillierter beschrieben.</span><span class="sxs-lookup"><span data-stu-id="e0588-307">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="e0588-308">Das [Label Tag Helper (Taghilfsprogramm für Label)](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generiert den Titel des Labels und das `for`-Attribut für die Eigenschaft `Title`.</span><span class="sxs-lookup"><span data-stu-id="e0588-308">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="e0588-309">Das [Hilfsprogramm für Eingabetags](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) verwendet die Attribute von [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) und generiert HTML-Attribute, die auf der Clientseite für die jQuery-Validierung erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="e0588-309">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e0588-310">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="e0588-310">Additional resources</span></span>

* [<span data-ttu-id="e0588-311">Dieses Tutorial auf YouTube</span><span class="sxs-lookup"><span data-stu-id="e0588-311">YouTube version of this tutorial</span></span>](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> <span data-ttu-id="e0588-312">[Zurück: Hinzufügen eines Modells](xref:tutorials/razor-pages/model)
> [Nächster Schritt: Datenbank](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="e0588-312">[Previous: Adding a model](xref:tutorials/razor-pages/model)
[Next: Database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end

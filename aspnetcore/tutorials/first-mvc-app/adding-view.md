---
title: Hinzufügen einer Ansicht zu einer ASP.NET Core MVC-App
author: rick-anderson
description: Hinzufügen einer Ansicht zu einer einfachen ASP.NET Core MVC-App
ms.author: riande
ms.date: 03/04/2017
uid: tutorials/first-mvc-app/adding-view
ms.openlocfilehash: 32eddb233a8a6b9b8f480926673d15d568ce6ede
ms.sourcegitcommit: c47d7c131eebbcd8811e31edda210d64cf4b9d6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55236431"
---
# <a name="add-a-view-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="780e8-103">Hinzufügen einer Ansicht zu einer ASP.NET Core MVC-App</span><span class="sxs-lookup"><span data-stu-id="780e8-103">Add a view to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="780e8-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="780e8-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="780e8-105">In diesem Abschnitt ändern Sie die `HelloWorldController`-Klasse so, dass [Razor](xref:mvc/views/razor)-Ansichtsdateien verwendet werden, um den Prozess des Generierens von HTML-Antworten für einen Client sauber zu kapseln.</span><span class="sxs-lookup"><span data-stu-id="780e8-105">In this section you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="780e8-106">Sie erstellen eine Ansichtsvorlagendatei mithilfe von Razor.</span><span class="sxs-lookup"><span data-stu-id="780e8-106">You create a view template file using Razor.</span></span> <span data-ttu-id="780e8-107">Razor-basierte Ansichtsvorlagen haben die Dateinamenerweiterung *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="780e8-107">Razor-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="780e8-108">Sie bieten eine elegante Möglichkeit zum Erstellen einer HTML-Ausgabe mit C#.</span><span class="sxs-lookup"><span data-stu-id="780e8-108">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="780e8-109">Derzeit gibt die `Index`-Methode eine Zeichenfolge mit der Meldung zurück, die in der Controllerklasse hartcodiert ist.</span><span class="sxs-lookup"><span data-stu-id="780e8-109">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="780e8-110">Ersetzen Sie in der `HelloWorldController`-Klasse die `Index`-Methode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="780e8-110">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="780e8-111">Der vorangehende Code ruft die Methode<xref:Microsoft.AspNetCore.Mvc.Controller.View*> des Controllers auf.</span><span class="sxs-lookup"><span data-stu-id="780e8-111">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="780e8-112">Er verwendet eine Ansichtsvorlage zum Generieren einer HTML-Antwort.</span><span class="sxs-lookup"><span data-stu-id="780e8-112">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="780e8-113">Controllermethoden (auch *Aktionsmethoden* genannt), wie z. B. die `Index`-Methode oben, geben in der Regel ein <xref:Microsoft.AspNetCore.Mvc.IActionResult> (oder eine von <xref:Microsoft.AspNetCore.Mvc.ActionResult> abgeleitete Klasse) und keinen Typ wie `string` zurück.</span><span class="sxs-lookup"><span data-stu-id="780e8-113">Controller methods (also known as *action methods*), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="780e8-114">Hinzufügen einer Ansicht</span><span class="sxs-lookup"><span data-stu-id="780e8-114">Add a view</span></span>

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="780e8-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="780e8-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="780e8-116">Klicken Sie mit der rechten Maustaste auf den Ordner *Views*, und klicken Sie dann **Hinzufügen > Neuer Ordner**. Nennen Sie den Ordner *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="780e8-116">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="780e8-117">Klicken Sie mit der rechten Maustaste auf den Ordner *Views/HelloWorld*, und klicken Sie dann **Hinzufügen > Neues Element**.</span><span class="sxs-lookup"><span data-stu-id="780e8-117">Right click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="780e8-118">Im Dialogfeld **Neues Element hinzufügen: MvcMovie**</span><span class="sxs-lookup"><span data-stu-id="780e8-118">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="780e8-119">Geben Sie in das Suchfeld rechts oben *Ansicht* ein.</span><span class="sxs-lookup"><span data-stu-id="780e8-119">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="780e8-120">Wählen Sie **Razor-Ansicht** aus.</span><span class="sxs-lookup"><span data-stu-id="780e8-120">Select **Razor View**</span></span>

  * <span data-ttu-id="780e8-121">Übernehmen Sie den im Feld **Name** stehenden Wert *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="780e8-121">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="780e8-122">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="780e8-122">Select **Add**</span></span>

![Dialogfeld „Neues Element hinzufügen“](adding-view/_static/add_view.png)

<!-- Code -------------------------->
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="780e8-124">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="780e8-124">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="780e8-125">Fügen Sie die Ansicht `Index` für `HelloWorldController` hinzu.</span><span class="sxs-lookup"><span data-stu-id="780e8-125">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="780e8-126">Fügen Sie einen neuen Ordner namens *Views/HelloWorld* hinzu.</span><span class="sxs-lookup"><span data-stu-id="780e8-126">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="780e8-127">Fügen Sie dem Ordner *Views/HelloWorld* eine neue Datei namens *Index.cshtml* hinzu.</span><span class="sxs-lookup"><span data-stu-id="780e8-127">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

<!-- Mac -------------------------->
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="780e8-128">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="780e8-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="780e8-129">Klicken Sie mit der rechten Maustaste auf den Ordner *Views*, und klicken Sie dann **Hinzufügen > Neuer Ordner**. Nennen Sie den Ordner *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="780e8-129">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="780e8-130">Klicken Sie mit der rechten Maustaste auf den Ordner *Views/HelloWorld*, und klicken Sie dann **Hinzufügen > Neue Datei**.</span><span class="sxs-lookup"><span data-stu-id="780e8-130">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="780e8-131">Führen Sie im Dialogfeld **Neue Datei** folgende Aktionen aus:</span><span class="sxs-lookup"><span data-stu-id="780e8-131">In the **New File** dialog:</span></span>

  * <span data-ttu-id="780e8-132">Wählen Sie im linken Bereich **Web** aus.</span><span class="sxs-lookup"><span data-stu-id="780e8-132">Select **Web** in the left pane.</span></span>
  * <span data-ttu-id="780e8-133">Wählen Sie im mittleren Bereich **Leere HTML-Datei** aus.</span><span class="sxs-lookup"><span data-stu-id="780e8-133">Select **Empty HTML file** in the center pane.</span></span>
  * <span data-ttu-id="780e8-134">Geben Sie *Index.cshtml* in das Feld **Name** ein.</span><span class="sxs-lookup"><span data-stu-id="780e8-134">Type *Index.cshtml* in the **Name** box.</span></span>
  * <span data-ttu-id="780e8-135">Wählen Sie **Neu** aus.</span><span class="sxs-lookup"><span data-stu-id="780e8-135">Select **New**.</span></span>

![Dialogfeld „Neues Element hinzufügen“](adding-view/_static/add_view.png)

---  
<!-- End of VS tabs -->

<span data-ttu-id="780e8-137">Ersetzen Sie den Inhalt der Razor-Ansichtsdatei *Views/HelloWorld/Index.cshtml* durch Folgendes:</span><span class="sxs-lookup"><span data-stu-id="780e8-137">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="780e8-138">Navigieren Sie zu `https://localhost:xxxx/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="780e8-138">Navigate to `https://localhost:xxxx/HelloWorld`.</span></span> <span data-ttu-id="780e8-139">Die `Index`-Methode im `HelloWorldController` hatte nicht viel zu tun. Sie diente zum Ausführen der Anweisung `return View();`, die angab, dass die Methode eine Ansichtsvorlagendatei zum Rendern einer Antwort im Browser verwenden sollte.</span><span class="sxs-lookup"><span data-stu-id="780e8-139">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="780e8-140">Da Sie den Namen der Ansichtsvorlagendatei nicht explizit angegeben haben, verwendete MVC standardmäßig die Ansichtsdatei *Index.cshtml* im Ordner */Views/HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="780e8-140">Because you didn't explicitly specify the name of the view template file, MVC defaulted to using the *Index.cshtml* view file in the */Views/HelloWorld* folder.</span></span> <span data-ttu-id="780e8-141">Die folgende Abbildung zeigt die Zeichenfolge "Hello from our View Template!“,</span><span class="sxs-lookup"><span data-stu-id="780e8-141">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="780e8-142">die in der Ansicht hartcodiert ist.</span><span class="sxs-lookup"><span data-stu-id="780e8-142">hard-coded in the view.</span></span>

![Browserfenster](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="780e8-144">Ändern von Ansichten und Layoutseiten</span><span class="sxs-lookup"><span data-stu-id="780e8-144">Change views and layout pages</span></span>

<span data-ttu-id="780e8-145">Wählen Sie die Menülinks aus (**MvcMovie**, **Home**, **Privacy**).</span><span class="sxs-lookup"><span data-stu-id="780e8-145">Select the menu links (**MvcMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="780e8-146">Auf jeder Seite wird dasselbe Menülayout gezeigt.</span><span class="sxs-lookup"><span data-stu-id="780e8-146">Each page shows the same menu layout.</span></span> <span data-ttu-id="780e8-147">Das Menülayout wird mithilfe der Datei *Views/Shared/_Layout.cshtml* implementiert.</span><span class="sxs-lookup"><span data-stu-id="780e8-147">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="780e8-148">Öffnen Sie die Datei *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="780e8-148">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="780e8-149">[Layout](xref:mvc/views/layout)-Vorlagen ermöglichen Ihnen, das HTML-Containerlayout Ihrer Website zentral anzugeben und dann auf mehrere Seiten Ihrer Website anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="780e8-149">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="780e8-150">Suchen Sie die Zeile `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="780e8-150">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="780e8-151">`RenderBody` ist ein Platzhalter, bei dem alle ansichtsspezifischen Seiten, die Sie erstellen, von der Layoutseite *umschlossen* angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="780e8-151">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="780e8-152">Wenn Sie beispielsweise den Link **Privacy** auswählen, wird die Ansicht **Views/Home/Privacy.cshtml** in der `RenderBody`-Methode gerendert.</span><span class="sxs-lookup"><span data-stu-id="780e8-152">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="780e8-153">Ändern des Titels, der Fußzeile und Menülinks in der Layoutdatei</span><span class="sxs-lookup"><span data-stu-id="780e8-153">Change the title, footer, and menu link in the layout file</span></span>

* <span data-ttu-id="780e8-154">Ändern Sie in den Elementen „Titel“ und „Fußzeile“ `MvcMovie` in `Movie App`.</span><span class="sxs-lookup"><span data-stu-id="780e8-154">In the title and footer elements, change `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="780e8-155">Ändern Sie das Ankerlement `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` in `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span><span class="sxs-lookup"><span data-stu-id="780e8-155">Change the anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="780e8-156">Im folgenden Markup sind die Änderungen hervorgehoben dargestellt:</span><span class="sxs-lookup"><span data-stu-id="780e8-156">The following markup shows the highlighted changes:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Shared/_Layout.cshtml?highlight=6,24,51)]

<span data-ttu-id="780e8-157">Im obigen Markup wurde das `asp-area`-[Anchor-Taghilfsprogramm Attribut](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) weggelassen, weil in dieser App keine [Bereiche](xref:mvc/controllers/areas) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="780e8-157">In the preceding markup, the `asp-area` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<!-- Routing has changed in 2.2, it's going to the last route.
>[!WARNING]
> We haven't implemented the `Movies` controller yet, so if you click the `Movie App` link, you get a 404 (Not found) error.
-->

<span data-ttu-id="780e8-158">**Hinweis:** Der `Movies`-Controller wurde nicht implementiert.</span><span class="sxs-lookup"><span data-stu-id="780e8-158">**Note**: The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="780e8-159">An diesem Punkt ist der `Movie App`-Link nicht funktionsfähig.</span><span class="sxs-lookup"><span data-stu-id="780e8-159">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="780e8-160">Speichern Sie Ihre Änderungen, und wählen den **Privacy**-Link aus.</span><span class="sxs-lookup"><span data-stu-id="780e8-160">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="780e8-161">Wie Sie sehen, wird auf der Browserregisterkarte der Titel **Privacy Policy - Movie App** anstelle des Titels **Privacy Policy - Mvc Movie** angezeigt:</span><span class="sxs-lookup"><span data-stu-id="780e8-161">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![Registerkarte „Privacy“](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

<span data-ttu-id="780e8-163">Wählen Sie den Link **Home** aus, und beachten Sie, dass im Text für den Titel und den Anker ebenfalls **Movie App** angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="780e8-163">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="780e8-164">Wir haben also eine Änderung des Texts und Titels in der Layoutvorlage einmalig vorgenommen, die von der gesamten Website übernommen wird.</span><span class="sxs-lookup"><span data-stu-id="780e8-164">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="780e8-165">Untersuchen Sie die Datei *Views/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="780e8-165">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```HTML
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="780e8-166">Die Datei *Views/_ViewStart.cshtml* fügt jeder Ansicht die Datei *Views/Shared/_Layout.cshtml* hinzu.</span><span class="sxs-lookup"><span data-stu-id="780e8-166">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="780e8-167">Die `Layout`-Eigenschaft kann verwendet werden, um eine andere Layoutansicht festzulegen, oder legen Sie sie auf `null` fest, damit keine Layoutdatei verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="780e8-167">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="780e8-168">Ändern Sie den Titel und das `<h2>`-Element der Ansichtsdatei *Views/HelloWorld/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="780e8-168">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="780e8-169">Der Titel und das `<h2>`-Element sind geringfügig anders, sodass Sie sehen können, welcher Codeabschnitt die Anzeige ändert.</span><span class="sxs-lookup"><span data-stu-id="780e8-169">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="780e8-170">`ViewData["Title"] = "Movie List";` im Code oben legt die `Title`-Eigenschaft des Wörterbuchs `ViewData`auf „Movie List“ fest.</span><span class="sxs-lookup"><span data-stu-id="780e8-170">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="780e8-171">Die `Title`-Eigenschaft wird im HTML-Element `<title>` der Layoutseite verwendet:</span><span class="sxs-lookup"><span data-stu-id="780e8-171">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```HTML
<title>@ViewData["Title"] - Movie App</title>
   ```

<span data-ttu-id="780e8-172">Speichern Sie die Änderung, und navigieren Sie zu `https://localhost:xxxx/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="780e8-172">Save the change and navigate to `https://localhost:xxxx/HelloWorld`.</span></span> <span data-ttu-id="780e8-173">Sie sehen, dass sich der Browsertitel, die primäre Überschrift und die sekundären Überschriften geändert haben.</span><span class="sxs-lookup"><span data-stu-id="780e8-173">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="780e8-174">(Wenn die Änderungen nicht im Browser angezeigt werden, zeigen Sie ggf. zwischengespeicherten Inhalt an.</span><span class="sxs-lookup"><span data-stu-id="780e8-174">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="780e8-175">Drücken Sie in Ihrem Browser STRG+F5, um das Laden der Antwort vom Server zu erzwingen.) Der Titel des Browsers wird mithilfe des Elements `ViewData["Title"]` erstellt, das wir in der Ansichtsvorlange *Index.cshtml* festgelegt haben, und des zusätzlichen Elements „- Movie App“, das in der Layoutdatei hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="780e8-175">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="780e8-176">Beachten Sie außerdem, wie der Inhalt der Ansichtsvorlage *Index.cshtml* mit der Ansichtsvorlage *Views/Shared/_Layout.cshtml* zusammengeführt und eine einzelne HTML-Antwort an den Browser gesendet wurde.</span><span class="sxs-lookup"><span data-stu-id="780e8-176">Also notice how the content in the *Index.cshtml* view template was merged with the *Views/Shared/_Layout.cshtml* view template and a single HTML response was sent to the browser.</span></span> <span data-ttu-id="780e8-177">Layoutvorlagen erleichtern ungemein das Vornehmen von Änderungen an allen Seiten in Ihrer Anwendung.</span><span class="sxs-lookup"><span data-stu-id="780e8-177">Layout templates make it really easy to make changes that apply across all of the pages in your application.</span></span> <span data-ttu-id="780e8-178">Weitere Informationen dazu finden Sie unter [Layout](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="780e8-178">To learn more see [Layout](xref:mvc/views/layout).</span></span>

![Ansicht mit Filmliste](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="780e8-180">Unser kleiner Beitrag zu den „Daten“ (in diesem Fall die Meldung "Hello from our View Template!")</span><span class="sxs-lookup"><span data-stu-id="780e8-180">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="780e8-181">ist jedoch hartcodiert.</span><span class="sxs-lookup"><span data-stu-id="780e8-181">message) is hard-coded, though.</span></span> <span data-ttu-id="780e8-182">Die MVC-Anwendung weist ein „V“ (View [Ansicht]) auf, und Sie verfügen über ein „C“ (Controller), aber noch nicht über ein „M“ (Modell).</span><span class="sxs-lookup"><span data-stu-id="780e8-182">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="780e8-183">Übergeben von Daten vom Controller an die Ansicht</span><span class="sxs-lookup"><span data-stu-id="780e8-183">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="780e8-184">Controlleraktionen werden als Antwort auf eine eingehende URL-Anforderung aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="780e8-184">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="780e8-185">Eine Controllerklasse ist der Ort, an dem der Code geschrieben wird, der die eingehenden Browseranforderungen verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="780e8-185">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="780e8-186">Der Controller ruft Daten aus einer Datenquelle ab und entscheidet, welche Art von Antwort an den Browser zurückgesendet wird.</span><span class="sxs-lookup"><span data-stu-id="780e8-186">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="780e8-187">Ansichtsvorlagen können von einem Controller zum Generieren und Formatieren einer HTML-Antwort an den Browser verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="780e8-187">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="780e8-188">Controller sind für die Bereitstellung der benötigten Daten zuständig, damit eine Ansichtsvorlage eine Antwort liefern kann.</span><span class="sxs-lookup"><span data-stu-id="780e8-188">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="780e8-189">Eine bewährte Methode: Ansichtsvorlagen sollten **keine** Geschäftslogik ausführen bzw. nicht direkt mit einer Datenbank interagieren.</span><span class="sxs-lookup"><span data-stu-id="780e8-189">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="780e8-190">Eine Ansichtsvorlage sollte stattdessen nur mit den Daten arbeiten, die ihr vom Controller bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="780e8-190">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="780e8-191">Durch Beibehaltung dieser Bereichstrennung bleibt der Code übersichtlich, testfähig und verwaltbar.</span><span class="sxs-lookup"><span data-stu-id="780e8-191">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="780e8-192">Derzeit verwendet die `Welcome`-Methode in der `HelloWorldController`-Klasse die Parameter `name` und `ID` und gibt anschließend die Werte direkt an den Browser aus.</span><span class="sxs-lookup"><span data-stu-id="780e8-192">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="780e8-193">Anstatt den Controller diese Antwort als Zeichenfolge rendern zu lassen, passen Sie den Controller so an, dass er eine Ansichtsvorlage verwendet.</span><span class="sxs-lookup"><span data-stu-id="780e8-193">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="780e8-194">Die Ansichtsvorlage erstellt eine dynamische Antwort, was bedeutet, dass entsprechende Datenbestandteile vom Controller an die Ansicht übergeben werden müssen, damit eine Antwort erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="780e8-194">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="780e8-195">Lassen Sie hierzu den Controller die dynamischen Daten (Parameter), die die Ansichtsvorlage benötigt, in einem `ViewData`-Wörterbuch ablegen, auf das die Ansichtsvorlage zugreifen kann.</span><span class="sxs-lookup"><span data-stu-id="780e8-195">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="780e8-196">Ändern Sie in *HelloWorldController.cs* die `Welcome`-Methode so, dass die Werte `Message` und `NumTimes` dem Wörterbuch `ViewData` hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="780e8-196">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="780e8-197">Das Wörterbuch `ViewData` ist ein dynamisches Objekt, was bedeutet, dass jeder beliebige Typ verwendet werden kann. Das `ViewData`-Objekt hat erst dann definierte Eigenschaften, wenn Sie ihm etwas hinzugefügt haben.</span><span class="sxs-lookup"><span data-stu-id="780e8-197">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="780e8-198">Das [MVC-Modellbindungssystem](xref:mvc/models/model-binding) ordnet automatisch die benannten Parameter (`name` und `numTimes`) aus der Abfragezeichenfolge auf der Adressleiste den Parametern der Methode zu.</span><span class="sxs-lookup"><span data-stu-id="780e8-198">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="780e8-199">Die vollständige Datei *HelloWorldController.cs* sieht wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="780e8-199">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="780e8-200">Das Wörterbuchobjekt `ViewData` enthält Daten, die an die Ansicht übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="780e8-200">The `ViewData` dictionary object contains data that will be passed to the view.</span></span> 

<span data-ttu-id="780e8-201">Erstellen Sie die Ansichtsvorlage für die Begrüßung namens *Views/HelloWorld/Welcome.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="780e8-201">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="780e8-202">Erstellen Sie eine Schleife in der Ansichtsvorlage *Welcome.cshtml*, die „Hello“ `NumTimes` anzeigt.</span><span class="sxs-lookup"><span data-stu-id="780e8-202">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="780e8-203">Ersetzen Sie den Inhalt von *Views/HelloWorld/Welcome.cshtml* durch Folgendes:</span><span class="sxs-lookup"><span data-stu-id="780e8-203">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="780e8-204">Speichern Sie die Änderungen, und navigieren Sie zur folgenden URL:</span><span class="sxs-lookup"><span data-stu-id="780e8-204">Save your changes and browse to the following URL:</span></span>

`https://localhost:xxxx/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="780e8-205">Daten werden der URL entnommen und mithilfe der [MVC-Modellbindung](xref:mvc/models/model-binding) an den Controller übergeben.</span><span class="sxs-lookup"><span data-stu-id="780e8-205">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="780e8-206">Der Controller packt die Daten in einem `ViewData`-Wörterbuch und übergibt das Objekt an die Ansicht.</span><span class="sxs-lookup"><span data-stu-id="780e8-206">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="780e8-207">Die Ansicht rendert dann die Daten im HTML-Format im Browser.</span><span class="sxs-lookup"><span data-stu-id="780e8-207">The view then renders the data as HTML to the browser.</span></span>

![Die Ansicht „Privacy“ mit der Beschriftung „Welcome“ und der viermal gezeigten Wortfolge „Hello Rick“](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="780e8-209">Im obigen Beispiel wurde das Wörterbuch `ViewData` verwendet, um Daten vom Controller an eine Ansicht zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="780e8-209">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="780e8-210">Später in diesem Tutorial wird ein Ansichtsmodell verwendet, um Daten von einem Controller an eine Ansicht zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="780e8-210">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="780e8-211">Der Ansatz mit dem Ansichtsmodell für das Übergeben von Daten ist im Allgemeinen dem Ansatz mit dem Wörterbuch `ViewData` vorzuziehen.</span><span class="sxs-lookup"><span data-stu-id="780e8-211">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="780e8-212">Weitere Informationen finden Sie in diesem Artikel zur [Verwendung von ViewBag, ViewData oder TempData](http://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) (in englischer Sprache).</span><span class="sxs-lookup"><span data-stu-id="780e8-212">See [When to use ViewBag, ViewData, or TempData ](http://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="780e8-213">Im nächsten Tutorial wird eine Filmdatenbank erstellt.</span><span class="sxs-lookup"><span data-stu-id="780e8-213">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="780e8-214">[Zurück](adding-controller.md)
> [Weiter](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="780e8-214">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

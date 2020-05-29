---
<span data-ttu-id="baa84-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="baa84-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="baa84-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="baa84-102">'Blazor'</span></span>
- <span data-ttu-id="baa84-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="baa84-103">'Identity'</span></span>
- <span data-ttu-id="baa84-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="baa84-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="baa84-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="baa84-105">'Razor'</span></span>
- <span data-ttu-id="baa84-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="baa84-106">'SignalR' uid:</span></span> 

---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a><span data-ttu-id="baa84-107">Migrieren von ASP.net-Web-API zu ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="baa84-107">Migrate from ASP.NET Web API to ASP.NET Core</span></span>

<span data-ttu-id="baa84-108">Von [Scott Adder](https://twitter.com/scott_addie) und [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="baa84-108">By [Scott Addie](https://twitter.com/scott_addie) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="baa84-109">Eine ASP.NET 4. x-Web-API ist ein HTTP-Dienst, der eine breite Palette von Clients, einschließlich Browser und mobiler Geräte, erreicht.</span><span class="sxs-lookup"><span data-stu-id="baa84-109">An ASP.NET 4.x Web API is an HTTP service that reaches a broad range of clients, including browsers and mobile devices.</span></span> <span data-ttu-id="baa84-110">ASP.net Core kombiniert die MVC-und Web-API-APP-Modelle von ASP.NET 4. x in einem einzelnen Programmiermodell, das als ASP.net Core MVC bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="baa84-110">ASP.NET Core combines ASP.NET 4.x's MVC and Web API app models into a single programming model known as ASP.NET Core MVC.</span></span> <span data-ttu-id="baa84-111">In diesem Artikel werden die erforderlichen Schritte zum Migrieren von der ASP.NET 4. x-Web-API zu ASP.net Core MVC erläutert.</span><span class="sxs-lookup"><span data-stu-id="baa84-111">This article demonstrates the steps required to migrate from ASP.NET 4.x Web API to ASP.NET Core MVC.</span></span>

<span data-ttu-id="baa84-112">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="baa84-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="baa84-113">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="baa84-113">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="baa84-114">ASP.NET 4. x-Web-API-Projekt überprüfen</span><span class="sxs-lookup"><span data-stu-id="baa84-114">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="baa84-115">In diesem Artikel wird das Projekt *producungapp* verwendet, das Sie unter [Getting Started with ASP.net-Web-API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="baa84-115">This article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="baa84-116">In diesem Projekt wird ein einfaches ASP.NET 4. x-Web-API-Projekt wie folgt konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="baa84-116">In that project, a basic ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="baa84-117">In *Global.asax.cs*wird ein-Rückruf für Folgendes durchgeführt `WebApiConfig.Register` :</span><span class="sxs-lookup"><span data-stu-id="baa84-117">In *Global.asax.cs*, a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="baa84-118">Die `WebApiConfig` -Klasse befindet sich im Ordner " *App_Start* " und verfügt über eine statische- `Register` Methode:</span><span class="sxs-lookup"><span data-stu-id="baa84-118">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="baa84-119">Die vorherige-Klasse:</span><span class="sxs-lookup"><span data-stu-id="baa84-119">The preceding class:</span></span>

* <span data-ttu-id="baa84-120">Konfiguriert das [Attribut Routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), obwohl es nicht tatsächlich verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="baa84-120">Configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used.</span></span>
* <span data-ttu-id="baa84-121">Konfiguriert die Routing Tabelle.</span><span class="sxs-lookup"><span data-stu-id="baa84-121">Configures the routing table.</span></span>
<span data-ttu-id="baa84-122">Im Beispielcode wird erwartet, dass URLs dem Format entsprechen `/api/{controller}/{id}` , wobei `{id}` optional ist.</span><span class="sxs-lookup"><span data-stu-id="baa84-122">The sample code expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="baa84-123">In den folgenden Abschnitten wird die Migration des Web-API-Projekts zu ASP.net Core MVC veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="baa84-123">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-the-destination-project"></a><span data-ttu-id="baa84-124">Erstellen des Ziel Projekts</span><span class="sxs-lookup"><span data-stu-id="baa84-124">Create the destination project</span></span>

<span data-ttu-id="baa84-125">Erstellen Sie eine neue leere Projekt Mappe in Visual Studio, und fügen Sie der Migration das ASP.NET 4. x-Web-API-Projekt hinzu:</span><span class="sxs-lookup"><span data-stu-id="baa84-125">Create a new blank solution in Visual Studio and add the ASP.NET 4.x Web API project to migrate:</span></span>

1. <span data-ttu-id="baa84-126">Klicken Sie im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="baa84-126">From the **File** menu, select **New** > **Project**.</span></span>
1. <span data-ttu-id="baa84-127">Wählen Sie die Vorlage **leere** Vorlage aus, und klicken Sie auf **weiter**.</span><span class="sxs-lookup"><span data-stu-id="baa84-127">Select the **Blank Solution** template and select **Next**.</span></span>
1. <span data-ttu-id="baa84-128">Nennen Sie die Projekt Mappe *webapimigration*.</span><span class="sxs-lookup"><span data-stu-id="baa84-128">Name the solution *WebAPIMigration*.</span></span> <span data-ttu-id="baa84-129">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="baa84-129">Select **Create**.</span></span>
1. <span data-ttu-id="baa84-130">Fügen Sie der Projekt Mappe das vorhandene *producungapp* -Projekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="baa84-130">Add the existing *ProductsApp* project to the solution.</span></span>

<span data-ttu-id="baa84-131">Fügen Sie ein neues API-Projekt für die Migration hinzu:</span><span class="sxs-lookup"><span data-stu-id="baa84-131">Add a new API project to migrate to:</span></span>

1. <span data-ttu-id="baa84-132">Fügen Sie der Projekt Mappe ein neues **ASP.net Core-Webanwendungs** Projekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="baa84-132">Add a new **ASP.NET Core Web Application** project to the solution.</span></span>
1. <span data-ttu-id="baa84-133">Benennen Sie im Dialogfeld **Neues Projekt konfigurieren** das Projekt *productscore*, und wählen Sie dann **Erstellen**aus.</span><span class="sxs-lookup"><span data-stu-id="baa84-133">In the **Configure your new project** dialog, Name the project *ProductsCore*, and select **Create**.</span></span>
1. <span data-ttu-id="baa84-134">Vergewissern Sie sich, dass im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Optionen **.NET Core** und **ASP.NET Core 3.1** ausgewählt sind.</span><span class="sxs-lookup"><span data-stu-id="baa84-134">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="baa84-135">Wählen Sie die Projektvorlage **API** aus, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="baa84-135">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="baa84-136">Entfernen Sie die Beispieldateien *WeatherForecast.cs* und *Controllers/weatherforecastcontroller. cs* aus dem neuen *productscore* -Projekt.</span><span class="sxs-lookup"><span data-stu-id="baa84-136">Remove the *WeatherForecast.cs* and *Controllers/WeatherForecastController.cs* example files from the new *ProductsCore* project.</span></span>

<span data-ttu-id="baa84-137">Die Projekt Mappe enthält jetzt zwei Projekte.</span><span class="sxs-lookup"><span data-stu-id="baa84-137">The solution now contains two projects.</span></span> <span data-ttu-id="baa84-138">In den folgenden Abschnitten wird erläutert, wie der Inhalt des *productsapp* -Projekts in das *productscore* -Projekt migriert wird.</span><span class="sxs-lookup"><span data-stu-id="baa84-138">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="baa84-139">Migrating Configuration (Migrieren der Konfiguration)</span><span class="sxs-lookup"><span data-stu-id="baa84-139">Migrate configuration</span></span>

<span data-ttu-id="baa84-140">ASP.net Core verwendet nicht den Ordner *App_Start* oder die Datei *Global. asax* .</span><span class="sxs-lookup"><span data-stu-id="baa84-140">ASP.NET Core doesn't use the *App_Start* folder or the *Global.asax* file.</span></span> <span data-ttu-id="baa84-141">Außerdem wird die Datei " *Web. config* " zum Zeitpunkt der Veröffentlichung hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="baa84-141">Additionally, the *web.config* file is added at publish time.</span></span>

<span data-ttu-id="baa84-142">Die `Startup`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="baa84-142">The `Startup` class:</span></span>

* <span data-ttu-id="baa84-143">Ersetzt *Global. asax*.</span><span class="sxs-lookup"><span data-stu-id="baa84-143">Replaces *Global.asax*.</span></span>
* <span data-ttu-id="baa84-144">Behandelt alle App-Start Tasks.</span><span class="sxs-lookup"><span data-stu-id="baa84-144">Handles all app startup tasks.</span></span>

<span data-ttu-id="baa84-145">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="baa84-145">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="baa84-146">Migrieren von Modellen und Controllern</span><span class="sxs-lookup"><span data-stu-id="baa84-146">Migrate models and controllers</span></span>

<span data-ttu-id="baa84-147">Der folgende Code zeigt `ProductsController` , dass für ASP.net Core aktualisiert werden soll:</span><span class="sxs-lookup"><span data-stu-id="baa84-147">The following code shows the `ProductsController` to be updated for ASP.NET Core:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/Controllers/ProductsController.cs)]

<span data-ttu-id="baa84-148">Aktualisieren Sie das- `ProductsController` ASP.net Core:</span><span class="sxs-lookup"><span data-stu-id="baa84-148">Update the `ProductsController` for ASP.NET Core:</span></span>

1. <span data-ttu-id="baa84-149">Kopieren Sie " *Controllers/productioncontroller. cs* " und den Ordner " *Models* " aus dem ursprünglichen Projekt in das neue Projekt.</span><span class="sxs-lookup"><span data-stu-id="baa84-149">Copy *Controllers/ProductsController.cs* and the *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="baa84-150">Ändern Sie den Stamm Namespace der kopierten Dateien in `ProductsCore` .</span><span class="sxs-lookup"><span data-stu-id="baa84-150">Change the copied files' root namespace to `ProductsCore`.</span></span>
1. <span data-ttu-id="baa84-151">Aktualisieren Sie die- `using ProductsApp.Models;` Anweisung in `using ProductsCore.Models;` .</span><span class="sxs-lookup"><span data-stu-id="baa84-151">Update the `using ProductsApp.Models;` statement to `using ProductsCore.Models;`.</span></span>

<span data-ttu-id="baa84-152">Die folgenden Komponenten sind nicht in ASP.net Core vorhanden:</span><span class="sxs-lookup"><span data-stu-id="baa84-152">The following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="baa84-153">`ApiController`-Klasse</span><span class="sxs-lookup"><span data-stu-id="baa84-153">`ApiController` class</span></span>
* <span data-ttu-id="baa84-154">`System.Web.Http`-Namespace</span><span class="sxs-lookup"><span data-stu-id="baa84-154">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="baa84-155">`IHttpActionResult`-Schnittstelle</span><span class="sxs-lookup"><span data-stu-id="baa84-155">`IHttpActionResult` interface</span></span>

<span data-ttu-id="baa84-156">Nehmen Sie die folgenden Änderungen vor:</span><span class="sxs-lookup"><span data-stu-id="baa84-156">Make the following changes:</span></span>

1. <span data-ttu-id="baa84-157">Ändern Sie `ApiController` in <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span><span class="sxs-lookup"><span data-stu-id="baa84-157">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="baa84-158">Fügen Sie hinzu `using Microsoft.AspNetCore.Mvc;` , um den `ControllerBase` Verweis aufzulösen.</span><span class="sxs-lookup"><span data-stu-id="baa84-158">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="baa84-159">Löschen Sie `using System.Web.Http;`.</span><span class="sxs-lookup"><span data-stu-id="baa84-159">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="baa84-160">Ändern `GetProduct` Sie den Rückgabetyp der Aktion von `IHttpActionResult` in `ActionResult<Product>` .</span><span class="sxs-lookup"><span data-stu-id="baa84-160">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>
1. <span data-ttu-id="baa84-161">Vereinfachen `GetProduct` Sie die-Anweisung der Aktion wie `return` folgt:</span><span class="sxs-lookup"><span data-stu-id="baa84-161">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a><span data-ttu-id="baa84-162">Konfigurieren des Routings</span><span class="sxs-lookup"><span data-stu-id="baa84-162">Configure routing</span></span>

<span data-ttu-id="baa84-163">Die ASP.net Core- *API* -Projektvorlage enthält die Konfiguration des Endpunkt Routings im generierten Code.</span><span class="sxs-lookup"><span data-stu-id="baa84-163">The ASP.NET Core *API* project template includes endpoint routing configuration in the generated code.</span></span>

<span data-ttu-id="baa84-164">Die folgenden <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> -und- <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> Aufrufe:</span><span class="sxs-lookup"><span data-stu-id="baa84-164">The following <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> calls:</span></span>

* <span data-ttu-id="baa84-165">Registrieren Sie den Routen Abgleich und die [middleware](xref:fundamentals/middleware/index) Endpunkt Ausführung in der middlewarepipeline.</span><span class="sxs-lookup"><span data-stu-id="baa84-165">Register route matching and endpoint execution in the [middleware](xref:fundamentals/middleware/index) pipeline.</span></span>
* <span data-ttu-id="baa84-166">Ersetzen Sie die *App_Start/webapiconfig.cs* -Datei des Projekts " *producungapp* ".</span><span class="sxs-lookup"><span data-stu-id="baa84-166">Replace the *ProductsApp* project's *App_Start/WebApiConfig.cs* file.</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=10,14)]

<span data-ttu-id="baa84-167">Konfigurieren Sie das Routing wie folgt:</span><span class="sxs-lookup"><span data-stu-id="baa84-167">Configure routing as follows:</span></span>

1. <span data-ttu-id="baa84-168">Markieren Sie die `ProductsController` Klasse mit den folgenden Attributen:</span><span class="sxs-lookup"><span data-stu-id="baa84-168">Mark the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="baa84-169">Das vorangehende [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) Attribut konfiguriert das Attribut Routing Muster des Controllers.</span><span class="sxs-lookup"><span data-stu-id="baa84-169">The preceding [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="baa84-170">Das [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) Attribut bewirkt, dass das Attribut Routing für alle Aktionen in diesem Controller erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="baa84-170">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="baa84-171">Das Attribut Routing unterstützt Token, wie z `[controller]` `[action]` . b. und.</span><span class="sxs-lookup"><span data-stu-id="baa84-171">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="baa84-172">Zur Laufzeit wird jedes Token durch den Namen des Controllers bzw. der Aktion ersetzt, auf das das Attribut angewendet wurde.</span><span class="sxs-lookup"><span data-stu-id="baa84-172">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="baa84-173">Die Token:</span><span class="sxs-lookup"><span data-stu-id="baa84-173">The tokens:</span></span>
    * <span data-ttu-id="baa84-174">Reduzieren Sie die Anzahl der magischen Zeichen folgen im Projekt.</span><span class="sxs-lookup"><span data-stu-id="baa84-174">Reduce the number of magic strings in the project.</span></span>
    * <span data-ttu-id="baa84-175">Stellen Sie sicher, dass die Routen mit den entsprechenden Controllern und Aktionen synchronisiert bleiben, wenn Sie refacktoren für automatisches umbenennen anwenden.</span><span class="sxs-lookup"><span data-stu-id="baa84-175">Ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="baa84-176">HTTP GET-Anforderungen für die `ProductController` Aktionen aktivieren:</span><span class="sxs-lookup"><span data-stu-id="baa84-176">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="baa84-177">Wenden Sie das- [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) Attribut auf die `GetAllProducts` Aktion an.</span><span class="sxs-lookup"><span data-stu-id="baa84-177">Apply the [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="baa84-178">Wenden Sie das- `[HttpGet("{id}")]` Attribut auf die `GetProduct` Aktion an.</span><span class="sxs-lookup"><span data-stu-id="baa84-178">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="baa84-179">Führen Sie das migrierte Projekt aus, und navigieren Sie zu `/api/products` .</span><span class="sxs-lookup"><span data-stu-id="baa84-179">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="baa84-180">Es wird eine vollständige Liste der drei Produkte angezeigt.</span><span class="sxs-lookup"><span data-stu-id="baa84-180">A full list of three products appears.</span></span> <span data-ttu-id="baa84-181">Navigieren Sie zu `/api/products/1`.</span><span class="sxs-lookup"><span data-stu-id="baa84-181">Browse to `/api/products/1`.</span></span> <span data-ttu-id="baa84-182">Das erste Produkt wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="baa84-182">The first product appears.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="baa84-183">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="baa84-183">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"
## <a name="prerequisites"></a><span data-ttu-id="baa84-184">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="baa84-184">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="baa84-185">ASP.NET 4. x-Web-API-Projekt überprüfen</span><span class="sxs-lookup"><span data-stu-id="baa84-185">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="baa84-186">In diesem Artikel wird das Projekt *producungapp* verwendet, das Sie unter [Getting Started with ASP.net-Web-API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="baa84-186">This article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="baa84-187">In diesem Projekt wird ein einfaches ASP.NET 4. x-Web-API-Projekt wie folgt konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="baa84-187">In that project, a basic ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="baa84-188">In *Global.asax.cs*wird ein-Rückruf für Folgendes durchgeführt `WebApiConfig.Register` :</span><span class="sxs-lookup"><span data-stu-id="baa84-188">In *Global.asax.cs*, a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="baa84-189">Die `WebApiConfig` -Klasse befindet sich im Ordner " *App_Start* " und verfügt über eine statische- `Register` Methode:</span><span class="sxs-lookup"><span data-stu-id="baa84-189">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="baa84-190">Diese Klasse konfiguriert das [Attribut Routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), obwohl Sie nicht tatsächlich im Projekt verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="baa84-190">This class configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used in the project.</span></span> <span data-ttu-id="baa84-191">Außerdem wird die Routing Tabelle konfiguriert, die von ASP.net-Web-API verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="baa84-191">It also configures the routing table, which is used by ASP.NET Web API.</span></span> <span data-ttu-id="baa84-192">In diesem Fall erwartet die ASP.NET 4. x-Web-API, dass URLs dem Format entsprechen `/api/{controller}/{id}` , wobei `{id}` Sie optional ist.</span><span class="sxs-lookup"><span data-stu-id="baa84-192">In this case, ASP.NET 4.x Web API expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="baa84-193">In den folgenden Abschnitten wird die Migration des Web-API-Projekts zu ASP.net Core MVC veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="baa84-193">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-the-destination-project"></a><span data-ttu-id="baa84-194">Erstellen des Ziel Projekts</span><span class="sxs-lookup"><span data-stu-id="baa84-194">Create the destination project</span></span>

<span data-ttu-id="baa84-195">Führen Sie in Visual Studio die folgenden Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="baa84-195">Complete the following steps in Visual Studio:</span></span>

* <span data-ttu-id="baa84-196">Wechseln Sie zu **Datei**  >  **neu**  >  **Projekt**  >  **andere Projekttypen**  >  **Visual Studio**-Projektmappen.</span><span class="sxs-lookup"><span data-stu-id="baa84-196">Go to **File** > **New** > **Project** > **Other Project Types** > **Visual Studio Solutions**.</span></span> <span data-ttu-id="baa84-197">Wählen Sie **leere**Projekt Mappe aus, und nennen Sie die Projekt Mappe *webapimigration*.</span><span class="sxs-lookup"><span data-stu-id="baa84-197">Select **Blank Solution**, and name the solution *WebAPIMigration*.</span></span> <span data-ttu-id="baa84-198">Klicken Sie auf die Schaltfläche **OK** .</span><span class="sxs-lookup"><span data-stu-id="baa84-198">Click the **OK** button.</span></span>
* <span data-ttu-id="baa84-199">Fügen Sie der Projekt Mappe das vorhandene *producungapp* -Projekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="baa84-199">Add the existing *ProductsApp* project to the solution.</span></span>
* <span data-ttu-id="baa84-200">Fügen Sie der Projekt Mappe ein neues **ASP.net Core-Webanwendungs** Projekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="baa84-200">Add a new **ASP.NET Core Web Application** project to the solution.</span></span> <span data-ttu-id="baa84-201">Wählen Sie in der Dropdown Liste das Ziel Framework **.net Core** aus, und wählen Sie die Vorlage **API** -Projekt aus.</span><span class="sxs-lookup"><span data-stu-id="baa84-201">Select the **.NET Core** target framework from the drop-down, and select the **API** project template.</span></span> <span data-ttu-id="baa84-202">Nennen Sie das Projekt *productscore*, und klicken Sie auf die Schaltfläche **OK** .</span><span class="sxs-lookup"><span data-stu-id="baa84-202">Name the project *ProductsCore*, and click the **OK** button.</span></span>

<span data-ttu-id="baa84-203">Die Projekt Mappe enthält jetzt zwei Projekte.</span><span class="sxs-lookup"><span data-stu-id="baa84-203">The solution now contains two projects.</span></span> <span data-ttu-id="baa84-204">In den folgenden Abschnitten wird erläutert, wie der Inhalt des *productsapp* -Projekts in das *productscore* -Projekt migriert wird.</span><span class="sxs-lookup"><span data-stu-id="baa84-204">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="baa84-205">Migrating Configuration (Migrieren der Konfiguration)</span><span class="sxs-lookup"><span data-stu-id="baa84-205">Migrate configuration</span></span>

<span data-ttu-id="baa84-206">ASP.net Core nicht verwendet:</span><span class="sxs-lookup"><span data-stu-id="baa84-206">ASP.NET Core doesn't use:</span></span>

* <span data-ttu-id="baa84-207">*App_Start* Ordner oder die Datei " *Global. asax* "</span><span class="sxs-lookup"><span data-stu-id="baa84-207">*App_Start* folder or the *Global.asax* file</span></span>
* <span data-ttu-id="baa84-208">die Datei " *Web. config* " wird zum Zeitpunkt der Veröffentlichung hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="baa84-208">*web.config* file is added at publish time.</span></span>

<span data-ttu-id="baa84-209">Die `Startup`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="baa84-209">The `Startup` class:</span></span>

* <span data-ttu-id="baa84-210">Ersetzt *Global. asax*.</span><span class="sxs-lookup"><span data-stu-id="baa84-210">Replaces *Global.asax*.</span></span>
* <span data-ttu-id="baa84-211">Behandelt alle App-Start Tasks.</span><span class="sxs-lookup"><span data-stu-id="baa84-211">Handles all app startup tasks.</span></span>

<span data-ttu-id="baa84-212">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="baa84-212">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="baa84-213">In ASP.net Core MVC ist das Attribut Routing standardmäßig enthalten, wenn <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> in aufgerufen wird `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="baa84-213">In ASP.NET Core MVC, attribute routing is included by default when <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> is called in `Startup.Configure`.</span></span> <span data-ttu-id="baa84-214">Der folgende `UseMvc` -Befehl ersetzt die *App_Start/webapiconfig.cs* -Datei des Projekts *producungapp* :</span><span class="sxs-lookup"><span data-stu-id="baa84-214">The following `UseMvc` call replaces the *ProductsApp* project's *App_Start/WebApiConfig.cs* file:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13)]

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="baa84-215">Migrieren von Modellen und Controllern</span><span class="sxs-lookup"><span data-stu-id="baa84-215">Migrate models and controllers</span></span>

<span data-ttu-id="baa84-216">Der folgende Code zeigt das `ProductsController` Update für ASP.net Core:[!code-csharp[](webapi/sample/2.x/ProductsApp/Controllers/ProductsController.cs)]</span><span class="sxs-lookup"><span data-stu-id="baa84-216">The following code shows the `ProductsController` update for ASP.NET Core: [!code-csharp[](webapi/sample/2.x/ProductsApp/Controllers/ProductsController.cs)]</span></span>

<span data-ttu-id="baa84-217">Aktualisieren Sie das- `ProductsController` ASP.net Core:</span><span class="sxs-lookup"><span data-stu-id="baa84-217">Update the `ProductsController` for ASP.NET Core:</span></span>

1. <span data-ttu-id="baa84-218">Kopieren Sie " *Controllers/productioncontroller. cs* " aus dem ursprünglichen Projekt in das neue Projekt.</span><span class="sxs-lookup"><span data-stu-id="baa84-218">Copy *Controllers/ProductsController.cs* from the original project to the new one.</span></span>
1. <span data-ttu-id="baa84-219">Kopieren Sie den Ordner *Models* aus dem ursprünglichen Projekt in das neue Projekt.</span><span class="sxs-lookup"><span data-stu-id="baa84-219">Copy the *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="baa84-220">Ändern Sie den Stamm Namespace der kopierten Dateien in `ProductsCore` .</span><span class="sxs-lookup"><span data-stu-id="baa84-220">Change the copied files' root namespace to `ProductsCore`.</span></span>
1. <span data-ttu-id="baa84-221">Aktualisieren Sie die- `using ProductsApp.Models;` Anweisung in `using ProductsCore.Models;` .</span><span class="sxs-lookup"><span data-stu-id="baa84-221">Update the `using ProductsApp.Models;` statement to `using ProductsCore.Models;`.</span></span>

<span data-ttu-id="baa84-222">Die folgenden Komponenten sind nicht in ASP.net Core vorhanden:</span><span class="sxs-lookup"><span data-stu-id="baa84-222">The following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="baa84-223">`ApiController`-Klasse</span><span class="sxs-lookup"><span data-stu-id="baa84-223">`ApiController` class</span></span>
* <span data-ttu-id="baa84-224">`System.Web.Http`-Namespace</span><span class="sxs-lookup"><span data-stu-id="baa84-224">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="baa84-225">`IHttpActionResult`-Schnittstelle</span><span class="sxs-lookup"><span data-stu-id="baa84-225">`IHttpActionResult` interface</span></span>

<span data-ttu-id="baa84-226">Nehmen Sie die folgenden Änderungen vor:</span><span class="sxs-lookup"><span data-stu-id="baa84-226">Make the following changes:</span></span>

1. <span data-ttu-id="baa84-227">Ändern Sie `ApiController` in <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span><span class="sxs-lookup"><span data-stu-id="baa84-227">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="baa84-228">Fügen Sie hinzu `using Microsoft.AspNetCore.Mvc;` , um den `ControllerBase` Verweis aufzulösen.</span><span class="sxs-lookup"><span data-stu-id="baa84-228">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="baa84-229">Löschen Sie `using System.Web.Http;`.</span><span class="sxs-lookup"><span data-stu-id="baa84-229">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="baa84-230">Ändern `GetProduct` Sie den Rückgabetyp der Aktion von `IHttpActionResult` in `ActionResult<Product>` .</span><span class="sxs-lookup"><span data-stu-id="baa84-230">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>
1. <span data-ttu-id="baa84-231">Vereinfachen `GetProduct` Sie die-Anweisung der Aktion wie `return` folgt:</span><span class="sxs-lookup"><span data-stu-id="baa84-231">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a><span data-ttu-id="baa84-232">Konfigurieren des Routings</span><span class="sxs-lookup"><span data-stu-id="baa84-232">Configure routing</span></span>

<span data-ttu-id="baa84-233">Konfigurieren Sie das Routing wie folgt:</span><span class="sxs-lookup"><span data-stu-id="baa84-233">Configure routing as follows:</span></span>

1. <span data-ttu-id="baa84-234">Markieren Sie die `ProductsController` Klasse mit den folgenden Attributen:</span><span class="sxs-lookup"><span data-stu-id="baa84-234">Mark the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="baa84-235">Das vorangehende [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) Attribut konfiguriert das Attribut Routing Muster des Controllers.</span><span class="sxs-lookup"><span data-stu-id="baa84-235">The preceding [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="baa84-236">Das [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) Attribut bewirkt, dass das Attribut Routing für alle Aktionen in diesem Controller erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="baa84-236">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="baa84-237">Das Attribut Routing unterstützt Token, wie z `[controller]` `[action]` . b. und.</span><span class="sxs-lookup"><span data-stu-id="baa84-237">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="baa84-238">Zur Laufzeit wird jedes Token durch den Namen des Controllers bzw. der Aktion ersetzt, auf das das Attribut angewendet wurde.</span><span class="sxs-lookup"><span data-stu-id="baa84-238">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="baa84-239">Die Token reduzieren die Anzahl der magischen Zeichen folgen im Projekt.</span><span class="sxs-lookup"><span data-stu-id="baa84-239">The tokens reduce the number of magic strings in the project.</span></span> <span data-ttu-id="baa84-240">Die Token stellen außerdem sicher, dass Routen mit den entsprechenden Controllern und Aktionen synchronisiert bleiben, wenn die refactoren für automatisches umbenennen angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="baa84-240">The tokens also ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="baa84-241">Legen Sie den Kompatibilitätsmodus des Projekts auf ASP.net Core 2,2 fest:</span><span class="sxs-lookup"><span data-stu-id="baa84-241">Set the project's compatibility mode to ASP.NET Core 2.2:</span></span>

    [!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    <span data-ttu-id="baa84-242">Die vorherige Änderung:</span><span class="sxs-lookup"><span data-stu-id="baa84-242">The preceding change:</span></span>

    * <span data-ttu-id="baa84-243">Ist erforderlich, um das- `[ApiController]` Attribut auf Controller Ebene zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="baa84-243">Is required to use the `[ApiController]` attribute at the controller level.</span></span>
    * <span data-ttu-id="baa84-244">Gibt an, dass potenziell wichtige Verhaltensweisen in ASP.net Core 2,2 eingeführt werden.</span><span class="sxs-lookup"><span data-stu-id="baa84-244">Opts in to potentially breaking behaviors introduced in ASP.NET Core 2.2.</span></span>
1. <span data-ttu-id="baa84-245">HTTP GET-Anforderungen für die `ProductController` Aktionen aktivieren:</span><span class="sxs-lookup"><span data-stu-id="baa84-245">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="baa84-246">Wenden Sie das- [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) Attribut auf die `GetAllProducts` Aktion an.</span><span class="sxs-lookup"><span data-stu-id="baa84-246">Apply the [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="baa84-247">Wenden Sie das- `[HttpGet("{id}")]` Attribut auf die `GetProduct` Aktion an.</span><span class="sxs-lookup"><span data-stu-id="baa84-247">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="baa84-248">Führen Sie das migrierte Projekt aus, und navigieren Sie zu `/api/products` .</span><span class="sxs-lookup"><span data-stu-id="baa84-248">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="baa84-249">Es wird eine vollständige Liste der drei Produkte angezeigt.</span><span class="sxs-lookup"><span data-stu-id="baa84-249">A full list of three products appears.</span></span> <span data-ttu-id="baa84-250">Navigieren Sie zu `/api/products/1`.</span><span class="sxs-lookup"><span data-stu-id="baa84-250">Browse to `/api/products/1`.</span></span> <span data-ttu-id="baa84-251">Das erste Produkt wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="baa84-251">The first product appears.</span></span>

## <a name="compatibility-shim"></a><span data-ttu-id="baa84-252">Kompatibilitäts-Shim</span><span class="sxs-lookup"><span data-stu-id="baa84-252">Compatibility shim</span></span>

<span data-ttu-id="baa84-253">Die [Microsoft. aspnetcore. MVC. webapicompatshim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) -Bibliothek stellt einen Kompatibilitäts-Shim bereit, um ASP.NET 4. x-Web-API-Projekte in ASP.net Core zu verschieben.</span><span class="sxs-lookup"><span data-stu-id="baa84-253">The [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) library provides a compatibility shim to move ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="baa84-254">Der Kompatibilitäts-Shim erweitert ASP.net Core, um eine Reihe von Konventionen von ASP.NET 4. x Web-API 2 zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="baa84-254">The compatibility shim extends ASP.NET Core to support a number of conventions from ASP.NET 4.x Web API 2.</span></span> <span data-ttu-id="baa84-255">Das zuvor in diesem Dokument portierte Beispiel ist so einfach, dass das Kompatibilitäts-Shim unnötig ist.</span><span class="sxs-lookup"><span data-stu-id="baa84-255">The sample ported previously in this document is basic enough that the compatibility shim was unnecessary.</span></span> <span data-ttu-id="baa84-256">Bei größeren Projekten kann die Verwendung des Kompatibilitäts-Shims nützlich sein, um die API-Lücke zwischen ASP.net Core und ASP.NET 4. x-Web-API 2 vorübergehend zu überbrücken.</span><span class="sxs-lookup"><span data-stu-id="baa84-256">For larger projects, using the compatibility shim can be useful for temporarily bridging the API gap between ASP.NET Core and ASP.NET 4.x Web API 2.</span></span>

<span data-ttu-id="baa84-257">Das Web-API-Kompatibilitäts-Shim ist als temporäre Maßnahme zur Unterstützung der Migration von großen ASP.NET 4. x-Web-API-Projekten zu ASP.net Core gedacht.</span><span class="sxs-lookup"><span data-stu-id="baa84-257">The Web API compatibility shim is meant to be used as a temporary measure to support migrating large ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="baa84-258">Im Laufe der Zeit sollten Projekte aktualisiert werden, um ASP.net Core Muster zu verwenden, anstatt sich auf den Kompatibilitäts-Shim zu verlassen.</span><span class="sxs-lookup"><span data-stu-id="baa84-258">Over time, projects should be updated to use ASP.NET Core patterns instead of relying on the compatibility shim.</span></span>

<span data-ttu-id="baa84-259">Zu den in enthaltenen Kompatibilitäts Features `Microsoft.AspNetCore.Mvc.WebApiCompatShim` gehören:</span><span class="sxs-lookup"><span data-stu-id="baa84-259">Compatibility features included in `Microsoft.AspNetCore.Mvc.WebApiCompatShim` include:</span></span>

* <span data-ttu-id="baa84-260">Fügt einen `ApiController` Typ hinzu, sodass die Basis Typen der Controller nicht aktualisiert werden müssen.</span><span class="sxs-lookup"><span data-stu-id="baa84-260">Adds an `ApiController` type so that controllers' base types don't need to be updated.</span></span>
* <span data-ttu-id="baa84-261">Ermöglicht die Modell Bindung im Web-API-Stil.</span><span class="sxs-lookup"><span data-stu-id="baa84-261">Enables Web API-style model binding.</span></span> <span data-ttu-id="baa84-262">ASP.net Core die MVC-Modell Bindung ähnlich der von ASP.NET 4. x MVC 5 standardmäßig.</span><span class="sxs-lookup"><span data-stu-id="baa84-262">ASP.NET Core MVC model binding functions similarly to that of ASP.NET 4.x MVC 5, by default.</span></span> <span data-ttu-id="baa84-263">Mit dem Kompatibilitäts-Shim wird die Modell Bindung so geändert, dass Sie den Modell Bindungs Konventionen von ASP.NET 4. x Web API 2 ähnelt.</span><span class="sxs-lookup"><span data-stu-id="baa84-263">The compatibility shim changes model binding to be more similar to ASP.NET 4.x Web API 2 model binding conventions.</span></span> <span data-ttu-id="baa84-264">Komplexe Typen werden z. b. automatisch vom Anforderungs Text gebunden.</span><span class="sxs-lookup"><span data-stu-id="baa84-264">For example, complex types are automatically bound from the request body.</span></span>
* <span data-ttu-id="baa84-265">Erweitert die Modell Bindung, sodass Controller Aktionen Parameter des Typs annehmen können `HttpRequestMessage` .</span><span class="sxs-lookup"><span data-stu-id="baa84-265">Extends model binding so that controller actions can take parameters of type `HttpRequestMessage`.</span></span>
* <span data-ttu-id="baa84-266">Fügt nachrichtenformatierer hinzu, sodass Aktionen Ergebnisse vom Typ zurückgeben können `HttpResponseMessage` .</span><span class="sxs-lookup"><span data-stu-id="baa84-266">Adds message formatters allowing actions to return results of type `HttpResponseMessage`.</span></span>
* <span data-ttu-id="baa84-267">Fügt zusätzliche Antwort Methoden hinzu, die von Web-API 2-Aktionen verwendet werden können, um Antworten zu erfüllen:</span><span class="sxs-lookup"><span data-stu-id="baa84-267">Adds additional response methods that Web API 2 actions may have used to serve responses:</span></span>
  * <span data-ttu-id="baa84-268">`HttpResponseMessage`ger</span><span class="sxs-lookup"><span data-stu-id="baa84-268">`HttpResponseMessage` generators:</span></span>
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * <span data-ttu-id="baa84-269">Aktions Ergebnis Methoden:</span><span class="sxs-lookup"><span data-stu-id="baa84-269">Action result methods:</span></span>
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* <span data-ttu-id="baa84-270">Fügt `IContentNegotiator` dem Container für die Abhängigkeitsinjektion der App eine Instanz von hinzu und stellt die inhaltsaushandlungs bezogenen Typen von [Microsoft. Aspnet. WebAPI. Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/)zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="baa84-270">Adds an instance of `IContentNegotiator` to the app's dependency injection (DI) container and makes available the content negotiation-related types from [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span></span> <span data-ttu-id="baa84-271">Beispiele für solche Typen sind `DefaultContentNegotiator` und `MediaTypeFormatter` .</span><span class="sxs-lookup"><span data-stu-id="baa84-271">Examples of such types include `DefaultContentNegotiator` and `MediaTypeFormatter`.</span></span>

<span data-ttu-id="baa84-272">So verwenden Sie den Kompatibilitäts-Shim:</span><span class="sxs-lookup"><span data-stu-id="baa84-272">To use the compatibility shim:</span></span>

1. <span data-ttu-id="baa84-273">Installieren Sie das nuget-Paket [Microsoft. aspnetcore. MVC. webapicompatshim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) .</span><span class="sxs-lookup"><span data-stu-id="baa84-273">Install the [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet package.</span></span>
1. <span data-ttu-id="baa84-274">Registrieren Sie die Dienste des Kompatibilitäts-Shims mit dem di-Container der APP, indem Sie `services.AddMvc().AddWebApiConventions()` in Aufrufen `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="baa84-274">Register the compatibility shim's services with the app's DI container by calling `services.AddMvc().AddWebApiConventions()` in `Startup.ConfigureServices`.</span></span>
1. <span data-ttu-id="baa84-275">Definieren Sie Web-API-spezifische Routen mithilfe `MapWebApiRoute` von für den `IRouteBuilder` im App-Befehl `IApplicationBuilder.UseMvc` .</span><span class="sxs-lookup"><span data-stu-id="baa84-275">Define web API-specific routes using `MapWebApiRoute` on the `IRouteBuilder` in the app's `IApplicationBuilder.UseMvc` call.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="baa84-276">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="baa84-276">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
::: moniker-end

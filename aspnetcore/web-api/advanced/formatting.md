---
<span data-ttu-id="e33a5-101">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="e33a5-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e33a5-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-102">'Blazor'</span></span>
- <span data-ttu-id="e33a5-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e33a5-103">'Identity'</span></span>
- <span data-ttu-id="e33a5-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e33a5-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="e33a5-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-105">'Razor'</span></span>
- <span data-ttu-id="e33a5-106">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="e33a5-106">'SignalR' uid:</span></span> 

---
# <a name="format-response-data-in-aspnet-core-web-api"></a><span data-ttu-id="e33a5-107">Formatieren von Antwortdaten in Web-APIs in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e33a5-107">Format response data in ASP.NET Core Web API</span></span>

<span data-ttu-id="e33a5-108">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="e33a5-108">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="e33a5-109">ASP.NET Core MVC unterstützt das Formatieren von Antwortdaten.</span><span class="sxs-lookup"><span data-stu-id="e33a5-109">ASP.NET Core MVC has support for formatting response data.</span></span> <span data-ttu-id="e33a5-110">Antwortdaten können mithilfe bestimmter Formate oder durch Übernahme des vom Client angeforderten Formats formatiert werden.</span><span class="sxs-lookup"><span data-stu-id="e33a5-110">Response data can be formatted using specific formats or in response to client requested format.</span></span>

<span data-ttu-id="e33a5-111">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e33a5-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="e33a5-112">Formatspezifische Aktionsergebnisse</span><span class="sxs-lookup"><span data-stu-id="e33a5-112">Format-specific Action Results</span></span>

<span data-ttu-id="e33a5-113">Einige Aktionsergebnistypen sind für ein bestimmtes Format spezifisch, wie z.B. <xref:Microsoft.AspNetCore.Mvc.JsonResult> und <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span><span class="sxs-lookup"><span data-stu-id="e33a5-113">Some action result types are specific to a particular format, such as <xref:Microsoft.AspNetCore.Mvc.JsonResult> and <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span></span> <span data-ttu-id="e33a5-114">Aktionen können Ergebnisse zurückgeben, die in einem bestimmten Format formatiert sind, unabhängig von den Clienteinstellungen.</span><span class="sxs-lookup"><span data-stu-id="e33a5-114">Actions can return results that are formatted in a particular format, regardless of client preferences.</span></span> <span data-ttu-id="e33a5-115">Mit `JsonResult` beispielsweise werden JSON-formatierte Daten zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="e33a5-115">For example, returning `JsonResult` returns JSON-formatted data.</span></span> <span data-ttu-id="e33a5-116">Mit `ContentResult` oder einer Zeichenfolge werden Zeichenfolgendaten in Nur-Text-Format zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="e33a5-116">Returning `ContentResult` or a string returns plain-text-formatted string data.</span></span>

<span data-ttu-id="e33a5-117">Zum Zurückgeben eines bestimmten Typs ist keine Aktion erforderlich.</span><span class="sxs-lookup"><span data-stu-id="e33a5-117">An action isn't required to return any specific type.</span></span> <span data-ttu-id="e33a5-118">ASP.NET Core unterstützt jeden Objektrückgabewert.</span><span class="sxs-lookup"><span data-stu-id="e33a5-118">ASP.NET Core supports any object return value.</span></span>  <span data-ttu-id="e33a5-119">Ergebnisse von Aktionen, die Objekte zurückgeben, deren Typ nicht <xref:Microsoft.AspNetCore.Mvc.IActionResult> ist, werden mit der entsprechenden <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter>-Implementierung serialisiert.</span><span class="sxs-lookup"><span data-stu-id="e33a5-119">Results from actions that return objects that are not <xref:Microsoft.AspNetCore.Mvc.IActionResult> types are serialized using the appropriate <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementation.</span></span> <span data-ttu-id="e33a5-120">Weitere Informationen finden Sie unter <xref:web-api/action-return-types>.</span><span class="sxs-lookup"><span data-stu-id="e33a5-120">For more information, see <xref:web-api/action-return-types>.</span></span>

<span data-ttu-id="e33a5-121">Die integrierte Hilfsmethode <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> gibt JSON-formatierte Daten zurück: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span><span class="sxs-lookup"><span data-stu-id="e33a5-121">The built-in helper method <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> returns JSON-formatted data: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span></span>

<span data-ttu-id="e33a5-122">Der Beispieldownload gibt die Liste der Autoren zurück.</span><span class="sxs-lookup"><span data-stu-id="e33a5-122">The sample download returns the list of authors.</span></span> <span data-ttu-id="e33a5-123">Bei Verwendung der F12-Entwicklertools im Browser oder von [Postman](https://www.getpostman.com/tools) mit dem obigen Code gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="e33a5-123">Using the F12 browser developer tools or [Postman](https://www.getpostman.com/tools) with the previous code:</span></span>

* <span data-ttu-id="e33a5-124">Der Antwortheader mit **content-type:** `application/json; charset=utf-8` wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="e33a5-124">The response header containing **content-type:** `application/json; charset=utf-8` is displayed.</span></span>
* <span data-ttu-id="e33a5-125">Die Anforderungsheader werden angezeigt.</span><span class="sxs-lookup"><span data-stu-id="e33a5-125">The request headers are displayed.</span></span> <span data-ttu-id="e33a5-126">Beispiel: Der Header `Accept`.</span><span class="sxs-lookup"><span data-stu-id="e33a5-126">For example, the `Accept` header.</span></span> <span data-ttu-id="e33a5-127">Der `Accept`-Header wird vom vorangehenden Code ignoriert.</span><span class="sxs-lookup"><span data-stu-id="e33a5-127">The `Accept` header is ignored by the preceding code.</span></span>

<span data-ttu-id="e33a5-128">Wenn Sie Daten im Textformat zurückgeben möchten, verwenden Sie <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> und das <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content>-Hilfsprogramm:</span><span class="sxs-lookup"><span data-stu-id="e33a5-128">To return plain text formatted data, use <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> and the <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> helper:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

<span data-ttu-id="e33a5-129">Im obigen Code wird `text/plain` als `Content-Type` zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="e33a5-129">In the preceding code, the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="e33a5-130">Das Zurückgeben einer Zeichenfolge liefert `text/plain` als `Content-Type`:</span><span class="sxs-lookup"><span data-stu-id="e33a5-130">Returning a string delivers `Content-Type` of `text/plain`:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

<span data-ttu-id="e33a5-131">Bei Aktionen mit mehreren Rückgabetypen wird `IActionResult` zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="e33a5-131">For actions with multiple return types, return `IActionResult`.</span></span> <span data-ttu-id="e33a5-132">Beispiel: Die Rückgabe von verschiedenen HTTP-Statuscodes basierend auf dem Ergebnis der ausgeführten Vorgänge.</span><span class="sxs-lookup"><span data-stu-id="e33a5-132">For example, returning different HTTP status codes based on the result of operations performed.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="e33a5-133">Inhaltsaushandlung</span><span class="sxs-lookup"><span data-stu-id="e33a5-133">Content negotiation</span></span>

<span data-ttu-id="e33a5-134">Eine Inhaltsaushandlung tritt auf, wenn der Client einen [Accept-Header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html) angibt.</span><span class="sxs-lookup"><span data-stu-id="e33a5-134">Content negotiation occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="e33a5-135">Das von ASP.NET Core verwendete Standardformat ist [JSON](https://json.org/).</span><span class="sxs-lookup"><span data-stu-id="e33a5-135">The default format used by ASP.NET Core is [JSON](https://json.org/).</span></span> <span data-ttu-id="e33a5-136">Für die Inhaltsaushandlung gilt:</span><span class="sxs-lookup"><span data-stu-id="e33a5-136">Content negotiation is:</span></span>

* <span data-ttu-id="e33a5-137">Sie wird durch <xref:Microsoft.AspNetCore.Mvc.ObjectResult> implementiert.</span><span class="sxs-lookup"><span data-stu-id="e33a5-137">Implemented by <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span></span>
* <span data-ttu-id="e33a5-138">Sie ist in die Statuscode-spezifischen Aktionsergebnisse integriert, die von den Hilfsmethoden zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="e33a5-138">Built into the status code-specific action results returned from the helper methods.</span></span> <span data-ttu-id="e33a5-139">Die Hilfsmethoden für Aktionsergebnisse basieren auf `ObjectResult`.</span><span class="sxs-lookup"><span data-stu-id="e33a5-139">The action results helper methods are based on `ObjectResult`.</span></span>

<span data-ttu-id="e33a5-140">Wenn ein Modelltyp zurückgegeben wird, lautet der Rückgabetyp `ObjectResult`.</span><span class="sxs-lookup"><span data-stu-id="e33a5-140">When a model type is returned,  the return type is `ObjectResult`.</span></span>

<span data-ttu-id="e33a5-141">Die folgende Aktionsmethode verwendet die Hilfsmethoden `Ok` und `NotFound`:</span><span class="sxs-lookup"><span data-stu-id="e33a5-141">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

<span data-ttu-id="e33a5-142">Standardmäßig unterstützt ASP.NET Core `application/json`-, `text/json`- und `text/plain`-Medientypen.</span><span class="sxs-lookup"><span data-stu-id="e33a5-142">By default, ASP.NET Core supports `application/json`, `text/json`, and `text/plain` media types.</span></span> <span data-ttu-id="e33a5-143">Tools wie [Fiddler](https://www.telerik.com/fiddler) oder [Postman](https://www.getpostman.com/tools) können den `Accept`-Anforderungsheader festlegen, um das Rückgabeformat anzugeben.</span><span class="sxs-lookup"><span data-stu-id="e33a5-143">Tools such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/tools) can set the `Accept` request header to specify the return format.</span></span> <span data-ttu-id="e33a5-144">Wenn der `Accept`-Header einen vom Server unterstützten Typ enthält, wird dieser Typ zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="e33a5-144">When the `Accept` header contains a type the server supports, that type is returned.</span></span> <span data-ttu-id="e33a5-145">Der nächste Abschnitt zeigt, wie zusätzliche Formatierer hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="e33a5-145">The next section shows how to add additional formatters.</span></span>

<span data-ttu-id="e33a5-146">Controlleraktionen können POCOs (Plain Old CLR Objects) zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="e33a5-146">Controller actions can return POCOs (Plain Old CLR Objects).</span></span> <span data-ttu-id="e33a5-147">Wenn ein POCO zurückgegeben wird, erstellt die Runtime automatisch ein `ObjectResult`, das das Objekt umschließt.</span><span class="sxs-lookup"><span data-stu-id="e33a5-147">When a POCO is returned, the runtime automatically creates an `ObjectResult` that wraps the object.</span></span> <span data-ttu-id="e33a5-148">Der Client empfängt das formatierte serialisierte Objekt.</span><span class="sxs-lookup"><span data-stu-id="e33a5-148">The client gets the formatted serialized object.</span></span> <span data-ttu-id="e33a5-149">Wenn das zurückgegebene Objekt `null` ist, wird eine `204 No Content`-Antwort zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="e33a5-149">If the object being returned is `null`, a `204 No Content` response is returned.</span></span>

<span data-ttu-id="e33a5-150">Zurückgeben eines Objekttyps:</span><span class="sxs-lookup"><span data-stu-id="e33a5-150">Returning an object type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

<span data-ttu-id="e33a5-151">Im oben stehenden Code gibt die Anforderung eines gültigen Autoralias eine `200 OK`-Antwort mit den Daten des Autors zurück.</span><span class="sxs-lookup"><span data-stu-id="e33a5-151">In the preceding code, a request for a valid author alias returns a `200 OK` response with the author's data.</span></span> <span data-ttu-id="e33a5-152">Die Anforderung eines ungültigen Alias gibt eine `204 No Content`-Antwort zurück.</span><span class="sxs-lookup"><span data-stu-id="e33a5-152">A request for an invalid alias returns a `204 No Content` response.</span></span>

### <a name="the-accept-header"></a><span data-ttu-id="e33a5-153">Der Accept-Header</span><span class="sxs-lookup"><span data-stu-id="e33a5-153">The Accept header</span></span>

<span data-ttu-id="e33a5-154">Eine *Aushandlung* des Inhalts findet statt, wenn in der Anforderung ein `Accept`-Header angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="e33a5-154">Content *negotiation* takes place when an `Accept` header appears in the request.</span></span> <span data-ttu-id="e33a5-155">Wenn eine Anforderung einen Accept-Header enthält, führt ASP.NET Core Folgendes aus:</span><span class="sxs-lookup"><span data-stu-id="e33a5-155">When a request contains an accept header, ASP.NET Core:</span></span>

* <span data-ttu-id="e33a5-156">Die Medientypen im Accept-Header werden in der bevorzugten Reihenfolge aufgelistet.</span><span class="sxs-lookup"><span data-stu-id="e33a5-156">Enumerates the media types in the accept header in preference order.</span></span>
* <span data-ttu-id="e33a5-157">Es wird versucht, einen Formatierer zu finden, der eine Antwort in einem der angegebenen Formate erzeugen kann.</span><span class="sxs-lookup"><span data-stu-id="e33a5-157">Tries to find a formatter that can produce a response in one of the formats specified.</span></span>

<span data-ttu-id="e33a5-158">Wenn kein Formatierer gefunden wird, der die Clientanforderung erfüllen kann, führt ASP.NET Core Folgendes aus:</span><span class="sxs-lookup"><span data-stu-id="e33a5-158">If no formatter is found that can satisfy the client's request, ASP.NET Core:</span></span>

* <span data-ttu-id="e33a5-159">Es wird `406 Not Acceptable` zurückgegeben, wenn <xref:Microsoft.AspNetCore.Mvc.MvcOptions> festgelegt wurde, oder</span><span class="sxs-lookup"><span data-stu-id="e33a5-159">Returns `406 Not Acceptable` if <xref:Microsoft.AspNetCore.Mvc.MvcOptions> has been set, or -</span></span>
* <span data-ttu-id="e33a5-160">Es wird versucht, den ersten Formatierer zu finden, der eine Antwort erzeugen kann.</span><span class="sxs-lookup"><span data-stu-id="e33a5-160">Tries to find the first formatter that can produce a response.</span></span>

<span data-ttu-id="e33a5-161">Wenn kein Formatierer für das angeforderte Format konfiguriert wurde, wird der erste Formatierer verwendet, der das Objekt formatieren kann.</span><span class="sxs-lookup"><span data-stu-id="e33a5-161">If no formatter is configured for the requested format, the first formatter that can format the object is used.</span></span> <span data-ttu-id="e33a5-162">Wenn in der Anforderung kein `Accept`-Header vorhanden ist, gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="e33a5-162">If no `Accept` header appears in the request:</span></span>

* <span data-ttu-id="e33a5-163">Der erste Formatierer, der das Objekt verarbeiten kann, wird zum Serialisieren der Antwort verwendet.</span><span class="sxs-lookup"><span data-stu-id="e33a5-163">The first formatter that can handle the object is used to serialize the response.</span></span>
* <span data-ttu-id="e33a5-164">Es findet keine Aushandlung statt.</span><span class="sxs-lookup"><span data-stu-id="e33a5-164">There isn't any negotiation taking place.</span></span> <span data-ttu-id="e33a5-165">Der Server bestimmt, welches Format zurückgegeben werden soll.</span><span class="sxs-lookup"><span data-stu-id="e33a5-165">The server is determining what format to return.</span></span>

<span data-ttu-id="e33a5-166">Enthält der Accept-Header `*/*`, wird der Header ignoriert, es sei denn, `RespectBrowserAcceptHeader` ist in <xref:Microsoft.AspNetCore.Mvc.MvcOptions> auf TRUE festgelegt.</span><span class="sxs-lookup"><span data-stu-id="e33a5-166">If the Accept header contains `*/*`, the Header is ignored unless `RespectBrowserAcceptHeader` is set to true on <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="e33a5-167">Browser und Inhaltsaushandlung</span><span class="sxs-lookup"><span data-stu-id="e33a5-167">Browsers and content negotiation</span></span>

<span data-ttu-id="e33a5-168">Im Gegensatz zu typischen API-Clients, stellen Webbrowser `Accept`-Header bereit.</span><span class="sxs-lookup"><span data-stu-id="e33a5-168">Unlike typical API clients, web browsers supply `Accept` headers.</span></span> <span data-ttu-id="e33a5-169">Webbrowser geben viele Formate an, einschließlich Platzhaltern.</span><span class="sxs-lookup"><span data-stu-id="e33a5-169">Web browser specify many formats, including wildcards.</span></span> <span data-ttu-id="e33a5-170">Wenn das Framework erkennt, dass eine Anforderung von einem Browser stammt, wird standardmäßig Folgendes ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="e33a5-170">By default, when the framework detects that the request is coming from a browser:</span></span>

* <span data-ttu-id="e33a5-171">Der `Accept`-Header wird ignoriert.</span><span class="sxs-lookup"><span data-stu-id="e33a5-171">The `Accept` header is ignored.</span></span>
* <span data-ttu-id="e33a5-172">Der Inhalt wird im JSON-Format zurückgegeben, sofern nicht anders konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="e33a5-172">The content is returned in JSON, unless otherwise configured.</span></span>

<span data-ttu-id="e33a5-173">Dies sorgt bei der Verwendung von APIs für ein konsistenteres browserübergreifendes Benutzererlebnis.</span><span class="sxs-lookup"><span data-stu-id="e33a5-173">This provides a more consistent experience across browsers when consuming APIs.</span></span>

<span data-ttu-id="e33a5-174">Um eine App so zu konfigurieren, dass Accept-Header in Browsern berücksichtigt werden, legen Sie <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> auf `true` fest:</span><span class="sxs-lookup"><span data-stu-id="e33a5-174">To configure an app to honor browser accept headers, set <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> to `true`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a><span data-ttu-id="e33a5-175">Konfigurieren von Formatierern</span><span class="sxs-lookup"><span data-stu-id="e33a5-175">Configure formatters</span></span>

<span data-ttu-id="e33a5-176">Apps, die zusätzliche Formate unterstützen müssen, können die geeigneten NuGet-Pakete hinzufügen und die Unterstützung konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="e33a5-176">Apps that need to support additional formats can add the appropriate NuGet packages and configure support.</span></span> <span data-ttu-id="e33a5-177">Es gibt unterschiedliche Formatierungsprogramme für Ein- und für Ausgaben.</span><span class="sxs-lookup"><span data-stu-id="e33a5-177">There are separate formatters for input and output.</span></span> <span data-ttu-id="e33a5-178">Eingabeformatierer werden von der [Modellbindung](xref:mvc/models/model-binding) verwendet.</span><span class="sxs-lookup"><span data-stu-id="e33a5-178">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="e33a5-179">Ausgabeformatierer werden zum Formatieren von Antworten verwendet.</span><span class="sxs-lookup"><span data-stu-id="e33a5-179">Output formatters are used to format responses.</span></span> <span data-ttu-id="e33a5-180">Informationen zum Erstellen eines benutzerdefinierten Formatierers finden Sie unter [Benutzerdefinierte Formatierer](xref:web-api/advanced/custom-formatters).</span><span class="sxs-lookup"><span data-stu-id="e33a5-180">For information on creating a custom formatter, see [Custom Formatters](xref:web-api/advanced/custom-formatters).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a><span data-ttu-id="e33a5-181">Hinzufügen von Unterstützung für das XML-Format</span><span class="sxs-lookup"><span data-stu-id="e33a5-181">Add XML format support</span></span>

<span data-ttu-id="e33a5-182">XML-Formatierer, die mithilfe von <xref:System.Xml.Serialization.XmlSerializer> implementiert wurden, werden durch Aufruf von <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="e33a5-182">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

<span data-ttu-id="e33a5-183">Der oben stehende Code serialisiert Ergebnisse mithilfe von `XmlSerializer`.</span><span class="sxs-lookup"><span data-stu-id="e33a5-183">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="e33a5-184">Bei Verwendung dieses Codes geben Controllermethoden das geeignete Format basierend auf dem `Accept`-Header der Anforderung zurück.</span><span class="sxs-lookup"><span data-stu-id="e33a5-184">When using the preceding code, controller methods return the appropriate format based on the request's `Accept` header.</span></span>

### <a name="configure-systemtextjson-based-formatters"></a><span data-ttu-id="e33a5-185">Konfigurieren von System.Text.Json-basierten Formatierern</span><span class="sxs-lookup"><span data-stu-id="e33a5-185">Configure System.Text.Json-based formatters</span></span>

<span data-ttu-id="e33a5-186">Funktionen für die `System.Text.Json`-basierten Formatierer können mithilfe von `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions` konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="e33a5-186">Features for the `System.Text.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span></span>

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="e33a5-187">Optionen zur Ausgabeserialisierung können aktionsweise mithilfe von `JsonResult` konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="e33a5-187">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="e33a5-188">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e33a5-188">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        options.WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a><span data-ttu-id="e33a5-189">Hinzufügen von Newtonsoft.Json-basierter Unterstützung für das JSON-Format</span><span class="sxs-lookup"><span data-stu-id="e33a5-189">Add Newtonsoft.Json-based JSON format support</span></span>

<span data-ttu-id="e33a5-190">Vor ASP.NET Core 3.0 wurden standardmäßig die JSON-Formatierer verwendet, die mithilfe des `Newtonsoft.Json`-Pakets implementiert wurden.</span><span class="sxs-lookup"><span data-stu-id="e33a5-190">Prior to ASP.NET Core 3.0, the default used JSON formatters implemented using the `Newtonsoft.Json` package.</span></span> <span data-ttu-id="e33a5-191">In ASP.NET Core 3.0 oder höher basieren die Standardformatierer für JSON auf `System.Text.Json`.</span><span class="sxs-lookup"><span data-stu-id="e33a5-191">In ASP.NET Core 3.0 or later, the default JSON formatters are based on `System.Text.Json`.</span></span> <span data-ttu-id="e33a5-192">Unterstützung für `Newtonsoft.Json` basierte Formatierer und Features ist verfügbar, indem [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) Sie das nuget-Paket installieren und in konfigurieren `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="e33a5-192">Support for `Newtonsoft.Json` based formatters and features is available by installing the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package and configuring it in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

<span data-ttu-id="e33a5-193">Einige Features funktionieren mit `System.Text.Json`-basierten Formatierern möglicherweise nicht gut und erfordern einen Verweis auf die `Newtonsoft.Json`-basierten Formatierer.</span><span class="sxs-lookup"><span data-stu-id="e33a5-193">Some features may not work well with `System.Text.Json`-based formatters and require a reference to the `Newtonsoft.Json`-based formatters.</span></span> <span data-ttu-id="e33a5-194">Verwenden Sie weiterhin `Newtonsoft.Json`-basierte Formatierer, wenn für die App Folgendes gilt:</span><span class="sxs-lookup"><span data-stu-id="e33a5-194">Continue using the `Newtonsoft.Json`-based formatters if the app:</span></span>

* <span data-ttu-id="e33a5-195">Sie verwendet `Newtonsoft.Json`-Attribute.</span><span class="sxs-lookup"><span data-stu-id="e33a5-195">Uses `Newtonsoft.Json` attributes.</span></span> <span data-ttu-id="e33a5-196">Zum Beispiel: `[JsonProperty]` oder `[JsonIgnore]`.</span><span class="sxs-lookup"><span data-stu-id="e33a5-196">For example, `[JsonProperty]` or `[JsonIgnore]`.</span></span>
* <span data-ttu-id="e33a5-197">Sie passt die Serialisierungseinstellungen an.</span><span class="sxs-lookup"><span data-stu-id="e33a5-197">Customizes the serialization settings.</span></span>
* <span data-ttu-id="e33a5-198">Sie nutzt Features, die von `Newtonsoft.Json` bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="e33a5-198">Relies on features that `Newtonsoft.Json` provides.</span></span>
* <span data-ttu-id="e33a5-199">`Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings` konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="e33a5-199">Configures `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span></span> <span data-ttu-id="e33a5-200">Vor ASP.NET Core 3.0 akzeptiert `JsonResult.SerializerSettings` eine Instanz von `JsonSerializerSettings`, die für `Newtonsoft.Json` spezifisch ist.</span><span class="sxs-lookup"><span data-stu-id="e33a5-200">Prior to ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepts an instance of `JsonSerializerSettings` that is specific to `Newtonsoft.Json`.</span></span>
* <span data-ttu-id="e33a5-201">die [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>)-Dokumentation generiert.</span><span class="sxs-lookup"><span data-stu-id="e33a5-201">Generates [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) documentation.</span></span>

<span data-ttu-id="e33a5-202">Funktionen für die `Newtonsoft.Json`-basierten Formatierer können mithilfe von `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings` konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="e33a5-202">Features for the `Newtonsoft.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span></span>

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="e33a5-203">Optionen zur Ausgabeserialisierung können aktionsweise mithilfe von `JsonResult` konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="e33a5-203">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="e33a5-204">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e33a5-204">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerSettings
    {
        options.Formatting = Formatting.Indented,
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

### <a name="add-xml-format-support"></a><span data-ttu-id="e33a5-205">Hinzufügen von Unterstützung für das XML-Format</span><span class="sxs-lookup"><span data-stu-id="e33a5-205">Add XML format support</span></span>

<span data-ttu-id="e33a5-206">Die XML-Formatierung erfordert das NuGet-Paket [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/).</span><span class="sxs-lookup"><span data-stu-id="e33a5-206">XML formatting requires the [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet package.</span></span>

<span data-ttu-id="e33a5-207">XML-Formatierer, die mithilfe von <xref:System.Xml.Serialization.XmlSerializer> implementiert wurden, werden durch Aufruf von <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="e33a5-207">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

<span data-ttu-id="e33a5-208">Der oben stehende Code serialisiert Ergebnisse mithilfe von `XmlSerializer`.</span><span class="sxs-lookup"><span data-stu-id="e33a5-208">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="e33a5-209">Bei Verwendung dieses Codes sollten Controllermethoden das geeignete Format basierend auf dem `Accept`-Header der Anforderung zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="e33a5-209">When using the preceding code, controller methods should return the appropriate format based on the request's `Accept` header.</span></span>

::: moniker-end

### <a name="specify-a-format"></a><span data-ttu-id="e33a5-210">Angeben eines Formats</span><span class="sxs-lookup"><span data-stu-id="e33a5-210">Specify a format</span></span>

<span data-ttu-id="e33a5-211">Um die Antwortformate einzuschränken, wenden Sie den [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) Filter an.</span><span class="sxs-lookup"><span data-stu-id="e33a5-211">To restrict the response formats, apply the [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter.</span></span> <span data-ttu-id="e33a5-212">Wie die [Filters](xref:mvc/controllers/filters)meisten Filter `[Produces]` können auch auf Aktion, Controller oder globaler Bereich angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="e33a5-212">Like most [Filters](xref:mvc/controllers/filters), `[Produces]` can be applied at the action, controller, or global scope:</span></span>

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

<span data-ttu-id="e33a5-213">Der vorangehende [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) Filter:</span><span class="sxs-lookup"><span data-stu-id="e33a5-213">The preceding [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter:</span></span>

* <span data-ttu-id="e33a5-214">Er erzwingt die Rückgabe von JSON-formatierten Antworten von allen Aktionen im Controller.</span><span class="sxs-lookup"><span data-stu-id="e33a5-214">Forces all actions within the controller to return JSON-formatted responses.</span></span>
* <span data-ttu-id="e33a5-215">Wenn andere Formatierer konfiguriert sind und der Client ein anderes Format angibt, wird JSON zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="e33a5-215">If other formatters are configured and the client specifies a different format, JSON is returned.</span></span>

<span data-ttu-id="e33a5-216">Weitere Informationen finden Sie unter [Filter](xref:mvc/controllers/filters).</span><span class="sxs-lookup"><span data-stu-id="e33a5-216">For more information, see [Filters](xref:mvc/controllers/filters).</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="e33a5-217">Formatierer für besondere Fälle</span><span class="sxs-lookup"><span data-stu-id="e33a5-217">Special case formatters</span></span>

<span data-ttu-id="e33a5-218">Einige besondere Fälle werden mithilfe von integrierten Formatierungsprogrammen implementiert.</span><span class="sxs-lookup"><span data-stu-id="e33a5-218">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="e33a5-219">Standardmäßig werden `string`-Rückgabetypen als *text/plain* formatiert (bzw. als *text/html*, wenn sie über den `Accept`-Header angefordert werden).</span><span class="sxs-lookup"><span data-stu-id="e33a5-219">By default, `string` return types are formatted as *text/plain* (*text/html* if requested via the `Accept` header).</span></span> <span data-ttu-id="e33a5-220">Dieses Verhalten kann durch Entfernen von <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter> gelöscht werden.</span><span class="sxs-lookup"><span data-stu-id="e33a5-220">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>.</span></span> <span data-ttu-id="e33a5-221">Formatierer werden in der `ConfigureServices`-Methode entfernt.</span><span class="sxs-lookup"><span data-stu-id="e33a5-221">Formatters are removed in the `ConfigureServices` method.</span></span> <span data-ttu-id="e33a5-222">Aktionen mit einem Modellobjekt-Rückgabetyp geben `null` zurück, wenn der Rückgabewert `204 No Content` lautet.</span><span class="sxs-lookup"><span data-stu-id="e33a5-222">Actions that have a model object return type return `204 No Content` when returning `null`.</span></span> <span data-ttu-id="e33a5-223">Dieses Verhalten kann durch Entfernen von <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter> gelöscht werden.</span><span class="sxs-lookup"><span data-stu-id="e33a5-223">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span></span> <span data-ttu-id="e33a5-224">Der folgende Code entfernt `StringOutputFormatter` und `HttpNoContentOutputFormatter`.</span><span class="sxs-lookup"><span data-stu-id="e33a5-224">The following code removes the `StringOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

<span data-ttu-id="e33a5-225">Ohne den `StringOutputFormatter` formatiert der integrierte JSON-Formatierer `string`-Rückgabetypen.</span><span class="sxs-lookup"><span data-stu-id="e33a5-225">Without the `StringOutputFormatter`, the built-in JSON formatter formats `string` return types.</span></span> <span data-ttu-id="e33a5-226">Wenn der integrierte JSON-Formatierer entfernt wird und ein XML-Formatierer verfügbar ist, formatiert der XML-Formatierer `string`-Rückgabetypen.</span><span class="sxs-lookup"><span data-stu-id="e33a5-226">If the built-in JSON formatter is removed and an XML formatter is available, the XML formatter formats `string` return types.</span></span> <span data-ttu-id="e33a5-227">Andernfalls geben `string`-Rückgabetypen `406 Not Acceptable` zurück.</span><span class="sxs-lookup"><span data-stu-id="e33a5-227">Otherwise, `string` return types return `406 Not Acceptable`.</span></span>

<span data-ttu-id="e33a5-228">Ohne `HttpNoContentOutputFormatter` werden NULL-Objekte mithilfe des konfigurierten Formatierungsprogramms formatiert.</span><span class="sxs-lookup"><span data-stu-id="e33a5-228">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="e33a5-229">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e33a5-229">For example:</span></span>

* <span data-ttu-id="e33a5-230">Der JSON-Formatierer gibt eine Antwort mit dem Text `null` zurück.</span><span class="sxs-lookup"><span data-stu-id="e33a5-230">The JSON formatter returns a response with a body of `null`.</span></span>
* <span data-ttu-id="e33a5-231">Der XML-Formatierer gibt ein leeres XML-Element mit festgelegtem Attribut `xsi:nil="true"` zurück.</span><span class="sxs-lookup"><span data-stu-id="e33a5-231">The XML formatter returns an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="e33a5-232">Zuordnung des Antwortformats durch URLs</span><span class="sxs-lookup"><span data-stu-id="e33a5-232">Response format URL mappings</span></span>

<span data-ttu-id="e33a5-233">Clients können in der URL ein bestimmtes Format anfordern, beispielsweise folgendermaßen:</span><span class="sxs-lookup"><span data-stu-id="e33a5-233">Clients can request a particular format as part of the URL, for example:</span></span>

* <span data-ttu-id="e33a5-234">In der Abfragezeichenfolge oder als Teil des Pfads.</span><span class="sxs-lookup"><span data-stu-id="e33a5-234">In the query string or part of the path.</span></span>
* <span data-ttu-id="e33a5-235">Durch Verwendung einer formatspezifischen Dateierweiterung wie „.xml“ oder „.json“.</span><span class="sxs-lookup"><span data-stu-id="e33a5-235">By using a format-specific file extension such as .xml or .json.</span></span>

<span data-ttu-id="e33a5-236">Die Zuordnung des Anforderungspfads sollte in der Route angegeben werden, die die API verwendet.</span><span class="sxs-lookup"><span data-stu-id="e33a5-236">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="e33a5-237">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e33a5-237">For example:</span></span>

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="e33a5-238">Mit der oben genannten Route kann das angeforderte Format als optionale Dateierweiterung angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="e33a5-238">The preceding route allows the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="e33a5-239">Das [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) -Attribut überprüft das vorhanden sein des Format Werts im `RouteData` und ordnet das Antwortformat dem entsprechenden Formatierer zu, wenn die Antwort erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="e33a5-239">The [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) attribute checks for the existence of the format value in the `RouteData` and maps the response format to the appropriate formatter when the response is created.</span></span>

|           <span data-ttu-id="e33a5-240">Route</span><span class="sxs-lookup"><span data-stu-id="e33a5-240">Route</span></span>        |             <span data-ttu-id="e33a5-241">Formatierungsprogramm</span><span class="sxs-lookup"><span data-stu-id="e33a5-241">Formatter</span></span>              |
|---
<span data-ttu-id="e33a5-242">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="e33a5-242">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e33a5-243">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-243">'Blazor'</span></span>
- <span data-ttu-id="e33a5-244">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e33a5-244">'Identity'</span></span>
- <span data-ttu-id="e33a5-245">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e33a5-245">'Let's Encrypt'</span></span>
- <span data-ttu-id="e33a5-246">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-246">'Razor'</span></span>
- <span data-ttu-id="e33a5-247">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="e33a5-247">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e33a5-248">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="e33a5-248">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e33a5-249">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-249">'Blazor'</span></span>
- <span data-ttu-id="e33a5-250">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e33a5-250">'Identity'</span></span>
- <span data-ttu-id="e33a5-251">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e33a5-251">'Let's Encrypt'</span></span>
- <span data-ttu-id="e33a5-252">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-252">'Razor'</span></span>
- <span data-ttu-id="e33a5-253">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="e33a5-253">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e33a5-254">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="e33a5-254">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e33a5-255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-255">'Blazor'</span></span>
- <span data-ttu-id="e33a5-256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e33a5-256">'Identity'</span></span>
- <span data-ttu-id="e33a5-257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e33a5-257">'Let's Encrypt'</span></span>
- <span data-ttu-id="e33a5-258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-258">'Razor'</span></span>
- <span data-ttu-id="e33a5-259">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="e33a5-259">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e33a5-260">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="e33a5-260">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e33a5-261">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-261">'Blazor'</span></span>
- <span data-ttu-id="e33a5-262">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e33a5-262">'Identity'</span></span>
- <span data-ttu-id="e33a5-263">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e33a5-263">'Let's Encrypt'</span></span>
- <span data-ttu-id="e33a5-264">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-264">'Razor'</span></span>
- <span data-ttu-id="e33a5-265">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="e33a5-265">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e33a5-266">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="e33a5-266">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e33a5-267">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-267">'Blazor'</span></span>
- <span data-ttu-id="e33a5-268">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e33a5-268">'Identity'</span></span>
- <span data-ttu-id="e33a5-269">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e33a5-269">'Let's Encrypt'</span></span>
- <span data-ttu-id="e33a5-270">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-270">'Razor'</span></span>
- <span data-ttu-id="e33a5-271">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="e33a5-271">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e33a5-272">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="e33a5-272">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e33a5-273">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-273">'Blazor'</span></span>
- <span data-ttu-id="e33a5-274">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e33a5-274">'Identity'</span></span>
- <span data-ttu-id="e33a5-275">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e33a5-275">'Let's Encrypt'</span></span>
- <span data-ttu-id="e33a5-276">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-276">'Razor'</span></span>
- <span data-ttu-id="e33a5-277">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="e33a5-277">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e33a5-278">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="e33a5-278">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e33a5-279">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-279">'Blazor'</span></span>
- <span data-ttu-id="e33a5-280">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e33a5-280">'Identity'</span></span>
- <span data-ttu-id="e33a5-281">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e33a5-281">'Let's Encrypt'</span></span>
- <span data-ttu-id="e33a5-282">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-282">'Razor'</span></span>
- <span data-ttu-id="e33a5-283">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="e33a5-283">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e33a5-284">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="e33a5-284">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e33a5-285">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-285">'Blazor'</span></span>
- <span data-ttu-id="e33a5-286">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e33a5-286">'Identity'</span></span>
- <span data-ttu-id="e33a5-287">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e33a5-287">'Let's Encrypt'</span></span>
- <span data-ttu-id="e33a5-288">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-288">'Razor'</span></span>
- <span data-ttu-id="e33a5-289">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="e33a5-289">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e33a5-290">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="e33a5-290">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e33a5-291">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-291">'Blazor'</span></span>
- <span data-ttu-id="e33a5-292">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e33a5-292">'Identity'</span></span>
- <span data-ttu-id="e33a5-293">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e33a5-293">'Let's Encrypt'</span></span>
- <span data-ttu-id="e33a5-294">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-294">'Razor'</span></span>
- <span data-ttu-id="e33a5-295">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="e33a5-295">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e33a5-296">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="e33a5-296">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e33a5-297">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-297">'Blazor'</span></span>
- <span data-ttu-id="e33a5-298">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e33a5-298">'Identity'</span></span>
- <span data-ttu-id="e33a5-299">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e33a5-299">'Let's Encrypt'</span></span>
- <span data-ttu-id="e33a5-300">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-300">'Razor'</span></span>
- <span data-ttu-id="e33a5-301">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="e33a5-301">'SignalR' uid:</span></span> 

<span data-ttu-id="e33a5-302">------------|---Titel: Author: Description: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="e33a5-302">------------|--- title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e33a5-303">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-303">'Blazor'</span></span>
- <span data-ttu-id="e33a5-304">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e33a5-304">'Identity'</span></span>
- <span data-ttu-id="e33a5-305">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e33a5-305">'Let's Encrypt'</span></span>
- <span data-ttu-id="e33a5-306">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-306">'Razor'</span></span>
- <span data-ttu-id="e33a5-307">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="e33a5-307">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e33a5-308">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="e33a5-308">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e33a5-309">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-309">'Blazor'</span></span>
- <span data-ttu-id="e33a5-310">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e33a5-310">'Identity'</span></span>
- <span data-ttu-id="e33a5-311">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e33a5-311">'Let's Encrypt'</span></span>
- <span data-ttu-id="e33a5-312">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-312">'Razor'</span></span>
- <span data-ttu-id="e33a5-313">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="e33a5-313">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e33a5-314">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="e33a5-314">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e33a5-315">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-315">'Blazor'</span></span>
- <span data-ttu-id="e33a5-316">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e33a5-316">'Identity'</span></span>
- <span data-ttu-id="e33a5-317">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e33a5-317">'Let's Encrypt'</span></span>
- <span data-ttu-id="e33a5-318">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-318">'Razor'</span></span>
- <span data-ttu-id="e33a5-319">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="e33a5-319">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e33a5-320">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="e33a5-320">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e33a5-321">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-321">'Blazor'</span></span>
- <span data-ttu-id="e33a5-322">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e33a5-322">'Identity'</span></span>
- <span data-ttu-id="e33a5-323">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e33a5-323">'Let's Encrypt'</span></span>
- <span data-ttu-id="e33a5-324">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-324">'Razor'</span></span>
- <span data-ttu-id="e33a5-325">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="e33a5-325">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e33a5-326">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="e33a5-326">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e33a5-327">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-327">'Blazor'</span></span>
- <span data-ttu-id="e33a5-328">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e33a5-328">'Identity'</span></span>
- <span data-ttu-id="e33a5-329">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e33a5-329">'Let's Encrypt'</span></span>
- <span data-ttu-id="e33a5-330">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-330">'Razor'</span></span>
- <span data-ttu-id="e33a5-331">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="e33a5-331">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e33a5-332">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="e33a5-332">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e33a5-333">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-333">'Blazor'</span></span>
- <span data-ttu-id="e33a5-334">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e33a5-334">'Identity'</span></span>
- <span data-ttu-id="e33a5-335">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e33a5-335">'Let's Encrypt'</span></span>
- <span data-ttu-id="e33a5-336">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-336">'Razor'</span></span>
- <span data-ttu-id="e33a5-337">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="e33a5-337">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e33a5-338">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="e33a5-338">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e33a5-339">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-339">'Blazor'</span></span>
- <span data-ttu-id="e33a5-340">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e33a5-340">'Identity'</span></span>
- <span data-ttu-id="e33a5-341">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e33a5-341">'Let's Encrypt'</span></span>
- <span data-ttu-id="e33a5-342">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-342">'Razor'</span></span>
- <span data-ttu-id="e33a5-343">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="e33a5-343">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e33a5-344">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="e33a5-344">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e33a5-345">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-345">'Blazor'</span></span>
- <span data-ttu-id="e33a5-346">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e33a5-346">'Identity'</span></span>
- <span data-ttu-id="e33a5-347">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e33a5-347">'Let's Encrypt'</span></span>
- <span data-ttu-id="e33a5-348">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-348">'Razor'</span></span>
- <span data-ttu-id="e33a5-349">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="e33a5-349">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e33a5-350">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="e33a5-350">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e33a5-351">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-351">'Blazor'</span></span>
- <span data-ttu-id="e33a5-352">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e33a5-352">'Identity'</span></span>
- <span data-ttu-id="e33a5-353">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e33a5-353">'Let's Encrypt'</span></span>
- <span data-ttu-id="e33a5-354">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-354">'Razor'</span></span>
- <span data-ttu-id="e33a5-355">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="e33a5-355">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e33a5-356">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="e33a5-356">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e33a5-357">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-357">'Blazor'</span></span>
- <span data-ttu-id="e33a5-358">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e33a5-358">'Identity'</span></span>
- <span data-ttu-id="e33a5-359">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e33a5-359">'Let's Encrypt'</span></span>
- <span data-ttu-id="e33a5-360">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-360">'Razor'</span></span>
- <span data-ttu-id="e33a5-361">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="e33a5-361">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e33a5-362">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="e33a5-362">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e33a5-363">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-363">'Blazor'</span></span>
- <span data-ttu-id="e33a5-364">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e33a5-364">'Identity'</span></span>
- <span data-ttu-id="e33a5-365">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e33a5-365">'Let's Encrypt'</span></span>
- <span data-ttu-id="e33a5-366">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-366">'Razor'</span></span>
- <span data-ttu-id="e33a5-367">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="e33a5-367">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e33a5-368">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="e33a5-368">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e33a5-369">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-369">'Blazor'</span></span>
- <span data-ttu-id="e33a5-370">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e33a5-370">'Identity'</span></span>
- <span data-ttu-id="e33a5-371">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e33a5-371">'Let's Encrypt'</span></span>
- <span data-ttu-id="e33a5-372">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-372">'Razor'</span></span>
- <span data-ttu-id="e33a5-373">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="e33a5-373">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e33a5-374">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="e33a5-374">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e33a5-375">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-375">'Blazor'</span></span>
- <span data-ttu-id="e33a5-376">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e33a5-376">'Identity'</span></span>
- <span data-ttu-id="e33a5-377">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e33a5-377">'Let's Encrypt'</span></span>
- <span data-ttu-id="e33a5-378">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-378">'Razor'</span></span>
- <span data-ttu-id="e33a5-379">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="e33a5-379">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e33a5-380">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="e33a5-380">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e33a5-381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-381">'Blazor'</span></span>
- <span data-ttu-id="e33a5-382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e33a5-382">'Identity'</span></span>
- <span data-ttu-id="e33a5-383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e33a5-383">'Let's Encrypt'</span></span>
- <span data-ttu-id="e33a5-384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-384">'Razor'</span></span>
- <span data-ttu-id="e33a5-385">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="e33a5-385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e33a5-386">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="e33a5-386">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e33a5-387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-387">'Blazor'</span></span>
- <span data-ttu-id="e33a5-388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e33a5-388">'Identity'</span></span>
- <span data-ttu-id="e33a5-389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e33a5-389">'Let's Encrypt'</span></span>
- <span data-ttu-id="e33a5-390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-390">'Razor'</span></span>
- <span data-ttu-id="e33a5-391">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="e33a5-391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e33a5-392">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="e33a5-392">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e33a5-393">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-393">'Blazor'</span></span>
- <span data-ttu-id="e33a5-394">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e33a5-394">'Identity'</span></span>
- <span data-ttu-id="e33a5-395">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e33a5-395">'Let's Encrypt'</span></span>
- <span data-ttu-id="e33a5-396">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e33a5-396">'Razor'</span></span>
- <span data-ttu-id="e33a5-397">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="e33a5-397">'SignalR' uid:</span></span> 

<span data-ttu-id="e33a5-398">------------------| |   `/api/products/5`    |    Der standardausgabeformatierer | | `/api/products/5.json` | Der JSON-Formatierer (sofern konfiguriert) | | `/api/products/5.xml`  | Der XML-Formatierer (sofern konfiguriert) |</span><span class="sxs-lookup"><span data-stu-id="e33a5-398">------------------| |   `/api/products/5`    |    The default output formatter    | | `/api/products/5.json` | The JSON formatter (if configured) | | `/api/products/5.xml`  | The XML formatter (if configured)  |</span></span>

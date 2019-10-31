---
title: Modellbindung in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie die Modellbindung in ASP.NET Core funktioniert und wie Sie ihr Verhalten anpassen.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 05/31/2019
uid: mvc/models/model-binding
ms.openlocfilehash: aeb2da7e11df1eab5a17e2ae0a3971420c9383b4
ms.sourcegitcommit: 032113208bb55ecfb2faeb6d3e9ea44eea827950
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/31/2019
ms.locfileid: "73190594"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="387e6-103">Modellbindung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="387e6-103">Model Binding in ASP.NET Core</span></span>

<span data-ttu-id="387e6-104">In diesem Artikel wird erläutert, was Modellbindung ist, wie sie funktioniert, und wie Sie ihr Verhalten anpassen können.</span><span class="sxs-lookup"><span data-stu-id="387e6-104">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="387e6-105">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="387e6-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="387e6-106">Was ist Modellbindung?</span><span class="sxs-lookup"><span data-stu-id="387e6-106">What is Model binding</span></span>

<span data-ttu-id="387e6-107">Controller und Razor Pages arbeiten mit Daten, die aus HTTP-Anforderungen stammen.</span><span class="sxs-lookup"><span data-stu-id="387e6-107">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="387e6-108">Routendaten können beispielsweise einen Datensatzschlüssel enthalten, und bereitgestellte Formularfelder können Werte für die Eigenschaften des Modells bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="387e6-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="387e6-109">Das Schreiben von Code zum Abrufen jedes dieser Werte und deren Konvertierung aus Zeichenfolgen in .NET-Datentypen wäre mühsam und fehleranfällig.</span><span class="sxs-lookup"><span data-stu-id="387e6-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="387e6-110">Modellbindung automatisiert diesen Vorgang.</span><span class="sxs-lookup"><span data-stu-id="387e6-110">Model binding automates this process.</span></span> <span data-ttu-id="387e6-111">Das Modellbindungssystem:</span><span class="sxs-lookup"><span data-stu-id="387e6-111">The model binding system:</span></span>

* <span data-ttu-id="387e6-112">Ruft Daten aus verschiedenen Quellen ab, z. B. Routendaten, Formularfelder und Abfragezeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="387e6-112">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="387e6-113">Stellt diese Daten Controllern und Razor Pages in Methodenparametern und öffentlichen Eigenschaften bereit.</span><span class="sxs-lookup"><span data-stu-id="387e6-113">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="387e6-114">Konvertiert Zeichenfolgendaten in .NET-Typen.</span><span class="sxs-lookup"><span data-stu-id="387e6-114">Converts string data to .NET types.</span></span>
* <span data-ttu-id="387e6-115">Aktualisiert Eigenschaften komplexer Typen.</span><span class="sxs-lookup"><span data-stu-id="387e6-115">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="387e6-116">Beispiel</span><span class="sxs-lookup"><span data-stu-id="387e6-116">Example</span></span>

<span data-ttu-id="387e6-117">Angenommen Sie haben die folgende Aktionsmethode:</span><span class="sxs-lookup"><span data-stu-id="387e6-117">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="387e6-118">Und die App empfängt eine Anforderung mit dieser URL:</span><span class="sxs-lookup"><span data-stu-id="387e6-118">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="387e6-119">Die Modellbindung durchläuft die folgenden Schritte, nachdem das Routingsystem die Aktionsmethode ausgewählt hat:</span><span class="sxs-lookup"><span data-stu-id="387e6-119">Model binding goes though the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="387e6-120">Findet den ersten Parameters von `GetByID`, eine ganze Zahl namens `id`.</span><span class="sxs-lookup"><span data-stu-id="387e6-120">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="387e6-121">Durchsucht die verfügbaren Quellen in der HTTP-Anforderung und findet `id` = „2“ in den Routendaten.</span><span class="sxs-lookup"><span data-stu-id="387e6-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="387e6-122">Konvertiert der Zeichenfolge „2“ in die ganze Zahl 2.</span><span class="sxs-lookup"><span data-stu-id="387e6-122">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="387e6-123">Findet den nächsten Parameter von `GetByID`, einen booleschen Wert namens `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="387e6-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="387e6-124">Durchsucht die Quellen und findet „DogsOnly=True“ in der Abfragezeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="387e6-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="387e6-125">Beim Abgleich von Namen wird die Groß- und Kleinschreibung nicht berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="387e6-125">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="387e6-126">Konvertiert die Zeichenfolge „true“ in den booleschen Wert `true`.</span><span class="sxs-lookup"><span data-stu-id="387e6-126">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="387e6-127">Das Framework ruft dann die `GetById`-Methode auf, und übergibt dabei als Eingabe „2“ für den `id`-Parameter und `true` für den `dogsOnly`-Parameter.</span><span class="sxs-lookup"><span data-stu-id="387e6-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="387e6-128">Im vorherigen Beispiel sind die Ziele der Modellbindung Methodenparameter, die einfache Typen sind.</span><span class="sxs-lookup"><span data-stu-id="387e6-128">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="387e6-129">Ziele können aber auch die Eigenschaften eines komplexen Typs sein.</span><span class="sxs-lookup"><span data-stu-id="387e6-129">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="387e6-130">Nachdem jede Eigenschaft erfolgreich gebunden wurde, erfolgt die [Modellvalidierung](xref:mvc/models/validation) für diese Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="387e6-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="387e6-131">Der Datensatz darüber, welche Daten an das Modell gebunden sind, sowie mit allen Bindungs- oder Validierungsfehlern wird in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) oder [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) gespeichert.</span><span class="sxs-lookup"><span data-stu-id="387e6-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="387e6-132">Um herauszufinden, ob dieser Vorgang erfolgreich war, überprüft die App das Flag [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid).</span><span class="sxs-lookup"><span data-stu-id="387e6-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="387e6-133">Ziele</span><span class="sxs-lookup"><span data-stu-id="387e6-133">Targets</span></span>

<span data-ttu-id="387e6-134">Die Modellbindung versucht, Werte für die folgenden Arten von Zielen zu finden:</span><span class="sxs-lookup"><span data-stu-id="387e6-134">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="387e6-135">Parameter der Controlleraktionsmethode, zu der eine Anforderung weitergeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="387e6-135">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="387e6-136">Parameter der Razor Pages-Handlermethode, zu der eine Anforderung weitergeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="387e6-136">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="387e6-137">Öffentliche Eigenschaften eines Controllers oder einer `PageModel`-Klasse, falls durch Attribute angegeben.</span><span class="sxs-lookup"><span data-stu-id="387e6-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="387e6-138">[BindProperty]-Attribut</span><span class="sxs-lookup"><span data-stu-id="387e6-138">[BindProperty] attribute</span></span>

<span data-ttu-id="387e6-139">Kann auf eine öffentliche Eigenschaft eines Controllers oder einer `PageModel`-Klasse angewendet werden, um die Modellbindung anzuweisen, diese Eigenschaft als Ziel zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="387e6-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=7-8)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="387e6-140">[BindProperties]-Attribut</span><span class="sxs-lookup"><span data-stu-id="387e6-140">[BindProperties] attribute</span></span>

<span data-ttu-id="387e6-141">Verfügbar in ASP.NET Core 2.1 und höher.</span><span class="sxs-lookup"><span data-stu-id="387e6-141">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="387e6-142">Kann auf einen Controller oder eine `PageModel`-Klasse angewendet werden, um die Modellbindung anzuweisen, alle öffentlichen Eigenschaften dieser Klasse als Ziel zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="387e6-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="387e6-143">Modellbindung für HTTP-GET-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="387e6-143">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="387e6-144">Standardmäßig sind Eigenschaften für HTTP GET-Anforderungen nicht gebunden.</span><span class="sxs-lookup"><span data-stu-id="387e6-144">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="387e6-145">In der Regel ist alles, was Sie für eine GET-Anforderung benötigen, ein Datensatz-ID-Parameter.</span><span class="sxs-lookup"><span data-stu-id="387e6-145">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="387e6-146">Die Datensatz-ID wird verwendet, um das Element in der Datenbank zu suchen.</span><span class="sxs-lookup"><span data-stu-id="387e6-146">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="387e6-147">Daher besteht keine Notwendigkeit, eine Eigenschaft zu binden, die eine Instanz des Modells enthält.</span><span class="sxs-lookup"><span data-stu-id="387e6-147">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="387e6-148">In Szenarien, in denen Sie Eigenschaften an Daten aus GET-Anforderungen binden möchten, legen Sie die Eigenschaft `SupportsGet` auf `true` fest:</span><span class="sxs-lookup"><span data-stu-id="387e6-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="387e6-149">Quellen</span><span class="sxs-lookup"><span data-stu-id="387e6-149">Sources</span></span>

<span data-ttu-id="387e6-150">Standardmäßig ruft die Modellbindung Daten in Form von Schlüssel-Wert-Paaren aus den folgenden Quellen in einer HTTP-Anforderung ab:</span><span class="sxs-lookup"><span data-stu-id="387e6-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="387e6-151">Formularfelder</span><span class="sxs-lookup"><span data-stu-id="387e6-151">Form fields</span></span> 
1. <span data-ttu-id="387e6-152">Der Anforderungstext (für [Controller mit dem [ApiController]-Attribut](xref:web-api/index#binding-source-parameter-inference))</span><span class="sxs-lookup"><span data-stu-id="387e6-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="387e6-153">Routendaten</span><span class="sxs-lookup"><span data-stu-id="387e6-153">Route data</span></span>
1. <span data-ttu-id="387e6-154">Abfragezeichenfolgeparameter</span><span class="sxs-lookup"><span data-stu-id="387e6-154">Query string parameters</span></span>
1. <span data-ttu-id="387e6-155">Hochgeladene Dateien</span><span class="sxs-lookup"><span data-stu-id="387e6-155">Uploaded files</span></span> 

<span data-ttu-id="387e6-156">Für jeden Zielparameter oder jede Zieleigenschaft werden die Quellen in der in dieser Liste angegebenen Reihenfolge überprüft.</span><span class="sxs-lookup"><span data-stu-id="387e6-156">For each target parameter or property, the sources are scanned in the order indicated in this list.</span></span> <span data-ttu-id="387e6-157">Es gibt ein paar Ausnahmen:</span><span class="sxs-lookup"><span data-stu-id="387e6-157">There are a few exceptions:</span></span>

* <span data-ttu-id="387e6-158">Routendaten und Abfragezeichenfolgenwerte werden nur für einfache Typen verwendet.</span><span class="sxs-lookup"><span data-stu-id="387e6-158">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="387e6-159">Hochgeladene Dateien werden nur an Zieltypen gebunden, die `IFormFile` oder `IEnumerable<IFormFile>` implementieren.</span><span class="sxs-lookup"><span data-stu-id="387e6-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="387e6-160">Wenn das Standardverhalten nicht die richtigen Ergebnisse liefert, können Sie eins der folgenden Attribute verwenden, um die für jedes vorgegebene Ziel zu verwendende Quelle anzugeben.</span><span class="sxs-lookup"><span data-stu-id="387e6-160">If the default behavior doesn't give the right results, you can use one of the following attributes to specify the source to use for any given target.</span></span> 

* <span data-ttu-id="387e6-161">[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute): Ruft Werte aus der Abfragezeichenfolge ab.</span><span class="sxs-lookup"><span data-stu-id="387e6-161">[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="387e6-162">[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute): Ruft Werte aus Routendaten ab.</span><span class="sxs-lookup"><span data-stu-id="387e6-162">[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="387e6-163">[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute): Ruft Werte aus bereitgestellten Formularfeldern ab.</span><span class="sxs-lookup"><span data-stu-id="387e6-163">[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="387e6-164">[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute): Ruft Werte aus dem Abfragezeichentext ab.</span><span class="sxs-lookup"><span data-stu-id="387e6-164">[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="387e6-165">[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute): Ruft Werte aus HTTP-Headern ab.</span><span class="sxs-lookup"><span data-stu-id="387e6-165">[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="387e6-166">Diese Attribute:</span><span class="sxs-lookup"><span data-stu-id="387e6-166">These attributes:</span></span>

* <span data-ttu-id="387e6-167">Werden Modelleigenschaften einzeln hinzugefügt (nicht zur Modellklasse), wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="387e6-167">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="387e6-168">Akzeptieren optional einen Modellnamenswert im Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="387e6-168">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="387e6-169">Diese Option wird für den Fall bereitgestellt, dass der Eigenschaftenname nicht mit dem Wert in der Anforderung übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="387e6-169">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="387e6-170">Beispielsweise könnte der Wert in der Anforderung ein Header mit einem Bindestrich in seinem Namen sein, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="387e6-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="387e6-171">[FromBody]-Attribut</span><span class="sxs-lookup"><span data-stu-id="387e6-171">[FromBody] attribute</span></span>

<span data-ttu-id="387e6-172">Die Anforderungstextdaten werden mithilfe von Eingabeformatierern analysiert, die für den Inhaltstyp der Anforderung spezifisch sind.</span><span class="sxs-lookup"><span data-stu-id="387e6-172">The request body data is parsed by using input formatters specific to the content type of the request.</span></span> <span data-ttu-id="387e6-173">Eingabeformatierer werden [später in diesem Artikel](#input-formatters) erklärt.</span><span class="sxs-lookup"><span data-stu-id="387e6-173">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="387e6-174">Wenden Sie `[FromBody]` auf nicht mehr als einen Parameter pro Aktionsmethode an.</span><span class="sxs-lookup"><span data-stu-id="387e6-174">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="387e6-175">Die ASP.NET Core-Runtime delegiert die Verantwortung, für das Lesen des Anforderungsdatenstroms an den Eingabeformatierer.</span><span class="sxs-lookup"><span data-stu-id="387e6-175">The ASP.NET Core runtime delegates the responsibility of reading the request stream to the input formatter.</span></span> <span data-ttu-id="387e6-176">Sobald der Anforderungsdatenstrom gelesen wurde, ist er nicht mehr verfügbar, um für die Bindung anderer `[FromBody]`-Parameter erneut gelesen zu werden.</span><span class="sxs-lookup"><span data-stu-id="387e6-176">Once the request stream is read, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="387e6-177">Zusätzliche Quellen</span><span class="sxs-lookup"><span data-stu-id="387e6-177">Additional sources</span></span>

<span data-ttu-id="387e6-178">Quelldaten werden dem Modellbindungssystem durch *Wertanbieter* bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="387e6-178">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="387e6-179">Sie können benutzerdefinierte Wertanbieter schreiben und registrieren, die Daten für die Modellbindung aus anderen Quellen abrufen.</span><span class="sxs-lookup"><span data-stu-id="387e6-179">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="387e6-180">Beispielsweise können Sie Daten aus Cookies oder Sitzungszuständen abrufen.</span><span class="sxs-lookup"><span data-stu-id="387e6-180">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="387e6-181">So rufen Sie Daten aus einer neuen Quelle ab</span><span class="sxs-lookup"><span data-stu-id="387e6-181">To get data from a new source:</span></span>

* <span data-ttu-id="387e6-182">Erstellen Sie eine Klasse, die das `IValueProvider` implementiert.</span><span class="sxs-lookup"><span data-stu-id="387e6-182">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="387e6-183">Erstellen Sie eine Klasse, die das `IValueProviderFactory` implementiert.</span><span class="sxs-lookup"><span data-stu-id="387e6-183">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="387e6-184">Registrieren Sie die Factoryklasse in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="387e6-184">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="387e6-185">Die Beispiel-App umfasst ein Beispiel für einen [Wertanbieter](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/CookieValueProvider.cs) und eine [Factory](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/CookieValueProviderFactory.cs), das Werte aus Cookies abruft.</span><span class="sxs-lookup"><span data-stu-id="387e6-185">The sample app includes a [value provider](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/CookieValueProvider.cs) and [factory](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="387e6-186">Dies ist der Registrierungscode in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="387e6-186">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="387e6-187">Der angezeigte Code fügt den benutzerdefinierten Wertanbieter hinter allen integrierten Wertanbietern ein.</span><span class="sxs-lookup"><span data-stu-id="387e6-187">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="387e6-188">Damit er der erste in der Liste wird, rufen Sie `Insert(0, new CookieValueProviderFactory())` anstelle von `Add` auf.</span><span class="sxs-lookup"><span data-stu-id="387e6-188">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="387e6-189">Keine Quelle für eine Modelleigenschaft</span><span class="sxs-lookup"><span data-stu-id="387e6-189">No source for a model property</span></span>

<span data-ttu-id="387e6-190">Standardmäßig wird kein Modellzustandsfehler erstellt, wenn kein Wert für eine Modelleigenschaft gefunden wird.</span><span class="sxs-lookup"><span data-stu-id="387e6-190">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="387e6-191">Die Eigenschaft wird auf „null“ oder einen Standardwert festgelegt:</span><span class="sxs-lookup"><span data-stu-id="387e6-191">The property is set to null or a default value:</span></span>

* <span data-ttu-id="387e6-192">Einfache Nullable-Typen werden auf `null` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="387e6-192">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="387e6-193">Nicht-Nullable-Werttypen werden auf `default(T)` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="387e6-193">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="387e6-194">Beispiel: Ein Parameter `int id` wird auf „0“ festgelegt.</span><span class="sxs-lookup"><span data-stu-id="387e6-194">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="387e6-195">Für komplexe Typen erstellt die Modellbindung eine Instanz, indem der Standardkonstruktor verwendet wird, ohne Eigenschaften festzulegen.</span><span class="sxs-lookup"><span data-stu-id="387e6-195">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="387e6-196">Arrays werden auf `Array.Empty<T>()` festgelegt, mit der Ausnahme, dass `byte[]`-Arrays auf `null` festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="387e6-196">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="387e6-197">Wenn der Modellzustand ungültig gemacht werden soll, wenn in Fomularfeldern für eine Modelleigenschaft nichts gefunden wird, verwenden Sie das [[BindRequired]-Attribut](#bindrequired-attribute).</span><span class="sxs-lookup"><span data-stu-id="387e6-197">If model state should be invalidated when nothing is found in form fields for a model property, use the [[BindRequired] attribute](#bindrequired-attribute).</span></span>

<span data-ttu-id="387e6-198">Beachten Sie, dass dieses `[BindRequired]`-Verhalten für die Modellbindung von Daten aus bereitgestellten Formulardaten gilt, nicht für JSON- oder XML-Daten in einem Anforderungstext.</span><span class="sxs-lookup"><span data-stu-id="387e6-198">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="387e6-199">Anforderungstextdaten werden von [Eingabeformatierern](#input-formatters) verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="387e6-199">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="387e6-200">Typkonvertierungsfehler</span><span class="sxs-lookup"><span data-stu-id="387e6-200">Type conversion errors</span></span>

<span data-ttu-id="387e6-201">Wenn eine Quelle gefunden wird, aber nicht in den Zieltyp konvertiert werden kann, wird der Modellzustand als „ungültig“ gekennzeichnet.</span><span class="sxs-lookup"><span data-stu-id="387e6-201">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="387e6-202">Der Zielparameter oder die Zieleigenschaft wird auf „null“ oder einen Standardwert festgelegt, wie bereits im vorherigen Abschnitt erwähnt.</span><span class="sxs-lookup"><span data-stu-id="387e6-202">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="387e6-203">In einem API-Controller, der über das `[ApiController]`-Attribut verfügt, führt ein ungültiger Modellzustand zu einer automatischen „HTTP 400“-Antwort.</span><span class="sxs-lookup"><span data-stu-id="387e6-203">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="387e6-204">Auf einer Razor Page wird die Seite erneut mit einer Fehlermeldung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="387e6-204">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="387e6-205">Die clientseitige Validierung fängt die meisten ungültigen Daten ab, die andernfalls an ein Razor Pages-Formular übermittelt würden.</span><span class="sxs-lookup"><span data-stu-id="387e6-205">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="387e6-206">Diese Validierung erschwert es, den voranstehenden, hervorgehobenen Code auszulösen.</span><span class="sxs-lookup"><span data-stu-id="387e6-206">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="387e6-207">Die Beispiel-App umfasst eine Schaltfläche **Submit with Invalid Date** (Mit ungültigem Datum absenden), die ungültige Daten in das Feld **Hire Date** (Einstellungsdatum) einfügt und das Formular absendet.</span><span class="sxs-lookup"><span data-stu-id="387e6-207">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="387e6-208">Diese Schaltfläche zeigt, wie der Code zum erneuten Anzeigen der Seite funktioniert, wenn Datenkonvertierungsfehler auftreten.</span><span class="sxs-lookup"><span data-stu-id="387e6-208">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="387e6-209">Wenn die Seite von dem vorangehenden Code erneut angezeigt wird, wird die ungültige Eingabe nicht im Formularfeld angezeigt.</span><span class="sxs-lookup"><span data-stu-id="387e6-209">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="387e6-210">Dies liegt daran, dass die Modelleigenschaft auf „null“ oder einen Standardwert festgelegt wurde.</span><span class="sxs-lookup"><span data-stu-id="387e6-210">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="387e6-211">Die ungültige Eingabe wird jedoch in einer Fehlermeldung angezeigt.</span><span class="sxs-lookup"><span data-stu-id="387e6-211">The invalid input does appear in an error message.</span></span> <span data-ttu-id="387e6-212">Wenn Sie aber die ungültigen Daten im Formularfeld erneut anzeigen möchten, sollten Sie aus der Modelleigenschaft eine Zeichenfolge machen und die Datenkonvertierung manuell ausführen.</span><span class="sxs-lookup"><span data-stu-id="387e6-212">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="387e6-213">Dieselbe Strategie empfiehlt sich, wenn Sie nicht möchten, dass Typkonvertierungsfehler zu Modellzustandsfehlern führen.</span><span class="sxs-lookup"><span data-stu-id="387e6-213">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="387e6-214">In diesem Fall machen Sie aus der Modelleigenschaft eine Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="387e6-214">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="387e6-215">Einfache Typen</span><span class="sxs-lookup"><span data-stu-id="387e6-215">Simple types</span></span>

<span data-ttu-id="387e6-216">Die einfachen Typen, in die die Modellbindung Quellzeichenfolgen konvertieren kann, sind unter anderem:</span><span class="sxs-lookup"><span data-stu-id="387e6-216">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="387e6-217">Boolean</span><span class="sxs-lookup"><span data-stu-id="387e6-217">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="387e6-218">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="387e6-218">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="387e6-219">Char</span><span class="sxs-lookup"><span data-stu-id="387e6-219">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="387e6-220">DateTime</span><span class="sxs-lookup"><span data-stu-id="387e6-220">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="387e6-221">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="387e6-221">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="387e6-222">Decimal</span><span class="sxs-lookup"><span data-stu-id="387e6-222">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="387e6-223">Double</span><span class="sxs-lookup"><span data-stu-id="387e6-223">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="387e6-224">Enum</span><span class="sxs-lookup"><span data-stu-id="387e6-224">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="387e6-225">Guid</span><span class="sxs-lookup"><span data-stu-id="387e6-225">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="387e6-226">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="387e6-226">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="387e6-227">Single</span><span class="sxs-lookup"><span data-stu-id="387e6-227">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="387e6-228">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="387e6-228">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="387e6-229">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="387e6-229">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="387e6-230">Uri</span><span class="sxs-lookup"><span data-stu-id="387e6-230">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="387e6-231">Version</span><span class="sxs-lookup"><span data-stu-id="387e6-231">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="387e6-232">Komplexe Typen</span><span class="sxs-lookup"><span data-stu-id="387e6-232">Complex types</span></span>

<span data-ttu-id="387e6-233">Ein komplexer Typ muss einen öffentlichen Standardkonstruktor und öffentliche schreibbare Eigenschaften besitzen, die gebunden werden können.</span><span class="sxs-lookup"><span data-stu-id="387e6-233">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="387e6-234">Wenn die Modellbindung erfolgt, wird die Klasse mit dem öffentlichen Standardkonstruktor instanziiert.</span><span class="sxs-lookup"><span data-stu-id="387e6-234">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="387e6-235">Für jede Eigenschaft des komplexen Typs durchsucht die Modellbindung die Quellen für das Namensmuster *prefix.property_name*.</span><span class="sxs-lookup"><span data-stu-id="387e6-235">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="387e6-236">Wenn nichts gefunden wird, sucht sie nur nach *property_name* ohne das Präfix.</span><span class="sxs-lookup"><span data-stu-id="387e6-236">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="387e6-237">Beim Binden an einen Parameter ist das Präfix der Name des Parameters.</span><span class="sxs-lookup"><span data-stu-id="387e6-237">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="387e6-238">Beim Binden an eine öffentliche Eigenschaft `PageModel` ist das Präfix der Name der öffentlichen Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="387e6-238">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="387e6-239">Einige Attribute besitzen eine Eigenschaft `Prefix`, die es Ihnen gestattet, die Standardverwendung des Parameter- oder Eigenschaftennamens außer Kraft zu setzen.</span><span class="sxs-lookup"><span data-stu-id="387e6-239">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="387e6-240">Nehmen Sie beispielsweise an, der komplexe Typ ist die folgende `Instructor`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="387e6-240">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="387e6-241">Präfix = Parametername</span><span class="sxs-lookup"><span data-stu-id="387e6-241">Prefix = parameter name</span></span>

<span data-ttu-id="387e6-242">Wenn das zu bindende Modell ein Parameter namens `instructorToUpdate` ist:</span><span class="sxs-lookup"><span data-stu-id="387e6-242">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="387e6-243">Die Modellbindung beginnt, indem sie die Quellen nach dem Schlüssel `instructorToUpdate.ID` durchsucht.</span><span class="sxs-lookup"><span data-stu-id="387e6-243">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="387e6-244">Wenn dieser nicht gefunden wird, sucht sie nach `ID` ohne Präfix.</span><span class="sxs-lookup"><span data-stu-id="387e6-244">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="387e6-245">Präfix = Name der Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="387e6-245">Prefix = property name</span></span>

<span data-ttu-id="387e6-246">Wenn das zu bindende Modell eine Eigenschaft des Controllers oder der `PageModel`-Klasse namens `Instructor` ist:</span><span class="sxs-lookup"><span data-stu-id="387e6-246">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="387e6-247">Die Modellbindung beginnt, indem sie die Quellen nach dem Schlüssel `Instructor.ID` durchsucht.</span><span class="sxs-lookup"><span data-stu-id="387e6-247">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="387e6-248">Wenn dieser nicht gefunden wird, sucht sie nach `ID` ohne Präfix.</span><span class="sxs-lookup"><span data-stu-id="387e6-248">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="387e6-249">Benutzerdefiniertes Präfix</span><span class="sxs-lookup"><span data-stu-id="387e6-249">Custom prefix</span></span>

<span data-ttu-id="387e6-250">Wenn das zu bindende Modell ein Parameter namens `instructorToUpdate` ist, und ein `Bind`-Attribut `Instructor` als Präfix angibt:</span><span class="sxs-lookup"><span data-stu-id="387e6-250">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="387e6-251">Die Modellbindung beginnt, indem sie die Quellen nach dem Schlüssel `Instructor.ID` durchsucht.</span><span class="sxs-lookup"><span data-stu-id="387e6-251">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="387e6-252">Wenn dieser nicht gefunden wird, sucht sie nach `ID` ohne Präfix.</span><span class="sxs-lookup"><span data-stu-id="387e6-252">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="387e6-253">Attribute für Ziele komplexen Typs</span><span class="sxs-lookup"><span data-stu-id="387e6-253">Attributes for complex type targets</span></span>

<span data-ttu-id="387e6-254">Mehrere integrierte Attribute stehen für die Kontrolle der Modellbindung komplexer Typen zur Verfügung:</span><span class="sxs-lookup"><span data-stu-id="387e6-254">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="387e6-255">Diese Attribute wirken sich auf die Modellbindung aus, wenn bereitgestellte Formulardaten die Quelle der Wert sind.</span><span class="sxs-lookup"><span data-stu-id="387e6-255">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="387e6-256">Sie haben keine Auswirkungen auf Eingabeformatierer, die bereitgestellte JSON- und XML-Anforderungstexte verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="387e6-256">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="387e6-257">Eingabeformatierer werden [später in diesem Artikel](#input-formatters) erklärt.</span><span class="sxs-lookup"><span data-stu-id="387e6-257">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="387e6-258">Lesen Sie auch die Diskussion des `[Required]`-Attributs in der [Modellvalidierung](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="387e6-258">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="387e6-259">[BindRequired]-Attribut</span><span class="sxs-lookup"><span data-stu-id="387e6-259">[BindRequired] attribute</span></span>

<span data-ttu-id="387e6-260">Kann nur auf Modelleigenschaften angewendet werden, nicht auf Methodenparameter.</span><span class="sxs-lookup"><span data-stu-id="387e6-260">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="387e6-261">Bewirkt, dass die Modellbindung einen Modellzustandsfehler hinzufügt, wenn die Bindung für die Eigenschaft eines Modells nicht erfolgen kann.</span><span class="sxs-lookup"><span data-stu-id="387e6-261">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="387e6-262">Im Folgenden ein Beispiel:</span><span class="sxs-lookup"><span data-stu-id="387e6-262">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="387e6-263">[BindNever]-Attribut</span><span class="sxs-lookup"><span data-stu-id="387e6-263">[BindNever] attribute</span></span>

<span data-ttu-id="387e6-264">Kann nur auf Modelleigenschaften angewendet werden, nicht auf Methodenparameter.</span><span class="sxs-lookup"><span data-stu-id="387e6-264">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="387e6-265">Verhindert, dass die Modellbindung die Eigenschaft eines Modells festlegt.</span><span class="sxs-lookup"><span data-stu-id="387e6-265">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="387e6-266">Im Folgenden ein Beispiel:</span><span class="sxs-lookup"><span data-stu-id="387e6-266">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="387e6-267">[Bind]-Attribut</span><span class="sxs-lookup"><span data-stu-id="387e6-267">[Bind] attribute</span></span>

<span data-ttu-id="387e6-268">Kann auf eine Klasse oder einen Methodenparameter angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="387e6-268">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="387e6-269">Gibt an, welche Eigenschaften eines Modells in die Modellbindung aufgenommen werden sollen.</span><span class="sxs-lookup"><span data-stu-id="387e6-269">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="387e6-270">Im folgenden Beispiel werden nur die angegebenen Eigenschaften des `Instructor`-Modells gebunden, wenn ein Ereignishandler oder eine Aktionsmethode aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="387e6-270">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="387e6-271">Im folgenden Beispiel werden nur die angegebenen Eigenschaften des `Instructor`-Modells gebunden, wenn die `OnPost`-Methode aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="387e6-271">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="387e6-272">Das `[Bind]`-Attribut kann zum Schutz vor Overposting in *Erstellungs*szenarien (create) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="387e6-272">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="387e6-273">Es funktioniert nicht gut in Bearbeitungsszenarien (edit), weil ausgeschlossene Eigenschaften auf „null“ oder einen Standardwert festgelegt werden, anstatt unverändert zu bleiben.</span><span class="sxs-lookup"><span data-stu-id="387e6-273">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="387e6-274">Zum Schutz vor Overposting werden Ansichtsmodelle empfohlen, anstelle des `[Bind]`-Attributs.</span><span class="sxs-lookup"><span data-stu-id="387e6-274">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="387e6-275">Weitere Informationen finden Sie unter [Sicherheitshinweis zum Overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="387e6-275">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="387e6-276">Auflistungen</span><span class="sxs-lookup"><span data-stu-id="387e6-276">Collections</span></span>

<span data-ttu-id="387e6-277">Bei Zielen, die Sammlungen einfacher Typen sind, sucht die Modellbindung nach Übereinstimmungen mit *parameter_name* oder *property_name*.</span><span class="sxs-lookup"><span data-stu-id="387e6-277">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="387e6-278">Wird keine Übereinstimmung gefunden, sucht sie nach einem der unterstützten Formate ohne Präfix.</span><span class="sxs-lookup"><span data-stu-id="387e6-278">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="387e6-279">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="387e6-279">For example:</span></span>

* <span data-ttu-id="387e6-280">Angenommen, der zu bindende Parameter ist ein Array namens `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="387e6-280">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="387e6-281">Formular- oder Abfragezeichenfolgendaten können eins der folgenden Formate haben:</span><span class="sxs-lookup"><span data-stu-id="387e6-281">Form or query string data can be in one of the following formats:</span></span>
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* <span data-ttu-id="387e6-282">Das folgende Format wird nur für Formulardaten unterstützt:</span><span class="sxs-lookup"><span data-stu-id="387e6-282">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="387e6-283">Bei allen der vorangehenden Beispielformate übergibt die Modellbindung ein Array von zwei Elementen an den `selectedCourses`-Parameter:</span><span class="sxs-lookup"><span data-stu-id="387e6-283">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="387e6-284">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="387e6-284">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="387e6-285">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="387e6-285">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="387e6-286">Datenformate, die Indexnummern verwenden(... [0]... [1] ...), müssen sicherstellen, dass sie fortlaufend nummeriert sind, beginnend mit 0 (null).</span><span class="sxs-lookup"><span data-stu-id="387e6-286">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="387e6-287">Treten bei der Indexnummerierung Lücken auf, werden alle Elemente, die auf die Lücke folgen, ignoriert.</span><span class="sxs-lookup"><span data-stu-id="387e6-287">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="387e6-288">Wenn die Indizes beispielsweise 0 und 2 anstelle von 0 und 1 sind, wird beispielsweise das zweite Element ignoriert.</span><span class="sxs-lookup"><span data-stu-id="387e6-288">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="387e6-289">Wörterbücher</span><span class="sxs-lookup"><span data-stu-id="387e6-289">Dictionaries</span></span>

<span data-ttu-id="387e6-290">Bei `Dictionary`-Zielen sucht die Modellbindung nach Übereinstimmungen mit *parameter_name* oder *property_name*.</span><span class="sxs-lookup"><span data-stu-id="387e6-290">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="387e6-291">Wird keine Übereinstimmung gefunden, sucht sie nach einem der unterstützten Formate ohne Präfix.</span><span class="sxs-lookup"><span data-stu-id="387e6-291">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="387e6-292">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="387e6-292">For example:</span></span>

* <span data-ttu-id="387e6-293">Angenommen, der Zielparameter ist eine `Dictionary<int, string>` mit dem Namen `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="387e6-293">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="387e6-294">Die bereitgestellten Formular- oder Abfragezeichenfolgendaten können wie eins der folgenden Beispiele aussehen:</span><span class="sxs-lookup"><span data-stu-id="387e6-294">The posted form or query string data can look like one of the following examples:</span></span>

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* <span data-ttu-id="387e6-295">Bei allen der vorangehenden Beispielformate übergibt die Modellbindung ein Wörterbuch aus zwei Elementen an den `selectedCourses`-Parameter:</span><span class="sxs-lookup"><span data-stu-id="387e6-295">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="387e6-296">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="387e6-296">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="387e6-297">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="387e6-297">selectedCourses["2000"]="Economics"</span></span>

## <a name="special-data-types"></a><span data-ttu-id="387e6-298">Spezielle Datentypen</span><span class="sxs-lookup"><span data-stu-id="387e6-298">Special data types</span></span>

<span data-ttu-id="387e6-299">Es gibt einige spezielle Datentypen, die die Modellbindung verarbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="387e6-299">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="387e6-300">„IFormFile“ und „IFormFileCollection“</span><span class="sxs-lookup"><span data-stu-id="387e6-300">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="387e6-301">Eine in der HTTP-Anforderung enthaltene, hochgeladenen Datei.</span><span class="sxs-lookup"><span data-stu-id="387e6-301">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="387e6-302">Außerdem wird `IEnumerable<IFormFile>` für mehrere Dateien unterstützt.</span><span class="sxs-lookup"><span data-stu-id="387e6-302">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="387e6-303">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="387e6-303">CancellationToken</span></span>

<span data-ttu-id="387e6-304">Wird verwendet, um die Aktivität in asynchronen Controllern zu beenden.</span><span class="sxs-lookup"><span data-stu-id="387e6-304">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="387e6-305">„FormCollection“</span><span class="sxs-lookup"><span data-stu-id="387e6-305">FormCollection</span></span>

<span data-ttu-id="387e6-306">Wird verwendet, um alle Werte aus bereitgestellten Formulardaten abzurufen.</span><span class="sxs-lookup"><span data-stu-id="387e6-306">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="387e6-307">Eingabeformatierer</span><span class="sxs-lookup"><span data-stu-id="387e6-307">Input formatters</span></span>

<span data-ttu-id="387e6-308">Daten im Anforderungstext können im JSON-, XML- oder einem anderen Format sein.</span><span class="sxs-lookup"><span data-stu-id="387e6-308">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="387e6-309">Um diese Daten zu analysieren, verwendet die Modellbindung einen *Eingabeformatierer*, der für die Verarbeitung eines bestimmten Inhaltstyps konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="387e6-309">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="387e6-310">ASP.NET Core enthält standardmäßig JSON-basierte Eingabeformatierer für die Verarbeitung von JSON-Daten.</span><span class="sxs-lookup"><span data-stu-id="387e6-310">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="387e6-311">Sie können andere Formatierer für andere Inhaltstypen hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="387e6-311">You can add other formatters for other content types.</span></span>

<span data-ttu-id="387e6-312">ASP.NET Core wählt Eingabeformatierer auf Grundlage des [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute)-Attributs aus.</span><span class="sxs-lookup"><span data-stu-id="387e6-312">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="387e6-313">Wenn kein Attribut vorhanden ist, verwendet es den [Content-Type-Header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="387e6-313">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="387e6-314">So verwenden Sie die integrierte XML-Eingabeformatierer</span><span class="sxs-lookup"><span data-stu-id="387e6-314">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="387e6-315">Installieren Sie das NuGet-Paket `Microsoft.AspNetCore.Mvc.Formatters.Xml`.</span><span class="sxs-lookup"><span data-stu-id="387e6-315">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="387e6-316">In `Startup.ConfigureServices`, rufen Sie <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> oder <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*> auf.</span><span class="sxs-lookup"><span data-stu-id="387e6-316">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="387e6-317">Wenden Sie das `Consumes`-Attribut auf Controllerklassen oder Aktionsmethoden an, die XML im Anforderungstext erwarten sollten.</span><span class="sxs-lookup"><span data-stu-id="387e6-317">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="387e6-318">Weitere Informationen finden Sie unter [Einführung der XML-Serialisierung](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="387e6-318">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="387e6-319">Ausschließen angegebener Typen aus der Modellbindung</span><span class="sxs-lookup"><span data-stu-id="387e6-319">Exclude specified types from model binding</span></span>

<span data-ttu-id="387e6-320">Das Verhalten der Modellbindung und des Validierungssystems wird von der Klasse [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata) gesteuert.</span><span class="sxs-lookup"><span data-stu-id="387e6-320">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="387e6-321">Sie können `ModelMetadata` anpassen, indem Sie [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders) einen Detailanbieter hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="387e6-321">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="387e6-322">Integrierte Detailanbieter sind verfügbar, um die Modellbindung oder Validierung für angegebene Typen zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="387e6-322">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="387e6-323">Um die Modellbindung für alle Modelle eines angegebenen Typs zu deaktivieren, fügen Sie in `Startup.ConfigureServices` einen <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> hinzu.</span><span class="sxs-lookup"><span data-stu-id="387e6-323">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="387e6-324">Beispielsweise können Sie die Modellbindung für alle Modelle vom Typ `System.Version` wie folgt deaktivieren:</span><span class="sxs-lookup"><span data-stu-id="387e6-324">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="387e6-325">Um die Validierung für Eigenschaften eines angegebenen Typs zu deaktivieren, fügen Sie in `Startup.ConfigureServices` einen <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> hinzu.</span><span class="sxs-lookup"><span data-stu-id="387e6-325">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="387e6-326">Beispielsweise können Sie die Überprüfung von Eigenschaften vom Typ `System.Guid` wie folgt deaktivieren:</span><span class="sxs-lookup"><span data-stu-id="387e6-326">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="387e6-327">Benutzerdefinierte Modellbindungen</span><span class="sxs-lookup"><span data-stu-id="387e6-327">Custom model binders</span></span>

<span data-ttu-id="387e6-328">Sie können die Modellbindung erweitern, indem Sie eine benutzerdefinierte Modellbindung schreiben und das `[ModelBinder]`-Attribut verwenden, um diese für ein bestimmtes Ziel auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="387e6-328">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="387e6-329">Erfahren Sie mehr über die [benutzerdefinierte Modellbindung](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="387e6-329">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="387e6-330">Manuelle Modellbindung</span><span class="sxs-lookup"><span data-stu-id="387e6-330">Manual model binding</span></span>

<span data-ttu-id="387e6-331">Die Modellbindung kann mithilfe der <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>-Methode manuell aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="387e6-331">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="387e6-332">Die Methode ist für die beiden Klassen `ControllerBase` und `PageModel` definiert.</span><span class="sxs-lookup"><span data-stu-id="387e6-332">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="387e6-333">Mithilfe von Methodenüberladungen können Sie das Präfix und den Wertanbieter festlegen, die verwendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="387e6-333">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="387e6-334">Die Methode gibt `false` zurück, wenn die Modellbindung fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="387e6-334">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="387e6-335">Im Folgenden ein Beispiel:</span><span class="sxs-lookup"><span data-stu-id="387e6-335">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="387e6-336">[FromServices]-Attribut</span><span class="sxs-lookup"><span data-stu-id="387e6-336">[FromServices] attribute</span></span>

<span data-ttu-id="387e6-337">Der Name dieses Attributs folgt dem Muster von Modellbindungsattributen, die eine Datenquelle angeben.</span><span class="sxs-lookup"><span data-stu-id="387e6-337">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="387e6-338">Es ist aber nicht zum Binden von Daten aus einem Wertanbieter gedacht.</span><span class="sxs-lookup"><span data-stu-id="387e6-338">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="387e6-339">Es ruft eine Instanz eines Typs aus dem [Dependency Injection](xref:fundamentals/dependency-injection)-Container (Abhängigkeitsinjektion) ab.</span><span class="sxs-lookup"><span data-stu-id="387e6-339">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="387e6-340">Sein Zweck besteht darin, eine Alternative zur „Constructor Injection“ (Konstruktorinjektion) bereitzustellen, wenn Sie einen Dienst nur dann benötigen, wenn eine bestimmte Methode aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="387e6-340">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="387e6-341">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="387e6-341">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

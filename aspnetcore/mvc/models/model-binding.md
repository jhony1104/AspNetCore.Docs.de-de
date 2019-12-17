---
title: Modellbindung in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie die Modellbindung in ASP.NET Core funktioniert und wie Sie ihr Verhalten anpassen.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 11/21/2019
uid: mvc/models/model-binding
ms.openlocfilehash: da6cc25e0bbb1b2301529b34eab4c91f9ccb46eb
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74944294"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="82b9a-103">Modellbindung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="82b9a-103">Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="82b9a-104">In diesem Artikel wird erläutert, was Modellbindung ist, wie sie funktioniert, und wie Sie ihr Verhalten anpassen können.</span><span class="sxs-lookup"><span data-stu-id="82b9a-104">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="82b9a-105">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="82b9a-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="82b9a-106">Was ist Modellbindung?</span><span class="sxs-lookup"><span data-stu-id="82b9a-106">What is Model binding</span></span>

<span data-ttu-id="82b9a-107">Controller und Razor Pages arbeiten mit Daten, die aus HTTP-Anforderungen stammen.</span><span class="sxs-lookup"><span data-stu-id="82b9a-107">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="82b9a-108">Routendaten können beispielsweise einen Datensatzschlüssel enthalten, und bereitgestellte Formularfelder können Werte für die Eigenschaften des Modells bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="82b9a-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="82b9a-109">Das Schreiben von Code zum Abrufen jedes dieser Werte und deren Konvertierung aus Zeichenfolgen in .NET-Datentypen wäre mühsam und fehleranfällig.</span><span class="sxs-lookup"><span data-stu-id="82b9a-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="82b9a-110">Modellbindung automatisiert diesen Vorgang.</span><span class="sxs-lookup"><span data-stu-id="82b9a-110">Model binding automates this process.</span></span> <span data-ttu-id="82b9a-111">Das Modellbindungssystem:</span><span class="sxs-lookup"><span data-stu-id="82b9a-111">The model binding system:</span></span>

* <span data-ttu-id="82b9a-112">Ruft Daten aus verschiedenen Quellen ab, z. B. Routendaten, Formularfelder und Abfragezeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="82b9a-112">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="82b9a-113">Stellt diese Daten Controllern und Razor Pages in Methodenparametern und öffentlichen Eigenschaften bereit.</span><span class="sxs-lookup"><span data-stu-id="82b9a-113">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="82b9a-114">Konvertiert Zeichenfolgendaten in .NET-Typen.</span><span class="sxs-lookup"><span data-stu-id="82b9a-114">Converts string data to .NET types.</span></span>
* <span data-ttu-id="82b9a-115">Aktualisiert Eigenschaften komplexer Typen.</span><span class="sxs-lookup"><span data-stu-id="82b9a-115">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="82b9a-116">Beispiel</span><span class="sxs-lookup"><span data-stu-id="82b9a-116">Example</span></span>

<span data-ttu-id="82b9a-117">Angenommen Sie haben die folgende Aktionsmethode:</span><span class="sxs-lookup"><span data-stu-id="82b9a-117">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="82b9a-118">Und die App empfängt eine Anforderung mit dieser URL:</span><span class="sxs-lookup"><span data-stu-id="82b9a-118">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="82b9a-119">Die Modellbindung durchläuft die folgenden Schritte, nachdem das Routingsystem die Aktionsmethode ausgewählt hat:</span><span class="sxs-lookup"><span data-stu-id="82b9a-119">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="82b9a-120">Findet den ersten Parameters von `GetByID`, eine ganze Zahl namens `id`.</span><span class="sxs-lookup"><span data-stu-id="82b9a-120">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="82b9a-121">Durchsucht die verfügbaren Quellen in der HTTP-Anforderung und findet `id` = „2“ in den Routendaten.</span><span class="sxs-lookup"><span data-stu-id="82b9a-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="82b9a-122">Konvertiert der Zeichenfolge „2“ in die ganze Zahl 2.</span><span class="sxs-lookup"><span data-stu-id="82b9a-122">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="82b9a-123">Findet den nächsten Parameter von `GetByID`, einen booleschen Wert namens `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="82b9a-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="82b9a-124">Durchsucht die Quellen und findet „DogsOnly=True“ in der Abfragezeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="82b9a-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="82b9a-125">Beim Abgleich von Namen wird die Groß- und Kleinschreibung nicht berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="82b9a-125">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="82b9a-126">Konvertiert die Zeichenfolge „true“ in den booleschen Wert `true`.</span><span class="sxs-lookup"><span data-stu-id="82b9a-126">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="82b9a-127">Das Framework ruft dann die `GetById`-Methode auf, und übergibt dabei als Eingabe „2“ für den `id`-Parameter und `true` für den `dogsOnly`-Parameter.</span><span class="sxs-lookup"><span data-stu-id="82b9a-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="82b9a-128">Im vorherigen Beispiel sind die Ziele der Modellbindung Methodenparameter, die einfache Typen sind.</span><span class="sxs-lookup"><span data-stu-id="82b9a-128">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="82b9a-129">Ziele können aber auch die Eigenschaften eines komplexen Typs sein.</span><span class="sxs-lookup"><span data-stu-id="82b9a-129">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="82b9a-130">Nachdem jede Eigenschaft erfolgreich gebunden wurde, erfolgt die [Modellvalidierung](xref:mvc/models/validation) für diese Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="82b9a-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="82b9a-131">Der Datensatz darüber, welche Daten an das Modell gebunden sind, sowie mit allen Bindungs- oder Validierungsfehlern wird in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) oder [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) gespeichert.</span><span class="sxs-lookup"><span data-stu-id="82b9a-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="82b9a-132">Um herauszufinden, ob dieser Vorgang erfolgreich war, überprüft die App das Flag [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid).</span><span class="sxs-lookup"><span data-stu-id="82b9a-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="82b9a-133">Ziele</span><span class="sxs-lookup"><span data-stu-id="82b9a-133">Targets</span></span>

<span data-ttu-id="82b9a-134">Die Modellbindung versucht, Werte für die folgenden Arten von Zielen zu finden:</span><span class="sxs-lookup"><span data-stu-id="82b9a-134">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="82b9a-135">Parameter der Controlleraktionsmethode, zu der eine Anforderung weitergeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="82b9a-135">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="82b9a-136">Parameter der Razor Pages-Handlermethode, zu der eine Anforderung weitergeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="82b9a-136">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="82b9a-137">Öffentliche Eigenschaften eines Controllers oder einer `PageModel`-Klasse, falls durch Attribute angegeben.</span><span class="sxs-lookup"><span data-stu-id="82b9a-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="82b9a-138">[BindProperty]-Attribut</span><span class="sxs-lookup"><span data-stu-id="82b9a-138">[BindProperty] attribute</span></span>

<span data-ttu-id="82b9a-139">Kann auf eine öffentliche Eigenschaft eines Controllers oder einer `PageModel`-Klasse angewendet werden, um die Modellbindung anzuweisen, diese Eigenschaft als Ziel zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="82b9a-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="82b9a-140">[BindProperties]-Attribut</span><span class="sxs-lookup"><span data-stu-id="82b9a-140">[BindProperties] attribute</span></span>

<span data-ttu-id="82b9a-141">Verfügbar in ASP.NET Core 2.1 und höher.</span><span class="sxs-lookup"><span data-stu-id="82b9a-141">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="82b9a-142">Kann auf einen Controller oder eine `PageModel`-Klasse angewendet werden, um die Modellbindung anzuweisen, alle öffentlichen Eigenschaften dieser Klasse als Ziel zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="82b9a-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="82b9a-143">Modellbindung für HTTP-GET-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="82b9a-143">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="82b9a-144">Standardmäßig sind Eigenschaften für HTTP GET-Anforderungen nicht gebunden.</span><span class="sxs-lookup"><span data-stu-id="82b9a-144">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="82b9a-145">In der Regel ist alles, was Sie für eine GET-Anforderung benötigen, ein Datensatz-ID-Parameter.</span><span class="sxs-lookup"><span data-stu-id="82b9a-145">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="82b9a-146">Die Datensatz-ID wird verwendet, um das Element in der Datenbank zu suchen.</span><span class="sxs-lookup"><span data-stu-id="82b9a-146">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="82b9a-147">Daher besteht keine Notwendigkeit, eine Eigenschaft zu binden, die eine Instanz des Modells enthält.</span><span class="sxs-lookup"><span data-stu-id="82b9a-147">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="82b9a-148">In Szenarien, in denen Sie Eigenschaften an Daten aus GET-Anforderungen binden möchten, legen Sie die Eigenschaft `SupportsGet` auf `true` fest:</span><span class="sxs-lookup"><span data-stu-id="82b9a-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="82b9a-149">Quellen</span><span class="sxs-lookup"><span data-stu-id="82b9a-149">Sources</span></span>

<span data-ttu-id="82b9a-150">Standardmäßig ruft die Modellbindung Daten in Form von Schlüssel-Wert-Paaren aus den folgenden Quellen in einer HTTP-Anforderung ab:</span><span class="sxs-lookup"><span data-stu-id="82b9a-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="82b9a-151">Formularfelder</span><span class="sxs-lookup"><span data-stu-id="82b9a-151">Form fields</span></span>
1. <span data-ttu-id="82b9a-152">Der Anforderungstext (für [Controller mit dem [ApiController]-Attribut](xref:web-api/index#binding-source-parameter-inference))</span><span class="sxs-lookup"><span data-stu-id="82b9a-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="82b9a-153">Routendaten</span><span class="sxs-lookup"><span data-stu-id="82b9a-153">Route data</span></span>
1. <span data-ttu-id="82b9a-154">Abfragezeichenfolgeparameter</span><span class="sxs-lookup"><span data-stu-id="82b9a-154">Query string parameters</span></span>
1. <span data-ttu-id="82b9a-155">Hochgeladene Dateien</span><span class="sxs-lookup"><span data-stu-id="82b9a-155">Uploaded files</span></span>

<span data-ttu-id="82b9a-156">Für jeden Zielparameter oder jede Zieleigenschaft werden die Quellen nach der oben aufgeführten Reihenfolge überprüft.</span><span class="sxs-lookup"><span data-stu-id="82b9a-156">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="82b9a-157">Es gibt ein paar Ausnahmen:</span><span class="sxs-lookup"><span data-stu-id="82b9a-157">There are a few exceptions:</span></span>

* <span data-ttu-id="82b9a-158">Routendaten und Abfragezeichenfolgenwerte werden nur für einfache Typen verwendet.</span><span class="sxs-lookup"><span data-stu-id="82b9a-158">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="82b9a-159">Hochgeladene Dateien werden nur an Zieltypen gebunden, die `IFormFile` oder `IEnumerable<IFormFile>` implementieren.</span><span class="sxs-lookup"><span data-stu-id="82b9a-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="82b9a-160">Wenn die Standardquelle nicht richtig ist, verwenden Sie eines der folgenden Attribute zum Festlegen der Quelle:</span><span class="sxs-lookup"><span data-stu-id="82b9a-160">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="82b9a-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute): Ruft Werte aus der Abfragezeichenfolge ab.</span><span class="sxs-lookup"><span data-stu-id="82b9a-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="82b9a-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute): Ruft Werte aus Routendaten ab.</span><span class="sxs-lookup"><span data-stu-id="82b9a-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="82b9a-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute): Ruft Werte aus bereitgestellten Formularfeldern ab.</span><span class="sxs-lookup"><span data-stu-id="82b9a-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="82b9a-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute): Ruft Werte aus dem Anforderungstext ab.</span><span class="sxs-lookup"><span data-stu-id="82b9a-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="82b9a-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute): Ruft Werte aus HTTP-Headern ab.</span><span class="sxs-lookup"><span data-stu-id="82b9a-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="82b9a-166">Diese Attribute:</span><span class="sxs-lookup"><span data-stu-id="82b9a-166">These attributes:</span></span>

* <span data-ttu-id="82b9a-167">Werden Modelleigenschaften einzeln hinzugefügt (nicht zur Modellklasse), wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="82b9a-167">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="82b9a-168">Akzeptieren optional einen Modellnamenswert im Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="82b9a-168">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="82b9a-169">Diese Option wird für den Fall bereitgestellt, dass der Eigenschaftenname nicht mit dem Wert in der Anforderung übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="82b9a-169">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="82b9a-170">Beispielsweise könnte der Wert in der Anforderung ein Header mit einem Bindestrich in seinem Namen sein, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="82b9a-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="82b9a-171">[FromBody]-Attribut</span><span class="sxs-lookup"><span data-stu-id="82b9a-171">[FromBody] attribute</span></span>

<span data-ttu-id="82b9a-172">Wenden Sie das `[FromBody]`-Attribut auf einen Parameter an, um dessen Eigenschaften über den Text einer HTTP-Anforderung aufzufüllen.</span><span class="sxs-lookup"><span data-stu-id="82b9a-172">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="82b9a-173">Die ASP.NET Core-Runtime delegiert die Verantwortung, für das Lesen des Texts an einen Eingabeformatierer.</span><span class="sxs-lookup"><span data-stu-id="82b9a-173">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="82b9a-174">Eingabeformatierer werden [später in diesem Artikel](#input-formatters) erklärt.</span><span class="sxs-lookup"><span data-stu-id="82b9a-174">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="82b9a-175">Wenn `[FromBody]` auf einen komplexen Typparameter angewendet wird, werden alle Bindungsquellenattribute ignoriert, die auf die Eigenschaften angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="82b9a-175">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="82b9a-176">Die folgende `Create`-Aktion legt beispielsweise fest, dass der `pet`-Parameter mithilfe des Texts aufgefüllt wird:</span><span class="sxs-lookup"><span data-stu-id="82b9a-176">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="82b9a-177">Die `Pet`-Klasse legt fest, dass ihre `Breed`-Eigenschaft mithilfe eines Abfragezeichenfolgenparameters aufgefüllt wird:</span><span class="sxs-lookup"><span data-stu-id="82b9a-177">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="82b9a-178">Im vorherigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="82b9a-178">In the preceding example:</span></span>

* <span data-ttu-id="82b9a-179">Das `[FromQuery]`-Attribut wird ignoriert.</span><span class="sxs-lookup"><span data-stu-id="82b9a-179">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="82b9a-180">Die `Breed`-Eigenschaft wird nicht mithilfe eines Abfragezeichenfolgenparameters aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="82b9a-180">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="82b9a-181">Eingabeformatierer lesen nur den Text und verstehen Bindungsquellenattribute nicht.</span><span class="sxs-lookup"><span data-stu-id="82b9a-181">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="82b9a-182">Wenn ein geeigneter Wert im Text gefunden wird, wird dieser Wert zum Auffüllen der `Breed`-Eigenschaft verwendet.</span><span class="sxs-lookup"><span data-stu-id="82b9a-182">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="82b9a-183">Wenden Sie `[FromBody]` auf nicht mehr als einen Parameter pro Aktionsmethode an.</span><span class="sxs-lookup"><span data-stu-id="82b9a-183">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="82b9a-184">Sobald der Anforderungsdatenstrom von einem Eingabeformatierer gelesen wurde, ist er nicht mehr verfügbar, um für die Bindung anderer `[FromBody]`-Parameter nochmal gelesen zu werden.</span><span class="sxs-lookup"><span data-stu-id="82b9a-184">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="82b9a-185">Zusätzliche Quellen</span><span class="sxs-lookup"><span data-stu-id="82b9a-185">Additional sources</span></span>

<span data-ttu-id="82b9a-186">Quelldaten werden dem Modellbindungssystem durch *Wertanbieter* bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="82b9a-186">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="82b9a-187">Sie können benutzerdefinierte Wertanbieter schreiben und registrieren, die Daten für die Modellbindung aus anderen Quellen abrufen.</span><span class="sxs-lookup"><span data-stu-id="82b9a-187">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="82b9a-188">Beispielsweise können Sie Daten aus Cookies oder Sitzungszuständen abrufen.</span><span class="sxs-lookup"><span data-stu-id="82b9a-188">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="82b9a-189">So rufen Sie Daten aus einer neuen Quelle ab</span><span class="sxs-lookup"><span data-stu-id="82b9a-189">To get data from a new source:</span></span>

* <span data-ttu-id="82b9a-190">Erstellen Sie eine Klasse, die das `IValueProvider` implementiert.</span><span class="sxs-lookup"><span data-stu-id="82b9a-190">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="82b9a-191">Erstellen Sie eine Klasse, die das `IValueProviderFactory` implementiert.</span><span class="sxs-lookup"><span data-stu-id="82b9a-191">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="82b9a-192">Registrieren Sie die Factoryklasse in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="82b9a-192">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="82b9a-193">Die Beispiel-App umfasst ein Beispiel für einen [Wertanbieter](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) und eine [Factory](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs), das Werte aus Cookies abruft.</span><span class="sxs-lookup"><span data-stu-id="82b9a-193">The sample app includes a [value provider](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="82b9a-194">Dies ist der Registrierungscode in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="82b9a-194">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="82b9a-195">Der angezeigte Code fügt den benutzerdefinierten Wertanbieter hinter allen integrierten Wertanbietern ein.</span><span class="sxs-lookup"><span data-stu-id="82b9a-195">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="82b9a-196">Damit er der erste in der Liste wird, rufen Sie `Insert(0, new CookieValueProviderFactory())` anstelle von `Add` auf.</span><span class="sxs-lookup"><span data-stu-id="82b9a-196">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="82b9a-197">Keine Quelle für eine Modelleigenschaft</span><span class="sxs-lookup"><span data-stu-id="82b9a-197">No source for a model property</span></span>

<span data-ttu-id="82b9a-198">Standardmäßig wird kein Modellzustandsfehler erstellt, wenn kein Wert für eine Modelleigenschaft gefunden wird.</span><span class="sxs-lookup"><span data-stu-id="82b9a-198">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="82b9a-199">Die Eigenschaft wird auf „null“ oder einen Standardwert festgelegt:</span><span class="sxs-lookup"><span data-stu-id="82b9a-199">The property is set to null or a default value:</span></span>

* <span data-ttu-id="82b9a-200">Einfache Nullable-Typen werden auf `null` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="82b9a-200">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="82b9a-201">Nicht-Nullable-Werttypen werden auf `default(T)` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="82b9a-201">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="82b9a-202">Beispiel: Ein Parameter `int id` wird auf „0“ festgelegt.</span><span class="sxs-lookup"><span data-stu-id="82b9a-202">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="82b9a-203">Für komplexe Typen erstellt die Modellbindung eine Instanz, indem der Standardkonstruktor verwendet wird, ohne Eigenschaften festzulegen.</span><span class="sxs-lookup"><span data-stu-id="82b9a-203">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="82b9a-204">Arrays werden auf `Array.Empty<T>()` festgelegt, mit der Ausnahme, dass `byte[]`-Arrays auf `null` festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="82b9a-204">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="82b9a-205">Um den Modellzustand ungültig zu machen, wenn in Formularfeldern für eine Modelleigenschaft nichts gefunden wird, verwenden Sie das [`[BindRequired]`](#bindrequired-attribute)-Attribut.</span><span class="sxs-lookup"><span data-stu-id="82b9a-205">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="82b9a-206">Beachten Sie, dass dieses `[BindRequired]`-Verhalten für die Modellbindung von Daten aus bereitgestellten Formulardaten gilt, nicht für JSON- oder XML-Daten in einem Anforderungstext.</span><span class="sxs-lookup"><span data-stu-id="82b9a-206">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="82b9a-207">Anforderungstextdaten werden von [Eingabeformatierern](#input-formatters) verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="82b9a-207">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="82b9a-208">Typkonvertierungsfehler</span><span class="sxs-lookup"><span data-stu-id="82b9a-208">Type conversion errors</span></span>

<span data-ttu-id="82b9a-209">Wenn eine Quelle gefunden wird, aber nicht in den Zieltyp konvertiert werden kann, wird der Modellzustand als „ungültig“ gekennzeichnet.</span><span class="sxs-lookup"><span data-stu-id="82b9a-209">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="82b9a-210">Der Zielparameter oder die Zieleigenschaft wird auf „null“ oder einen Standardwert festgelegt, wie bereits im vorherigen Abschnitt erwähnt.</span><span class="sxs-lookup"><span data-stu-id="82b9a-210">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="82b9a-211">In einem API-Controller, der über das `[ApiController]`-Attribut verfügt, führt ein ungültiger Modellzustand zu einer automatischen „HTTP 400“-Antwort.</span><span class="sxs-lookup"><span data-stu-id="82b9a-211">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="82b9a-212">Auf einer Razor Page wird die Seite erneut mit einer Fehlermeldung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="82b9a-212">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="82b9a-213">Die clientseitige Validierung fängt die meisten ungültigen Daten ab, die andernfalls an ein Razor Pages-Formular übermittelt würden.</span><span class="sxs-lookup"><span data-stu-id="82b9a-213">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="82b9a-214">Diese Validierung erschwert es, den voranstehenden, hervorgehobenen Code auszulösen.</span><span class="sxs-lookup"><span data-stu-id="82b9a-214">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="82b9a-215">Die Beispiel-App umfasst eine Schaltfläche **Submit with Invalid Date** (Mit ungültigem Datum absenden), die ungültige Daten in das Feld **Hire Date** (Einstellungsdatum) einfügt und das Formular absendet.</span><span class="sxs-lookup"><span data-stu-id="82b9a-215">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="82b9a-216">Diese Schaltfläche zeigt, wie der Code zum erneuten Anzeigen der Seite funktioniert, wenn Datenkonvertierungsfehler auftreten.</span><span class="sxs-lookup"><span data-stu-id="82b9a-216">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="82b9a-217">Wenn die Seite von dem vorangehenden Code erneut angezeigt wird, wird die ungültige Eingabe nicht im Formularfeld angezeigt.</span><span class="sxs-lookup"><span data-stu-id="82b9a-217">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="82b9a-218">Dies liegt daran, dass die Modelleigenschaft auf „null“ oder einen Standardwert festgelegt wurde.</span><span class="sxs-lookup"><span data-stu-id="82b9a-218">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="82b9a-219">Die ungültige Eingabe wird jedoch in einer Fehlermeldung angezeigt.</span><span class="sxs-lookup"><span data-stu-id="82b9a-219">The invalid input does appear in an error message.</span></span> <span data-ttu-id="82b9a-220">Wenn Sie aber die ungültigen Daten im Formularfeld erneut anzeigen möchten, sollten Sie aus der Modelleigenschaft eine Zeichenfolge machen und die Datenkonvertierung manuell ausführen.</span><span class="sxs-lookup"><span data-stu-id="82b9a-220">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="82b9a-221">Dieselbe Strategie empfiehlt sich, wenn Sie nicht möchten, dass Typkonvertierungsfehler zu Modellzustandsfehlern führen.</span><span class="sxs-lookup"><span data-stu-id="82b9a-221">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="82b9a-222">In diesem Fall machen Sie aus der Modelleigenschaft eine Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="82b9a-222">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="82b9a-223">Einfache Typen</span><span class="sxs-lookup"><span data-stu-id="82b9a-223">Simple types</span></span>

<span data-ttu-id="82b9a-224">Die einfachen Typen, in die die Modellbindung Quellzeichenfolgen konvertieren kann, sind unter anderem:</span><span class="sxs-lookup"><span data-stu-id="82b9a-224">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="82b9a-225">Boolean</span><span class="sxs-lookup"><span data-stu-id="82b9a-225">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="82b9a-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="82b9a-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="82b9a-227">Char</span><span class="sxs-lookup"><span data-stu-id="82b9a-227">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="82b9a-228">DateTime</span><span class="sxs-lookup"><span data-stu-id="82b9a-228">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="82b9a-229">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="82b9a-229">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="82b9a-230">Decimal</span><span class="sxs-lookup"><span data-stu-id="82b9a-230">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="82b9a-231">Double</span><span class="sxs-lookup"><span data-stu-id="82b9a-231">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="82b9a-232">Enum</span><span class="sxs-lookup"><span data-stu-id="82b9a-232">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="82b9a-233">Guid</span><span class="sxs-lookup"><span data-stu-id="82b9a-233">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="82b9a-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="82b9a-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="82b9a-235">Single</span><span class="sxs-lookup"><span data-stu-id="82b9a-235">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="82b9a-236">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="82b9a-236">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="82b9a-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="82b9a-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="82b9a-238">Uri</span><span class="sxs-lookup"><span data-stu-id="82b9a-238">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="82b9a-239">Version</span><span class="sxs-lookup"><span data-stu-id="82b9a-239">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="82b9a-240">Komplexe Typen</span><span class="sxs-lookup"><span data-stu-id="82b9a-240">Complex types</span></span>

<span data-ttu-id="82b9a-241">Ein komplexer Typ muss einen öffentlichen Standardkonstruktor und öffentliche schreibbare Eigenschaften besitzen, die gebunden werden können.</span><span class="sxs-lookup"><span data-stu-id="82b9a-241">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="82b9a-242">Wenn die Modellbindung erfolgt, wird die Klasse mit dem öffentlichen Standardkonstruktor instanziiert.</span><span class="sxs-lookup"><span data-stu-id="82b9a-242">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="82b9a-243">Für jede Eigenschaft des komplexen Typs durchsucht die Modellbindung die Quellen für das Namensmuster *prefix.property_name*.</span><span class="sxs-lookup"><span data-stu-id="82b9a-243">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="82b9a-244">Wenn nichts gefunden wird, sucht sie nur nach *property_name* ohne das Präfix.</span><span class="sxs-lookup"><span data-stu-id="82b9a-244">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="82b9a-245">Beim Binden an einen Parameter ist das Präfix der Name des Parameters.</span><span class="sxs-lookup"><span data-stu-id="82b9a-245">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="82b9a-246">Beim Binden an eine öffentliche Eigenschaft `PageModel` ist das Präfix der Name der öffentlichen Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="82b9a-246">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="82b9a-247">Einige Attribute besitzen eine Eigenschaft `Prefix`, die es Ihnen gestattet, die Standardverwendung des Parameter- oder Eigenschaftennamens außer Kraft zu setzen.</span><span class="sxs-lookup"><span data-stu-id="82b9a-247">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="82b9a-248">Nehmen Sie beispielsweise an, der komplexe Typ ist die folgende `Instructor`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="82b9a-248">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="82b9a-249">Präfix = Parametername</span><span class="sxs-lookup"><span data-stu-id="82b9a-249">Prefix = parameter name</span></span>

<span data-ttu-id="82b9a-250">Wenn das zu bindende Modell ein Parameter namens `instructorToUpdate` ist:</span><span class="sxs-lookup"><span data-stu-id="82b9a-250">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="82b9a-251">Die Modellbindung beginnt, indem sie die Quellen nach dem Schlüssel `instructorToUpdate.ID` durchsucht.</span><span class="sxs-lookup"><span data-stu-id="82b9a-251">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="82b9a-252">Wenn dieser nicht gefunden wird, sucht sie nach `ID` ohne Präfix.</span><span class="sxs-lookup"><span data-stu-id="82b9a-252">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="82b9a-253">Präfix = Name der Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="82b9a-253">Prefix = property name</span></span>

<span data-ttu-id="82b9a-254">Wenn das zu bindende Modell eine Eigenschaft des Controllers oder der `PageModel`-Klasse namens `Instructor` ist:</span><span class="sxs-lookup"><span data-stu-id="82b9a-254">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="82b9a-255">Die Modellbindung beginnt, indem sie die Quellen nach dem Schlüssel `Instructor.ID` durchsucht.</span><span class="sxs-lookup"><span data-stu-id="82b9a-255">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="82b9a-256">Wenn dieser nicht gefunden wird, sucht sie nach `ID` ohne Präfix.</span><span class="sxs-lookup"><span data-stu-id="82b9a-256">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="82b9a-257">Benutzerdefiniertes Präfix</span><span class="sxs-lookup"><span data-stu-id="82b9a-257">Custom prefix</span></span>

<span data-ttu-id="82b9a-258">Wenn das zu bindende Modell ein Parameter namens `instructorToUpdate` ist, und ein `Bind`-Attribut `Instructor` als Präfix angibt:</span><span class="sxs-lookup"><span data-stu-id="82b9a-258">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="82b9a-259">Die Modellbindung beginnt, indem sie die Quellen nach dem Schlüssel `Instructor.ID` durchsucht.</span><span class="sxs-lookup"><span data-stu-id="82b9a-259">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="82b9a-260">Wenn dieser nicht gefunden wird, sucht sie nach `ID` ohne Präfix.</span><span class="sxs-lookup"><span data-stu-id="82b9a-260">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="82b9a-261">Attribute für Ziele komplexen Typs</span><span class="sxs-lookup"><span data-stu-id="82b9a-261">Attributes for complex type targets</span></span>

<span data-ttu-id="82b9a-262">Mehrere integrierte Attribute stehen für die Kontrolle der Modellbindung komplexer Typen zur Verfügung:</span><span class="sxs-lookup"><span data-stu-id="82b9a-262">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="82b9a-263">Diese Attribute wirken sich auf die Modellbindung aus, wenn bereitgestellte Formulardaten die Quelle der Wert sind.</span><span class="sxs-lookup"><span data-stu-id="82b9a-263">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="82b9a-264">Sie haben keine Auswirkungen auf Eingabeformatierer, die bereitgestellte JSON- und XML-Anforderungstexte verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="82b9a-264">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="82b9a-265">Eingabeformatierer werden [später in diesem Artikel](#input-formatters) erklärt.</span><span class="sxs-lookup"><span data-stu-id="82b9a-265">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="82b9a-266">Lesen Sie auch die Diskussion des `[Required]`-Attributs in der [Modellvalidierung](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="82b9a-266">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="82b9a-267">[BindRequired]-Attribut</span><span class="sxs-lookup"><span data-stu-id="82b9a-267">[BindRequired] attribute</span></span>

<span data-ttu-id="82b9a-268">Kann nur auf Modelleigenschaften angewendet werden, nicht auf Methodenparameter.</span><span class="sxs-lookup"><span data-stu-id="82b9a-268">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="82b9a-269">Bewirkt, dass die Modellbindung einen Modellzustandsfehler hinzufügt, wenn die Bindung für die Eigenschaft eines Modells nicht erfolgen kann.</span><span class="sxs-lookup"><span data-stu-id="82b9a-269">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="82b9a-270">Im Folgenden ein Beispiel:</span><span class="sxs-lookup"><span data-stu-id="82b9a-270">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="82b9a-271">[BindNever]-Attribut</span><span class="sxs-lookup"><span data-stu-id="82b9a-271">[BindNever] attribute</span></span>

<span data-ttu-id="82b9a-272">Kann nur auf Modelleigenschaften angewendet werden, nicht auf Methodenparameter.</span><span class="sxs-lookup"><span data-stu-id="82b9a-272">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="82b9a-273">Verhindert, dass die Modellbindung die Eigenschaft eines Modells festlegt.</span><span class="sxs-lookup"><span data-stu-id="82b9a-273">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="82b9a-274">Im Folgenden ein Beispiel:</span><span class="sxs-lookup"><span data-stu-id="82b9a-274">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="82b9a-275">[Bind]-Attribut</span><span class="sxs-lookup"><span data-stu-id="82b9a-275">[Bind] attribute</span></span>

<span data-ttu-id="82b9a-276">Kann auf eine Klasse oder einen Methodenparameter angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="82b9a-276">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="82b9a-277">Gibt an, welche Eigenschaften eines Modells in die Modellbindung aufgenommen werden sollen.</span><span class="sxs-lookup"><span data-stu-id="82b9a-277">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="82b9a-278">Im folgenden Beispiel werden nur die angegebenen Eigenschaften des `Instructor`-Modells gebunden, wenn ein Ereignishandler oder eine Aktionsmethode aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="82b9a-278">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="82b9a-279">Im folgenden Beispiel werden nur die angegebenen Eigenschaften des `Instructor`-Modells gebunden, wenn die `OnPost`-Methode aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="82b9a-279">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="82b9a-280">Das `[Bind]`-Attribut kann zum Schutz vor Overposting in *Erstellungs*szenarien (create) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="82b9a-280">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="82b9a-281">Es funktioniert nicht gut in Bearbeitungsszenarien (edit), weil ausgeschlossene Eigenschaften auf „null“ oder einen Standardwert festgelegt werden, anstatt unverändert zu bleiben.</span><span class="sxs-lookup"><span data-stu-id="82b9a-281">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="82b9a-282">Zum Schutz vor Overposting werden Ansichtsmodelle empfohlen, anstelle des `[Bind]`-Attributs.</span><span class="sxs-lookup"><span data-stu-id="82b9a-282">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="82b9a-283">Weitere Informationen finden Sie unter [Sicherheitshinweis zum Overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="82b9a-283">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="82b9a-284">Auflistungen</span><span class="sxs-lookup"><span data-stu-id="82b9a-284">Collections</span></span>

<span data-ttu-id="82b9a-285">Bei Zielen, die Sammlungen einfacher Typen sind, sucht die Modellbindung nach Übereinstimmungen mit *parameter_name* oder *property_name*.</span><span class="sxs-lookup"><span data-stu-id="82b9a-285">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="82b9a-286">Wird keine Übereinstimmung gefunden, sucht sie nach einem der unterstützten Formate ohne Präfix.</span><span class="sxs-lookup"><span data-stu-id="82b9a-286">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="82b9a-287">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="82b9a-287">For example:</span></span>

* <span data-ttu-id="82b9a-288">Angenommen, der zu bindende Parameter ist ein Array namens `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="82b9a-288">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="82b9a-289">Formular- oder Abfragezeichenfolgendaten können eins der folgenden Formate haben:</span><span class="sxs-lookup"><span data-stu-id="82b9a-289">Form or query string data can be in one of the following formats:</span></span>
   
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

* <span data-ttu-id="82b9a-290">Das folgende Format wird nur für Formulardaten unterstützt:</span><span class="sxs-lookup"><span data-stu-id="82b9a-290">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="82b9a-291">Bei allen der vorangehenden Beispielformate übergibt die Modellbindung ein Array von zwei Elementen an den `selectedCourses`-Parameter:</span><span class="sxs-lookup"><span data-stu-id="82b9a-291">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="82b9a-292">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="82b9a-292">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="82b9a-293">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="82b9a-293">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="82b9a-294">Datenformate, die Indexnummern verwenden(... [0]... [1] ...), müssen sicherstellen, dass sie fortlaufend nummeriert sind, beginnend mit 0 (null).</span><span class="sxs-lookup"><span data-stu-id="82b9a-294">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="82b9a-295">Treten bei der Indexnummerierung Lücken auf, werden alle Elemente, die auf die Lücke folgen, ignoriert.</span><span class="sxs-lookup"><span data-stu-id="82b9a-295">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="82b9a-296">Wenn die Indizes beispielsweise 0 und 2 anstelle von 0 und 1 sind, wird beispielsweise das zweite Element ignoriert.</span><span class="sxs-lookup"><span data-stu-id="82b9a-296">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="82b9a-297">Wörterbücher</span><span class="sxs-lookup"><span data-stu-id="82b9a-297">Dictionaries</span></span>

<span data-ttu-id="82b9a-298">Bei `Dictionary`-Zielen sucht die Modellbindung nach Übereinstimmungen mit *parameter_name* oder *property_name*.</span><span class="sxs-lookup"><span data-stu-id="82b9a-298">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="82b9a-299">Wird keine Übereinstimmung gefunden, sucht sie nach einem der unterstützten Formate ohne Präfix.</span><span class="sxs-lookup"><span data-stu-id="82b9a-299">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="82b9a-300">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="82b9a-300">For example:</span></span>

* <span data-ttu-id="82b9a-301">Angenommen, der Zielparameter ist eine `Dictionary<int, string>` mit dem Namen `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="82b9a-301">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="82b9a-302">Die bereitgestellten Formular- oder Abfragezeichenfolgendaten können wie eins der folgenden Beispiele aussehen:</span><span class="sxs-lookup"><span data-stu-id="82b9a-302">The posted form or query string data can look like one of the following examples:</span></span>

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

* <span data-ttu-id="82b9a-303">Bei allen der vorangehenden Beispielformate übergibt die Modellbindung ein Wörterbuch aus zwei Elementen an den `selectedCourses`-Parameter:</span><span class="sxs-lookup"><span data-stu-id="82b9a-303">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="82b9a-304">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="82b9a-304">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="82b9a-305">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="82b9a-305">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="82b9a-306">Globalisierungsverhalten der Routendaten und Abfragezeichenfolgen für die Modellbindung</span><span class="sxs-lookup"><span data-stu-id="82b9a-306">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="82b9a-307">Der ASP.NET Core-Routenwertanbieter und der Abfragezeichenfolgenwert-Anbieter:</span><span class="sxs-lookup"><span data-stu-id="82b9a-307">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="82b9a-308">behandeln Werte als invariante Kulturen.</span><span class="sxs-lookup"><span data-stu-id="82b9a-308">Treat values as invariant culture.</span></span>
* <span data-ttu-id="82b9a-309">erwarten, dass URLs kulturinvariant sind.</span><span class="sxs-lookup"><span data-stu-id="82b9a-309">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="82b9a-310">Im Gegensatz dazu durchlaufen Werte, die aus Formulardaten stammen, eine kulturabhängige Konvertierung.</span><span class="sxs-lookup"><span data-stu-id="82b9a-310">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="82b9a-311">Dies ist beabsichtigt, damit URLs zwischen Gebietsschemas freigegeben werden können.</span><span class="sxs-lookup"><span data-stu-id="82b9a-311">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="82b9a-312">So lassen Sie den ASP.NET Core-Routenwertanbieter und den Abfragezeichenfolgenwert-Anbieter eine kulturabhängige Konvertierung durchlaufen:</span><span class="sxs-lookup"><span data-stu-id="82b9a-312">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="82b9a-313">Sie erben von <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory>.</span><span class="sxs-lookup"><span data-stu-id="82b9a-313">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="82b9a-314">Kopieren Sie den Code aus [QueryStringValueProviderFactory](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) oder [RouteValueValueProviderFactory](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs).</span><span class="sxs-lookup"><span data-stu-id="82b9a-314">Copy the code from [QueryStringValueProviderFactory](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="82b9a-315">Ersetzen Sie den an den Wertanbieterkonstruktor übergebenen [Culture-Wert](https://github.com/aspnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) durch [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture).</span><span class="sxs-lookup"><span data-stu-id="82b9a-315">Replace the [culture value](https://github.com/aspnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="82b9a-316">Ersetzen Sie die Standardwertanbieter-Zuordnungsinstanz in den MVC-Optionen durch Ihre neue:</span><span class="sxs-lookup"><span data-stu-id="82b9a-316">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="82b9a-317">Spezielle Datentypen</span><span class="sxs-lookup"><span data-stu-id="82b9a-317">Special data types</span></span>

<span data-ttu-id="82b9a-318">Es gibt einige spezielle Datentypen, die die Modellbindung verarbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="82b9a-318">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="82b9a-319">„IFormFile“ und „IFormFileCollection“</span><span class="sxs-lookup"><span data-stu-id="82b9a-319">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="82b9a-320">Eine in der HTTP-Anforderung enthaltene, hochgeladenen Datei.</span><span class="sxs-lookup"><span data-stu-id="82b9a-320">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="82b9a-321">Außerdem wird `IEnumerable<IFormFile>` für mehrere Dateien unterstützt.</span><span class="sxs-lookup"><span data-stu-id="82b9a-321">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="82b9a-322">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="82b9a-322">CancellationToken</span></span>

<span data-ttu-id="82b9a-323">Wird verwendet, um die Aktivität in asynchronen Controllern zu beenden.</span><span class="sxs-lookup"><span data-stu-id="82b9a-323">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="82b9a-324">„FormCollection“</span><span class="sxs-lookup"><span data-stu-id="82b9a-324">FormCollection</span></span>

<span data-ttu-id="82b9a-325">Wird verwendet, um alle Werte aus bereitgestellten Formulardaten abzurufen.</span><span class="sxs-lookup"><span data-stu-id="82b9a-325">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="82b9a-326">Eingabeformatierer</span><span class="sxs-lookup"><span data-stu-id="82b9a-326">Input formatters</span></span>

<span data-ttu-id="82b9a-327">Daten im Anforderungstext können im JSON-, XML- oder einem anderen Format sein.</span><span class="sxs-lookup"><span data-stu-id="82b9a-327">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="82b9a-328">Um diese Daten zu analysieren, verwendet die Modellbindung einen *Eingabeformatierer*, der für die Verarbeitung eines bestimmten Inhaltstyps konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="82b9a-328">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="82b9a-329">ASP.NET Core enthält standardmäßig JSON-basierte Eingabeformatierer für die Verarbeitung von JSON-Daten.</span><span class="sxs-lookup"><span data-stu-id="82b9a-329">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="82b9a-330">Sie können andere Formatierer für andere Inhaltstypen hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="82b9a-330">You can add other formatters for other content types.</span></span>

<span data-ttu-id="82b9a-331">ASP.NET Core wählt Eingabeformatierer auf Grundlage des [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute)-Attributs aus.</span><span class="sxs-lookup"><span data-stu-id="82b9a-331">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="82b9a-332">Wenn kein Attribut vorhanden ist, verwendet es den [Content-Type-Header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="82b9a-332">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="82b9a-333">So verwenden Sie die integrierte XML-Eingabeformatierer</span><span class="sxs-lookup"><span data-stu-id="82b9a-333">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="82b9a-334">Installieren Sie das NuGet-Paket `Microsoft.AspNetCore.Mvc.Formatters.Xml`.</span><span class="sxs-lookup"><span data-stu-id="82b9a-334">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="82b9a-335">In `Startup.ConfigureServices`, rufen Sie <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> oder <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*> auf.</span><span class="sxs-lookup"><span data-stu-id="82b9a-335">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="82b9a-336">Wenden Sie das `Consumes`-Attribut auf Controllerklassen oder Aktionsmethoden an, die XML im Anforderungstext erwarten sollten.</span><span class="sxs-lookup"><span data-stu-id="82b9a-336">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="82b9a-337">Weitere Informationen finden Sie unter [Einführung der XML-Serialisierung](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="82b9a-337">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="82b9a-338">Ausschließen angegebener Typen aus der Modellbindung</span><span class="sxs-lookup"><span data-stu-id="82b9a-338">Exclude specified types from model binding</span></span>

<span data-ttu-id="82b9a-339">Das Verhalten der Modellbindung und des Validierungssystems wird von der Klasse [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata) gesteuert.</span><span class="sxs-lookup"><span data-stu-id="82b9a-339">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="82b9a-340">Sie können `ModelMetadata` anpassen, indem Sie [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders) einen Detailanbieter hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="82b9a-340">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="82b9a-341">Integrierte Detailanbieter sind verfügbar, um die Modellbindung oder Validierung für angegebene Typen zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="82b9a-341">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="82b9a-342">Um die Modellbindung für alle Modelle eines angegebenen Typs zu deaktivieren, fügen Sie in `Startup.ConfigureServices` einen <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> hinzu.</span><span class="sxs-lookup"><span data-stu-id="82b9a-342">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="82b9a-343">Beispielsweise können Sie die Modellbindung für alle Modelle vom Typ `System.Version` wie folgt deaktivieren:</span><span class="sxs-lookup"><span data-stu-id="82b9a-343">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="82b9a-344">Um die Validierung für Eigenschaften eines angegebenen Typs zu deaktivieren, fügen Sie in `Startup.ConfigureServices` einen <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> hinzu.</span><span class="sxs-lookup"><span data-stu-id="82b9a-344">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="82b9a-345">Beispielsweise können Sie die Überprüfung von Eigenschaften vom Typ `System.Guid` wie folgt deaktivieren:</span><span class="sxs-lookup"><span data-stu-id="82b9a-345">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="82b9a-346">Benutzerdefinierte Modellbindungen</span><span class="sxs-lookup"><span data-stu-id="82b9a-346">Custom model binders</span></span>

<span data-ttu-id="82b9a-347">Sie können die Modellbindung erweitern, indem Sie eine benutzerdefinierte Modellbindung schreiben und das `[ModelBinder]`-Attribut verwenden, um diese für ein bestimmtes Ziel auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="82b9a-347">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="82b9a-348">Erfahren Sie mehr über die [benutzerdefinierte Modellbindung](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="82b9a-348">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="82b9a-349">Manuelle Modellbindung</span><span class="sxs-lookup"><span data-stu-id="82b9a-349">Manual model binding</span></span>

<span data-ttu-id="82b9a-350">Die Modellbindung kann mithilfe der <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>-Methode manuell aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="82b9a-350">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="82b9a-351">Die Methode ist für die beiden Klassen `ControllerBase` und `PageModel` definiert.</span><span class="sxs-lookup"><span data-stu-id="82b9a-351">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="82b9a-352">Mithilfe von Methodenüberladungen können Sie das Präfix und den Wertanbieter festlegen, die verwendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="82b9a-352">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="82b9a-353">Die Methode gibt `false` zurück, wenn die Modellbindung fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="82b9a-353">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="82b9a-354">Im Folgenden ein Beispiel:</span><span class="sxs-lookup"><span data-stu-id="82b9a-354">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="82b9a-355">[FromServices]-Attribut</span><span class="sxs-lookup"><span data-stu-id="82b9a-355">[FromServices] attribute</span></span>

<span data-ttu-id="82b9a-356">Der Name dieses Attributs folgt dem Muster von Modellbindungsattributen, die eine Datenquelle angeben.</span><span class="sxs-lookup"><span data-stu-id="82b9a-356">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="82b9a-357">Es ist aber nicht zum Binden von Daten aus einem Wertanbieter gedacht.</span><span class="sxs-lookup"><span data-stu-id="82b9a-357">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="82b9a-358">Es ruft eine Instanz eines Typs aus dem [Dependency Injection](xref:fundamentals/dependency-injection)-Container (Abhängigkeitsinjektion) ab.</span><span class="sxs-lookup"><span data-stu-id="82b9a-358">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="82b9a-359">Sein Zweck besteht darin, eine Alternative zur „Constructor Injection“ (Konstruktorinjektion) bereitzustellen, wenn Sie einen Dienst nur dann benötigen, wenn eine bestimmte Methode aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="82b9a-359">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="82b9a-360">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="82b9a-360">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="82b9a-361">In diesem Artikel wird erläutert, was Modellbindung ist, wie sie funktioniert, und wie Sie ihr Verhalten anpassen können.</span><span class="sxs-lookup"><span data-stu-id="82b9a-361">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="82b9a-362">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="82b9a-362">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="82b9a-363">Was ist Modellbindung?</span><span class="sxs-lookup"><span data-stu-id="82b9a-363">What is Model binding</span></span>

<span data-ttu-id="82b9a-364">Controller und Razor Pages arbeiten mit Daten, die aus HTTP-Anforderungen stammen.</span><span class="sxs-lookup"><span data-stu-id="82b9a-364">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="82b9a-365">Routendaten können beispielsweise einen Datensatzschlüssel enthalten, und bereitgestellte Formularfelder können Werte für die Eigenschaften des Modells bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="82b9a-365">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="82b9a-366">Das Schreiben von Code zum Abrufen jedes dieser Werte und deren Konvertierung aus Zeichenfolgen in .NET-Datentypen wäre mühsam und fehleranfällig.</span><span class="sxs-lookup"><span data-stu-id="82b9a-366">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="82b9a-367">Modellbindung automatisiert diesen Vorgang.</span><span class="sxs-lookup"><span data-stu-id="82b9a-367">Model binding automates this process.</span></span> <span data-ttu-id="82b9a-368">Das Modellbindungssystem:</span><span class="sxs-lookup"><span data-stu-id="82b9a-368">The model binding system:</span></span>

* <span data-ttu-id="82b9a-369">Ruft Daten aus verschiedenen Quellen ab, z. B. Routendaten, Formularfelder und Abfragezeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="82b9a-369">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="82b9a-370">Stellt diese Daten Controllern und Razor Pages in Methodenparametern und öffentlichen Eigenschaften bereit.</span><span class="sxs-lookup"><span data-stu-id="82b9a-370">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="82b9a-371">Konvertiert Zeichenfolgendaten in .NET-Typen.</span><span class="sxs-lookup"><span data-stu-id="82b9a-371">Converts string data to .NET types.</span></span>
* <span data-ttu-id="82b9a-372">Aktualisiert Eigenschaften komplexer Typen.</span><span class="sxs-lookup"><span data-stu-id="82b9a-372">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="82b9a-373">Beispiel</span><span class="sxs-lookup"><span data-stu-id="82b9a-373">Example</span></span>

<span data-ttu-id="82b9a-374">Angenommen Sie haben die folgende Aktionsmethode:</span><span class="sxs-lookup"><span data-stu-id="82b9a-374">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="82b9a-375">Und die App empfängt eine Anforderung mit dieser URL:</span><span class="sxs-lookup"><span data-stu-id="82b9a-375">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="82b9a-376">Die Modellbindung durchläuft die folgenden Schritte, nachdem das Routingsystem die Aktionsmethode ausgewählt hat:</span><span class="sxs-lookup"><span data-stu-id="82b9a-376">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="82b9a-377">Findet den ersten Parameters von `GetByID`, eine ganze Zahl namens `id`.</span><span class="sxs-lookup"><span data-stu-id="82b9a-377">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="82b9a-378">Durchsucht die verfügbaren Quellen in der HTTP-Anforderung und findet `id` = „2“ in den Routendaten.</span><span class="sxs-lookup"><span data-stu-id="82b9a-378">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="82b9a-379">Konvertiert der Zeichenfolge „2“ in die ganze Zahl 2.</span><span class="sxs-lookup"><span data-stu-id="82b9a-379">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="82b9a-380">Findet den nächsten Parameter von `GetByID`, einen booleschen Wert namens `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="82b9a-380">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="82b9a-381">Durchsucht die Quellen und findet „DogsOnly=True“ in der Abfragezeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="82b9a-381">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="82b9a-382">Beim Abgleich von Namen wird die Groß- und Kleinschreibung nicht berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="82b9a-382">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="82b9a-383">Konvertiert die Zeichenfolge „true“ in den booleschen Wert `true`.</span><span class="sxs-lookup"><span data-stu-id="82b9a-383">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="82b9a-384">Das Framework ruft dann die `GetById`-Methode auf, und übergibt dabei als Eingabe „2“ für den `id`-Parameter und `true` für den `dogsOnly`-Parameter.</span><span class="sxs-lookup"><span data-stu-id="82b9a-384">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="82b9a-385">Im vorherigen Beispiel sind die Ziele der Modellbindung Methodenparameter, die einfache Typen sind.</span><span class="sxs-lookup"><span data-stu-id="82b9a-385">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="82b9a-386">Ziele können aber auch die Eigenschaften eines komplexen Typs sein.</span><span class="sxs-lookup"><span data-stu-id="82b9a-386">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="82b9a-387">Nachdem jede Eigenschaft erfolgreich gebunden wurde, erfolgt die [Modellvalidierung](xref:mvc/models/validation) für diese Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="82b9a-387">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="82b9a-388">Der Datensatz darüber, welche Daten an das Modell gebunden sind, sowie mit allen Bindungs- oder Validierungsfehlern wird in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) oder [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) gespeichert.</span><span class="sxs-lookup"><span data-stu-id="82b9a-388">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="82b9a-389">Um herauszufinden, ob dieser Vorgang erfolgreich war, überprüft die App das Flag [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid).</span><span class="sxs-lookup"><span data-stu-id="82b9a-389">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="82b9a-390">Ziele</span><span class="sxs-lookup"><span data-stu-id="82b9a-390">Targets</span></span>

<span data-ttu-id="82b9a-391">Die Modellbindung versucht, Werte für die folgenden Arten von Zielen zu finden:</span><span class="sxs-lookup"><span data-stu-id="82b9a-391">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="82b9a-392">Parameter der Controlleraktionsmethode, zu der eine Anforderung weitergeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="82b9a-392">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="82b9a-393">Parameter der Razor Pages-Handlermethode, zu der eine Anforderung weitergeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="82b9a-393">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="82b9a-394">Öffentliche Eigenschaften eines Controllers oder einer `PageModel`-Klasse, falls durch Attribute angegeben.</span><span class="sxs-lookup"><span data-stu-id="82b9a-394">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="82b9a-395">[BindProperty]-Attribut</span><span class="sxs-lookup"><span data-stu-id="82b9a-395">[BindProperty] attribute</span></span>

<span data-ttu-id="82b9a-396">Kann auf eine öffentliche Eigenschaft eines Controllers oder einer `PageModel`-Klasse angewendet werden, um die Modellbindung anzuweisen, diese Eigenschaft als Ziel zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="82b9a-396">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="82b9a-397">[BindProperties]-Attribut</span><span class="sxs-lookup"><span data-stu-id="82b9a-397">[BindProperties] attribute</span></span>

<span data-ttu-id="82b9a-398">Verfügbar in ASP.NET Core 2.1 und höher.</span><span class="sxs-lookup"><span data-stu-id="82b9a-398">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="82b9a-399">Kann auf einen Controller oder eine `PageModel`-Klasse angewendet werden, um die Modellbindung anzuweisen, alle öffentlichen Eigenschaften dieser Klasse als Ziel zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="82b9a-399">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="82b9a-400">Modellbindung für HTTP-GET-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="82b9a-400">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="82b9a-401">Standardmäßig sind Eigenschaften für HTTP GET-Anforderungen nicht gebunden.</span><span class="sxs-lookup"><span data-stu-id="82b9a-401">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="82b9a-402">In der Regel ist alles, was Sie für eine GET-Anforderung benötigen, ein Datensatz-ID-Parameter.</span><span class="sxs-lookup"><span data-stu-id="82b9a-402">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="82b9a-403">Die Datensatz-ID wird verwendet, um das Element in der Datenbank zu suchen.</span><span class="sxs-lookup"><span data-stu-id="82b9a-403">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="82b9a-404">Daher besteht keine Notwendigkeit, eine Eigenschaft zu binden, die eine Instanz des Modells enthält.</span><span class="sxs-lookup"><span data-stu-id="82b9a-404">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="82b9a-405">In Szenarien, in denen Sie Eigenschaften an Daten aus GET-Anforderungen binden möchten, legen Sie die Eigenschaft `SupportsGet` auf `true` fest:</span><span class="sxs-lookup"><span data-stu-id="82b9a-405">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="82b9a-406">Quellen</span><span class="sxs-lookup"><span data-stu-id="82b9a-406">Sources</span></span>

<span data-ttu-id="82b9a-407">Standardmäßig ruft die Modellbindung Daten in Form von Schlüssel-Wert-Paaren aus den folgenden Quellen in einer HTTP-Anforderung ab:</span><span class="sxs-lookup"><span data-stu-id="82b9a-407">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="82b9a-408">Formularfelder</span><span class="sxs-lookup"><span data-stu-id="82b9a-408">Form fields</span></span>
1. <span data-ttu-id="82b9a-409">Der Anforderungstext (für [Controller mit dem [ApiController]-Attribut](xref:web-api/index#binding-source-parameter-inference))</span><span class="sxs-lookup"><span data-stu-id="82b9a-409">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="82b9a-410">Routendaten</span><span class="sxs-lookup"><span data-stu-id="82b9a-410">Route data</span></span>
1. <span data-ttu-id="82b9a-411">Abfragezeichenfolgeparameter</span><span class="sxs-lookup"><span data-stu-id="82b9a-411">Query string parameters</span></span>
1. <span data-ttu-id="82b9a-412">Hochgeladene Dateien</span><span class="sxs-lookup"><span data-stu-id="82b9a-412">Uploaded files</span></span>

<span data-ttu-id="82b9a-413">Für jeden Zielparameter oder jede Zieleigenschaft werden die Quellen nach der oben aufgeführten Reihenfolge überprüft.</span><span class="sxs-lookup"><span data-stu-id="82b9a-413">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="82b9a-414">Es gibt ein paar Ausnahmen:</span><span class="sxs-lookup"><span data-stu-id="82b9a-414">There are a few exceptions:</span></span>

* <span data-ttu-id="82b9a-415">Routendaten und Abfragezeichenfolgenwerte werden nur für einfache Typen verwendet.</span><span class="sxs-lookup"><span data-stu-id="82b9a-415">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="82b9a-416">Hochgeladene Dateien werden nur an Zieltypen gebunden, die `IFormFile` oder `IEnumerable<IFormFile>` implementieren.</span><span class="sxs-lookup"><span data-stu-id="82b9a-416">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="82b9a-417">Wenn die Standardquelle nicht richtig ist, verwenden Sie eines der folgenden Attribute zum Festlegen der Quelle:</span><span class="sxs-lookup"><span data-stu-id="82b9a-417">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="82b9a-418">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute): Ruft Werte aus der Abfragezeichenfolge ab.</span><span class="sxs-lookup"><span data-stu-id="82b9a-418">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="82b9a-419">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute): Ruft Werte aus Routendaten ab.</span><span class="sxs-lookup"><span data-stu-id="82b9a-419">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="82b9a-420">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute): Ruft Werte aus bereitgestellten Formularfeldern ab.</span><span class="sxs-lookup"><span data-stu-id="82b9a-420">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="82b9a-421">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute): Ruft Werte aus dem Anforderungstext ab.</span><span class="sxs-lookup"><span data-stu-id="82b9a-421">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="82b9a-422">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute): Ruft Werte aus HTTP-Headern ab.</span><span class="sxs-lookup"><span data-stu-id="82b9a-422">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="82b9a-423">Diese Attribute:</span><span class="sxs-lookup"><span data-stu-id="82b9a-423">These attributes:</span></span>

* <span data-ttu-id="82b9a-424">Werden Modelleigenschaften einzeln hinzugefügt (nicht zur Modellklasse), wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="82b9a-424">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="82b9a-425">Akzeptieren optional einen Modellnamenswert im Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="82b9a-425">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="82b9a-426">Diese Option wird für den Fall bereitgestellt, dass der Eigenschaftenname nicht mit dem Wert in der Anforderung übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="82b9a-426">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="82b9a-427">Beispielsweise könnte der Wert in der Anforderung ein Header mit einem Bindestrich in seinem Namen sein, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="82b9a-427">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="82b9a-428">[FromBody]-Attribut</span><span class="sxs-lookup"><span data-stu-id="82b9a-428">[FromBody] attribute</span></span>

<span data-ttu-id="82b9a-429">Wenden Sie das `[FromBody]`-Attribut auf einen Parameter an, um dessen Eigenschaften über den Text einer HTTP-Anforderung aufzufüllen.</span><span class="sxs-lookup"><span data-stu-id="82b9a-429">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="82b9a-430">Die ASP.NET Core-Runtime delegiert die Verantwortung, für das Lesen des Texts an einen Eingabeformatierer.</span><span class="sxs-lookup"><span data-stu-id="82b9a-430">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="82b9a-431">Eingabeformatierer werden [später in diesem Artikel](#input-formatters) erklärt.</span><span class="sxs-lookup"><span data-stu-id="82b9a-431">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="82b9a-432">Wenn `[FromBody]` auf einen komplexen Typparameter angewendet wird, werden alle Bindungsquellenattribute ignoriert, die auf die Eigenschaften angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="82b9a-432">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="82b9a-433">Die folgende `Create`-Aktion legt beispielsweise fest, dass der `pet`-Parameter mithilfe des Texts aufgefüllt wird:</span><span class="sxs-lookup"><span data-stu-id="82b9a-433">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="82b9a-434">Die `Pet`-Klasse legt fest, dass ihre `Breed`-Eigenschaft mithilfe eines Abfragezeichenfolgenparameters aufgefüllt wird:</span><span class="sxs-lookup"><span data-stu-id="82b9a-434">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="82b9a-435">Im vorherigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="82b9a-435">In the preceding example:</span></span>

* <span data-ttu-id="82b9a-436">Das `[FromQuery]`-Attribut wird ignoriert.</span><span class="sxs-lookup"><span data-stu-id="82b9a-436">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="82b9a-437">Die `Breed`-Eigenschaft wird nicht mithilfe eines Abfragezeichenfolgenparameters aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="82b9a-437">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="82b9a-438">Eingabeformatierer lesen nur den Text und verstehen Bindungsquellenattribute nicht.</span><span class="sxs-lookup"><span data-stu-id="82b9a-438">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="82b9a-439">Wenn ein geeigneter Wert im Text gefunden wird, wird dieser Wert zum Auffüllen der `Breed`-Eigenschaft verwendet.</span><span class="sxs-lookup"><span data-stu-id="82b9a-439">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="82b9a-440">Wenden Sie `[FromBody]` auf nicht mehr als einen Parameter pro Aktionsmethode an.</span><span class="sxs-lookup"><span data-stu-id="82b9a-440">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="82b9a-441">Sobald der Anforderungsdatenstrom von einem Eingabeformatierer gelesen wurde, ist er nicht mehr verfügbar, um für die Bindung anderer `[FromBody]`-Parameter nochmal gelesen zu werden.</span><span class="sxs-lookup"><span data-stu-id="82b9a-441">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="82b9a-442">Zusätzliche Quellen</span><span class="sxs-lookup"><span data-stu-id="82b9a-442">Additional sources</span></span>

<span data-ttu-id="82b9a-443">Quelldaten werden dem Modellbindungssystem durch *Wertanbieter* bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="82b9a-443">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="82b9a-444">Sie können benutzerdefinierte Wertanbieter schreiben und registrieren, die Daten für die Modellbindung aus anderen Quellen abrufen.</span><span class="sxs-lookup"><span data-stu-id="82b9a-444">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="82b9a-445">Beispielsweise können Sie Daten aus Cookies oder Sitzungszuständen abrufen.</span><span class="sxs-lookup"><span data-stu-id="82b9a-445">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="82b9a-446">So rufen Sie Daten aus einer neuen Quelle ab</span><span class="sxs-lookup"><span data-stu-id="82b9a-446">To get data from a new source:</span></span>

* <span data-ttu-id="82b9a-447">Erstellen Sie eine Klasse, die das `IValueProvider` implementiert.</span><span class="sxs-lookup"><span data-stu-id="82b9a-447">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="82b9a-448">Erstellen Sie eine Klasse, die das `IValueProviderFactory` implementiert.</span><span class="sxs-lookup"><span data-stu-id="82b9a-448">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="82b9a-449">Registrieren Sie die Factoryklasse in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="82b9a-449">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="82b9a-450">Die Beispiel-App umfasst ein Beispiel für einen [Wertanbieter](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) und eine [Factory](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs), das Werte aus Cookies abruft.</span><span class="sxs-lookup"><span data-stu-id="82b9a-450">The sample app includes a [value provider](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="82b9a-451">Dies ist der Registrierungscode in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="82b9a-451">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="82b9a-452">Der angezeigte Code fügt den benutzerdefinierten Wertanbieter hinter allen integrierten Wertanbietern ein.</span><span class="sxs-lookup"><span data-stu-id="82b9a-452">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="82b9a-453">Damit er der erste in der Liste wird, rufen Sie `Insert(0, new CookieValueProviderFactory())` anstelle von `Add` auf.</span><span class="sxs-lookup"><span data-stu-id="82b9a-453">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="82b9a-454">Keine Quelle für eine Modelleigenschaft</span><span class="sxs-lookup"><span data-stu-id="82b9a-454">No source for a model property</span></span>

<span data-ttu-id="82b9a-455">Standardmäßig wird kein Modellzustandsfehler erstellt, wenn kein Wert für eine Modelleigenschaft gefunden wird.</span><span class="sxs-lookup"><span data-stu-id="82b9a-455">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="82b9a-456">Die Eigenschaft wird auf „null“ oder einen Standardwert festgelegt:</span><span class="sxs-lookup"><span data-stu-id="82b9a-456">The property is set to null or a default value:</span></span>

* <span data-ttu-id="82b9a-457">Einfache Nullable-Typen werden auf `null` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="82b9a-457">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="82b9a-458">Nicht-Nullable-Werttypen werden auf `default(T)` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="82b9a-458">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="82b9a-459">Beispiel: Ein Parameter `int id` wird auf „0“ festgelegt.</span><span class="sxs-lookup"><span data-stu-id="82b9a-459">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="82b9a-460">Für komplexe Typen erstellt die Modellbindung eine Instanz, indem der Standardkonstruktor verwendet wird, ohne Eigenschaften festzulegen.</span><span class="sxs-lookup"><span data-stu-id="82b9a-460">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="82b9a-461">Arrays werden auf `Array.Empty<T>()` festgelegt, mit der Ausnahme, dass `byte[]`-Arrays auf `null` festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="82b9a-461">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="82b9a-462">Um den Modellzustand ungültig zu machen, wenn in Formularfeldern für eine Modelleigenschaft nichts gefunden wird, verwenden Sie das [`[BindRequired]`](#bindrequired-attribute)-Attribut.</span><span class="sxs-lookup"><span data-stu-id="82b9a-462">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="82b9a-463">Beachten Sie, dass dieses `[BindRequired]`-Verhalten für die Modellbindung von Daten aus bereitgestellten Formulardaten gilt, nicht für JSON- oder XML-Daten in einem Anforderungstext.</span><span class="sxs-lookup"><span data-stu-id="82b9a-463">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="82b9a-464">Anforderungstextdaten werden von [Eingabeformatierern](#input-formatters) verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="82b9a-464">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="82b9a-465">Typkonvertierungsfehler</span><span class="sxs-lookup"><span data-stu-id="82b9a-465">Type conversion errors</span></span>

<span data-ttu-id="82b9a-466">Wenn eine Quelle gefunden wird, aber nicht in den Zieltyp konvertiert werden kann, wird der Modellzustand als „ungültig“ gekennzeichnet.</span><span class="sxs-lookup"><span data-stu-id="82b9a-466">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="82b9a-467">Der Zielparameter oder die Zieleigenschaft wird auf „null“ oder einen Standardwert festgelegt, wie bereits im vorherigen Abschnitt erwähnt.</span><span class="sxs-lookup"><span data-stu-id="82b9a-467">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="82b9a-468">In einem API-Controller, der über das `[ApiController]`-Attribut verfügt, führt ein ungültiger Modellzustand zu einer automatischen „HTTP 400“-Antwort.</span><span class="sxs-lookup"><span data-stu-id="82b9a-468">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="82b9a-469">Auf einer Razor Page wird die Seite erneut mit einer Fehlermeldung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="82b9a-469">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="82b9a-470">Die clientseitige Validierung fängt die meisten ungültigen Daten ab, die andernfalls an ein Razor Pages-Formular übermittelt würden.</span><span class="sxs-lookup"><span data-stu-id="82b9a-470">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="82b9a-471">Diese Validierung erschwert es, den voranstehenden, hervorgehobenen Code auszulösen.</span><span class="sxs-lookup"><span data-stu-id="82b9a-471">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="82b9a-472">Die Beispiel-App umfasst eine Schaltfläche **Submit with Invalid Date** (Mit ungültigem Datum absenden), die ungültige Daten in das Feld **Hire Date** (Einstellungsdatum) einfügt und das Formular absendet.</span><span class="sxs-lookup"><span data-stu-id="82b9a-472">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="82b9a-473">Diese Schaltfläche zeigt, wie der Code zum erneuten Anzeigen der Seite funktioniert, wenn Datenkonvertierungsfehler auftreten.</span><span class="sxs-lookup"><span data-stu-id="82b9a-473">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="82b9a-474">Wenn die Seite von dem vorangehenden Code erneut angezeigt wird, wird die ungültige Eingabe nicht im Formularfeld angezeigt.</span><span class="sxs-lookup"><span data-stu-id="82b9a-474">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="82b9a-475">Dies liegt daran, dass die Modelleigenschaft auf „null“ oder einen Standardwert festgelegt wurde.</span><span class="sxs-lookup"><span data-stu-id="82b9a-475">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="82b9a-476">Die ungültige Eingabe wird jedoch in einer Fehlermeldung angezeigt.</span><span class="sxs-lookup"><span data-stu-id="82b9a-476">The invalid input does appear in an error message.</span></span> <span data-ttu-id="82b9a-477">Wenn Sie aber die ungültigen Daten im Formularfeld erneut anzeigen möchten, sollten Sie aus der Modelleigenschaft eine Zeichenfolge machen und die Datenkonvertierung manuell ausführen.</span><span class="sxs-lookup"><span data-stu-id="82b9a-477">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="82b9a-478">Dieselbe Strategie empfiehlt sich, wenn Sie nicht möchten, dass Typkonvertierungsfehler zu Modellzustandsfehlern führen.</span><span class="sxs-lookup"><span data-stu-id="82b9a-478">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="82b9a-479">In diesem Fall machen Sie aus der Modelleigenschaft eine Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="82b9a-479">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="82b9a-480">Einfache Typen</span><span class="sxs-lookup"><span data-stu-id="82b9a-480">Simple types</span></span>

<span data-ttu-id="82b9a-481">Die einfachen Typen, in die die Modellbindung Quellzeichenfolgen konvertieren kann, sind unter anderem:</span><span class="sxs-lookup"><span data-stu-id="82b9a-481">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="82b9a-482">Boolean</span><span class="sxs-lookup"><span data-stu-id="82b9a-482">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="82b9a-483">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="82b9a-483">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="82b9a-484">Char</span><span class="sxs-lookup"><span data-stu-id="82b9a-484">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="82b9a-485">DateTime</span><span class="sxs-lookup"><span data-stu-id="82b9a-485">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="82b9a-486">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="82b9a-486">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="82b9a-487">Decimal</span><span class="sxs-lookup"><span data-stu-id="82b9a-487">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="82b9a-488">Double</span><span class="sxs-lookup"><span data-stu-id="82b9a-488">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="82b9a-489">Enum</span><span class="sxs-lookup"><span data-stu-id="82b9a-489">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="82b9a-490">Guid</span><span class="sxs-lookup"><span data-stu-id="82b9a-490">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="82b9a-491">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="82b9a-491">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="82b9a-492">Single</span><span class="sxs-lookup"><span data-stu-id="82b9a-492">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="82b9a-493">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="82b9a-493">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="82b9a-494">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="82b9a-494">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="82b9a-495">Uri</span><span class="sxs-lookup"><span data-stu-id="82b9a-495">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="82b9a-496">Version</span><span class="sxs-lookup"><span data-stu-id="82b9a-496">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="82b9a-497">Komplexe Typen</span><span class="sxs-lookup"><span data-stu-id="82b9a-497">Complex types</span></span>

<span data-ttu-id="82b9a-498">Ein komplexer Typ muss einen öffentlichen Standardkonstruktor und öffentliche schreibbare Eigenschaften besitzen, die gebunden werden können.</span><span class="sxs-lookup"><span data-stu-id="82b9a-498">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="82b9a-499">Wenn die Modellbindung erfolgt, wird die Klasse mit dem öffentlichen Standardkonstruktor instanziiert.</span><span class="sxs-lookup"><span data-stu-id="82b9a-499">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="82b9a-500">Für jede Eigenschaft des komplexen Typs durchsucht die Modellbindung die Quellen für das Namensmuster *prefix.property_name*.</span><span class="sxs-lookup"><span data-stu-id="82b9a-500">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="82b9a-501">Wenn nichts gefunden wird, sucht sie nur nach *property_name* ohne das Präfix.</span><span class="sxs-lookup"><span data-stu-id="82b9a-501">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="82b9a-502">Beim Binden an einen Parameter ist das Präfix der Name des Parameters.</span><span class="sxs-lookup"><span data-stu-id="82b9a-502">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="82b9a-503">Beim Binden an eine öffentliche Eigenschaft `PageModel` ist das Präfix der Name der öffentlichen Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="82b9a-503">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="82b9a-504">Einige Attribute besitzen eine Eigenschaft `Prefix`, die es Ihnen gestattet, die Standardverwendung des Parameter- oder Eigenschaftennamens außer Kraft zu setzen.</span><span class="sxs-lookup"><span data-stu-id="82b9a-504">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="82b9a-505">Nehmen Sie beispielsweise an, der komplexe Typ ist die folgende `Instructor`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="82b9a-505">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="82b9a-506">Präfix = Parametername</span><span class="sxs-lookup"><span data-stu-id="82b9a-506">Prefix = parameter name</span></span>

<span data-ttu-id="82b9a-507">Wenn das zu bindende Modell ein Parameter namens `instructorToUpdate` ist:</span><span class="sxs-lookup"><span data-stu-id="82b9a-507">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="82b9a-508">Die Modellbindung beginnt, indem sie die Quellen nach dem Schlüssel `instructorToUpdate.ID` durchsucht.</span><span class="sxs-lookup"><span data-stu-id="82b9a-508">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="82b9a-509">Wenn dieser nicht gefunden wird, sucht sie nach `ID` ohne Präfix.</span><span class="sxs-lookup"><span data-stu-id="82b9a-509">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="82b9a-510">Präfix = Name der Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="82b9a-510">Prefix = property name</span></span>

<span data-ttu-id="82b9a-511">Wenn das zu bindende Modell eine Eigenschaft des Controllers oder der `PageModel`-Klasse namens `Instructor` ist:</span><span class="sxs-lookup"><span data-stu-id="82b9a-511">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="82b9a-512">Die Modellbindung beginnt, indem sie die Quellen nach dem Schlüssel `Instructor.ID` durchsucht.</span><span class="sxs-lookup"><span data-stu-id="82b9a-512">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="82b9a-513">Wenn dieser nicht gefunden wird, sucht sie nach `ID` ohne Präfix.</span><span class="sxs-lookup"><span data-stu-id="82b9a-513">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="82b9a-514">Benutzerdefiniertes Präfix</span><span class="sxs-lookup"><span data-stu-id="82b9a-514">Custom prefix</span></span>

<span data-ttu-id="82b9a-515">Wenn das zu bindende Modell ein Parameter namens `instructorToUpdate` ist, und ein `Bind`-Attribut `Instructor` als Präfix angibt:</span><span class="sxs-lookup"><span data-stu-id="82b9a-515">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="82b9a-516">Die Modellbindung beginnt, indem sie die Quellen nach dem Schlüssel `Instructor.ID` durchsucht.</span><span class="sxs-lookup"><span data-stu-id="82b9a-516">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="82b9a-517">Wenn dieser nicht gefunden wird, sucht sie nach `ID` ohne Präfix.</span><span class="sxs-lookup"><span data-stu-id="82b9a-517">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="82b9a-518">Attribute für Ziele komplexen Typs</span><span class="sxs-lookup"><span data-stu-id="82b9a-518">Attributes for complex type targets</span></span>

<span data-ttu-id="82b9a-519">Mehrere integrierte Attribute stehen für die Kontrolle der Modellbindung komplexer Typen zur Verfügung:</span><span class="sxs-lookup"><span data-stu-id="82b9a-519">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="82b9a-520">Diese Attribute wirken sich auf die Modellbindung aus, wenn bereitgestellte Formulardaten die Quelle der Wert sind.</span><span class="sxs-lookup"><span data-stu-id="82b9a-520">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="82b9a-521">Sie haben keine Auswirkungen auf Eingabeformatierer, die bereitgestellte JSON- und XML-Anforderungstexte verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="82b9a-521">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="82b9a-522">Eingabeformatierer werden [später in diesem Artikel](#input-formatters) erklärt.</span><span class="sxs-lookup"><span data-stu-id="82b9a-522">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="82b9a-523">Lesen Sie auch die Diskussion des `[Required]`-Attributs in der [Modellvalidierung](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="82b9a-523">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="82b9a-524">[BindRequired]-Attribut</span><span class="sxs-lookup"><span data-stu-id="82b9a-524">[BindRequired] attribute</span></span>

<span data-ttu-id="82b9a-525">Kann nur auf Modelleigenschaften angewendet werden, nicht auf Methodenparameter.</span><span class="sxs-lookup"><span data-stu-id="82b9a-525">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="82b9a-526">Bewirkt, dass die Modellbindung einen Modellzustandsfehler hinzufügt, wenn die Bindung für die Eigenschaft eines Modells nicht erfolgen kann.</span><span class="sxs-lookup"><span data-stu-id="82b9a-526">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="82b9a-527">Im Folgenden ein Beispiel:</span><span class="sxs-lookup"><span data-stu-id="82b9a-527">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="82b9a-528">[BindNever]-Attribut</span><span class="sxs-lookup"><span data-stu-id="82b9a-528">[BindNever] attribute</span></span>

<span data-ttu-id="82b9a-529">Kann nur auf Modelleigenschaften angewendet werden, nicht auf Methodenparameter.</span><span class="sxs-lookup"><span data-stu-id="82b9a-529">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="82b9a-530">Verhindert, dass die Modellbindung die Eigenschaft eines Modells festlegt.</span><span class="sxs-lookup"><span data-stu-id="82b9a-530">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="82b9a-531">Im Folgenden ein Beispiel:</span><span class="sxs-lookup"><span data-stu-id="82b9a-531">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="82b9a-532">[Bind]-Attribut</span><span class="sxs-lookup"><span data-stu-id="82b9a-532">[Bind] attribute</span></span>

<span data-ttu-id="82b9a-533">Kann auf eine Klasse oder einen Methodenparameter angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="82b9a-533">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="82b9a-534">Gibt an, welche Eigenschaften eines Modells in die Modellbindung aufgenommen werden sollen.</span><span class="sxs-lookup"><span data-stu-id="82b9a-534">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="82b9a-535">Im folgenden Beispiel werden nur die angegebenen Eigenschaften des `Instructor`-Modells gebunden, wenn ein Ereignishandler oder eine Aktionsmethode aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="82b9a-535">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="82b9a-536">Im folgenden Beispiel werden nur die angegebenen Eigenschaften des `Instructor`-Modells gebunden, wenn die `OnPost`-Methode aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="82b9a-536">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="82b9a-537">Das `[Bind]`-Attribut kann zum Schutz vor Overposting in *Erstellungs*szenarien (create) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="82b9a-537">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="82b9a-538">Es funktioniert nicht gut in Bearbeitungsszenarien (edit), weil ausgeschlossene Eigenschaften auf „null“ oder einen Standardwert festgelegt werden, anstatt unverändert zu bleiben.</span><span class="sxs-lookup"><span data-stu-id="82b9a-538">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="82b9a-539">Zum Schutz vor Overposting werden Ansichtsmodelle empfohlen, anstelle des `[Bind]`-Attributs.</span><span class="sxs-lookup"><span data-stu-id="82b9a-539">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="82b9a-540">Weitere Informationen finden Sie unter [Sicherheitshinweis zum Overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="82b9a-540">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="82b9a-541">Auflistungen</span><span class="sxs-lookup"><span data-stu-id="82b9a-541">Collections</span></span>

<span data-ttu-id="82b9a-542">Bei Zielen, die Sammlungen einfacher Typen sind, sucht die Modellbindung nach Übereinstimmungen mit *parameter_name* oder *property_name*.</span><span class="sxs-lookup"><span data-stu-id="82b9a-542">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="82b9a-543">Wird keine Übereinstimmung gefunden, sucht sie nach einem der unterstützten Formate ohne Präfix.</span><span class="sxs-lookup"><span data-stu-id="82b9a-543">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="82b9a-544">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="82b9a-544">For example:</span></span>

* <span data-ttu-id="82b9a-545">Angenommen, der zu bindende Parameter ist ein Array namens `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="82b9a-545">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="82b9a-546">Formular- oder Abfragezeichenfolgendaten können eins der folgenden Formate haben:</span><span class="sxs-lookup"><span data-stu-id="82b9a-546">Form or query string data can be in one of the following formats:</span></span>
   
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

* <span data-ttu-id="82b9a-547">Das folgende Format wird nur für Formulardaten unterstützt:</span><span class="sxs-lookup"><span data-stu-id="82b9a-547">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="82b9a-548">Bei allen der vorangehenden Beispielformate übergibt die Modellbindung ein Array von zwei Elementen an den `selectedCourses`-Parameter:</span><span class="sxs-lookup"><span data-stu-id="82b9a-548">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="82b9a-549">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="82b9a-549">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="82b9a-550">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="82b9a-550">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="82b9a-551">Datenformate, die Indexnummern verwenden(... [0]... [1] ...), müssen sicherstellen, dass sie fortlaufend nummeriert sind, beginnend mit 0 (null).</span><span class="sxs-lookup"><span data-stu-id="82b9a-551">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="82b9a-552">Treten bei der Indexnummerierung Lücken auf, werden alle Elemente, die auf die Lücke folgen, ignoriert.</span><span class="sxs-lookup"><span data-stu-id="82b9a-552">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="82b9a-553">Wenn die Indizes beispielsweise 0 und 2 anstelle von 0 und 1 sind, wird beispielsweise das zweite Element ignoriert.</span><span class="sxs-lookup"><span data-stu-id="82b9a-553">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="82b9a-554">Wörterbücher</span><span class="sxs-lookup"><span data-stu-id="82b9a-554">Dictionaries</span></span>

<span data-ttu-id="82b9a-555">Bei `Dictionary`-Zielen sucht die Modellbindung nach Übereinstimmungen mit *parameter_name* oder *property_name*.</span><span class="sxs-lookup"><span data-stu-id="82b9a-555">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="82b9a-556">Wird keine Übereinstimmung gefunden, sucht sie nach einem der unterstützten Formate ohne Präfix.</span><span class="sxs-lookup"><span data-stu-id="82b9a-556">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="82b9a-557">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="82b9a-557">For example:</span></span>

* <span data-ttu-id="82b9a-558">Angenommen, der Zielparameter ist eine `Dictionary<int, string>` mit dem Namen `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="82b9a-558">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="82b9a-559">Die bereitgestellten Formular- oder Abfragezeichenfolgendaten können wie eins der folgenden Beispiele aussehen:</span><span class="sxs-lookup"><span data-stu-id="82b9a-559">The posted form or query string data can look like one of the following examples:</span></span>

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

* <span data-ttu-id="82b9a-560">Bei allen der vorangehenden Beispielformate übergibt die Modellbindung ein Wörterbuch aus zwei Elementen an den `selectedCourses`-Parameter:</span><span class="sxs-lookup"><span data-stu-id="82b9a-560">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="82b9a-561">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="82b9a-561">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="82b9a-562">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="82b9a-562">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="82b9a-563">Globalisierungsverhalten der Routendaten und Abfragezeichenfolgen für die Modellbindung</span><span class="sxs-lookup"><span data-stu-id="82b9a-563">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="82b9a-564">Der ASP.NET Core-Routenwertanbieter und der Abfragezeichenfolgenwert-Anbieter:</span><span class="sxs-lookup"><span data-stu-id="82b9a-564">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="82b9a-565">behandeln Werte als invariante Kulturen.</span><span class="sxs-lookup"><span data-stu-id="82b9a-565">Treat values as invariant culture.</span></span>
* <span data-ttu-id="82b9a-566">erwarten, dass URLs kulturinvariant sind.</span><span class="sxs-lookup"><span data-stu-id="82b9a-566">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="82b9a-567">Im Gegensatz dazu durchlaufen Werte, die aus Formulardaten stammen, eine kulturabhängige Konvertierung.</span><span class="sxs-lookup"><span data-stu-id="82b9a-567">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="82b9a-568">Dies ist beabsichtigt, damit URLs zwischen Gebietsschemas freigegeben werden können.</span><span class="sxs-lookup"><span data-stu-id="82b9a-568">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="82b9a-569">So lassen Sie den ASP.NET Core-Routenwertanbieter und den Abfragezeichenfolgenwert-Anbieter eine kulturabhängige Konvertierung durchlaufen:</span><span class="sxs-lookup"><span data-stu-id="82b9a-569">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="82b9a-570">Sie erben von <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory>.</span><span class="sxs-lookup"><span data-stu-id="82b9a-570">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="82b9a-571">Kopieren Sie den Code aus [QueryStringValueProviderFactory](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) oder [RouteValueValueProviderFactory](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs).</span><span class="sxs-lookup"><span data-stu-id="82b9a-571">Copy the code from [QueryStringValueProviderFactory](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="82b9a-572">Ersetzen Sie den an den Wertanbieterkonstruktor übergebenen [Culture-Wert](https://github.com/aspnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) durch [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture).</span><span class="sxs-lookup"><span data-stu-id="82b9a-572">Replace the [culture value](https://github.com/aspnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="82b9a-573">Ersetzen Sie die Standardwertanbieter-Zuordnungsinstanz in den MVC-Optionen durch Ihre neue:</span><span class="sxs-lookup"><span data-stu-id="82b9a-573">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="82b9a-574">Spezielle Datentypen</span><span class="sxs-lookup"><span data-stu-id="82b9a-574">Special data types</span></span>

<span data-ttu-id="82b9a-575">Es gibt einige spezielle Datentypen, die die Modellbindung verarbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="82b9a-575">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="82b9a-576">„IFormFile“ und „IFormFileCollection“</span><span class="sxs-lookup"><span data-stu-id="82b9a-576">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="82b9a-577">Eine in der HTTP-Anforderung enthaltene, hochgeladenen Datei.</span><span class="sxs-lookup"><span data-stu-id="82b9a-577">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="82b9a-578">Außerdem wird `IEnumerable<IFormFile>` für mehrere Dateien unterstützt.</span><span class="sxs-lookup"><span data-stu-id="82b9a-578">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="82b9a-579">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="82b9a-579">CancellationToken</span></span>

<span data-ttu-id="82b9a-580">Wird verwendet, um die Aktivität in asynchronen Controllern zu beenden.</span><span class="sxs-lookup"><span data-stu-id="82b9a-580">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="82b9a-581">„FormCollection“</span><span class="sxs-lookup"><span data-stu-id="82b9a-581">FormCollection</span></span>

<span data-ttu-id="82b9a-582">Wird verwendet, um alle Werte aus bereitgestellten Formulardaten abzurufen.</span><span class="sxs-lookup"><span data-stu-id="82b9a-582">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="82b9a-583">Eingabeformatierer</span><span class="sxs-lookup"><span data-stu-id="82b9a-583">Input formatters</span></span>

<span data-ttu-id="82b9a-584">Daten im Anforderungstext können im JSON-, XML- oder einem anderen Format sein.</span><span class="sxs-lookup"><span data-stu-id="82b9a-584">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="82b9a-585">Um diese Daten zu analysieren, verwendet die Modellbindung einen *Eingabeformatierer*, der für die Verarbeitung eines bestimmten Inhaltstyps konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="82b9a-585">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="82b9a-586">ASP.NET Core enthält standardmäßig JSON-basierte Eingabeformatierer für die Verarbeitung von JSON-Daten.</span><span class="sxs-lookup"><span data-stu-id="82b9a-586">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="82b9a-587">Sie können andere Formatierer für andere Inhaltstypen hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="82b9a-587">You can add other formatters for other content types.</span></span>

<span data-ttu-id="82b9a-588">ASP.NET Core wählt Eingabeformatierer auf Grundlage des [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute)-Attributs aus.</span><span class="sxs-lookup"><span data-stu-id="82b9a-588">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="82b9a-589">Wenn kein Attribut vorhanden ist, verwendet es den [Content-Type-Header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="82b9a-589">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="82b9a-590">So verwenden Sie die integrierte XML-Eingabeformatierer</span><span class="sxs-lookup"><span data-stu-id="82b9a-590">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="82b9a-591">Installieren Sie das NuGet-Paket `Microsoft.AspNetCore.Mvc.Formatters.Xml`.</span><span class="sxs-lookup"><span data-stu-id="82b9a-591">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="82b9a-592">In `Startup.ConfigureServices`, rufen Sie <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> oder <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*> auf.</span><span class="sxs-lookup"><span data-stu-id="82b9a-592">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="82b9a-593">Wenden Sie das `Consumes`-Attribut auf Controllerklassen oder Aktionsmethoden an, die XML im Anforderungstext erwarten sollten.</span><span class="sxs-lookup"><span data-stu-id="82b9a-593">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="82b9a-594">Weitere Informationen finden Sie unter [Einführung der XML-Serialisierung](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="82b9a-594">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="82b9a-595">Ausschließen angegebener Typen aus der Modellbindung</span><span class="sxs-lookup"><span data-stu-id="82b9a-595">Exclude specified types from model binding</span></span>

<span data-ttu-id="82b9a-596">Das Verhalten der Modellbindung und des Validierungssystems wird von der Klasse [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata) gesteuert.</span><span class="sxs-lookup"><span data-stu-id="82b9a-596">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="82b9a-597">Sie können `ModelMetadata` anpassen, indem Sie [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders) einen Detailanbieter hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="82b9a-597">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="82b9a-598">Integrierte Detailanbieter sind verfügbar, um die Modellbindung oder Validierung für angegebene Typen zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="82b9a-598">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="82b9a-599">Um die Modellbindung für alle Modelle eines angegebenen Typs zu deaktivieren, fügen Sie in `Startup.ConfigureServices` einen <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> hinzu.</span><span class="sxs-lookup"><span data-stu-id="82b9a-599">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="82b9a-600">Beispielsweise können Sie die Modellbindung für alle Modelle vom Typ `System.Version` wie folgt deaktivieren:</span><span class="sxs-lookup"><span data-stu-id="82b9a-600">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="82b9a-601">Um die Validierung für Eigenschaften eines angegebenen Typs zu deaktivieren, fügen Sie in `Startup.ConfigureServices` einen <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> hinzu.</span><span class="sxs-lookup"><span data-stu-id="82b9a-601">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="82b9a-602">Beispielsweise können Sie die Überprüfung von Eigenschaften vom Typ `System.Guid` wie folgt deaktivieren:</span><span class="sxs-lookup"><span data-stu-id="82b9a-602">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="82b9a-603">Benutzerdefinierte Modellbindungen</span><span class="sxs-lookup"><span data-stu-id="82b9a-603">Custom model binders</span></span>

<span data-ttu-id="82b9a-604">Sie können die Modellbindung erweitern, indem Sie eine benutzerdefinierte Modellbindung schreiben und das `[ModelBinder]`-Attribut verwenden, um diese für ein bestimmtes Ziel auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="82b9a-604">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="82b9a-605">Erfahren Sie mehr über die [benutzerdefinierte Modellbindung](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="82b9a-605">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="82b9a-606">Manuelle Modellbindung</span><span class="sxs-lookup"><span data-stu-id="82b9a-606">Manual model binding</span></span>

<span data-ttu-id="82b9a-607">Die Modellbindung kann mithilfe der <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>-Methode manuell aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="82b9a-607">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="82b9a-608">Die Methode ist für die beiden Klassen `ControllerBase` und `PageModel` definiert.</span><span class="sxs-lookup"><span data-stu-id="82b9a-608">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="82b9a-609">Mithilfe von Methodenüberladungen können Sie das Präfix und den Wertanbieter festlegen, die verwendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="82b9a-609">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="82b9a-610">Die Methode gibt `false` zurück, wenn die Modellbindung fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="82b9a-610">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="82b9a-611">Im Folgenden ein Beispiel:</span><span class="sxs-lookup"><span data-stu-id="82b9a-611">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="82b9a-612">[FromServices]-Attribut</span><span class="sxs-lookup"><span data-stu-id="82b9a-612">[FromServices] attribute</span></span>

<span data-ttu-id="82b9a-613">Der Name dieses Attributs folgt dem Muster von Modellbindungsattributen, die eine Datenquelle angeben.</span><span class="sxs-lookup"><span data-stu-id="82b9a-613">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="82b9a-614">Es ist aber nicht zum Binden von Daten aus einem Wertanbieter gedacht.</span><span class="sxs-lookup"><span data-stu-id="82b9a-614">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="82b9a-615">Es ruft eine Instanz eines Typs aus dem [Dependency Injection](xref:fundamentals/dependency-injection)-Container (Abhängigkeitsinjektion) ab.</span><span class="sxs-lookup"><span data-stu-id="82b9a-615">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="82b9a-616">Sein Zweck besteht darin, eine Alternative zur „Constructor Injection“ (Konstruktorinjektion) bereitzustellen, wenn Sie einen Dienst nur dann benötigen, wenn eine bestimmte Methode aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="82b9a-616">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="82b9a-617">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="82b9a-617">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end

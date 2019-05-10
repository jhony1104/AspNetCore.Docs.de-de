---
title: Erstellen von Web-APIs mit ASP.NET Core
author: scottaddie
description: Erfahren Sie mehr über die Grundlagen zum Erstellen einer Web-API in ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 04/11/2019
uid: web-api/index
ms.openlocfilehash: d804a7f1b4f0e89f433a3674116c97804705f7cc
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64882955"
---
# <a name="create-web-apis-with-aspnet-core"></a><span data-ttu-id="f4a11-103">Erstellen von Web-APIs mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f4a11-103">Create web APIs with ASP.NET Core</span></span>

<span data-ttu-id="f4a11-104">Von [Scott Addie](https://github.com/scottaddie) und [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="f4a11-104">By [Scott Addie](https://github.com/scottaddie) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="f4a11-105">ASP.NET Core unterstützt das Erstellen von RESTful-Diensten, die auch als Web-APIs bezeichnet werden, unter Verwendung von C#.</span><span class="sxs-lookup"><span data-stu-id="f4a11-105">ASP.NET Core supports creating RESTful services, also known as web APIs, using C#.</span></span> <span data-ttu-id="f4a11-106">Eine Web-API verwendet Controller, um Anforderungen zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="f4a11-106">To handle requests, a web API uses controllers.</span></span> <span data-ttu-id="f4a11-107">*Controller* in einer Web-API sind von `ControllerBase` abgeleitete Klassen.</span><span class="sxs-lookup"><span data-stu-id="f4a11-107">*Controllers* in a web API are classes that derive from `ControllerBase`.</span></span> <span data-ttu-id="f4a11-108">In diesem Artikel wird beschrieben, wie Sie Controller für die Verarbeitung von API-Anforderungen verwenden.</span><span class="sxs-lookup"><span data-stu-id="f4a11-108">This article shows how to use controllers for handling API requests.</span></span>

<span data-ttu-id="f4a11-109">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples)</span><span class="sxs-lookup"><span data-stu-id="f4a11-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples).</span></span> <span data-ttu-id="f4a11-110">([Informationen zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f4a11-110">([How to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="controllerbase-class"></a><span data-ttu-id="f4a11-111">ControllerBase-Klasse</span><span class="sxs-lookup"><span data-stu-id="f4a11-111">ControllerBase class</span></span>

<span data-ttu-id="f4a11-112">Eine Web-API verfügt über mindestens eine Controllerklasse, die von <xref:Microsoft.AspNetCore.Mvc.ControllerBase> abgeleitet ist.</span><span class="sxs-lookup"><span data-stu-id="f4a11-112">A web API has one or more controller classes that derive from <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="f4a11-113">Durch die Web-API-Projektvorlage wird beispielsweise ein Values-Controller erstellt:</span><span class="sxs-lookup"><span data-stu-id="f4a11-113">For example, the web API project template creates a Values controller:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_Signature&highlight=3)]

<span data-ttu-id="f4a11-114">Erstellen Sie keinen von der <xref:Microsoft.AspNetCore.Mvc.Controller>-Basisklasse abgeleiteten Web-API-Controller.</span><span class="sxs-lookup"><span data-stu-id="f4a11-114">Don't create a web API controller by deriving from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class.</span></span> <span data-ttu-id="f4a11-115">`Controller` wird von `ControllerBase` abgeleitet und fügt Unterstützung für Ansichten hinzu, wird also für die Verarbeitung von Webseiten verwendet und nicht für Web-API-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="f4a11-115">`Controller` derives from `ControllerBase` and adds support for views, so it's for handling web pages, not web API requests.</span></span>  <span data-ttu-id="f4a11-116">Es gibt eine Ausnahme von dieser Regel: Wenn Sie denselben Controller sowohl für Ansichten als auch für APIs verwenden möchten, leiten Sie ihn von `Controller` ab.</span><span class="sxs-lookup"><span data-stu-id="f4a11-116">There's an exception to this rule: if you plan to use the same controller for both views and APIs, derive it from `Controller`.</span></span>

<span data-ttu-id="f4a11-117">Die `ControllerBase`-Klasse bietet viele Eigenschaften und Methoden, die für die Verarbeitung von HTTP-Anforderungen hilfreich sind.</span><span class="sxs-lookup"><span data-stu-id="f4a11-117">The `ControllerBase` class provides many properties and methods that are useful for handling HTTP requests.</span></span> <span data-ttu-id="f4a11-118">`ControllerBase.CreatedAtAction` gibt beispielsweise Statuscode 201 zurück:</span><span class="sxs-lookup"><span data-stu-id="f4a11-118">For example, `ControllerBase.CreatedAtAction` returns a 201 status code:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_400And201&highlight=8-9)]

 <span data-ttu-id="f4a11-119">Hier einige weitere Beispiele für die von `ControllerBase` bereitgestellten Methoden.</span><span class="sxs-lookup"><span data-stu-id="f4a11-119">Here are some more examples of methods that `ControllerBase` provides.</span></span>

|<span data-ttu-id="f4a11-120">Methode</span><span class="sxs-lookup"><span data-stu-id="f4a11-120">Method</span></span>  |<span data-ttu-id="f4a11-121">Hinweise</span><span class="sxs-lookup"><span data-stu-id="f4a11-121">Notes</span></span>  |
|---------|---------|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>| <span data-ttu-id="f4a11-122">Gibt Statuscode 400 zurück.</span><span class="sxs-lookup"><span data-stu-id="f4a11-122">Returns 400 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> |<span data-ttu-id="f4a11-123">Gibt Statuscode 404 zurück.</span><span class="sxs-lookup"><span data-stu-id="f4a11-123">Returns 404 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.PhysicalFile*>|<span data-ttu-id="f4a11-124">Gibt eine Datei zurück.</span><span class="sxs-lookup"><span data-stu-id="f4a11-124">Returns a file.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>|<span data-ttu-id="f4a11-125">Ruft die [Modellbindung](xref:mvc/models/model-binding) auf.</span><span class="sxs-lookup"><span data-stu-id="f4a11-125">Invokes [model binding](xref:mvc/models/model-binding).</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryValidateModel*>|<span data-ttu-id="f4a11-126">Ruft die [Modellvalidierung](xref:mvc/models/validation) auf.</span><span class="sxs-lookup"><span data-stu-id="f4a11-126">Invokes [model validation](xref:mvc/models/validation).</span></span>|

<span data-ttu-id="f4a11-127">Eine Liste aller verfügbaren Methoden und Eigenschaften finden Sie unter <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span><span class="sxs-lookup"><span data-stu-id="f4a11-127">For a list of all available methods and properties, see <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span>

## <a name="attributes"></a><span data-ttu-id="f4a11-128">Attribute</span><span class="sxs-lookup"><span data-stu-id="f4a11-128">Attributes</span></span>

<span data-ttu-id="f4a11-129">Der <xref:Microsoft.AspNetCore.Mvc>-Namespace enthält Attribute, mit denen das Verhalten von Web-API-Controllern und Aktionsmethoden konfiguriert werden kann.</span><span class="sxs-lookup"><span data-stu-id="f4a11-129">The <xref:Microsoft.AspNetCore.Mvc> namespace provides attributes that can be used to configure the behavior of web API controllers and action methods.</span></span> <span data-ttu-id="f4a11-130">Im folgenden Beispiel werden mithilfe von Attributen die akzeptierte HTTP-Methode und die zurückgegebenen Statuscodes angegeben:</span><span class="sxs-lookup"><span data-stu-id="f4a11-130">The following example uses attributes to specify the HTTP method accepted and the status codes returned:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_400And201&highlight=1-3)]

<span data-ttu-id="f4a11-131">Hier einige weitere Beispiele für die verfügbaren Attribute.</span><span class="sxs-lookup"><span data-stu-id="f4a11-131">Here are some more examples of attributes that are available.</span></span>

|<span data-ttu-id="f4a11-132">Attribut</span><span class="sxs-lookup"><span data-stu-id="f4a11-132">Attribute</span></span>|<span data-ttu-id="f4a11-133">Hinweise</span><span class="sxs-lookup"><span data-stu-id="f4a11-133">Notes</span></span>|
|---------|-----|
|<span data-ttu-id="f4a11-134">[[Route]](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)</span><span class="sxs-lookup"><span data-stu-id="f4a11-134">[[Route]](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)</span></span>      |<span data-ttu-id="f4a11-135">Gibt ein URL-Muster für einen Controller oder eine Aktion an.</span><span class="sxs-lookup"><span data-stu-id="f4a11-135">Specifies URL pattern for a controller or action.</span></span>|
|<span data-ttu-id="f4a11-136">[[Bind]](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)</span><span class="sxs-lookup"><span data-stu-id="f4a11-136">[[Bind]](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)</span></span>        |<span data-ttu-id="f4a11-137">Gibt Präfixe und Eigenschaften an, die in die Modellbindung einbezogen werden sollen.</span><span class="sxs-lookup"><span data-stu-id="f4a11-137">Specifies prefix and properties to include for model binding.</span></span>|
|<span data-ttu-id="f4a11-138">[[HttpGet]](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)</span><span class="sxs-lookup"><span data-stu-id="f4a11-138">[[HttpGet]](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)</span></span>  |<span data-ttu-id="f4a11-139">Identifiziert eine Aktion, die die HTTP GET-Methode unterstützt.</span><span class="sxs-lookup"><span data-stu-id="f4a11-139">Identifies an action that supports the HTTP GET method.</span></span>|
|<span data-ttu-id="f4a11-140">[[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)</span><span class="sxs-lookup"><span data-stu-id="f4a11-140">[[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)</span></span>|<span data-ttu-id="f4a11-141">Gibt die von einer Aktion akzeptierten Datentypen an.</span><span class="sxs-lookup"><span data-stu-id="f4a11-141">Specifies data types that an action accepts.</span></span>|
|<span data-ttu-id="f4a11-142">[[Produces]](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)</span><span class="sxs-lookup"><span data-stu-id="f4a11-142">[[Produces]](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)</span></span>|<span data-ttu-id="f4a11-143">Gibt die von einer Aktion zurückgegebenen Datentypen an.</span><span class="sxs-lookup"><span data-stu-id="f4a11-143">Specifies data types that an action returns.</span></span>|

<span data-ttu-id="f4a11-144">Eine Liste mit den verfügbaren Attributen finden Sie im <xref:Microsoft.AspNetCore.Mvc>-Namespace.</span><span class="sxs-lookup"><span data-stu-id="f4a11-144">For a list that includes the available attributes, see the <xref:Microsoft.AspNetCore.Mvc> namespace.</span></span>

## <a name="apicontroller-attribute"></a><span data-ttu-id="f4a11-145">ApiController-Attribut</span><span class="sxs-lookup"><span data-stu-id="f4a11-145">ApiController attribute</span></span>

<span data-ttu-id="f4a11-146">Das [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)-Attribut kann auf eine Controllerklasse angewendet werden, um API-spezifische Verhalten zu aktivieren:</span><span class="sxs-lookup"><span data-stu-id="f4a11-146">The [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute can be applied to a controller class to enable API-specific behaviors:</span></span>

* [<span data-ttu-id="f4a11-147">Anforderung für das Attributrouting</span><span class="sxs-lookup"><span data-stu-id="f4a11-147">Attribute routing requirement</span></span>](#attribute-routing-requirement)
* [<span data-ttu-id="f4a11-148">Automatische HTTP 400-Antworten</span><span class="sxs-lookup"><span data-stu-id="f4a11-148">Automatic HTTP 400 responses</span></span>](#automatic-http-400-responses)
* [<span data-ttu-id="f4a11-149">Rückschluss auf Bindungsquellparameter</span><span class="sxs-lookup"><span data-stu-id="f4a11-149">Binding source parameter inference</span></span>](#binding-source-parameter-inference)
* [<span data-ttu-id="f4a11-150">Ableiten der Multipart/form-data-Anforderung</span><span class="sxs-lookup"><span data-stu-id="f4a11-150">Multipart/form-data request inference</span></span>](#multipartform-data-request-inference)
* [<span data-ttu-id="f4a11-151">Problemdetails für Fehlerstatuscodes</span><span class="sxs-lookup"><span data-stu-id="f4a11-151">Problem details for error status codes</span></span>](#problem-details-for-error-status-codes)

<span data-ttu-id="f4a11-152">Diese Features erfordern mindestens [Kompatibilitätsversion](<xref:mvc/compatibility-version>) 2.1.</span><span class="sxs-lookup"><span data-stu-id="f4a11-152">These features require a [compatibility version](<xref:mvc/compatibility-version>) of 2.1 or later.</span></span>

### <a name="apicontroller-on-specific-controllers"></a><span data-ttu-id="f4a11-153">ApiController für bestimmte Controller</span><span class="sxs-lookup"><span data-stu-id="f4a11-153">ApiController on specific controllers</span></span>

<span data-ttu-id="f4a11-154">Das `[ApiController]`-Attribut kann, wie im folgenden Beispiel der Projektvorlage ersichtlich, auf bestimmte Controller angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="f4a11-154">The `[ApiController]` attribute can be applied to specific controllers, as in the following example from the project template:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_Signature&highlight=2)]

### <a name="apicontroller-on-multiple-controllers"></a><span data-ttu-id="f4a11-155">ApiController für mehrere Controller</span><span class="sxs-lookup"><span data-stu-id="f4a11-155">ApiController on multiple controllers</span></span>

<span data-ttu-id="f4a11-156">Eine Möglichkeit, das Attribut für mehr als einen Controller zu verwenden, besteht darin, eine benutzerdefinierte Basiscontrollerklasse zu erstellen, die mit dem `[ApiController]`-Attribut kommentiert ist.</span><span class="sxs-lookup"><span data-stu-id="f4a11-156">One approach to using the attribute on more than one controller is to create a custom base controller class annotated with the `[ApiController]` attribute.</span></span> <span data-ttu-id="f4a11-157">Hier ein Beispiel für eine benutzerdefinierte Basisklasse und einen Controller, der von ihr abgeleitet wird:</span><span class="sxs-lookup"><span data-stu-id="f4a11-157">Here's an example showing a custom base class and a controller that derives from it:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/MyControllerBase.cs?name=snippet_MyControllerBase)]

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_Inherit)]

### <a name="apicontroller-on-an-assembly"></a><span data-ttu-id="f4a11-158">ApiController für eine Assembly</span><span class="sxs-lookup"><span data-stu-id="f4a11-158">ApiController on an assembly</span></span>

<span data-ttu-id="f4a11-159">Wenn die [Kompatibilitätsversion](<xref:mvc/compatibility-version>) auf 2.2 oder höher festgelegt ist, kann das `[ApiController]`-Attribut auf eine Assembly angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="f4a11-159">If [compatibility version](<xref:mvc/compatibility-version>) is set to 2.2 or later, the `[ApiController]` attribute can be applied to an assembly.</span></span> <span data-ttu-id="f4a11-160">Auf diese Weise wendet die Anmerkung das Web-API-Verhalten auf alle Controller in der Assembly an.</span><span class="sxs-lookup"><span data-stu-id="f4a11-160">Annotation in this manner applies web API behavior to all controllers in the assembly.</span></span> <span data-ttu-id="f4a11-161">Es gibt keine Möglichkeit, einzelne Controller auszuschließen.</span><span class="sxs-lookup"><span data-stu-id="f4a11-161">There's no way to opt out for individual controllers.</span></span> <span data-ttu-id="f4a11-162">Wenden Sie das Attribut auf Assemblyebene auf die `Startup`-Klasse an, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="f4a11-162">Apply the assembly-level attribute to the `Startup` class as shown in the following example:</span></span>

```csharp
[assembly: ApiController]
namespace WebApiSample
{
    public class Startup
    {
        ...
    }
}
```

## <a name="attribute-routing-requirement"></a><span data-ttu-id="f4a11-163">Anforderung für das Attributrouting</span><span class="sxs-lookup"><span data-stu-id="f4a11-163">Attribute routing requirement</span></span>

<span data-ttu-id="f4a11-164">Durch das `ApiController`-Attribut wird das Attributrouting zu einer Anforderung.</span><span class="sxs-lookup"><span data-stu-id="f4a11-164">The `ApiController` attribute makes attribute routing a requirement.</span></span> <span data-ttu-id="f4a11-165">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f4a11-165">For example:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_Signature&highlight=1)]

<span data-ttu-id="f4a11-166">Sie können über [konventionelle Routen](xref:mvc/controllers/routing#conventional-routing), die durch <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> oder <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> in `Startup.Configure` definiert sind, nicht auf Aktionen zugreifen.</span><span class="sxs-lookup"><span data-stu-id="f4a11-166">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined by <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> or <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> in `Startup.Configure`.</span></span>

## <a name="automatic-http-400-responses"></a><span data-ttu-id="f4a11-167">Automatische HTTP 400-Antwort</span><span class="sxs-lookup"><span data-stu-id="f4a11-167">Automatic HTTP 400 responses</span></span>

<span data-ttu-id="f4a11-168">Durch das `ApiController`-Attribut wird bei Modellvalidierungsfehlern automatisch eine HTTP 400-Antwort ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="f4a11-168">The `ApiController` attribute makes model validation errors automatically trigger an HTTP 400 response.</span></span> <span data-ttu-id="f4a11-169">Deshalb ist der folgende Code in einer Aktionsmethode überflüssig:</span><span class="sxs-lookup"><span data-stu-id="f4a11-169">Consequently, the following code is unnecessary in an action method:</span></span>

```csharp
if (!ModelState.IsValid)
{
    return BadRequest(ModelState);
}
```

### <a name="default-badrequest-response"></a><span data-ttu-id="f4a11-170">Standardmäßige BadRequest-Antwort</span><span class="sxs-lookup"><span data-stu-id="f4a11-170">Default BadRequest response</span></span> 

<span data-ttu-id="f4a11-171">Bei Kompatibilitätsversion 2.2 oder höher wird für HTTP 400-Antworten standardmäßig der Antworttyp <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> verwendet.</span><span class="sxs-lookup"><span data-stu-id="f4a11-171">With a compatibility version of 2.2 or later, the default response type for HTTP 400 responses is <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="f4a11-172">Der Typ `ValidationProblemDetails` entspricht der [RFC 7807-Spezifikation](https://tools.ietf.org/html/rfc7807).</span><span class="sxs-lookup"><span data-stu-id="f4a11-172">The `ValidationProblemDetails` type complies with the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807).</span></span>

<span data-ttu-id="f4a11-173">Um die Standardantwort in <xref:Microsoft.AspNetCore.Mvc.SerializableError> zu ändern, legen Sie die `SuppressUseValidationProblemDetailsForInvalidModelStateResponses`-Eigenschaft auf `true` in `Startup.ConfigureServices` fest, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="f4a11-173">To change the default response to <xref:Microsoft.AspNetCore.Mvc.SerializableError>, set the `SuppressUseValidationProblemDetailsForInvalidModelStateResponses` property to `true` in `Startup.ConfigureServices`, as shown in the following example:</span></span>

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,9)]

### <a name="customize-badrequest-response"></a><span data-ttu-id="f4a11-174">Anpassen der BadRequest-Antwort</span><span class="sxs-lookup"><span data-stu-id="f4a11-174">Customize BadRequest response</span></span>

<span data-ttu-id="f4a11-175">Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory>, um die aus einem Validierungsfehler resultierende Antwort anzupassen.</span><span class="sxs-lookup"><span data-stu-id="f4a11-175">To customize the response that results from a validation error, use <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory>.</span></span> <span data-ttu-id="f4a11-176">Fügen Sie nach `services.AddMvc().SetCompatibilityVersion` folgenden hervorgehobenen Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="f4a11-176">Add the following highlighted code after `services.AddMvc().SetCompatibilityVersion`:</span></span>

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureBadRequestResponse&highlight=3-20)]

### <a name="disable-automatic-400"></a><span data-ttu-id="f4a11-177">Deaktivieren des automatischen Verhaltens bei Statuscode 400</span><span class="sxs-lookup"><span data-stu-id="f4a11-177">Disable automatic 400</span></span>

<span data-ttu-id="f4a11-178">Um das automatische Verhalten bei Statuscode 400 zu deaktivieren, legen Sie die <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter>-Eigenschaft auf `true` fest.</span><span class="sxs-lookup"><span data-stu-id="f4a11-178">To disable the automatic 400 behavior, set the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> property to `true`.</span></span> <span data-ttu-id="f4a11-179">Fügen Sie in `Startup.ConfigureServices` nach `services.AddMvc().SetCompatibilityVersion` folgenden hervorgehobenen Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="f4a11-179">Add the following highlighted code in `Startup.ConfigureServices` after `services.AddMvc().SetCompatibilityVersion`:</span></span>

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,7)]

## <a name="binding-source-parameter-inference"></a><span data-ttu-id="f4a11-180">Rückschluss auf Bindungsquellparameter</span><span class="sxs-lookup"><span data-stu-id="f4a11-180">Binding source parameter inference</span></span>

<span data-ttu-id="f4a11-181">Ein Bindungsquellenattribut definiert den Speicherort, an dem der Wert eines Aktionsparameters vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="f4a11-181">A binding source attribute defines the location at which an action parameter's value is found.</span></span> <span data-ttu-id="f4a11-182">Es gibt die folgenden Bindungsquellattribute:</span><span class="sxs-lookup"><span data-stu-id="f4a11-182">The following binding source attributes exist:</span></span>

|<span data-ttu-id="f4a11-183">Attribut</span><span class="sxs-lookup"><span data-stu-id="f4a11-183">Attribute</span></span>|<span data-ttu-id="f4a11-184">Bindungsquelle</span><span class="sxs-lookup"><span data-stu-id="f4a11-184">Binding source</span></span> |
|---------|---------|
|<span data-ttu-id="f4a11-185">[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)</span><span class="sxs-lookup"><span data-stu-id="f4a11-185">[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)</span></span>     | <span data-ttu-id="f4a11-186">Anforderungstext</span><span class="sxs-lookup"><span data-stu-id="f4a11-186">Request body</span></span> |
|<span data-ttu-id="f4a11-187">[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)</span><span class="sxs-lookup"><span data-stu-id="f4a11-187">[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)</span></span>     | <span data-ttu-id="f4a11-188">Formulardaten im Anforderungstext</span><span class="sxs-lookup"><span data-stu-id="f4a11-188">Form data in the request body</span></span> |
|<span data-ttu-id="f4a11-189">[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute)</span><span class="sxs-lookup"><span data-stu-id="f4a11-189">[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute)</span></span> | <span data-ttu-id="f4a11-190">Anforderungsheader</span><span class="sxs-lookup"><span data-stu-id="f4a11-190">Request header</span></span> |
|<span data-ttu-id="f4a11-191">[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)</span><span class="sxs-lookup"><span data-stu-id="f4a11-191">[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)</span></span>   | <span data-ttu-id="f4a11-192">Abfragezeichenfolge-Parameter der Anforderung</span><span class="sxs-lookup"><span data-stu-id="f4a11-192">Request query string parameter</span></span> |
|<span data-ttu-id="f4a11-193">[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)</span><span class="sxs-lookup"><span data-stu-id="f4a11-193">[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)</span></span>   | <span data-ttu-id="f4a11-194">Routendaten aus aktuellen Anforderungen</span><span class="sxs-lookup"><span data-stu-id="f4a11-194">Route data from the current request</span></span> |
|<span data-ttu-id="f4a11-195">[[FromServices]](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices)</span><span class="sxs-lookup"><span data-stu-id="f4a11-195">[[FromServices]](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices)</span></span> | <span data-ttu-id="f4a11-196">Der als Aktionsparameter eingefügte Anforderungsdienst.</span><span class="sxs-lookup"><span data-stu-id="f4a11-196">The request service injected as an action parameter</span></span> |

> [!WARNING]
> <span data-ttu-id="f4a11-197">Verwenden Sie `[FromRoute]` nicht, wenn Werte möglicherweise `%2f` enthalten (d.h. `/`).</span><span class="sxs-lookup"><span data-stu-id="f4a11-197">Don't use `[FromRoute]` when values might contain `%2f` (that is `/`).</span></span> <span data-ttu-id="f4a11-198">`%2f` wird nicht durch Entfernen von Escapezeichen zu `/`.</span><span class="sxs-lookup"><span data-stu-id="f4a11-198">`%2f` won't be unescaped to `/`.</span></span> <span data-ttu-id="f4a11-199">Verwenden Sie `[FromQuery]`, wenn der Wert `%2f` enthalten könnte.</span><span class="sxs-lookup"><span data-stu-id="f4a11-199">Use `[FromQuery]` if the value might contain `%2f`.</span></span>

<span data-ttu-id="f4a11-200">Ohne das `[ApiController]`-Attribut oder Bindungsquellenattribute wie `[FromQuery]` versucht die ASP.NET Core-Runtime, die Modellbindung für komplexe Objekte zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="f4a11-200">Without the `[ApiController]` attribute or binding source attributes like `[FromQuery]`, the ASP.NET Core runtime attempts to use the complex object model binder.</span></span> <span data-ttu-id="f4a11-201">Bei der Modellbindung für komplexe Objekte werden Daten aus Wertanbietern in einer festgelegten Reihenfolge abgerufen.</span><span class="sxs-lookup"><span data-stu-id="f4a11-201">The complex object model binder pulls data from value providers in a defined order.</span></span>

<span data-ttu-id="f4a11-202">Im folgenden Beispiel gibt das `[FromQuery]`-Attribut an, dass der Parameterwert `discontinuedOnly` in der Abfragezeichenfolge der Anforderungs-URL bereitgestellt wird:</span><span class="sxs-lookup"><span data-stu-id="f4a11-202">In the following example, the `[FromQuery]` attribute indicates that the `discontinuedOnly` parameter value is provided in the request URL's query string:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

<span data-ttu-id="f4a11-203">Das `[ApiController]`-Attribut wendet Rückschlussregeln auf die Standarddatenquellen von Aktionsparametern an.</span><span class="sxs-lookup"><span data-stu-id="f4a11-203">The `[ApiController]` attribute applies inference rules for the default data sources of action parameters.</span></span> <span data-ttu-id="f4a11-204">Da durch diese Regeln Attribute auf die Aktionsparameter angewendet werden, müssen Sie Bindungsquellen nicht manuell identifizieren.</span><span class="sxs-lookup"><span data-stu-id="f4a11-204">These rules save you from having to identify binding sources manually by applying attributes to the action parameters.</span></span> <span data-ttu-id="f4a11-205">Die Rückschlussregeln für Bindungsquellen verhalten sich wie folgt:</span><span class="sxs-lookup"><span data-stu-id="f4a11-205">The binding source inference rules behave as follows:</span></span>

* <span data-ttu-id="f4a11-206">`[FromBody]` wird für komplexe Typparameter abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="f4a11-206">`[FromBody]` is inferred for complex type parameters.</span></span> <span data-ttu-id="f4a11-207">Jeder komplexe integrierte Typ mit spezieller Bedeutung, z. B. <xref:Microsoft.AspNetCore.Http.IFormCollection> und <xref:System.Threading.CancellationToken>, stellt eine Ausnahme von der `[FromBody]`-Rückschlussregel dar.</span><span class="sxs-lookup"><span data-stu-id="f4a11-207">An exception to the `[FromBody]` inference rule is any complex, built-in type with a special meaning, such as <xref:Microsoft.AspNetCore.Http.IFormCollection> and <xref:System.Threading.CancellationToken>.</span></span> <span data-ttu-id="f4a11-208">Der Rückschlusscode der Bindungsquelle ignoriert diese Typen.</span><span class="sxs-lookup"><span data-stu-id="f4a11-208">The binding source inference code ignores those special types.</span></span> 
* <span data-ttu-id="f4a11-209">`[FromForm]` wird für Aktionsparameter des Typs <xref:Microsoft.AspNetCore.Http.IFormFile> und <xref:Microsoft.AspNetCore.Http.IFormFileCollection> abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="f4a11-209">`[FromForm]` is inferred for action parameters of type <xref:Microsoft.AspNetCore.Http.IFormFile> and <xref:Microsoft.AspNetCore.Http.IFormFileCollection>.</span></span> <span data-ttu-id="f4a11-210">Es wird für keine einfachen oder benutzerdefinierte Typen abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="f4a11-210">It's not inferred for any simple or user-defined types.</span></span>
* <span data-ttu-id="f4a11-211">`[FromRoute]` wird für jeden Namen von Aktionsparametern abgeleitet, der mit einem Parameter in der Routenvorlage übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="f4a11-211">`[FromRoute]` is inferred for any action parameter name matching a parameter in the route template.</span></span> <span data-ttu-id="f4a11-212">Wenn mehr als eine Route mit einem Aktionsparameter übereinstimmt, gilt jeder Routenwert als `[FromRoute]`.</span><span class="sxs-lookup"><span data-stu-id="f4a11-212">When more than one route matches an action parameter, any route value is considered `[FromRoute]`.</span></span>
* <span data-ttu-id="f4a11-213">`[FromQuery]` wird für alle anderen Aktionsparameter abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="f4a11-213">`[FromQuery]` is inferred for any other action parameters.</span></span>

### <a name="frombody-inference-notes"></a><span data-ttu-id="f4a11-214">Hinweise zum FromBody-Rückschluss</span><span class="sxs-lookup"><span data-stu-id="f4a11-214">FromBody inference notes</span></span>

<span data-ttu-id="f4a11-215">`[FromBody]` wird für einfache Typen wie `string` oder `int` nicht abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="f4a11-215">`[FromBody]` isn't inferred for simple types such as `string` or `int`.</span></span> <span data-ttu-id="f4a11-216">Deshalb sollte das `[FromBody]`-Attribut für einfache Typen verwendet werden, wenn diese Funktionsweise benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="f4a11-216">Therefore, the `[FromBody]` attribute should be used for simple types when that functionality is needed.</span></span>

<span data-ttu-id="f4a11-217">Wenn an eine Aktion mehr als ein Parameter aus dem Anforderungstext gebunden ist, wird eine Ausnahme ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="f4a11-217">When an action has more than one parameter bound from the request body, an exception is thrown.</span></span> <span data-ttu-id="f4a11-218">Beispielsweise lösen alle Signaturen der folgenden Aktionsmethoden eine Ausnahme aus:</span><span class="sxs-lookup"><span data-stu-id="f4a11-218">For example, all of the following action method signatures cause an exception:</span></span>

* <span data-ttu-id="f4a11-219">`[FromBody]` wird für beide abgeleitet, da sie komplexe Typen darstellen.</span><span class="sxs-lookup"><span data-stu-id="f4a11-219">`[FromBody]` inferred on both because they're complex types.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action1(Product product, Order order)
  ```

* <span data-ttu-id="f4a11-220">Das `[FromBody]`-Attribut für einen Typ wird für den anderen abgeleitet, da es sich um einen komplexen Typ handelt.</span><span class="sxs-lookup"><span data-stu-id="f4a11-220">`[FromBody]` attribute on one, inferred on the other because it's a complex type.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action2(Product product, [FromBody] Order order)
  ```

* <span data-ttu-id="f4a11-221">Das `[FromBody]`-Attribut für beide.</span><span class="sxs-lookup"><span data-stu-id="f4a11-221">`[FromBody]` attribute on both.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action3([FromBody] Product product, [FromBody] Order order)
  ```

> [!NOTE]
> <span data-ttu-id="f4a11-222">In ASP.NET Core 2.1 werden Sammlungstypparameter wie Listen und Arrays fälschlicherweise als `[FromQuery]` abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="f4a11-222">In ASP.NET Core 2.1, collection type parameters such as lists and arrays are incorrectly inferred as `[FromQuery]`.</span></span> <span data-ttu-id="f4a11-223">Für diese Parameter sollte das `[FromBody]`-Attribut verwendet werden, wenn sie aus dem Anforderungstext gebunden werden sollen.</span><span class="sxs-lookup"><span data-stu-id="f4a11-223">The `[FromBody]` attribute should be used for these parameters if they are to be bound from the request body.</span></span> <span data-ttu-id="f4a11-224">Dieses Verhalten wurde bei ASP.NET Core 2.2 oder höher behoben. Hier werden Sammlungstypparameter standardmäßig als „[FromBody]“ abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="f4a11-224">This behavior is corrected in ASP.NET Core 2.2 or later, where collection type parameters are inferred to be bound from the body by default.</span></span>

### <a name="disable-inference-rules"></a><span data-ttu-id="f4a11-225">Deaktivieren von Rückschlussregeln</span><span class="sxs-lookup"><span data-stu-id="f4a11-225">Disable inference rules</span></span>

<span data-ttu-id="f4a11-226">Legen Sie <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> auf `true` fest, um den Rückschluss auf Bindungsquellen zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="f4a11-226">To disable binding source inference, set <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> to `true`.</span></span> <span data-ttu-id="f4a11-227">Fügen Sie den folgenden Code zu `Startup.ConfigureServices` hinter `services.AddMvc().SetCompatibilityVersion` hinzu:</span><span class="sxs-lookup"><span data-stu-id="f4a11-227">Add the following code in `Startup.ConfigureServices` after `services.AddMvc().SetCompatibilityVersion`:</span></span>

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,6)]

## <a name="multipartform-data-request-inference"></a><span data-ttu-id="f4a11-228">Ableiten der Multipart/form-data-Anforderung</span><span class="sxs-lookup"><span data-stu-id="f4a11-228">Multipart/form-data request inference</span></span>

<span data-ttu-id="f4a11-229">Das `[ApiController]`-Attribut wendet eine Rückschlussregel an, wenn ein Aktionsparameter mit dem [[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)-Attribut kommentiert ist: Der Inhaltstyp der `multipart/form-data`-Anforderung wird abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="f4a11-229">The `[ApiController]` attribute applies an inference rule when an action parameter is annotated with the [[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) attribute: the `multipart/form-data` request content type is inferred.</span></span>

<span data-ttu-id="f4a11-230">Um das Standardverhalten zu deaktivieren, legen Sie <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> auf `true` in `Startup.ConfigureServices` fest, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="f4a11-230">To disable the default behavior, set <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters>  to `true` in `Startup.ConfigureServices`, as shown in the following example:</span></span>

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,5)]

## <a name="problem-details-for-error-status-codes"></a><span data-ttu-id="f4a11-231">Problemdetails für Fehlerstatuscodes</span><span class="sxs-lookup"><span data-stu-id="f4a11-231">Problem details for error status codes</span></span>

<span data-ttu-id="f4a11-232">Bei Kompatibilitätsversion 2.2 oder höher wandelt MVC ein Fehlerergebnis (ein Ergebnis mit dem Statuscode 400 oder höher) in ein Ergebnis mit <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> um.</span><span class="sxs-lookup"><span data-stu-id="f4a11-232">When the compatibility version is 2.2 or later, MVC transforms an error result (a result with status code 400 or higher) to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span> <span data-ttu-id="f4a11-233">Die `ProblemDetails`-Typ basiert auf der [RFC 7807-Spezifikation](https://tools.ietf.org/html/rfc7807) für die Bereitstellung maschinenlesbarer Fehlerdetails in einer HTTP-Antwort.</span><span class="sxs-lookup"><span data-stu-id="f4a11-233">The `ProblemDetails` type is based on the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807) for providing machine-readable error details in an HTTP response.</span></span>

<span data-ttu-id="f4a11-234">Betrachten Sie den folgenden Code in einer Controlleraktion:</span><span class="sxs-lookup"><span data-stu-id="f4a11-234">Consider the following code in a controller action:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_ProblemDetailsStatusCode)]

<span data-ttu-id="f4a11-235">Die HTTP-Antwort für `NotFound` hat den Statuscode 404 mit einem `ProblemDetails`-Körper.</span><span class="sxs-lookup"><span data-stu-id="f4a11-235">The HTTP response for `NotFound` has a 404 status code with a `ProblemDetails` body.</span></span> <span data-ttu-id="f4a11-236">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f4a11-236">For example:</span></span>

```json
{
    type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
    title: "Not Found",
    status: 404,
    traceId: "0HLHLV31KRN83:00000001"
}
```

### <a name="customize-problemdetails-response"></a><span data-ttu-id="f4a11-237">Anpassen der ProblemDetails-Antwort</span><span class="sxs-lookup"><span data-stu-id="f4a11-237">Customize ProblemDetails response</span></span>

<span data-ttu-id="f4a11-238">Verwenden Sie die `ClientErrorMapping`-Eigenschaft zum Konfigurieren des Inhalts der `ProblemDetails`-Antwort.</span><span class="sxs-lookup"><span data-stu-id="f4a11-238">Use the `ClientErrorMapping` property to configure the contents of the `ProblemDetails` response.</span></span> <span data-ttu-id="f4a11-239">Der folgenden Code aktualisiert beispielsweise die `type`-Eigenschaft für 404-Antworten:</span><span class="sxs-lookup"><span data-stu-id="f4a11-239">For example, the following code updates the `type` property for 404 responses:</span></span>

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=10-11)]

### <a name="disable-problemdetails-response"></a><span data-ttu-id="f4a11-240">Deaktivieren der ProblemDetails-Antwort</span><span class="sxs-lookup"><span data-stu-id="f4a11-240">Disable ProblemDetails response</span></span>

<span data-ttu-id="f4a11-241">Die automatische Erstellung von `ProblemDetails` wird deaktiviert, wenn die `SuppressMapClientErrors`-Eigenschaft auf `true` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="f4a11-241">The automatic creation of `ProblemDetails` is disabled when the `SuppressMapClientErrors` property is set to `true`.</span></span> <span data-ttu-id="f4a11-242">Fügen Sie den folgenden Code zu `Startup.ConfigureServices` hinzu:</span><span class="sxs-lookup"><span data-stu-id="f4a11-242">Add the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,8)]

## <a name="additional-resources"></a><span data-ttu-id="f4a11-243">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="f4a11-243">Additional resources</span></span> 

* <xref:web-api/action-return-types>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>

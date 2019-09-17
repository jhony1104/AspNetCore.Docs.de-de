---
title: Erstellen von Web-APIs mit ASP.NET Core
author: scottaddie
description: Erfahren Sie mehr über die Grundlagen zum Erstellen einer Web-API in ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 09/12/2019
uid: web-api/index
ms.openlocfilehash: 6e1868690a2c384307a23c89467505d3ed8916db
ms.sourcegitcommit: 805f625d16d74e77f02f5f37326e5aceafcb78e3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2019
ms.locfileid: "70985463"
---
# <a name="create-web-apis-with-aspnet-core"></a><span data-ttu-id="9eb81-103">Erstellen von Web-APIs mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9eb81-103">Create web APIs with ASP.NET Core</span></span>

<span data-ttu-id="9eb81-104">Von [Scott Addie](https://github.com/scottaddie) und [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="9eb81-104">By [Scott Addie](https://github.com/scottaddie) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="9eb81-105">ASP.NET Core unterstützt das Erstellen von RESTful-Diensten, die auch als Web-APIs bezeichnet werden, unter Verwendung von C#.</span><span class="sxs-lookup"><span data-stu-id="9eb81-105">ASP.NET Core supports creating RESTful services, also known as web APIs, using C#.</span></span> <span data-ttu-id="9eb81-106">Eine Web-API verwendet Controller, um Anforderungen zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="9eb81-106">To handle requests, a web API uses controllers.</span></span> <span data-ttu-id="9eb81-107">*Controller* in einer Web-API sind von `ControllerBase` abgeleitete Klassen.</span><span class="sxs-lookup"><span data-stu-id="9eb81-107">*Controllers* in a web API are classes that derive from `ControllerBase`.</span></span> <span data-ttu-id="9eb81-108">In diesem Artikel wird beschrieben, wie Sie Controller für die Verarbeitung von Web-API-Anforderungen verwenden.</span><span class="sxs-lookup"><span data-stu-id="9eb81-108">This article shows how to use controllers for handling web API requests.</span></span>

<span data-ttu-id="9eb81-109">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples)</span><span class="sxs-lookup"><span data-stu-id="9eb81-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples).</span></span> <span data-ttu-id="9eb81-110">([Informationen zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9eb81-110">([How to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="controllerbase-class"></a><span data-ttu-id="9eb81-111">ControllerBase-Klasse</span><span class="sxs-lookup"><span data-stu-id="9eb81-111">ControllerBase class</span></span>

<span data-ttu-id="9eb81-112">Eine Web-API besteht aus mindestens einer Controllerklasse, die von <xref:Microsoft.AspNetCore.Mvc.ControllerBase> abgeleitet ist.</span><span class="sxs-lookup"><span data-stu-id="9eb81-112">A web API consists of one or more controller classes that derive from <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="9eb81-113">Die Web-API-Projektvorlage stellt einen Startercontroller bereit:</span><span class="sxs-lookup"><span data-stu-id="9eb81-113">The web API project template provides a starter controller:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

<span data-ttu-id="9eb81-114">Erstellen Sie einen Web-API-Controller nicht durch Ableitung aus der <xref:Microsoft.AspNetCore.Mvc.Controller>-Klasse.</span><span class="sxs-lookup"><span data-stu-id="9eb81-114">Don't create a web API controller by deriving from the <xref:Microsoft.AspNetCore.Mvc.Controller> class.</span></span> <span data-ttu-id="9eb81-115">`Controller` wird von `ControllerBase` abgeleitet und fügt Unterstützung für Ansichten hinzu, wird also für die Verarbeitung von Webseiten verwendet und nicht für Web-API-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="9eb81-115">`Controller` derives from `ControllerBase` and adds support for views, so it's for handling web pages, not web API requests.</span></span> <span data-ttu-id="9eb81-116">Es gibt eine Ausnahme von dieser Regel: Wenn Sie denselben Controller sowohl für Ansichten als auch für Web-APIs verwenden möchten, leiten Sie ihn von `Controller` ab.</span><span class="sxs-lookup"><span data-stu-id="9eb81-116">There's an exception to this rule: if you plan to use the same controller for both views and web APIs, derive it from `Controller`.</span></span>

<span data-ttu-id="9eb81-117">Die `ControllerBase`-Klasse bietet viele Eigenschaften und Methoden, die für die Verarbeitung von HTTP-Anforderungen hilfreich sind.</span><span class="sxs-lookup"><span data-stu-id="9eb81-117">The `ControllerBase` class provides many properties and methods that are useful for handling HTTP requests.</span></span> <span data-ttu-id="9eb81-118">`ControllerBase.CreatedAtAction` gibt beispielsweise Statuscode 201 zurück:</span><span class="sxs-lookup"><span data-stu-id="9eb81-118">For example, `ControllerBase.CreatedAtAction` returns a 201 status code:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_400And201&highlight=10)]

<span data-ttu-id="9eb81-119">Hier einige weitere Beispiele für die von `ControllerBase` bereitgestellten Methoden.</span><span class="sxs-lookup"><span data-stu-id="9eb81-119">Here are some more examples of methods that `ControllerBase` provides.</span></span>

|<span data-ttu-id="9eb81-120">Methode</span><span class="sxs-lookup"><span data-stu-id="9eb81-120">Method</span></span>   |<span data-ttu-id="9eb81-121">Hinweise</span><span class="sxs-lookup"><span data-stu-id="9eb81-121">Notes</span></span>    |
|---------|---------|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>| <span data-ttu-id="9eb81-122">Gibt Statuscode 400 zurück.</span><span class="sxs-lookup"><span data-stu-id="9eb81-122">Returns 400 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>|<span data-ttu-id="9eb81-123">Gibt Statuscode 404 zurück.</span><span class="sxs-lookup"><span data-stu-id="9eb81-123">Returns 404 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.PhysicalFile*>|<span data-ttu-id="9eb81-124">Gibt eine Datei zurück.</span><span class="sxs-lookup"><span data-stu-id="9eb81-124">Returns a file.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>|<span data-ttu-id="9eb81-125">Ruft die [Modellbindung](xref:mvc/models/model-binding) auf.</span><span class="sxs-lookup"><span data-stu-id="9eb81-125">Invokes [model binding](xref:mvc/models/model-binding).</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryValidateModel*>|<span data-ttu-id="9eb81-126">Ruft die [Modellvalidierung](xref:mvc/models/validation) auf.</span><span class="sxs-lookup"><span data-stu-id="9eb81-126">Invokes [model validation](xref:mvc/models/validation).</span></span>|

<span data-ttu-id="9eb81-127">Eine Liste aller verfügbaren Methoden und Eigenschaften finden Sie unter <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span><span class="sxs-lookup"><span data-stu-id="9eb81-127">For a list of all available methods and properties, see <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span>

## <a name="attributes"></a><span data-ttu-id="9eb81-128">Attribute</span><span class="sxs-lookup"><span data-stu-id="9eb81-128">Attributes</span></span>

<span data-ttu-id="9eb81-129">Der <xref:Microsoft.AspNetCore.Mvc>-Namespace enthält Attribute, mit denen das Verhalten von Web-API-Controllern und Aktionsmethoden konfiguriert werden kann.</span><span class="sxs-lookup"><span data-stu-id="9eb81-129">The <xref:Microsoft.AspNetCore.Mvc> namespace provides attributes that can be used to configure the behavior of web API controllers and action methods.</span></span> <span data-ttu-id="9eb81-130">Im folgenden Beispiel werden Attribute verwendet, um das unterstützte HTTP-Aktionsverb und alle bekannten HTTP-Statuscodes, die zurückgegeben werden können, anzugeben:</span><span class="sxs-lookup"><span data-stu-id="9eb81-130">The following example uses attributes to specify the supported HTTP action verb and any known HTTP status codes that could be returned:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_400And201&highlight=1-3)]

<span data-ttu-id="9eb81-131">Hier einige weitere Beispiele für die verfügbaren Attribute.</span><span class="sxs-lookup"><span data-stu-id="9eb81-131">Here are some more examples of attributes that are available.</span></span>

|<span data-ttu-id="9eb81-132">Attribut</span><span class="sxs-lookup"><span data-stu-id="9eb81-132">Attribute</span></span>|<span data-ttu-id="9eb81-133">Hinweise</span><span class="sxs-lookup"><span data-stu-id="9eb81-133">Notes</span></span>|
|---------|-----|
|<span data-ttu-id="9eb81-134">[[Route]](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)</span><span class="sxs-lookup"><span data-stu-id="9eb81-134">[[Route]](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)</span></span>      |<span data-ttu-id="9eb81-135">Gibt ein URL-Muster für einen Controller oder eine Aktion an.</span><span class="sxs-lookup"><span data-stu-id="9eb81-135">Specifies URL pattern for a controller or action.</span></span>|
|<span data-ttu-id="9eb81-136">[[Bind]](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)</span><span class="sxs-lookup"><span data-stu-id="9eb81-136">[[Bind]](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)</span></span>        |<span data-ttu-id="9eb81-137">Gibt Präfixe und Eigenschaften an, die in die Modellbindung einbezogen werden sollen.</span><span class="sxs-lookup"><span data-stu-id="9eb81-137">Specifies prefix and properties to include for model binding.</span></span>|
|<span data-ttu-id="9eb81-138">[[HttpGet]](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)</span><span class="sxs-lookup"><span data-stu-id="9eb81-138">[[HttpGet]](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)</span></span>  |<span data-ttu-id="9eb81-139">Identifiziert eine Aktion, die das HTTP GET-Aktionsverb unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9eb81-139">Identifies an action that supports the HTTP GET action verb.</span></span>|
|<span data-ttu-id="9eb81-140">[[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)</span><span class="sxs-lookup"><span data-stu-id="9eb81-140">[[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)</span></span>|<span data-ttu-id="9eb81-141">Gibt die von einer Aktion akzeptierten Datentypen an.</span><span class="sxs-lookup"><span data-stu-id="9eb81-141">Specifies data types that an action accepts.</span></span>|
|<span data-ttu-id="9eb81-142">[[Produces]](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)</span><span class="sxs-lookup"><span data-stu-id="9eb81-142">[[Produces]](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)</span></span>|<span data-ttu-id="9eb81-143">Gibt die von einer Aktion zurückgegebenen Datentypen an.</span><span class="sxs-lookup"><span data-stu-id="9eb81-143">Specifies data types that an action returns.</span></span>|

<span data-ttu-id="9eb81-144">Eine Liste mit den verfügbaren Attributen finden Sie im <xref:Microsoft.AspNetCore.Mvc>-Namespace.</span><span class="sxs-lookup"><span data-stu-id="9eb81-144">For a list that includes the available attributes, see the <xref:Microsoft.AspNetCore.Mvc> namespace.</span></span>

## <a name="apicontroller-attribute"></a><span data-ttu-id="9eb81-145">ApiController-Attribut</span><span class="sxs-lookup"><span data-stu-id="9eb81-145">ApiController attribute</span></span>

<span data-ttu-id="9eb81-146">Das [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)-Attribut kann auf eine Controllerklasse angewendet werden, um folgende API-spezifische Verhalten mit einigen vordefinierten Konfigurationen zu aktivieren:</span><span class="sxs-lookup"><span data-stu-id="9eb81-146">The [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute can be applied to a controller class to enable the following opinionated, API-specific behaviors:</span></span>

* [<span data-ttu-id="9eb81-147">Anforderung für das Attributrouting</span><span class="sxs-lookup"><span data-stu-id="9eb81-147">Attribute routing requirement</span></span>](#attribute-routing-requirement)
* [<span data-ttu-id="9eb81-148">Automatische HTTP 400-Antworten</span><span class="sxs-lookup"><span data-stu-id="9eb81-148">Automatic HTTP 400 responses</span></span>](#automatic-http-400-responses)
* [<span data-ttu-id="9eb81-149">Rückschluss auf Bindungsquellparameter</span><span class="sxs-lookup"><span data-stu-id="9eb81-149">Binding source parameter inference</span></span>](#binding-source-parameter-inference)
* [<span data-ttu-id="9eb81-150">Ableiten der Multipart/form-data-Anforderung</span><span class="sxs-lookup"><span data-stu-id="9eb81-150">Multipart/form-data request inference</span></span>](#multipartform-data-request-inference)
* [<span data-ttu-id="9eb81-151">Problemdetails für Fehlerstatuscodes</span><span class="sxs-lookup"><span data-stu-id="9eb81-151">Problem details for error status codes</span></span>](#problem-details-for-error-status-codes)

<span data-ttu-id="9eb81-152">Diese Features erfordern mindestens [Kompatibilitätsversion](xref:mvc/compatibility-version) 2.1.</span><span class="sxs-lookup"><span data-stu-id="9eb81-152">These features require a [compatibility version](xref:mvc/compatibility-version) of 2.1 or later.</span></span>

### <a name="attribute-on-specific-controllers"></a><span data-ttu-id="9eb81-153">Attribut für bestimmte Controller</span><span class="sxs-lookup"><span data-stu-id="9eb81-153">Attribute on specific controllers</span></span>

<span data-ttu-id="9eb81-154">Das `[ApiController]`-Attribut kann, wie im folgenden Beispiel der Projektvorlage ersichtlich, auf bestimmte Controller angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="9eb81-154">The `[ApiController]` attribute can be applied to specific controllers, as in the following example from the project template:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2])]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=2)]

::: moniker-end

### <a name="attribute-on-multiple-controllers"></a><span data-ttu-id="9eb81-155">Attribut für mehrere Controller</span><span class="sxs-lookup"><span data-stu-id="9eb81-155">Attribute on multiple controllers</span></span>

<span data-ttu-id="9eb81-156">Eine Möglichkeit, das Attribut für mehr als einen Controller zu verwenden, besteht darin, eine benutzerdefinierte Basiscontrollerklasse zu erstellen, die mit dem `[ApiController]`-Attribut kommentiert ist.</span><span class="sxs-lookup"><span data-stu-id="9eb81-156">One approach to using the attribute on more than one controller is to create a custom base controller class annotated with the `[ApiController]` attribute.</span></span> <span data-ttu-id="9eb81-157">Im folgenden Beispiel wird eine benutzerdefinierte Basisklasse und ein Controller, der von ihr abgeleitet wird, gezeigt:</span><span class="sxs-lookup"><span data-stu-id="9eb81-157">The following example shows a custom base class and a controller that derives from it:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/MyControllerBase.cs?name=snippet_MyControllerBase)]

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="attribute-on-an-assembly"></a><span data-ttu-id="9eb81-158">Attribut für eine Assembly</span><span class="sxs-lookup"><span data-stu-id="9eb81-158">Attribute on an assembly</span></span>

<span data-ttu-id="9eb81-159">Wenn die [Kompatibilitätsversion](xref:mvc/compatibility-version) auf 2.2 oder höher festgelegt ist, kann das `[ApiController]`-Attribut auf eine Assembly angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="9eb81-159">If [compatibility version](xref:mvc/compatibility-version) is set to 2.2 or later, the `[ApiController]` attribute can be applied to an assembly.</span></span> <span data-ttu-id="9eb81-160">Auf diese Weise wendet die Anmerkung das Web-API-Verhalten auf alle Controller in der Assembly an.</span><span class="sxs-lookup"><span data-stu-id="9eb81-160">Annotation in this manner applies web API behavior to all controllers in the assembly.</span></span> <span data-ttu-id="9eb81-161">Es gibt keine Möglichkeit, einzelne Controller auszuschließen.</span><span class="sxs-lookup"><span data-stu-id="9eb81-161">There's no way to opt out for individual controllers.</span></span> <span data-ttu-id="9eb81-162">Wenden Sie das Attribut auf Assemblyebene auf die Namespacedeklaration an, die die `Startup`-Klasse umgibt:</span><span class="sxs-lookup"><span data-stu-id="9eb81-162">Apply the assembly-level attribute to the namespace declaration surrounding the `Startup` class:</span></span>

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

::: moniker-end

## <a name="attribute-routing-requirement"></a><span data-ttu-id="9eb81-163">Anforderung für das Attributrouting</span><span class="sxs-lookup"><span data-stu-id="9eb81-163">Attribute routing requirement</span></span>

<span data-ttu-id="9eb81-164">Durch das `[ApiController]`-Attribut wird das Attributrouting zu einer Anforderung.</span><span class="sxs-lookup"><span data-stu-id="9eb81-164">The `[ApiController]` attribute makes attribute routing a requirement.</span></span> <span data-ttu-id="9eb81-165">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9eb81-165">For example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2)]

<span data-ttu-id="9eb81-166">Sie können über [konventionelle Routen](xref:mvc/controllers/routing#conventional-routing), die durch `UseEndpoints`, <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> oder <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> in `Startup.Configure` definiert sind, nicht auf Aktionen zugreifen.</span><span class="sxs-lookup"><span data-stu-id="9eb81-166">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined by `UseEndpoints`, <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>, or <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> in `Startup.Configure`.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=1)]

<span data-ttu-id="9eb81-167">Sie können über [konventionelle Routen](xref:mvc/controllers/routing#conventional-routing), die durch <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> oder <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> in `Startup.Configure` definiert sind, nicht auf Aktionen zugreifen.</span><span class="sxs-lookup"><span data-stu-id="9eb81-167">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined by <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> or <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> in `Startup.Configure`.</span></span>

::: moniker-end

## <a name="automatic-http-400-responses"></a><span data-ttu-id="9eb81-168">Automatische HTTP 400-Antwort</span><span class="sxs-lookup"><span data-stu-id="9eb81-168">Automatic HTTP 400 responses</span></span>

<span data-ttu-id="9eb81-169">Durch das `[ApiController]`-Attribut wird bei Modellvalidierungsfehlern automatisch eine HTTP 400-Antwort ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="9eb81-169">The `[ApiController]` attribute makes model validation errors automatically trigger an HTTP 400 response.</span></span> <span data-ttu-id="9eb81-170">Deshalb ist der folgende Code in einer Aktionsmethode überflüssig:</span><span class="sxs-lookup"><span data-stu-id="9eb81-170">Consequently, the following code is unnecessary in an action method:</span></span>

```csharp
if (!ModelState.IsValid)
{
    return BadRequest(ModelState);
}
```

<span data-ttu-id="9eb81-171">ASP.NET Core MVC verwendet den <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter>-Aktionsfilter, um die vorherige Überprüfung durchzuführen.</span><span class="sxs-lookup"><span data-stu-id="9eb81-171">ASP.NET Core MVC uses the <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter> action filter to do the preceding check.</span></span>

### <a name="default-badrequest-response"></a><span data-ttu-id="9eb81-172">Standardmäßige BadRequest-Antwort</span><span class="sxs-lookup"><span data-stu-id="9eb81-172">Default BadRequest response</span></span> 

<span data-ttu-id="9eb81-173">Bei Kompatibilitätsversion 2.1 wird für eine HTTP 400-Antwort standardmäßig der Antworttyp <xref:Microsoft.AspNetCore.Mvc.SerializableError> verwendet.</span><span class="sxs-lookup"><span data-stu-id="9eb81-173">With a compatibility version of 2.1, the default response type for an HTTP 400 response is <xref:Microsoft.AspNetCore.Mvc.SerializableError>.</span></span> <span data-ttu-id="9eb81-174">Der folgende Anforderungstext ist ein Beispiel für den serialisierten Typ:</span><span class="sxs-lookup"><span data-stu-id="9eb81-174">The following request body is an example of the serialized type:</span></span>

```json
{
  "": [
    "A non-empty request body is required."
  ]
}
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="9eb81-175">Bei Kompatibilitätsversion 2.2 oder höher wird für eine HTTP 400-Antwort standardmäßig der Antworttyp <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> verwendet.</span><span class="sxs-lookup"><span data-stu-id="9eb81-175">With a compatibility version of 2.2 or later, the default response type for an HTTP 400 response is <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="9eb81-176">Der folgende Anforderungstext ist ein Beispiel für den serialisierten Typ:</span><span class="sxs-lookup"><span data-stu-id="9eb81-176">The following request body is an example of the serialized type:</span></span>

```json
{
  "type": "https://tools.ietf.org/html/rfc7231#section-6.5.1",
  "title": "One or more validation errors occurred.",
  "status": 400,
  "traceId": "|7fb5e16a-4c8f23bbfc974667.",
  "errors": {
    "": [
      "A non-empty request body is required."
    ]
  }
}
```

<span data-ttu-id="9eb81-177">Der `ValidationProblemDetails`-Typ:</span><span class="sxs-lookup"><span data-stu-id="9eb81-177">The `ValidationProblemDetails` type:</span></span>

* <span data-ttu-id="9eb81-178">Bietet ein vom Computer lesbares Format zum Angeben von Fehlern in Web-API-Antworten.</span><span class="sxs-lookup"><span data-stu-id="9eb81-178">Provides a machine-readable format for specifying errors in web API responses.</span></span>
* <span data-ttu-id="9eb81-179">Entspricht der [RFC 7807-Spezifikation](https://tools.ietf.org/html/rfc7807).</span><span class="sxs-lookup"><span data-stu-id="9eb81-179">Complies with the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807).</span></span>

<span data-ttu-id="9eb81-180">Wenden Sie die in `Startup.ConfigureServices` hervorgehobenen Änderungen an, um den Standardantworttyp in `SerializableError` zu ändern.</span><span class="sxs-lookup"><span data-stu-id="9eb81-180">To change the default response type to `SerializableError`, apply the highlighted changes in `Startup.ConfigureServices`:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=4-13)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=5-14)]

::: moniker-end

### <a name="customize-badrequest-response"></a><span data-ttu-id="9eb81-181">Anpassen der BadRequest-Antwort</span><span class="sxs-lookup"><span data-stu-id="9eb81-181">Customize BadRequest response</span></span>

<span data-ttu-id="9eb81-182">Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory>, um die aus einem Validierungsfehler resultierende Antwort anzupassen.</span><span class="sxs-lookup"><span data-stu-id="9eb81-182">To customize the response that results from a validation error, use <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory>.</span></span> <span data-ttu-id="9eb81-183">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9eb81-183">For example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureBadRequestResponse&highlight=4-20)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureBadRequestResponse&highlight=5-21)]

::: moniker-end

### <a name="log-automatic-400-responses"></a><span data-ttu-id="9eb81-184">Protokollieren automatischer 400-Antworten</span><span class="sxs-lookup"><span data-stu-id="9eb81-184">Log automatic 400 responses</span></span>

<span data-ttu-id="9eb81-185">Informationen finden Sie unter [How to log automatic 400 responses on model validation errors (aspnet/AspNetCore.Docs #12157)](https://github.com/aspnet/AspNetCore.Docs/issues/12157) (Protokollieren automatischer 400-Antworten auf Modellvalidierungsfehler, aspnet/AspNetCore.Docs #12157).</span><span class="sxs-lookup"><span data-stu-id="9eb81-185">See [How to log automatic 400 responses on model validation errors (aspnet/AspNetCore.Docs #12157)](https://github.com/aspnet/AspNetCore.Docs/issues/12157).</span></span>

### <a name="disable-automatic-400-response"></a><span data-ttu-id="9eb81-186">Deaktivieren automatischer 400-Antwort</span><span class="sxs-lookup"><span data-stu-id="9eb81-186">Disable automatic 400 response</span></span>

<span data-ttu-id="9eb81-187">Um das automatische Verhalten bei Statuscode 400 zu deaktivieren, legen Sie die <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter>-Eigenschaft auf `true` fest.</span><span class="sxs-lookup"><span data-stu-id="9eb81-187">To disable the automatic 400 behavior, set the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> property to `true`.</span></span> <span data-ttu-id="9eb81-188">Fügen Sie folgenden hervorgehobenen Code in `Startup.ConfigureServices` hinzu:</span><span class="sxs-lookup"><span data-stu-id="9eb81-188">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,6)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,7)]

::: moniker-end

## <a name="binding-source-parameter-inference"></a><span data-ttu-id="9eb81-189">Rückschluss auf Bindungsquellparameter</span><span class="sxs-lookup"><span data-stu-id="9eb81-189">Binding source parameter inference</span></span>

<span data-ttu-id="9eb81-190">Ein Bindungsquellenattribut definiert den Speicherort, an dem der Wert eines Aktionsparameters vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="9eb81-190">A binding source attribute defines the location at which an action parameter's value is found.</span></span> <span data-ttu-id="9eb81-191">Es gibt die folgenden Bindungsquellattribute:</span><span class="sxs-lookup"><span data-stu-id="9eb81-191">The following binding source attributes exist:</span></span>

|<span data-ttu-id="9eb81-192">Attribut</span><span class="sxs-lookup"><span data-stu-id="9eb81-192">Attribute</span></span>|<span data-ttu-id="9eb81-193">Bindungsquelle</span><span class="sxs-lookup"><span data-stu-id="9eb81-193">Binding source</span></span> |
|---------|---------|
|<span data-ttu-id="9eb81-194">[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)</span><span class="sxs-lookup"><span data-stu-id="9eb81-194">[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)</span></span>     | <span data-ttu-id="9eb81-195">Anforderungstext</span><span class="sxs-lookup"><span data-stu-id="9eb81-195">Request body</span></span> |
|<span data-ttu-id="9eb81-196">[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)</span><span class="sxs-lookup"><span data-stu-id="9eb81-196">[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)</span></span>     | <span data-ttu-id="9eb81-197">Formulardaten im Anforderungstext</span><span class="sxs-lookup"><span data-stu-id="9eb81-197">Form data in the request body</span></span> |
|<span data-ttu-id="9eb81-198">[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute)</span><span class="sxs-lookup"><span data-stu-id="9eb81-198">[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute)</span></span> | <span data-ttu-id="9eb81-199">Anforderungsheader</span><span class="sxs-lookup"><span data-stu-id="9eb81-199">Request header</span></span> |
|<span data-ttu-id="9eb81-200">[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)</span><span class="sxs-lookup"><span data-stu-id="9eb81-200">[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)</span></span>   | <span data-ttu-id="9eb81-201">Abfragezeichenfolge-Parameter der Anforderung</span><span class="sxs-lookup"><span data-stu-id="9eb81-201">Request query string parameter</span></span> |
|<span data-ttu-id="9eb81-202">[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)</span><span class="sxs-lookup"><span data-stu-id="9eb81-202">[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)</span></span>   | <span data-ttu-id="9eb81-203">Routendaten aus aktuellen Anforderungen</span><span class="sxs-lookup"><span data-stu-id="9eb81-203">Route data from the current request</span></span> |
|<span data-ttu-id="9eb81-204">[[FromServices]](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices)</span><span class="sxs-lookup"><span data-stu-id="9eb81-204">[[FromServices]](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices)</span></span> | <span data-ttu-id="9eb81-205">Der als Aktionsparameter eingefügte Anforderungsdienst.</span><span class="sxs-lookup"><span data-stu-id="9eb81-205">The request service injected as an action parameter</span></span> |

> [!WARNING]
> <span data-ttu-id="9eb81-206">Verwenden Sie `[FromRoute]` nicht, wenn Werte möglicherweise `%2f` enthalten (d.h. `/`).</span><span class="sxs-lookup"><span data-stu-id="9eb81-206">Don't use `[FromRoute]` when values might contain `%2f` (that is `/`).</span></span> <span data-ttu-id="9eb81-207">`%2f` wird nicht durch Entfernen von Escapezeichen zu `/`.</span><span class="sxs-lookup"><span data-stu-id="9eb81-207">`%2f` won't be unescaped to `/`.</span></span> <span data-ttu-id="9eb81-208">Verwenden Sie `[FromQuery]`, wenn der Wert `%2f` enthalten könnte.</span><span class="sxs-lookup"><span data-stu-id="9eb81-208">Use `[FromQuery]` if the value might contain `%2f`.</span></span>

<span data-ttu-id="9eb81-209">Ohne das `[ApiController]`-Attribut oder Bindungsquellenattribute wie `[FromQuery]` versucht die ASP.NET Core-Runtime, die Modellbindung für komplexe Objekte zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="9eb81-209">Without the `[ApiController]` attribute or binding source attributes like `[FromQuery]`, the ASP.NET Core runtime attempts to use the complex object model binder.</span></span> <span data-ttu-id="9eb81-210">Bei der Modellbindung für komplexe Objekte werden Daten aus Wertanbietern in einer festgelegten Reihenfolge abgerufen.</span><span class="sxs-lookup"><span data-stu-id="9eb81-210">The complex object model binder pulls data from value providers in a defined order.</span></span>

<span data-ttu-id="9eb81-211">Im folgenden Beispiel gibt das `[FromQuery]`-Attribut an, dass der Parameterwert `discontinuedOnly` in der Abfragezeichenfolge der Anforderungs-URL bereitgestellt wird:</span><span class="sxs-lookup"><span data-stu-id="9eb81-211">In the following example, the `[FromQuery]` attribute indicates that the `discontinuedOnly` parameter value is provided in the request URL's query string:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

<span data-ttu-id="9eb81-212">Das `[ApiController]`-Attribut wendet Rückschlussregeln auf die Standarddatenquellen von Aktionsparametern an.</span><span class="sxs-lookup"><span data-stu-id="9eb81-212">The `[ApiController]` attribute applies inference rules for the default data sources of action parameters.</span></span> <span data-ttu-id="9eb81-213">Da durch diese Regeln Attribute auf die Aktionsparameter angewendet werden, müssen Sie Bindungsquellen nicht manuell identifizieren.</span><span class="sxs-lookup"><span data-stu-id="9eb81-213">These rules save you from having to identify binding sources manually by applying attributes to the action parameters.</span></span> <span data-ttu-id="9eb81-214">Die Rückschlussregeln für Bindungsquellen verhalten sich wie folgt:</span><span class="sxs-lookup"><span data-stu-id="9eb81-214">The binding source inference rules behave as follows:</span></span>

* <span data-ttu-id="9eb81-215">`[FromBody]` wird für komplexe Typparameter abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="9eb81-215">`[FromBody]` is inferred for complex type parameters.</span></span> <span data-ttu-id="9eb81-216">Jeder komplexe integrierte Typ mit spezieller Bedeutung, z. B. <xref:Microsoft.AspNetCore.Http.IFormCollection> und <xref:System.Threading.CancellationToken>, stellt eine Ausnahme von der `[FromBody]`-Rückschlussregel dar.</span><span class="sxs-lookup"><span data-stu-id="9eb81-216">An exception to the `[FromBody]` inference rule is any complex, built-in type with a special meaning, such as <xref:Microsoft.AspNetCore.Http.IFormCollection> and <xref:System.Threading.CancellationToken>.</span></span> <span data-ttu-id="9eb81-217">Der Rückschlusscode der Bindungsquelle ignoriert diese Typen.</span><span class="sxs-lookup"><span data-stu-id="9eb81-217">The binding source inference code ignores those special types.</span></span> 
* <span data-ttu-id="9eb81-218">`[FromForm]` wird für Aktionsparameter des Typs <xref:Microsoft.AspNetCore.Http.IFormFile> und <xref:Microsoft.AspNetCore.Http.IFormFileCollection> abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="9eb81-218">`[FromForm]` is inferred for action parameters of type <xref:Microsoft.AspNetCore.Http.IFormFile> and <xref:Microsoft.AspNetCore.Http.IFormFileCollection>.</span></span> <span data-ttu-id="9eb81-219">Es wird für keine einfachen oder benutzerdefinierte Typen abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="9eb81-219">It's not inferred for any simple or user-defined types.</span></span>
* <span data-ttu-id="9eb81-220">`[FromRoute]` wird für jeden Namen von Aktionsparametern abgeleitet, der mit einem Parameter in der Routenvorlage übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="9eb81-220">`[FromRoute]` is inferred for any action parameter name matching a parameter in the route template.</span></span> <span data-ttu-id="9eb81-221">Wenn mehr als eine Route mit einem Aktionsparameter übereinstimmt, gilt jeder Routenwert als `[FromRoute]`.</span><span class="sxs-lookup"><span data-stu-id="9eb81-221">When more than one route matches an action parameter, any route value is considered `[FromRoute]`.</span></span>
* <span data-ttu-id="9eb81-222">`[FromQuery]` wird für alle anderen Aktionsparameter abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="9eb81-222">`[FromQuery]` is inferred for any other action parameters.</span></span>

### <a name="frombody-inference-notes"></a><span data-ttu-id="9eb81-223">Hinweise zum FromBody-Rückschluss</span><span class="sxs-lookup"><span data-stu-id="9eb81-223">FromBody inference notes</span></span>

<span data-ttu-id="9eb81-224">`[FromBody]` wird für einfache Typen wie `string` oder `int` nicht abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="9eb81-224">`[FromBody]` isn't inferred for simple types such as `string` or `int`.</span></span> <span data-ttu-id="9eb81-225">Deshalb sollte das `[FromBody]`-Attribut für einfache Typen verwendet werden, wenn diese Funktionsweise benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="9eb81-225">Therefore, the `[FromBody]` attribute should be used for simple types when that functionality is needed.</span></span>

<span data-ttu-id="9eb81-226">Wenn an eine Aktion mehr als ein Parameter aus dem Anforderungstext gebunden ist, wird eine Ausnahme ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="9eb81-226">When an action has more than one parameter bound from the request body, an exception is thrown.</span></span> <span data-ttu-id="9eb81-227">Beispielsweise lösen alle Signaturen der folgenden Aktionsmethoden eine Ausnahme aus:</span><span class="sxs-lookup"><span data-stu-id="9eb81-227">For example, all of the following action method signatures cause an exception:</span></span>

* <span data-ttu-id="9eb81-228">`[FromBody]` wird für beide abgeleitet, da sie komplexe Typen darstellen.</span><span class="sxs-lookup"><span data-stu-id="9eb81-228">`[FromBody]` inferred on both because they're complex types.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action1(Product product, Order order)
  ```

* <span data-ttu-id="9eb81-229">Das `[FromBody]`-Attribut für einen Typ wird für den anderen abgeleitet, da es sich um einen komplexen Typ handelt.</span><span class="sxs-lookup"><span data-stu-id="9eb81-229">`[FromBody]` attribute on one, inferred on the other because it's a complex type.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action2(Product product, [FromBody] Order order)
  ```

* <span data-ttu-id="9eb81-230">Das `[FromBody]`-Attribut für beide.</span><span class="sxs-lookup"><span data-stu-id="9eb81-230">`[FromBody]` attribute on both.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action3([FromBody] Product product, [FromBody] Order order)
  ```

::: moniker range="= aspnetcore-2.1"

> [!NOTE]
> <span data-ttu-id="9eb81-231">In ASP.NET Core 2.1 werden Sammlungstypparameter wie Listen und Arrays fälschlicherweise als `[FromQuery]` abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="9eb81-231">In ASP.NET Core 2.1, collection type parameters such as lists and arrays are incorrectly inferred as `[FromQuery]`.</span></span> <span data-ttu-id="9eb81-232">Für diese Parameter sollte das `[FromBody]`-Attribut verwendet werden, wenn sie aus dem Anforderungstext gebunden werden sollen.</span><span class="sxs-lookup"><span data-stu-id="9eb81-232">The `[FromBody]` attribute should be used for these parameters if they are to be bound from the request body.</span></span> <span data-ttu-id="9eb81-233">Dieses Verhalten wurde bei ASP.NET Core 2.2 oder höher behoben. Hier werden Sammlungstypparameter standardmäßig als „[FromBody]“ abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="9eb81-233">This behavior is corrected in ASP.NET Core 2.2 or later, where collection type parameters are inferred to be bound from the body by default.</span></span>

::: moniker-end

### <a name="disable-inference-rules"></a><span data-ttu-id="9eb81-234">Deaktivieren von Rückschlussregeln</span><span class="sxs-lookup"><span data-stu-id="9eb81-234">Disable inference rules</span></span>

<span data-ttu-id="9eb81-235">Legen Sie <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> auf `true` fest, um den Rückschluss auf Bindungsquellen zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="9eb81-235">To disable binding source inference, set <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> to `true`.</span></span> <span data-ttu-id="9eb81-236">Fügen Sie den folgenden Code zu `Startup.ConfigureServices` hinzu:</span><span class="sxs-lookup"><span data-stu-id="9eb81-236">Add the following code in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,5)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,6)]

::: moniker-end

## <a name="multipartform-data-request-inference"></a><span data-ttu-id="9eb81-237">Ableiten der Multipart/form-data-Anforderung</span><span class="sxs-lookup"><span data-stu-id="9eb81-237">Multipart/form-data request inference</span></span>

<span data-ttu-id="9eb81-238">Das `[ApiController]`-Attribut wendet eine Rückschlussregel an, wenn ein Aktionsparameter mit dem [[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)-Attribut kommentiert ist.</span><span class="sxs-lookup"><span data-stu-id="9eb81-238">The `[ApiController]` attribute applies an inference rule when an action parameter is annotated with the [[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) attribute.</span></span> <span data-ttu-id="9eb81-239">Der `multipart/form-data`-Anforderungsinhaltstyp wird abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="9eb81-239">The `multipart/form-data` request content type is inferred.</span></span>

<span data-ttu-id="9eb81-240">Legen Sie die <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters>-Eigenschaft auf `true` in `Startup.ConfigureServices` fest, um das Standardverhalten zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="9eb81-240">To disable the default behavior, set the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> property to `true` in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,5)]

::: moniker-end

## <a name="problem-details-for-error-status-codes"></a><span data-ttu-id="9eb81-241">Problemdetails für Fehlerstatuscodes</span><span class="sxs-lookup"><span data-stu-id="9eb81-241">Problem details for error status codes</span></span>

<span data-ttu-id="9eb81-242">Bei Kompatibilitätsversion 2.2 oder höher wandelt MVC ein Fehlerergebnis (ein Ergebnis mit dem Statuscode 400 oder höher) in ein Ergebnis mit <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> um.</span><span class="sxs-lookup"><span data-stu-id="9eb81-242">When the compatibility version is 2.2 or later, MVC transforms an error result (a result with status code 400 or higher) to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span> <span data-ttu-id="9eb81-243">Die `ProblemDetails`-Typ basiert auf der [RFC 7807-Spezifikation](https://tools.ietf.org/html/rfc7807) für die Bereitstellung maschinenlesbarer Fehlerdetails in einer HTTP-Antwort.</span><span class="sxs-lookup"><span data-stu-id="9eb81-243">The `ProblemDetails` type is based on the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807) for providing machine-readable error details in an HTTP response.</span></span>

<span data-ttu-id="9eb81-244">Betrachten Sie den folgenden Code in einer Controlleraktion:</span><span class="sxs-lookup"><span data-stu-id="9eb81-244">Consider the following code in a controller action:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_ProblemDetailsStatusCode)]

<span data-ttu-id="9eb81-245">Die `NotFound`-Methode erzeugt einen HTTP-404-Statuscode mit einem `ProblemDetails`-Text.</span><span class="sxs-lookup"><span data-stu-id="9eb81-245">The `NotFound` method produces an HTTP 404 status code with a `ProblemDetails` body.</span></span> <span data-ttu-id="9eb81-246">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9eb81-246">For example:</span></span>

```json
{
  type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
  title: "Not Found",
  status: 404,
  traceId: "0HLHLV31KRN83:00000001"
}
```

### <a name="customize-problemdetails-response"></a><span data-ttu-id="9eb81-247">Anpassen der ProblemDetails-Antwort</span><span class="sxs-lookup"><span data-stu-id="9eb81-247">Customize ProblemDetails response</span></span>

<span data-ttu-id="9eb81-248">Verwenden Sie die <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping*>-Eigenschaft zum Konfigurieren des Inhalts der `ProblemDetails`-Antwort.</span><span class="sxs-lookup"><span data-stu-id="9eb81-248">Use the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping*> property to configure the contents of the `ProblemDetails` response.</span></span> <span data-ttu-id="9eb81-249">Der folgenden Code aktualisiert beispielsweise die `type`-Eigenschaft für 404-Antworten:</span><span class="sxs-lookup"><span data-stu-id="9eb81-249">For example, the following code updates the `type` property for 404 responses:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=8-9)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=9-10)]

::: moniker-end

### <a name="disable-problemdetails-response"></a><span data-ttu-id="9eb81-250">Deaktivieren der ProblemDetails-Antwort</span><span class="sxs-lookup"><span data-stu-id="9eb81-250">Disable ProblemDetails response</span></span>

<span data-ttu-id="9eb81-251">Die automatische Erstellung einer `ProblemDetails`-Instanz ist deaktiviert, wenn die <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors*>-Eigenschaft auf `true` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="9eb81-251">The automatic creation of a `ProblemDetails` instance is disabled when the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors*> property is set to `true`.</span></span> <span data-ttu-id="9eb81-252">Fügen Sie den folgenden Code zu `Startup.ConfigureServices` hinzu:</span><span class="sxs-lookup"><span data-stu-id="9eb81-252">Add the following code in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,7)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,8)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="9eb81-253">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="9eb81-253">Additional resources</span></span> 

* <xref:web-api/action-return-types>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>

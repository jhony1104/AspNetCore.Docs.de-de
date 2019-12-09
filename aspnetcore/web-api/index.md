---
title: Erstellen von Web-APIs mit ASP.NET Core
author: scottaddie
description: Erfahren Sie mehr über die Grundlagen zum Erstellen einer Web-API in ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 11/22/2019
uid: web-api/index
ms.openlocfilehash: 5ef8b4d012f4ed90339ffea191612e4dc365d958
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74880529"
---
# <a name="create-web-apis-with-aspnet-core"></a><span data-ttu-id="1049e-103">Erstellen von Web-APIs mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1049e-103">Create web APIs with ASP.NET Core</span></span>

<span data-ttu-id="1049e-104">Von [Scott Addie](https://github.com/scottaddie) und [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="1049e-104">By [Scott Addie](https://github.com/scottaddie) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="1049e-105">ASP.NET Core unterstützt das Erstellen von RESTful-Diensten, die auch als Web-APIs bezeichnet werden, unter Verwendung von C#.</span><span class="sxs-lookup"><span data-stu-id="1049e-105">ASP.NET Core supports creating RESTful services, also known as web APIs, using C#.</span></span> <span data-ttu-id="1049e-106">Eine Web-API verwendet Controller, um Anforderungen zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="1049e-106">To handle requests, a web API uses controllers.</span></span> <span data-ttu-id="1049e-107">*Controller* in einer Web-API sind von `ControllerBase` abgeleitete Klassen.</span><span class="sxs-lookup"><span data-stu-id="1049e-107">*Controllers* in a web API are classes that derive from `ControllerBase`.</span></span> <span data-ttu-id="1049e-108">In diesem Artikel wird beschrieben, wie Sie Controller für die Verarbeitung von Web-API-Anforderungen verwenden.</span><span class="sxs-lookup"><span data-stu-id="1049e-108">This article shows how to use controllers for handling web API requests.</span></span>

<span data-ttu-id="1049e-109">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples)</span><span class="sxs-lookup"><span data-stu-id="1049e-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples).</span></span> <span data-ttu-id="1049e-110">([Informationen zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1049e-110">([How to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="controllerbase-class"></a><span data-ttu-id="1049e-111">ControllerBase-Klasse</span><span class="sxs-lookup"><span data-stu-id="1049e-111">ControllerBase class</span></span>

<span data-ttu-id="1049e-112">Eine Web-API besteht aus mindestens einer Controllerklasse, die von <xref:Microsoft.AspNetCore.Mvc.ControllerBase> abgeleitet ist.</span><span class="sxs-lookup"><span data-stu-id="1049e-112">A web API consists of one or more controller classes that derive from <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="1049e-113">Die Web-API-Projektvorlage stellt einen Startercontroller bereit:</span><span class="sxs-lookup"><span data-stu-id="1049e-113">The web API project template provides a starter controller:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

<span data-ttu-id="1049e-114">Erstellen Sie einen Web-API-Controller nicht durch Ableitung aus der <xref:Microsoft.AspNetCore.Mvc.Controller>-Klasse.</span><span class="sxs-lookup"><span data-stu-id="1049e-114">Don't create a web API controller by deriving from the <xref:Microsoft.AspNetCore.Mvc.Controller> class.</span></span> <span data-ttu-id="1049e-115">`Controller` wird von `ControllerBase` abgeleitet und fügt Unterstützung für Ansichten hinzu, wird also für die Verarbeitung von Webseiten verwendet und nicht für Web-API-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="1049e-115">`Controller` derives from `ControllerBase` and adds support for views, so it's for handling web pages, not web API requests.</span></span> <span data-ttu-id="1049e-116">Es gibt eine Ausnahme von dieser Regel: Wenn Sie denselben Controller sowohl für Ansichten als auch für Web-APIs verwenden möchten, leiten Sie ihn von `Controller` ab.</span><span class="sxs-lookup"><span data-stu-id="1049e-116">There's an exception to this rule: if you plan to use the same controller for both views and web APIs, derive it from `Controller`.</span></span>

<span data-ttu-id="1049e-117">Die `ControllerBase`-Klasse bietet viele Eigenschaften und Methoden, die für die Verarbeitung von HTTP-Anforderungen hilfreich sind.</span><span class="sxs-lookup"><span data-stu-id="1049e-117">The `ControllerBase` class provides many properties and methods that are useful for handling HTTP requests.</span></span> <span data-ttu-id="1049e-118">`ControllerBase.CreatedAtAction` gibt beispielsweise Statuscode 201 zurück:</span><span class="sxs-lookup"><span data-stu-id="1049e-118">For example, `ControllerBase.CreatedAtAction` returns a 201 status code:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=10)]

<span data-ttu-id="1049e-119">Hier einige weitere Beispiele für die von `ControllerBase` bereitgestellten Methoden.</span><span class="sxs-lookup"><span data-stu-id="1049e-119">Here are some more examples of methods that `ControllerBase` provides.</span></span>

|<span data-ttu-id="1049e-120">Methode</span><span class="sxs-lookup"><span data-stu-id="1049e-120">Method</span></span>   |<span data-ttu-id="1049e-121">Hinweise</span><span class="sxs-lookup"><span data-stu-id="1049e-121">Notes</span></span>    |
|---------|---------|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest%2A>| <span data-ttu-id="1049e-122">Gibt Statuscode 400 zurück.</span><span class="sxs-lookup"><span data-stu-id="1049e-122">Returns 400 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>|<span data-ttu-id="1049e-123">Gibt Statuscode 404 zurück.</span><span class="sxs-lookup"><span data-stu-id="1049e-123">Returns 404 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.PhysicalFile%2A>|<span data-ttu-id="1049e-124">Gibt eine Datei zurück.</span><span class="sxs-lookup"><span data-stu-id="1049e-124">Returns a file.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync%2A>|<span data-ttu-id="1049e-125">Ruft die [Modellbindung](xref:mvc/models/model-binding) auf.</span><span class="sxs-lookup"><span data-stu-id="1049e-125">Invokes [model binding](xref:mvc/models/model-binding).</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryValidateModel%2A>|<span data-ttu-id="1049e-126">Ruft die [Modellvalidierung](xref:mvc/models/validation) auf.</span><span class="sxs-lookup"><span data-stu-id="1049e-126">Invokes [model validation](xref:mvc/models/validation).</span></span>|

<span data-ttu-id="1049e-127">Eine Liste aller verfügbaren Methoden und Eigenschaften finden Sie unter <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span><span class="sxs-lookup"><span data-stu-id="1049e-127">For a list of all available methods and properties, see <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span>

## <a name="attributes"></a><span data-ttu-id="1049e-128">Attribute</span><span class="sxs-lookup"><span data-stu-id="1049e-128">Attributes</span></span>

<span data-ttu-id="1049e-129">Der <xref:Microsoft.AspNetCore.Mvc>-Namespace enthält Attribute, mit denen das Verhalten von Web-API-Controllern und Aktionsmethoden konfiguriert werden kann.</span><span class="sxs-lookup"><span data-stu-id="1049e-129">The <xref:Microsoft.AspNetCore.Mvc> namespace provides attributes that can be used to configure the behavior of web API controllers and action methods.</span></span> <span data-ttu-id="1049e-130">Im folgenden Beispiel werden Attribute verwendet, um das unterstützte HTTP-Aktionsverb und alle bekannten HTTP-Statuscodes, die zurückgegeben werden können, anzugeben:</span><span class="sxs-lookup"><span data-stu-id="1049e-130">The following example uses attributes to specify the supported HTTP action verb and any known HTTP status codes that could be returned:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=1-3)]

<span data-ttu-id="1049e-131">Hier einige weitere Beispiele für die verfügbaren Attribute.</span><span class="sxs-lookup"><span data-stu-id="1049e-131">Here are some more examples of attributes that are available.</span></span>

|<span data-ttu-id="1049e-132">Attribut</span><span class="sxs-lookup"><span data-stu-id="1049e-132">Attribute</span></span>|<span data-ttu-id="1049e-133">Hinweise</span><span class="sxs-lookup"><span data-stu-id="1049e-133">Notes</span></span>|
|---------|-----|
|[`[Route]`](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)      |<span data-ttu-id="1049e-134">Gibt ein URL-Muster für einen Controller oder eine Aktion an.</span><span class="sxs-lookup"><span data-stu-id="1049e-134">Specifies URL pattern for a controller or action.</span></span>|
|[`[Bind]`](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)        |<span data-ttu-id="1049e-135">Gibt Präfixe und Eigenschaften an, die in die Modellbindung einbezogen werden sollen.</span><span class="sxs-lookup"><span data-stu-id="1049e-135">Specifies prefix and properties to include for model binding.</span></span>|
|[`[HttpGet]`](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)  |<span data-ttu-id="1049e-136">Identifiziert eine Aktion, die das HTTP GET-Aktionsverb unterstützt.</span><span class="sxs-lookup"><span data-stu-id="1049e-136">Identifies an action that supports the HTTP GET action verb.</span></span>|
|[`[Consumes]`](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)|<span data-ttu-id="1049e-137">Gibt die von einer Aktion akzeptierten Datentypen an.</span><span class="sxs-lookup"><span data-stu-id="1049e-137">Specifies data types that an action accepts.</span></span>|
|[`[Produces]`](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)|<span data-ttu-id="1049e-138">Gibt die von einer Aktion zurückgegebenen Datentypen an.</span><span class="sxs-lookup"><span data-stu-id="1049e-138">Specifies data types that an action returns.</span></span>|

<span data-ttu-id="1049e-139">Eine Liste mit den verfügbaren Attributen finden Sie im <xref:Microsoft.AspNetCore.Mvc>-Namespace.</span><span class="sxs-lookup"><span data-stu-id="1049e-139">For a list that includes the available attributes, see the <xref:Microsoft.AspNetCore.Mvc> namespace.</span></span>

## <a name="apicontroller-attribute"></a><span data-ttu-id="1049e-140">ApiController-Attribut</span><span class="sxs-lookup"><span data-stu-id="1049e-140">ApiController attribute</span></span>

<span data-ttu-id="1049e-141">Das [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)-Attribut kann auf eine Controllerklasse angewendet werden, um folgende API-spezifische Verhalten mit einigen vordefinierten Konfigurationen zu aktivieren:</span><span class="sxs-lookup"><span data-stu-id="1049e-141">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute can be applied to a controller class to enable the following opinionated, API-specific behaviors:</span></span>

* [<span data-ttu-id="1049e-142">Anforderung für das Attributrouting</span><span class="sxs-lookup"><span data-stu-id="1049e-142">Attribute routing requirement</span></span>](#attribute-routing-requirement)
* [<span data-ttu-id="1049e-143">Automatische HTTP 400-Antworten</span><span class="sxs-lookup"><span data-stu-id="1049e-143">Automatic HTTP 400 responses</span></span>](#automatic-http-400-responses)
* [<span data-ttu-id="1049e-144">Rückschluss auf Bindungsquellparameter</span><span class="sxs-lookup"><span data-stu-id="1049e-144">Binding source parameter inference</span></span>](#binding-source-parameter-inference)
* [<span data-ttu-id="1049e-145">Ableiten der Multipart/form-data-Anforderung</span><span class="sxs-lookup"><span data-stu-id="1049e-145">Multipart/form-data request inference</span></span>](#multipartform-data-request-inference)
* [<span data-ttu-id="1049e-146">Problemdetails für Fehlerstatuscodes</span><span class="sxs-lookup"><span data-stu-id="1049e-146">Problem details for error status codes</span></span>](#problem-details-for-error-status-codes)

<span data-ttu-id="1049e-147">Diese Features erfordern mindestens [Kompatibilitätsversion](xref:mvc/compatibility-version) 2.1.</span><span class="sxs-lookup"><span data-stu-id="1049e-147">These features require a [compatibility version](xref:mvc/compatibility-version) of 2.1 or later.</span></span>

### <a name="attribute-on-specific-controllers"></a><span data-ttu-id="1049e-148">Attribut für bestimmte Controller</span><span class="sxs-lookup"><span data-stu-id="1049e-148">Attribute on specific controllers</span></span>

<span data-ttu-id="1049e-149">Das `[ApiController]`-Attribut kann, wie im folgenden Beispiel der Projektvorlage ersichtlich, auf bestimmte Controller angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="1049e-149">The `[ApiController]` attribute can be applied to specific controllers, as in the following example from the project template:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2])]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=2)]

::: moniker-end

### <a name="attribute-on-multiple-controllers"></a><span data-ttu-id="1049e-150">Attribut für mehrere Controller</span><span class="sxs-lookup"><span data-stu-id="1049e-150">Attribute on multiple controllers</span></span>

<span data-ttu-id="1049e-151">Eine Möglichkeit, das Attribut für mehr als einen Controller zu verwenden, besteht darin, eine benutzerdefinierte Basiscontrollerklasse zu erstellen, die mit dem `[ApiController]`-Attribut kommentiert ist.</span><span class="sxs-lookup"><span data-stu-id="1049e-151">One approach to using the attribute on more than one controller is to create a custom base controller class annotated with the `[ApiController]` attribute.</span></span> <span data-ttu-id="1049e-152">Im folgenden Beispiel wird eine benutzerdefinierte Basisklasse und ein Controller, der von ihr abgeleitet wird, gezeigt:</span><span class="sxs-lookup"><span data-stu-id="1049e-152">The following example shows a custom base class and a controller that derives from it:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/MyControllerBase.cs?name=snippet_MyControllerBase)]

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="attribute-on-an-assembly"></a><span data-ttu-id="1049e-153">Attribut für eine Assembly</span><span class="sxs-lookup"><span data-stu-id="1049e-153">Attribute on an assembly</span></span>

<span data-ttu-id="1049e-154">Wenn die [Kompatibilitätsversion](xref:mvc/compatibility-version) auf 2.2 oder höher festgelegt ist, kann das `[ApiController]`-Attribut auf eine Assembly angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="1049e-154">If [compatibility version](xref:mvc/compatibility-version) is set to 2.2 or later, the `[ApiController]` attribute can be applied to an assembly.</span></span> <span data-ttu-id="1049e-155">Auf diese Weise wendet die Anmerkung das Web-API-Verhalten auf alle Controller in der Assembly an.</span><span class="sxs-lookup"><span data-stu-id="1049e-155">Annotation in this manner applies web API behavior to all controllers in the assembly.</span></span> <span data-ttu-id="1049e-156">Es gibt keine Möglichkeit, einzelne Controller auszuschließen.</span><span class="sxs-lookup"><span data-stu-id="1049e-156">There's no way to opt out for individual controllers.</span></span> <span data-ttu-id="1049e-157">Wenden Sie das Attribut auf Assemblyebene auf die Namespacedeklaration an, die die `Startup`-Klasse umgibt:</span><span class="sxs-lookup"><span data-stu-id="1049e-157">Apply the assembly-level attribute to the namespace declaration surrounding the `Startup` class:</span></span>

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

## <a name="attribute-routing-requirement"></a><span data-ttu-id="1049e-158">Anforderung für das Attributrouting</span><span class="sxs-lookup"><span data-stu-id="1049e-158">Attribute routing requirement</span></span>

<span data-ttu-id="1049e-159">Durch das `[ApiController]`-Attribut wird das Attributrouting zu einer Anforderung.</span><span class="sxs-lookup"><span data-stu-id="1049e-159">The `[ApiController]` attribute makes attribute routing a requirement.</span></span> <span data-ttu-id="1049e-160">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="1049e-160">For example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2)]

<span data-ttu-id="1049e-161">Sie können über [konventionelle Routen](xref:mvc/controllers/routing#conventional-routing), die durch `UseEndpoints`, <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> oder <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> in `Startup.Configure` definiert sind, nicht auf Aktionen zugreifen.</span><span class="sxs-lookup"><span data-stu-id="1049e-161">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined by `UseEndpoints`, <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>, or <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> in `Startup.Configure`.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=1)]

<span data-ttu-id="1049e-162">Sie können über [konventionelle Routen](xref:mvc/controllers/routing#conventional-routing), die durch <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> oder <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> in `Startup.Configure` definiert sind, nicht auf Aktionen zugreifen.</span><span class="sxs-lookup"><span data-stu-id="1049e-162">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined by <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> or <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> in `Startup.Configure`.</span></span>

::: moniker-end

## <a name="automatic-http-400-responses"></a><span data-ttu-id="1049e-163">Automatische HTTP 400-Antwort</span><span class="sxs-lookup"><span data-stu-id="1049e-163">Automatic HTTP 400 responses</span></span>

<span data-ttu-id="1049e-164">Durch das `[ApiController]`-Attribut wird bei Modellvalidierungsfehlern automatisch eine HTTP 400-Antwort ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="1049e-164">The `[ApiController]` attribute makes model validation errors automatically trigger an HTTP 400 response.</span></span> <span data-ttu-id="1049e-165">Deshalb ist der folgende Code in einer Aktionsmethode überflüssig:</span><span class="sxs-lookup"><span data-stu-id="1049e-165">Consequently, the following code is unnecessary in an action method:</span></span>

```csharp
if (!ModelState.IsValid)
{
    return BadRequest(ModelState);
}
```

<span data-ttu-id="1049e-166">ASP.NET Core MVC verwendet den <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter>-Aktionsfilter, um die vorherige Überprüfung durchzuführen.</span><span class="sxs-lookup"><span data-stu-id="1049e-166">ASP.NET Core MVC uses the <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter> action filter to do the preceding check.</span></span>

### <a name="default-badrequest-response"></a><span data-ttu-id="1049e-167">Standardmäßige BadRequest-Antwort</span><span class="sxs-lookup"><span data-stu-id="1049e-167">Default BadRequest response</span></span>

<span data-ttu-id="1049e-168">Bei Kompatibilitätsversion 2.1 wird für eine HTTP 400-Antwort standardmäßig der Antworttyp <xref:Microsoft.AspNetCore.Mvc.SerializableError> verwendet.</span><span class="sxs-lookup"><span data-stu-id="1049e-168">With a compatibility version of 2.1, the default response type for an HTTP 400 response is <xref:Microsoft.AspNetCore.Mvc.SerializableError>.</span></span> <span data-ttu-id="1049e-169">Der folgende Anforderungstext ist ein Beispiel für den serialisierten Typ:</span><span class="sxs-lookup"><span data-stu-id="1049e-169">The following request body is an example of the serialized type:</span></span>

```json
{
  "": [
    "A non-empty request body is required."
  ]
}
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="1049e-170">Bei Kompatibilitätsversion 2.2 oder höher wird für eine HTTP 400-Antwort standardmäßig der Antworttyp <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> verwendet.</span><span class="sxs-lookup"><span data-stu-id="1049e-170">With a compatibility version of 2.2 or later, the default response type for an HTTP 400 response is <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="1049e-171">Der folgende Anforderungstext ist ein Beispiel für den serialisierten Typ:</span><span class="sxs-lookup"><span data-stu-id="1049e-171">The following request body is an example of the serialized type:</span></span>

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

<span data-ttu-id="1049e-172">Der `ValidationProblemDetails`-Typ:</span><span class="sxs-lookup"><span data-stu-id="1049e-172">The `ValidationProblemDetails` type:</span></span>

* <span data-ttu-id="1049e-173">Bietet ein vom Computer lesbares Format zum Angeben von Fehlern in Web-API-Antworten.</span><span class="sxs-lookup"><span data-stu-id="1049e-173">Provides a machine-readable format for specifying errors in web API responses.</span></span>
* <span data-ttu-id="1049e-174">Entspricht der [RFC 7807-Spezifikation](https://tools.ietf.org/html/rfc7807).</span><span class="sxs-lookup"><span data-stu-id="1049e-174">Complies with the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807).</span></span>

::: moniker-end

### <a name="log-automatic-400-responses"></a><span data-ttu-id="1049e-175">Protokollieren automatischer 400-Antworten</span><span class="sxs-lookup"><span data-stu-id="1049e-175">Log automatic 400 responses</span></span>

<span data-ttu-id="1049e-176">Informationen finden Sie unter [How to log automatic 400 responses on model validation errors (aspnet/AspNetCore.Docs #12157)](https://github.com/aspnet/AspNetCore.Docs/issues/12157) (Protokollieren automatischer 400-Antworten auf Modellvalidierungsfehler, aspnet/AspNetCore.Docs #12157).</span><span class="sxs-lookup"><span data-stu-id="1049e-176">See [How to log automatic 400 responses on model validation errors (aspnet/AspNetCore.Docs #12157)](https://github.com/aspnet/AspNetCore.Docs/issues/12157).</span></span>

### <a name="disable-automatic-400-response"></a><span data-ttu-id="1049e-177">Deaktivieren automatischer 400-Antwort</span><span class="sxs-lookup"><span data-stu-id="1049e-177">Disable automatic 400 response</span></span>

<span data-ttu-id="1049e-178">Um das automatische Verhalten bei Statuscode 400 zu deaktivieren, legen Sie die <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter>-Eigenschaft auf `true` fest.</span><span class="sxs-lookup"><span data-stu-id="1049e-178">To disable the automatic 400 behavior, set the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> property to `true`.</span></span> <span data-ttu-id="1049e-179">Fügen Sie folgenden hervorgehobenen Code in `Startup.ConfigureServices` hinzu:</span><span class="sxs-lookup"><span data-stu-id="1049e-179">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,5)]

::: moniker-end

## <a name="binding-source-parameter-inference"></a><span data-ttu-id="1049e-180">Rückschluss auf Bindungsquellparameter</span><span class="sxs-lookup"><span data-stu-id="1049e-180">Binding source parameter inference</span></span>

<span data-ttu-id="1049e-181">Ein Bindungsquellenattribut definiert den Speicherort, an dem der Wert eines Aktionsparameters vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="1049e-181">A binding source attribute defines the location at which an action parameter's value is found.</span></span> <span data-ttu-id="1049e-182">Es gibt die folgenden Bindungsquellattribute:</span><span class="sxs-lookup"><span data-stu-id="1049e-182">The following binding source attributes exist:</span></span>

|<span data-ttu-id="1049e-183">Attribut</span><span class="sxs-lookup"><span data-stu-id="1049e-183">Attribute</span></span>|<span data-ttu-id="1049e-184">Bindungsquelle</span><span class="sxs-lookup"><span data-stu-id="1049e-184">Binding source</span></span> |
|---------|---------|
|[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)     | <span data-ttu-id="1049e-185">Anforderungstext</span><span class="sxs-lookup"><span data-stu-id="1049e-185">Request body</span></span> |
|[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)     | <span data-ttu-id="1049e-186">Formulardaten im Anforderungstext</span><span class="sxs-lookup"><span data-stu-id="1049e-186">Form data in the request body</span></span> |
|[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) | <span data-ttu-id="1049e-187">Anforderungsheader</span><span class="sxs-lookup"><span data-stu-id="1049e-187">Request header</span></span> |
|[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)   | <span data-ttu-id="1049e-188">Abfragezeichenfolge-Parameter der Anforderung</span><span class="sxs-lookup"><span data-stu-id="1049e-188">Request query string parameter</span></span> |
|[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)   | <span data-ttu-id="1049e-189">Routendaten aus aktuellen Anforderungen</span><span class="sxs-lookup"><span data-stu-id="1049e-189">Route data from the current request</span></span> |
|[`[FromServices]`](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices) | <span data-ttu-id="1049e-190">Der als Aktionsparameter eingefügte Anforderungsdienst.</span><span class="sxs-lookup"><span data-stu-id="1049e-190">The request service injected as an action parameter</span></span> |

> [!WARNING]
> <span data-ttu-id="1049e-191">Verwenden Sie `[FromRoute]` nicht, wenn Werte möglicherweise `%2f` enthalten (d.h. `/`).</span><span class="sxs-lookup"><span data-stu-id="1049e-191">Don't use `[FromRoute]` when values might contain `%2f` (that is `/`).</span></span> <span data-ttu-id="1049e-192">`%2f` wird nicht durch Entfernen von Escapezeichen zu `/`.</span><span class="sxs-lookup"><span data-stu-id="1049e-192">`%2f` won't be unescaped to `/`.</span></span> <span data-ttu-id="1049e-193">Verwenden Sie `[FromQuery]`, wenn der Wert `%2f` enthalten könnte.</span><span class="sxs-lookup"><span data-stu-id="1049e-193">Use `[FromQuery]` if the value might contain `%2f`.</span></span>

<span data-ttu-id="1049e-194">Ohne das `[ApiController]`-Attribut oder Bindungsquellenattribute wie `[FromQuery]` versucht die ASP.NET Core-Runtime, die Modellbindung für komplexe Objekte zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="1049e-194">Without the `[ApiController]` attribute or binding source attributes like `[FromQuery]`, the ASP.NET Core runtime attempts to use the complex object model binder.</span></span> <span data-ttu-id="1049e-195">Bei der Modellbindung für komplexe Objekte werden Daten aus Wertanbietern in einer festgelegten Reihenfolge abgerufen.</span><span class="sxs-lookup"><span data-stu-id="1049e-195">The complex object model binder pulls data from value providers in a defined order.</span></span>

<span data-ttu-id="1049e-196">Im folgenden Beispiel gibt das `[FromQuery]`-Attribut an, dass der Parameterwert `discontinuedOnly` in der Abfragezeichenfolge der Anforderungs-URL bereitgestellt wird:</span><span class="sxs-lookup"><span data-stu-id="1049e-196">In the following example, the `[FromQuery]` attribute indicates that the `discontinuedOnly` parameter value is provided in the request URL's query string:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

<span data-ttu-id="1049e-197">Das `[ApiController]`-Attribut wendet Rückschlussregeln auf die Standarddatenquellen von Aktionsparametern an.</span><span class="sxs-lookup"><span data-stu-id="1049e-197">The `[ApiController]` attribute applies inference rules for the default data sources of action parameters.</span></span> <span data-ttu-id="1049e-198">Da durch diese Regeln Attribute auf die Aktionsparameter angewendet werden, müssen Sie Bindungsquellen nicht manuell identifizieren.</span><span class="sxs-lookup"><span data-stu-id="1049e-198">These rules save you from having to identify binding sources manually by applying attributes to the action parameters.</span></span> <span data-ttu-id="1049e-199">Die Rückschlussregeln für Bindungsquellen verhalten sich wie folgt:</span><span class="sxs-lookup"><span data-stu-id="1049e-199">The binding source inference rules behave as follows:</span></span>

* <span data-ttu-id="1049e-200">`[FromBody]` wird für komplexe Typparameter abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="1049e-200">`[FromBody]` is inferred for complex type parameters.</span></span> <span data-ttu-id="1049e-201">Jeder komplexe integrierte Typ mit spezieller Bedeutung, z. B. <xref:Microsoft.AspNetCore.Http.IFormCollection> und <xref:System.Threading.CancellationToken>, stellt eine Ausnahme von der `[FromBody]`-Rückschlussregel dar.</span><span class="sxs-lookup"><span data-stu-id="1049e-201">An exception to the `[FromBody]` inference rule is any complex, built-in type with a special meaning, such as <xref:Microsoft.AspNetCore.Http.IFormCollection> and <xref:System.Threading.CancellationToken>.</span></span> <span data-ttu-id="1049e-202">Der Rückschlusscode der Bindungsquelle ignoriert diese Typen.</span><span class="sxs-lookup"><span data-stu-id="1049e-202">The binding source inference code ignores those special types.</span></span>
* <span data-ttu-id="1049e-203">`[FromForm]` wird für Aktionsparameter des Typs <xref:Microsoft.AspNetCore.Http.IFormFile> und <xref:Microsoft.AspNetCore.Http.IFormFileCollection> abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="1049e-203">`[FromForm]` is inferred for action parameters of type <xref:Microsoft.AspNetCore.Http.IFormFile> and <xref:Microsoft.AspNetCore.Http.IFormFileCollection>.</span></span> <span data-ttu-id="1049e-204">Es wird für keine einfachen oder benutzerdefinierte Typen abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="1049e-204">It's not inferred for any simple or user-defined types.</span></span>
* <span data-ttu-id="1049e-205">`[FromRoute]` wird für jeden Namen von Aktionsparametern abgeleitet, der mit einem Parameter in der Routenvorlage übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="1049e-205">`[FromRoute]` is inferred for any action parameter name matching a parameter in the route template.</span></span> <span data-ttu-id="1049e-206">Wenn mehr als eine Route mit einem Aktionsparameter übereinstimmt, gilt jeder Routenwert als `[FromRoute]`.</span><span class="sxs-lookup"><span data-stu-id="1049e-206">When more than one route matches an action parameter, any route value is considered `[FromRoute]`.</span></span>
* <span data-ttu-id="1049e-207">`[FromQuery]` wird für alle anderen Aktionsparameter abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="1049e-207">`[FromQuery]` is inferred for any other action parameters.</span></span>

### <a name="frombody-inference-notes"></a><span data-ttu-id="1049e-208">Hinweise zum FromBody-Rückschluss</span><span class="sxs-lookup"><span data-stu-id="1049e-208">FromBody inference notes</span></span>

<span data-ttu-id="1049e-209">`[FromBody]` wird für einfache Typen wie `string` oder `int` nicht abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="1049e-209">`[FromBody]` isn't inferred for simple types such as `string` or `int`.</span></span> <span data-ttu-id="1049e-210">Deshalb sollte das `[FromBody]`-Attribut für einfache Typen verwendet werden, wenn diese Funktionsweise benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="1049e-210">Therefore, the `[FromBody]` attribute should be used for simple types when that functionality is needed.</span></span>

<span data-ttu-id="1049e-211">Wenn an eine Aktion mehr als ein Parameter aus dem Anforderungstext gebunden ist, wird eine Ausnahme ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="1049e-211">When an action has more than one parameter bound from the request body, an exception is thrown.</span></span> <span data-ttu-id="1049e-212">Beispielsweise lösen alle Signaturen der folgenden Aktionsmethoden eine Ausnahme aus:</span><span class="sxs-lookup"><span data-stu-id="1049e-212">For example, all of the following action method signatures cause an exception:</span></span>

* <span data-ttu-id="1049e-213">`[FromBody]` wird für beide abgeleitet, da sie komplexe Typen darstellen.</span><span class="sxs-lookup"><span data-stu-id="1049e-213">`[FromBody]` inferred on both because they're complex types.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action1(Product product, Order order)
  ```

* <span data-ttu-id="1049e-214">Das `[FromBody]`-Attribut für einen Typ wird für den anderen abgeleitet, da es sich um einen komplexen Typ handelt.</span><span class="sxs-lookup"><span data-stu-id="1049e-214">`[FromBody]` attribute on one, inferred on the other because it's a complex type.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action2(Product product, [FromBody] Order order)
  ```

* <span data-ttu-id="1049e-215">Das `[FromBody]`-Attribut für beide.</span><span class="sxs-lookup"><span data-stu-id="1049e-215">`[FromBody]` attribute on both.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action3([FromBody] Product product, [FromBody] Order order)
  ```

::: moniker range="= aspnetcore-2.1"

> [!NOTE]
> <span data-ttu-id="1049e-216">In ASP.NET Core 2.1 werden Sammlungstypparameter wie Listen und Arrays fälschlicherweise als `[FromQuery]` abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="1049e-216">In ASP.NET Core 2.1, collection type parameters such as lists and arrays are incorrectly inferred as `[FromQuery]`.</span></span> <span data-ttu-id="1049e-217">Für diese Parameter sollte das `[FromBody]`-Attribut verwendet werden, wenn sie aus dem Anforderungstext gebunden werden sollen.</span><span class="sxs-lookup"><span data-stu-id="1049e-217">The `[FromBody]` attribute should be used for these parameters if they are to be bound from the request body.</span></span> <span data-ttu-id="1049e-218">Dieses Verhalten wurde bei ASP.NET Core 2.2 oder höher behoben. Hier werden Sammlungstypparameter standardmäßig als „[FromBody]“ abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="1049e-218">This behavior is corrected in ASP.NET Core 2.2 or later, where collection type parameters are inferred to be bound from the body by default.</span></span>

::: moniker-end

### <a name="disable-inference-rules"></a><span data-ttu-id="1049e-219">Deaktivieren von Rückschlussregeln</span><span class="sxs-lookup"><span data-stu-id="1049e-219">Disable inference rules</span></span>

<span data-ttu-id="1049e-220">Legen Sie <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> auf `true` fest, um den Rückschluss auf Bindungsquellen zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="1049e-220">To disable binding source inference, set <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> to `true`.</span></span> <span data-ttu-id="1049e-221">Fügen Sie den folgenden Code zu `Startup.ConfigureServices` hinzu:</span><span class="sxs-lookup"><span data-stu-id="1049e-221">Add the following code in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,5)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,4)]

::: moniker-end

## <a name="multipartform-data-request-inference"></a><span data-ttu-id="1049e-222">Ableiten der Multipart/form-data-Anforderung</span><span class="sxs-lookup"><span data-stu-id="1049e-222">Multipart/form-data request inference</span></span>

<span data-ttu-id="1049e-223">Das `[ApiController]`-Attribut wendet eine Rückschlussregel an, wenn ein Aktionsparameter mit dem [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)-Attribut kommentiert ist.</span><span class="sxs-lookup"><span data-stu-id="1049e-223">The `[ApiController]` attribute applies an inference rule when an action parameter is annotated with the [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) attribute.</span></span> <span data-ttu-id="1049e-224">Der `multipart/form-data`-Anforderungsinhaltstyp wird abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="1049e-224">The `multipart/form-data` request content type is inferred.</span></span>

<span data-ttu-id="1049e-225">Legen Sie die <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters>-Eigenschaft auf `true` in `Startup.ConfigureServices` fest, um das Standardverhalten zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="1049e-225">To disable the default behavior, set the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> property to `true` in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,4)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,5)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,3)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="problem-details-for-error-status-codes"></a><span data-ttu-id="1049e-226">Problemdetails für Fehlerstatuscodes</span><span class="sxs-lookup"><span data-stu-id="1049e-226">Problem details for error status codes</span></span>

<span data-ttu-id="1049e-227">Bei Kompatibilitätsversion 2.2 oder höher wandelt MVC ein Fehlerergebnis (ein Ergebnis mit dem Statuscode 400 oder höher) in ein Ergebnis mit <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> um.</span><span class="sxs-lookup"><span data-stu-id="1049e-227">When the compatibility version is 2.2 or later, MVC transforms an error result (a result with status code 400 or higher) to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span> <span data-ttu-id="1049e-228">Die `ProblemDetails`-Typ basiert auf der [RFC 7807-Spezifikation](https://tools.ietf.org/html/rfc7807) für die Bereitstellung maschinenlesbarer Fehlerdetails in einer HTTP-Antwort.</span><span class="sxs-lookup"><span data-stu-id="1049e-228">The `ProblemDetails` type is based on the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807) for providing machine-readable error details in an HTTP response.</span></span>

<span data-ttu-id="1049e-229">Betrachten Sie den folgenden Code in einer Controlleraktion:</span><span class="sxs-lookup"><span data-stu-id="1049e-229">Consider the following code in a controller action:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_ProblemDetailsStatusCode)]

<span data-ttu-id="1049e-230">Die `NotFound`-Methode erzeugt einen HTTP-404-Statuscode mit einem `ProblemDetails`-Text.</span><span class="sxs-lookup"><span data-stu-id="1049e-230">The `NotFound` method produces an HTTP 404 status code with a `ProblemDetails` body.</span></span> <span data-ttu-id="1049e-231">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="1049e-231">For example:</span></span>

```json
{
  type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
  title: "Not Found",
  status: 404,
  traceId: "0HLHLV31KRN83:00000001"
}
```

### <a name="disable-problemdetails-response"></a><span data-ttu-id="1049e-232">Deaktivieren der ProblemDetails-Antwort</span><span class="sxs-lookup"><span data-stu-id="1049e-232">Disable ProblemDetails response</span></span>

<span data-ttu-id="1049e-233">Die automatische Erstellung einer `ProblemDetails`-Instanz ist deaktiviert, wenn die <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors%2A>-Eigenschaft auf `true` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="1049e-233">The automatic creation of a `ProblemDetails` instance is disabled when the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors%2A> property is set to `true`.</span></span> <span data-ttu-id="1049e-234">Fügen Sie den folgenden Code zu `Startup.ConfigureServices` hinzu:</span><span class="sxs-lookup"><span data-stu-id="1049e-234">Add the following code in `Startup.ConfigureServices`:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,8)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="1049e-235">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="1049e-235">Additional resources</span></span>

* <xref:web-api/action-return-types>
* <xref:web-api/handle-errors>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>

---
title: Erstellen von Web-APIs mit ASP.NET Core
author: scottaddie
description: Lernen Sie die verfügbaren Features zum Erstellen einer Web-API in ASP.NET Core kennen, und erhalten Sie Informationen zur Verwendung dieser Features.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/11/2019
uid: web-api/index
ms.openlocfilehash: bc8be67957a56a818a88e0496d45db1e7b7aed0e
ms.sourcegitcommit: a467828b5e4eaae291d961ffe2279a571900de23
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58142357"
---
# <a name="build-web-apis-with-aspnet-core"></a><span data-ttu-id="ea8de-103">Erstellen von Web-APIs mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ea8de-103">Build web APIs with ASP.NET Core</span></span>

<span data-ttu-id="ea8de-104">Von [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="ea8de-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="ea8de-105">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/define-controller/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ea8de-105">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/define-controller/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="ea8de-106">In diesem Dokument wird erläutert, wie eine Web-API in ASP.NET Core erstellt werden kann und wann jedes Feature am besten verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="ea8de-106">This document explains how to build a web API in ASP.NET Core and when it's most appropriate to use each feature.</span></span>

## <a name="derive-class-from-controllerbase"></a><span data-ttu-id="ea8de-107">Ableiten einer Klasse von ControllerBase</span><span class="sxs-lookup"><span data-stu-id="ea8de-107">Derive class from ControllerBase</span></span>

<span data-ttu-id="ea8de-108">Leiten Sie Inhalte der <xref:Microsoft.AspNetCore.Mvc.ControllerBase>-Klasse in einem Controller ab, der als Web-API fungieren soll.</span><span class="sxs-lookup"><span data-stu-id="ea8de-108">Inherit from the <xref:Microsoft.AspNetCore.Mvc.ControllerBase> class in a controller that's intended to serve as a web API.</span></span> <span data-ttu-id="ea8de-109">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="ea8de-109">For example:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](define-controller/samples/WebApiSample.Api.21/Controllers/PetsController.cs?name=snippet_PetsController&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](define-controller/samples/WebApiSample.Api.Pre21/Controllers/PetsController.cs?name=snippet_PetsController&highlight=3)]

::: moniker-end

<span data-ttu-id="ea8de-110">Die `ControllerBase`-Klasse ermöglicht den Zugriff auf mehrere Eigenschaften und Methoden.</span><span class="sxs-lookup"><span data-stu-id="ea8de-110">The `ControllerBase` class provides access to several properties and methods.</span></span> <span data-ttu-id="ea8de-111">Zu den Beispielen im vorherigen Code zählen <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest(Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary)> und <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction(System.String,System.Object,System.Object)>.</span><span class="sxs-lookup"><span data-stu-id="ea8de-111">In the preceding code, examples include <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest(Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary)> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction(System.String,System.Object,System.Object)>.</span></span> <span data-ttu-id="ea8de-112">Diese Methoden werden innerhalb von Aktionsmethoden aufgerufen und geben dann jeweils die Statuscodes HTTP 400 und HTTP 201 zurück.</span><span class="sxs-lookup"><span data-stu-id="ea8de-112">These methods are called within action methods to return HTTP 400 and 201 status codes, respectively.</span></span> <span data-ttu-id="ea8de-113">Ein Zugriff erfolgt auf die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState>, die ebenfalls von `ControllerBase` bereitgestellt wird, um die Anforderungsvalidierung für das Modell zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="ea8de-113">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState> property, also provided by `ControllerBase`, is accessed to handle request model validation.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="annotation-with-apicontroller-attribute"></a><span data-ttu-id="ea8de-114">Anmerkungen mit dem ApiController-Attribut</span><span class="sxs-lookup"><span data-stu-id="ea8de-114">Annotation with ApiController attribute</span></span>

<span data-ttu-id="ea8de-115">In ASP.NET Core 2.1 wird das Attribut [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) eingeführt, um eine Klasse des Web-API-Controllers anzugeben.</span><span class="sxs-lookup"><span data-stu-id="ea8de-115">ASP.NET Core 2.1 introduces the [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute to denote a web API controller class.</span></span> <span data-ttu-id="ea8de-116">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="ea8de-116">For example:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_ControllerSignature&highlight=2)]

<span data-ttu-id="ea8de-117">Eine Kompatibilitätsversion von 2.1 oder höher, die über <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> festgelegt wird, ist für die Verwendung dieses Attributs auf Controllerebene erforderlich.</span><span class="sxs-lookup"><span data-stu-id="ea8de-117">A compatibility version of 2.1 or later, set via <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*>, is required to use this attribute at the controller level.</span></span> <span data-ttu-id="ea8de-118">Beispielsweise legt der hervorgehobene Code in `Startup.ConfigureServices` das Kompatibilitätsflag 2.1 fest:</span><span class="sxs-lookup"><span data-stu-id="ea8de-118">For example, the highlighted code in `Startup.ConfigureServices` sets the 2.1 compatibility flag:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.21/Startup.cs?name=snippet_SetCompatibilityVersion&highlight=2)]

<span data-ttu-id="ea8de-119">Weitere Informationen finden Sie unter <xref:mvc/compatibility-version>.</span><span class="sxs-lookup"><span data-stu-id="ea8de-119">For more information, see <xref:mvc/compatibility-version>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="ea8de-120">Das Attribut `[ApiController]` kann in ASP.NET Core 2.2 oder höher auf eine Assembly angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="ea8de-120">In ASP.NET Core 2.2 or later, the `[ApiController]` attribute can be applied to an assembly.</span></span> <span data-ttu-id="ea8de-121">Auf diese Weise wendet die Anmerkung das Web-API-Verhalten auf alle Controller in der Assembly an.</span><span class="sxs-lookup"><span data-stu-id="ea8de-121">Annotation in this manner applies web API behavior to all controllers in the assembly.</span></span> <span data-ttu-id="ea8de-122">Beachten Sie, dass Sie einzelne Controller davon nicht ausnehmen können.</span><span class="sxs-lookup"><span data-stu-id="ea8de-122">Beware that there's no way to opt out for individual controllers.</span></span> <span data-ttu-id="ea8de-123">Es wird empfohlen, Attribute auf Assemblyebene auf die Klasse `Startup` anzuwenden:</span><span class="sxs-lookup"><span data-stu-id="ea8de-123">As a recommendation, assembly-level attributes should be applied to the `Startup` class:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.22/Startup.cs?name=snippet_ApiControllerAttributeOnAssembly&highlight=1)]

<span data-ttu-id="ea8de-124">Eine Kompatibilitätsversion von 2.2 oder höher, die über <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> festgelegt wird, ist für die Verwendung dieses Attributs auf Assemblyebene erforderlich.</span><span class="sxs-lookup"><span data-stu-id="ea8de-124">A compatibility version of 2.2 or later, set via <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*>, is required to use this attribute at the assembly level.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="ea8de-125">Das `[ApiController]`-Attribut ist in der Regel mit `ControllerBase` gekoppelt, um das REST-spezifische Verhalten für Controller zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="ea8de-125">The `[ApiController]` attribute is commonly coupled with `ControllerBase` to enable REST-specific behavior for controllers.</span></span> <span data-ttu-id="ea8de-126">Mit `ControllerBase` kann auf Methoden wie <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> und <xref:Microsoft.AspNetCore.Mvc.ControllerBase.File*> zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="ea8de-126">`ControllerBase` provides access to methods such as <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.File*>.</span></span>

<span data-ttu-id="ea8de-127">Eine weitere Vorgehensweise ist das Erstellen einer benutzerdefinierten Basiscontrollerklasse, für die das Attribut `[ApiController]` angegeben ist:</span><span class="sxs-lookup"><span data-stu-id="ea8de-127">Another approach is to create a custom base controller class annotated with the `[ApiController]` attribute:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.21/Controllers/MyBaseController.cs?name=snippet_ControllerSignature)]

<span data-ttu-id="ea8de-128">In den folgenden Abschnitten werden praktische Features beschrieben, die vom Attribut hinzugefügt wurden.</span><span class="sxs-lookup"><span data-stu-id="ea8de-128">The following sections describe convenience features added by the attribute.</span></span>

### <a name="automatic-http-400-responses"></a><span data-ttu-id="ea8de-129">Automatische HTTP 400-Antwort</span><span class="sxs-lookup"><span data-stu-id="ea8de-129">Automatic HTTP 400 responses</span></span>

<span data-ttu-id="ea8de-130">Modellvalidierungsfehler lösen automatisch eine HTTP 400-Antwort aus.</span><span class="sxs-lookup"><span data-stu-id="ea8de-130">Model validation errors automatically trigger an HTTP 400 response.</span></span> <span data-ttu-id="ea8de-131">Deshalb wird der nachfolgende Code in Ihren Aktionen überflüssig:</span><span class="sxs-lookup"><span data-stu-id="ea8de-131">Consequently, the following code becomes unnecessary in your actions:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.Pre21/Controllers/PetsController.cs?name=snippet_ModelStateIsValidCheck)]

<span data-ttu-id="ea8de-132">Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> zum Anpassen der Antwortausgabe.</span><span class="sxs-lookup"><span data-stu-id="ea8de-132">Use <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> to customize the output of the resulting response.</span></span>

<span data-ttu-id="ea8de-133">Es kann nützlich sein, dass Standardverhalten zu deaktivieren, wenn Ihre Aktion nach einem Modellvalidierungsfehler wiederhergestellt werden kann.</span><span class="sxs-lookup"><span data-stu-id="ea8de-133">Disabling the default behavior is useful when your action can recover from a model validation error.</span></span> <span data-ttu-id="ea8de-134">Das Standardverhalten wird deaktiviert, sobald die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> auf `true` festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="ea8de-134">The default behavior is disabled when the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> property is set to `true`.</span></span> <span data-ttu-id="ea8de-135">Fügen Sie den folgenden Code zu `Startup.ConfigureServices` hinter `services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_<version_number>);` hinzu:</span><span class="sxs-lookup"><span data-stu-id="ea8de-135">Add the following code in `Startup.ConfigureServices` after `services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_<version_number>);`:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](define-controller/samples/WebApiSample.Api.22/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=7)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](define-controller/samples/WebApiSample.Api.21/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=5)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="ea8de-136">Mit dem Kompatibilitätsflag 2.2 oder höher wird für Antworten mit HTTP-Statuscode 400 standardmäßig der Typ <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="ea8de-136">With a compatibility flag of 2.2 or later, the default response type for HTTP 400 responses is <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="ea8de-137">Der Typ `ValidationProblemDetails` entspricht der [RFC 7807-Spezifikation](https://tools.ietf.org/html/rfc7807).</span><span class="sxs-lookup"><span data-stu-id="ea8de-137">The `ValidationProblemDetails` type complies with the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807).</span></span> <span data-ttu-id="ea8de-138">Legen Sie die `SuppressUseValidationProblemDetailsForInvalidModelStateResponses`-Eigenschaft auf `true` fest, um stattdessen <xref:Microsoft.AspNetCore.Mvc.SerializableError> im ASP.NET Core 2.1-Fehlerformat zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="ea8de-138">Set the `SuppressUseValidationProblemDetailsForInvalidModelStateResponses` property to `true` to instead return the ASP.NET Core 2.1 error format of <xref:Microsoft.AspNetCore.Mvc.SerializableError>.</span></span> <span data-ttu-id="ea8de-139">Fügen Sie den folgenden Code zu `Startup.ConfigureServices` hinzu:</span><span class="sxs-lookup"><span data-stu-id="ea8de-139">Add the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2)
    .ConfigureApiBehaviorOptions(options =>
    {
        options
          .SuppressUseValidationProblemDetailsForInvalidModelStateResponses = true;
    });
```

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

### <a name="binding-source-parameter-inference"></a><span data-ttu-id="ea8de-140">Rückschluss auf Bindungsquellparameter</span><span class="sxs-lookup"><span data-stu-id="ea8de-140">Binding source parameter inference</span></span>

<span data-ttu-id="ea8de-141">Ein Bindungsquellenattribut definiert den Speicherort, an dem der Wert eines Aktionsparameters vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="ea8de-141">A binding source attribute defines the location at which an action parameter's value is found.</span></span> <span data-ttu-id="ea8de-142">Es gibt die folgenden Bindungsquellattribute:</span><span class="sxs-lookup"><span data-stu-id="ea8de-142">The following binding source attributes exist:</span></span>

|<span data-ttu-id="ea8de-143">Attribut</span><span class="sxs-lookup"><span data-stu-id="ea8de-143">Attribute</span></span>|<span data-ttu-id="ea8de-144">Bindungsquelle</span><span class="sxs-lookup"><span data-stu-id="ea8de-144">Binding source</span></span> |
|---------|---------|
|<span data-ttu-id="ea8de-145">**[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)**</span><span class="sxs-lookup"><span data-stu-id="ea8de-145">**[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)**</span></span>     | <span data-ttu-id="ea8de-146">Anforderungstext</span><span class="sxs-lookup"><span data-stu-id="ea8de-146">Request body</span></span> |
|<span data-ttu-id="ea8de-147">**[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)**</span><span class="sxs-lookup"><span data-stu-id="ea8de-147">**[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)**</span></span>     | <span data-ttu-id="ea8de-148">Formulardaten im Anforderungstext</span><span class="sxs-lookup"><span data-stu-id="ea8de-148">Form data in the request body</span></span> |
|<span data-ttu-id="ea8de-149">**[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute)**</span><span class="sxs-lookup"><span data-stu-id="ea8de-149">**[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute)**</span></span> | <span data-ttu-id="ea8de-150">Anforderungsheader</span><span class="sxs-lookup"><span data-stu-id="ea8de-150">Request header</span></span> |
|<span data-ttu-id="ea8de-151">**[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)**</span><span class="sxs-lookup"><span data-stu-id="ea8de-151">**[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)**</span></span>   | <span data-ttu-id="ea8de-152">Abfragezeichenfolge-Parameter der Anforderung</span><span class="sxs-lookup"><span data-stu-id="ea8de-152">Request query string parameter</span></span> |
|<span data-ttu-id="ea8de-153">**[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)**</span><span class="sxs-lookup"><span data-stu-id="ea8de-153">**[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)**</span></span>   | <span data-ttu-id="ea8de-154">Routendaten aus aktuellen Anforderungen</span><span class="sxs-lookup"><span data-stu-id="ea8de-154">Route data from the current request</span></span> |
|<span data-ttu-id="ea8de-155">**[[FromServices]](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices)**</span><span class="sxs-lookup"><span data-stu-id="ea8de-155">**[[FromServices]](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices)**</span></span> | <span data-ttu-id="ea8de-156">Der als Aktionsparameter eingefügte Anforderungsdienst.</span><span class="sxs-lookup"><span data-stu-id="ea8de-156">The request service injected as an action parameter</span></span> |

> [!WARNING]
> <span data-ttu-id="ea8de-157">Verwenden Sie `[FromRoute]` nicht, wenn Werte möglicherweise `%2f` enthalten (d.h. `/`).</span><span class="sxs-lookup"><span data-stu-id="ea8de-157">Don't use `[FromRoute]` when values might contain `%2f` (that is `/`).</span></span> <span data-ttu-id="ea8de-158">`%2f` wird nicht durch Entfernen von Escapezeichen zu `/`.</span><span class="sxs-lookup"><span data-stu-id="ea8de-158">`%2f` won't be unescaped to `/`.</span></span> <span data-ttu-id="ea8de-159">Verwenden Sie `[FromQuery]`, wenn der Wert `%2f` enthalten könnte.</span><span class="sxs-lookup"><span data-stu-id="ea8de-159">Use `[FromQuery]` if the value might contain `%2f`.</span></span>

<span data-ttu-id="ea8de-160">Ohne das `[ApiController]`-Attribut werden Bindungsquellattribute explizit definiert.</span><span class="sxs-lookup"><span data-stu-id="ea8de-160">Without the `[ApiController]` attribute, binding source attributes are explicitly defined.</span></span> <span data-ttu-id="ea8de-161">Ohne `[ApiController]` oder ein anderes Bindungsquellattribut wie `[FromQuery]` versucht die ASP.NET Core-Runtime, die Modellbindung für komplexe Objekte zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="ea8de-161">Without `[ApiController]` or other binding source attributes like `[FromQuery]`, the ASP.NET Core runtime attempts to use the complex object model binder.</span></span> <span data-ttu-id="ea8de-162">Bei der Modellbindung für komplexe Objekte werden Daten aus Wertanbietern (mit definierter Reihenfolge) abgerufen.</span><span class="sxs-lookup"><span data-stu-id="ea8de-162">The complex object model binder pulls data from value providers (which have a defined order).</span></span> <span data-ttu-id="ea8de-163">Beispielsweise ist „body model binder“ immer eingeschlossen.</span><span class="sxs-lookup"><span data-stu-id="ea8de-163">For instance, the 'body model binder' is always opt in.</span></span>

<span data-ttu-id="ea8de-164">Im folgenden Beispiel gibt das `[FromQuery]`-Attribut an, dass der Parameterwert `discontinuedOnly` in der Abfragezeichenfolge der Anforderungs-URL bereitgestellt wird:</span><span class="sxs-lookup"><span data-stu-id="ea8de-164">In the following example, the `[FromQuery]` attribute indicates that the `discontinuedOnly` parameter value is provided in the request URL's query string:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

<span data-ttu-id="ea8de-165">Rückschlussregeln werden auf die Standarddatenquellen der Aktionsparameter angewendet.</span><span class="sxs-lookup"><span data-stu-id="ea8de-165">Inference rules are applied for the default data sources of action parameters.</span></span> <span data-ttu-id="ea8de-166">Diese Regeln konfigurieren die Bindungsquellen, die Sie sonst am ehesten manuell auf die Aktionsparameter anwenden.</span><span class="sxs-lookup"><span data-stu-id="ea8de-166">These rules configure the binding sources you're otherwise likely to manually apply to the action parameters.</span></span> <span data-ttu-id="ea8de-167">Die Bindungsquellenattribute verhalten sich wie folgt:</span><span class="sxs-lookup"><span data-stu-id="ea8de-167">The binding source attributes behave as follows:</span></span>

* <span data-ttu-id="ea8de-168">Für komplexe Typparameter wird **[FromBody]** abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="ea8de-168">**[FromBody]** is inferred for complex type parameters.</span></span> <span data-ttu-id="ea8de-169">Eine Ausnahme von der Regel ist jeder komplexe integrierte Typ mit spezieller Bedeutung, z.B. <xref:Microsoft.AspNetCore.Http.IFormCollection> und <xref:System.Threading.CancellationToken>.</span><span class="sxs-lookup"><span data-stu-id="ea8de-169">An exception to this rule is any complex, built-in type with a special meaning, such as <xref:Microsoft.AspNetCore.Http.IFormCollection> and <xref:System.Threading.CancellationToken>.</span></span> <span data-ttu-id="ea8de-170">Der Rückschlusscode der Bindungsquelle ignoriert diese Typen.</span><span class="sxs-lookup"><span data-stu-id="ea8de-170">The binding source inference code ignores those special types.</span></span> <span data-ttu-id="ea8de-171">`[FromBody]` wird für einfache Typen wie `string` oder `int` nicht abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="ea8de-171">`[FromBody]` isn't inferred for simple types such as `string` or `int`.</span></span> <span data-ttu-id="ea8de-172">Deshalb sollte das `[FromBody]`-Attribut für einfache Typen verwendet werden, wenn diese Funktionsweise benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="ea8de-172">Therefore, the `[FromBody]` attribute should be used for simple types when that functionality is needed.</span></span> <span data-ttu-id="ea8de-173">Wenn für eine Aktion mehr als ein Parameter explizit angegeben ist (über `[FromBody]`) oder als vom Anforderungstext gebunden hergeleitet wird, wird eine Ausnahme ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="ea8de-173">When an action has more than one parameter explicitly specified (via `[FromBody]`) or inferred as bound from the request body, an exception is thrown.</span></span> <span data-ttu-id="ea8de-174">Die folgenden Aktionssignaturen lösen beispielsweise eine Ausnahme aus:</span><span class="sxs-lookup"><span data-stu-id="ea8de-174">For example, the following action signatures cause an exception:</span></span>

    [!code-csharp[](define-controller/samples/WebApiSample.Api.21/Controllers/TestController.cs?name=snippet_ActionsCausingExceptions)]

    > [!NOTE]
    > <span data-ttu-id="ea8de-175">In ASP.NET Core 2.1 werden Sammlungstypparameter wie z.B. Listen und Arrays fälschlicherweise als [[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="ea8de-175">In ASP.NET Core 2.1, collection type parameters such as lists and arrays are incorrectly inferred as [[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute).</span></span> <span data-ttu-id="ea8de-176">Für diese Parameter sollte [[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) verwendet werden, wenn sie aus dem Anforderungstext gebunden werden sollen.</span><span class="sxs-lookup"><span data-stu-id="ea8de-176">[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) should be used for these parameters if they are to be bound from the request body.</span></span> <span data-ttu-id="ea8de-177">Dieses Verhalten wurde für ASP.NET Core 2.2 oder höher behoben. Hier werden Sammlungstypparameter standardmäßig als „[FromBody]“ abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="ea8de-177">This behavior is fixed in ASP.NET Core 2.2 or later, where collection type parameters are inferred to be bound from the body by default.</span></span>

* <span data-ttu-id="ea8de-178">**[FromForm]** wird für Aktionsparameter des Typs <xref:Microsoft.AspNetCore.Http.IFormFile> und <xref:Microsoft.AspNetCore.Http.IFormFileCollection> abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="ea8de-178">**[FromForm]** is inferred for action parameters of type <xref:Microsoft.AspNetCore.Http.IFormFile> and <xref:Microsoft.AspNetCore.Http.IFormFileCollection>.</span></span> <span data-ttu-id="ea8de-179">Es wird für keine einfachen oder benutzerdefinierte Typen abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="ea8de-179">It's not inferred for any simple or user-defined types.</span></span>
* <span data-ttu-id="ea8de-180">**[FromRoute]** wird für jeden Namen von Aktionsparametern abgeleitet, die mit einem Parameter in der Routenvorlage übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="ea8de-180">**[FromRoute]** is inferred for any action parameter name matching a parameter in the route template.</span></span> <span data-ttu-id="ea8de-181">Wenn mehr als eine Route mit einem Aktionsparameter übereinstimmt, gilt jeder Routenwert als `[FromRoute]`.</span><span class="sxs-lookup"><span data-stu-id="ea8de-181">When more than one route matches an action parameter, any route value is considered `[FromRoute]`.</span></span>
* <span data-ttu-id="ea8de-182">**[FromQuery]** wird für alle anderen Aktionsparameter abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="ea8de-182">**[FromQuery]** is inferred for any other action parameters.</span></span>

<span data-ttu-id="ea8de-183">Die standardmäßig festgelegten Rückschlussregeln werden deaktiviert, sobald die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> auf `true` festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="ea8de-183">The default inference rules are disabled when the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> property is set to `true`.</span></span> <span data-ttu-id="ea8de-184">Fügen Sie den folgenden Code zu `Startup.ConfigureServices` hinter `services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_<version_number>);` hinzu:</span><span class="sxs-lookup"><span data-stu-id="ea8de-184">Add the following code in `Startup.ConfigureServices` after `services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_<version_number>);`:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](define-controller/samples/WebApiSample.Api.22/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=6)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](define-controller/samples/WebApiSample.Api.21/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=4)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

### <a name="multipartform-data-request-inference"></a><span data-ttu-id="ea8de-185">Ableiten der Multipart/form-data-Anforderung</span><span class="sxs-lookup"><span data-stu-id="ea8de-185">Multipart/form-data request inference</span></span>

<span data-ttu-id="ea8de-186">Wenn ein Aktionsparameter mit dem [[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)-Attribut kommentiert wird, wird der Anforderungsinhaltstyp `multipart/form-data` abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="ea8de-186">When an action parameter is annotated with the [[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) attribute, the `multipart/form-data` request content type is inferred.</span></span>

<span data-ttu-id="ea8de-187">Das Standardverhalten wird deaktiviert, sobald die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> auf `true` festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="ea8de-187">The default behavior is disabled when the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> property is set to `true`.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="ea8de-188">Fügen Sie den folgenden Code zu `Startup.ConfigureServices` hinzu:</span><span class="sxs-lookup"><span data-stu-id="ea8de-188">Add the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.22/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=5)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="ea8de-189">Fügen Sie den folgenden Code zu `Startup.ConfigureServices` hinter `services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);` hinzu:</span><span class="sxs-lookup"><span data-stu-id="ea8de-189">Add the following code in `Startup.ConfigureServices` after `services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);`:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.21/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

### <a name="attribute-routing-requirement"></a><span data-ttu-id="ea8de-190">Anforderung für das Attributrouting</span><span class="sxs-lookup"><span data-stu-id="ea8de-190">Attribute routing requirement</span></span>

<span data-ttu-id="ea8de-191">Das Attributrouting wird zu einer Anforderung.</span><span class="sxs-lookup"><span data-stu-id="ea8de-191">Attribute routing becomes a requirement.</span></span> <span data-ttu-id="ea8de-192">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="ea8de-192">For example:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_ControllerSignature&highlight=1)]

<span data-ttu-id="ea8de-193">Sie können über [konventionelle Routen](xref:mvc/controllers/routing#conventional-routing), die in <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> oder von <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> in `Startup.Configure` definiert sind, nicht auf Aktionen zugreifen.</span><span class="sxs-lookup"><span data-stu-id="ea8de-193">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined in <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> or by <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> in `Startup.Configure`.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="problem-details-responses-for-error-status-codes"></a><span data-ttu-id="ea8de-194">Problemdetailantworten für Fehlerstatuscodes</span><span class="sxs-lookup"><span data-stu-id="ea8de-194">Problem details responses for error status codes</span></span>

<span data-ttu-id="ea8de-195">MVC wandelt in ASP.NET Core 2.2 oder höher ein Fehlerergebnis (ein Ergebnis mit dem Statuscode 400 oder höher) in ein Ergebnis mit <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> um.</span><span class="sxs-lookup"><span data-stu-id="ea8de-195">In ASP.NET Core 2.2 or later, MVC transforms an error result (a result with status code 400 or higher) to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span> <span data-ttu-id="ea8de-196">`ProblemDetails` ist:</span><span class="sxs-lookup"><span data-stu-id="ea8de-196">`ProblemDetails` is:</span></span>

* <span data-ttu-id="ea8de-197">ein Typ, der auf der [RCF 7807-Spezifikation](https://tools.ietf.org/html/rfc7807) basiert.</span><span class="sxs-lookup"><span data-stu-id="ea8de-197">A type based on the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807).</span></span>
* <span data-ttu-id="ea8de-198">ein standardisiertes Format zum Angeben maschinenlesbarer Fehlerdetails in einer HTTP-Antwort.</span><span class="sxs-lookup"><span data-stu-id="ea8de-198">A standardized format for specifying machine-readable error details in an HTTP response.</span></span>

<span data-ttu-id="ea8de-199">Betrachten Sie den folgenden Code in einer Controlleraktion:</span><span class="sxs-lookup"><span data-stu-id="ea8de-199">Consider the following code in a controller action:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.22/Controllers/ProductsController.cs?name=snippet_ProblemDetailsStatusCode)]

<span data-ttu-id="ea8de-200">Die HTTP-Antwort für `NotFound` hat den Statuscode 404 mit einem `ProblemDetails`-Körper.</span><span class="sxs-lookup"><span data-stu-id="ea8de-200">The HTTP response for `NotFound` has a 404 status code with a `ProblemDetails` body.</span></span> <span data-ttu-id="ea8de-201">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="ea8de-201">For example:</span></span>

```json
{
    type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
    title: "Not Found",
    status: 404,
    traceId: "0HLHLV31KRN83:00000001"
}
```

<span data-ttu-id="ea8de-202">Das Feature für Problemdetails erfordert das Kompatibilitätsflag 2.2 oder höher.</span><span class="sxs-lookup"><span data-stu-id="ea8de-202">The problem details feature requires a compatibility flag of 2.2 or later.</span></span> <span data-ttu-id="ea8de-203">Das Standardverhalten wird deaktiviert, sobald die Eigenschaft `SuppressMapClientErrors` auf `true` festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="ea8de-203">The default behavior is disabled when the `SuppressMapClientErrors` property is set to `true`.</span></span> <span data-ttu-id="ea8de-204">Fügen Sie den folgenden Code zu `Startup.ConfigureServices` hinzu:</span><span class="sxs-lookup"><span data-stu-id="ea8de-204">Add the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.22/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=8)]

<span data-ttu-id="ea8de-205">Verwenden Sie die `ClientErrorMapping`-Eigenschaft zum Konfigurieren des Inhalts der `ProblemDetails`-Antwort.</span><span class="sxs-lookup"><span data-stu-id="ea8de-205">Use the `ClientErrorMapping` property to configure the contents of the `ProblemDetails` response.</span></span> <span data-ttu-id="ea8de-206">Der folgenden Code aktualisiert beispielsweise die `type`-Eigenschaft für 404-Antworten:</span><span class="sxs-lookup"><span data-stu-id="ea8de-206">For example, the following code updates the `type` property for 404 responses:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.22/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=10-11)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="ea8de-207">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="ea8de-207">Additional resources</span></span>

* <xref:web-api/action-return-types>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>

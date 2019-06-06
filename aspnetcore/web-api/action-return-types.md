---
title: Rückgabetypen für Controlleraktionen in der ASP.NET Core-Web-API
author: scottaddie
description: In diesem Artikel erfahren Sie, wie die verschiedenen Rückgabetypen für Controlleraktionsmethoden in einer ASP.NET Core-Web-API verwendet werden.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/04/2019
uid: web-api/action-return-types
ms.openlocfilehash: b89ead55cd46ef62a3bc28b1cfc9077d3ce9aba2
ms.sourcegitcommit: a04eb20e81243930ec829a9db5dd5de49f669450
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/03/2019
ms.locfileid: "66470406"
---
# <a name="controller-action-return-types-in-aspnet-core-web-api"></a><span data-ttu-id="78205-103">Rückgabetypen für Controlleraktionen in der ASP.NET Core-Web-API</span><span class="sxs-lookup"><span data-stu-id="78205-103">Controller action return types in ASP.NET Core Web API</span></span>

<span data-ttu-id="78205-104">Von [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="78205-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="78205-105">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="78205-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="78205-106">In ASP.NET Core haben Sie die folgenden Optionen für Rückgabetypen für Web-API-Controlleraktionen:</span><span class="sxs-lookup"><span data-stu-id="78205-106">ASP.NET Core offers the following options for Web API controller action return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

* [<span data-ttu-id="78205-107">Spezifischer Typ</span><span class="sxs-lookup"><span data-stu-id="78205-107">Specific type</span></span>](#specific-type)
* [<span data-ttu-id="78205-108">IActionResult</span><span class="sxs-lookup"><span data-stu-id="78205-108">IActionResult</span></span>](#iactionresult-type)
* [<span data-ttu-id="78205-109">ActionResult\<T></span><span class="sxs-lookup"><span data-stu-id="78205-109">ActionResult\<T></span></span>](#actionresultt-type)

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* [<span data-ttu-id="78205-110">Spezifischer Typ</span><span class="sxs-lookup"><span data-stu-id="78205-110">Specific type</span></span>](#specific-type)
* [<span data-ttu-id="78205-111">IActionResult</span><span class="sxs-lookup"><span data-stu-id="78205-111">IActionResult</span></span>](#iactionresult-type)

::: moniker-end

<span data-ttu-id="78205-112">In diesem Artikel wird die Verwendung der einzelnen Rückgabetypen erklärt.</span><span class="sxs-lookup"><span data-stu-id="78205-112">This document explains when it's most appropriate to use each return type.</span></span>

## <a name="specific-type"></a><span data-ttu-id="78205-113">Spezifischer Typ</span><span class="sxs-lookup"><span data-stu-id="78205-113">Specific type</span></span>

<span data-ttu-id="78205-114">Die einfachste Aktion gibt einen primitiven oder komplexen Datentyp zurück (z.B. `string` oder einen benutzerdefinierten Objekttyp).</span><span class="sxs-lookup"><span data-stu-id="78205-114">The simplest action returns a primitive or complex data type (for example, `string` or a custom object type).</span></span> <span data-ttu-id="78205-115">Die folgende Aktion gibt eine Auflistung benutzerdefinierter `Product`-Objekte zurück:</span><span class="sxs-lookup"><span data-stu-id="78205-115">Consider the following action, which returns a collection of custom `Product` objects:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_Get)]

<span data-ttu-id="78205-116">Ohne bekannte Bedingungen, gegen die während der Ausführung einer Aktion Schutzmaßnahmen ergriffen werden müssen, reicht die Rückgabe eines spezifischen Typs möglicherweise aus.</span><span class="sxs-lookup"><span data-stu-id="78205-116">Without known conditions to safeguard against during action execution, returning a specific type could suffice.</span></span> <span data-ttu-id="78205-117">Die vorherige Aktion akzeptiert keine Parameter, weshalb eine Validierung von Parametereinschränkungen nicht erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="78205-117">The preceding action accepts no parameters, so parameter constraints validation isn't needed.</span></span>

<span data-ttu-id="78205-118">Wenn bekannte Bedingungen bei einer Aktion berücksichtig werden müssen, werden mehrere Rückgabepfade eingeführt.</span><span class="sxs-lookup"><span data-stu-id="78205-118">When known conditions need to be accounted for in an action, multiple return paths are introduced.</span></span> <span data-ttu-id="78205-119">In diesem Fall ist es üblich, einen [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult)-Rückgabetyp mit dem primitiven oder komplexen Rückgabetyp zu kombinieren.</span><span class="sxs-lookup"><span data-stu-id="78205-119">In such a case, it's common to mix an [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) return type with the primitive or complex return type.</span></span> <span data-ttu-id="78205-120">Für diesen Aktionstyp sind entweder der Rückgabetyp [IActionResult](#iactionresult-type) oder [ActionResult\<T>](#actionresultt-type) erforderlich.</span><span class="sxs-lookup"><span data-stu-id="78205-120">Either [IActionResult](#iactionresult-type) or [ActionResult\<T>](#actionresultt-type) are necessary to accommodate this type of action.</span></span>

## <a name="iactionresult-type"></a><span data-ttu-id="78205-121">IActionResult-Typ</span><span class="sxs-lookup"><span data-stu-id="78205-121">IActionResult type</span></span>

<span data-ttu-id="78205-122">Der Rückgabetyp [IActionResult](/dotnet/api/microsoft.aspnetcore.mvc.iactionresult) eignet sich in Fällen, in denen mehrere [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult)-Rückgabetypen in einer Aktion möglich sind.</span><span class="sxs-lookup"><span data-stu-id="78205-122">The [IActionResult](/dotnet/api/microsoft.aspnetcore.mvc.iactionresult) return type is appropriate when multiple [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) return types are possible in an action.</span></span> <span data-ttu-id="78205-123">Die `ActionResult`-Typen stellen verschiedene HTTP-Statuscodes dar.</span><span class="sxs-lookup"><span data-stu-id="78205-123">The `ActionResult` types represent various HTTP status codes.</span></span> <span data-ttu-id="78205-124">Einige gängige Rückgabetypen, die in diese Kategorie fallen, sind etwa [BadRequestResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestresult) (400), [NotFoundResult](/dotnet/api/microsoft.aspnetcore.mvc.notfoundresult) (404) und [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200).</span><span class="sxs-lookup"><span data-stu-id="78205-124">Some common return types falling into this category are [BadRequestResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestresult) (400), [NotFoundResult](/dotnet/api/microsoft.aspnetcore.mvc.notfoundresult) (404), and [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200).</span></span>

<span data-ttu-id="78205-125">Da es bei einer Aktion mehrere Rückgabetypen und Pfade gibt, sollte das Attribut [[ProducesResponseType]](/dotnet/api/microsoft.aspnetcore.mvc.producesresponsetypeattribute.-ctor) großzügig verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="78205-125">Because there are multiple return types and paths in the action, liberal use of the [[ProducesResponseType]](/dotnet/api/microsoft.aspnetcore.mvc.producesresponsetypeattribute.-ctor) attribute is necessary.</span></span> <span data-ttu-id="78205-126">Dieses Attribut erzeugt aussagekräftigere Antwortdetails für API-Hilfeseiten, die mit Tools wie [Swagger](/aspnet/core/tutorials/web-api-help-pages-using-swagger) erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="78205-126">This attribute produces more descriptive response details for API help pages generated by tools like [Swagger](/aspnet/core/tutorials/web-api-help-pages-using-swagger).</span></span> <span data-ttu-id="78205-127">`[ProducesResponseType]` gibt die bekannten Typen und HTTP-Statuscodes an, die von der Aktion zurückgegeben werden sollen.</span><span class="sxs-lookup"><span data-stu-id="78205-127">`[ProducesResponseType]` indicates the known types and HTTP status codes to be returned by the action.</span></span>

### <a name="synchronous-action"></a><span data-ttu-id="78205-128">Synchrone Aktion</span><span class="sxs-lookup"><span data-stu-id="78205-128">Synchronous action</span></span>

<span data-ttu-id="78205-129">Bei der folgenden synchronen Aktion gibt es zwei mögliche Rückgabetypen:</span><span class="sxs-lookup"><span data-stu-id="78205-129">Consider the following synchronous action in which there are two possible return types:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_GetById&highlight=8,11)]

<span data-ttu-id="78205-130">In der vorherigen Aktion wird der Statuscode 404 zurückgegeben, wenn das durch `id` dargestellte Produkt im zugrunde liegenden Datenspeicher nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="78205-130">In the preceding action, a 404 status code is returned when the product represented by `id` doesn't exist in the underlying data store.</span></span> <span data-ttu-id="78205-131">Die Hilfsmethode [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) wird als Verknüpfung zu `return new NotFoundResult();` aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="78205-131">The [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) helper method is invoked as a shortcut to `return new NotFoundResult();`.</span></span> <span data-ttu-id="78205-132">Ist das Produkt vorhanden, wird ein `Product`-Objekt, das die Nutzlast darstellt, mit dem Statuscode 200 zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="78205-132">If the product does exist, a `Product` object representing the payload is returned with a 200 status code.</span></span> <span data-ttu-id="78205-133">Die Hilfsmethode [Ok](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.ok) wird als Kurzform für `return new OkObjectResult(product);` aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="78205-133">The [Ok](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.ok) helper method is invoked as the shorthand form of `return new OkObjectResult(product);`.</span></span>

### <a name="asynchronous-action"></a><span data-ttu-id="78205-134">Asynchrone Aktion</span><span class="sxs-lookup"><span data-stu-id="78205-134">Asynchronous action</span></span>

<span data-ttu-id="78205-135">Bei der folgenden asynchronen Aktion gibt es zwei mögliche Rückgabetypen:</span><span class="sxs-lookup"><span data-stu-id="78205-135">Consider the following asynchronous action in which there are two possible return types:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_CreateAsync&highlight=8,13)]

<span data-ttu-id="78205-136">Für den Code oben gilt:</span><span class="sxs-lookup"><span data-stu-id="78205-136">In the preceding code:</span></span>

* <span data-ttu-id="78205-137">Ein Statuscode „400“ ([BadRequest](xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*)) wird von der ASP.NET Core-Runtime zurückgegeben, wenn die Produktbeschreibung „XYZ Widget“ enthält.</span><span class="sxs-lookup"><span data-stu-id="78205-137">A 400 status code ([BadRequest](xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*)) is returned by the ASP.NET Core runtime when the product description contains "XYZ Widget".</span></span>
* <span data-ttu-id="78205-138">Ein Statuscode „201“ wird von der Methode [CreatedAtAction](xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*) generiert, wenn ein Produkt erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="78205-138">A 201 status code is generated by the [CreatedAtAction](xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*) method when a product is created.</span></span> <span data-ttu-id="78205-139">In diesem Codepfad wird das `Product`-Objekt zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="78205-139">In this code path, the `Product` object is returned.</span></span>

<span data-ttu-id="78205-140">Das folgende Modell gibt beispielsweise an, dass Anforderungen die `Name`- und `Description`-Eigenschaften einbinden müssen.</span><span class="sxs-lookup"><span data-stu-id="78205-140">For example, the following model indicates that requests must include the `Name` and `Description` properties.</span></span> <span data-ttu-id="78205-141">Aus diesem Grund führt die fehlgeschlagene Bereitstellung eines `Name` und einer `Description` zu einer fehlgeschlagenen Modellvalidierung.</span><span class="sxs-lookup"><span data-stu-id="78205-141">Therefore, failure to provide `Name` and `Description` in the request causes model validation to fail.</span></span>

[!code-csharp[](../web-api/action-return-types/samples/WebApiSample.DataAccess/Models/Product.cs?name=snippet_ProductClass&highlight=5-6,8-9)]

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="78205-142">Wenn das [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)-Attribut in ASP.NET Core 2.1 oder höher angewendet wird, führen Modellvalidierungsfehler zu einem Statuscode „400“.</span><span class="sxs-lookup"><span data-stu-id="78205-142">If the [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute in ASP.NET Core 2.1 or later  is applied, model validation errors result in a 400 status code.</span></span> <span data-ttu-id="78205-143">Weitere Informationen finden Sie unter [Automatische HTTP 400-Antworten](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="78205-143">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="actionresultt-type"></a><span data-ttu-id="78205-144">ActionResult\<T>-Typ</span><span class="sxs-lookup"><span data-stu-id="78205-144">ActionResult\<T> type</span></span>

<span data-ttu-id="78205-145">In ASP.NET Core 2.1 wird der Rückgabetyp [ActionResult\<T>](/dotnet/api/microsoft.aspnetcore.mvc.actionresult-1) für Web-API-Controlleraktionen eingeführt.</span><span class="sxs-lookup"><span data-stu-id="78205-145">ASP.NET Core 2.1 introduces the [ActionResult\<T>](/dotnet/api/microsoft.aspnetcore.mvc.actionresult-1) return type for Web API controller actions.</span></span> <span data-ttu-id="78205-146">Damit wird die Rückgabe eines von [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) abgeleiteten Typs oder eines [spezifischen Typs](#specific-type) ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="78205-146">It enables you to return a type deriving from [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) or return a [specific type](#specific-type).</span></span> <span data-ttu-id="78205-147">`ActionResult<T>` besitzt gegenüber dem [IActionResult-Typ](#iactionresult-type) die folgenden Vorteile:</span><span class="sxs-lookup"><span data-stu-id="78205-147">`ActionResult<T>` offers the following benefits over the [IActionResult type](#iactionresult-type):</span></span>

* <span data-ttu-id="78205-148">Die `Type`-Eigenschaft des [[ProducesResponseType]](/dotnet/api/microsoft.aspnetcore.mvc.producesresponsetypeattribute)-Attributs kann ausgeschlossen werden.</span><span class="sxs-lookup"><span data-stu-id="78205-148">The [[ProducesResponseType]](/dotnet/api/microsoft.aspnetcore.mvc.producesresponsetypeattribute) attribute's `Type` property can be excluded.</span></span> <span data-ttu-id="78205-149">`[ProducesResponseType(200, Type = typeof(Product))]` wird beispielsweise zu `[ProducesResponseType(200)]` vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="78205-149">For example, `[ProducesResponseType(200, Type = typeof(Product))]` is simplified to `[ProducesResponseType(200)]`.</span></span> <span data-ttu-id="78205-150">Der erwartete Rückgabetyp der Aktion wird stattdessen von `T` in `ActionResult<T>` abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="78205-150">The action's expected return type is instead inferred from the `T` in `ActionResult<T>`.</span></span>
* <span data-ttu-id="78205-151">[Implizite Umwandlungsoperatoren](/dotnet/csharp/language-reference/keywords/implicit) unterstützen die Konvertierung von `T` und `ActionResult` in `ActionResult<T>`.</span><span class="sxs-lookup"><span data-stu-id="78205-151">[Implicit cast operators](/dotnet/csharp/language-reference/keywords/implicit) support the conversion of both `T` and `ActionResult` to `ActionResult<T>`.</span></span> <span data-ttu-id="78205-152">`T` wird in [ObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.objectresult) konvertiert, wodurch `return new ObjectResult(T);` in `return T;` vereinfacht wird.</span><span class="sxs-lookup"><span data-stu-id="78205-152">`T` converts to [ObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.objectresult), which means `return new ObjectResult(T);` is simplified to `return T;`.</span></span>

<span data-ttu-id="78205-153">C# unterstützt keine impliziten Umwandlungsoperatoren in Schnittstellen.</span><span class="sxs-lookup"><span data-stu-id="78205-153">C# doesn't support implicit cast operators on interfaces.</span></span> <span data-ttu-id="78205-154">Daher ist die Konvertierung der Schnittstelle in einen konkreten Typ erforderlich, um `ActionResult<T>` verwenden zu können.</span><span class="sxs-lookup"><span data-stu-id="78205-154">Consequently, conversion of the interface to a concrete type is necessary to use `ActionResult<T>`.</span></span> <span data-ttu-id="78205-155">Die Verwendung von `IEnumerable` im folgenden Beispiel funktioniert also nicht:</span><span class="sxs-lookup"><span data-stu-id="78205-155">For example, use of `IEnumerable` in the following example doesn't work:</span></span>

```csharp
[HttpGet]
public ActionResult<IEnumerable<Product>> Get()
{
    return _repository.GetProducts();
}
```

<span data-ttu-id="78205-156">Eine Möglichkeit zur Korrektur des oben stehenden Codes besteht darin, `_repository.GetProducts().ToList();` zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="78205-156">One option to fix the preceding code is to return `_repository.GetProducts().ToList();`.</span></span>

<span data-ttu-id="78205-157">Die meisten Aktionen haben einen bestimmten Rückgabetyp.</span><span class="sxs-lookup"><span data-stu-id="78205-157">Most actions have a specific return type.</span></span> <span data-ttu-id="78205-158">Während der Ausführung der Aktion können unerwartete Bedingungen auftreten, wodurch der spezifische Typ nicht zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="78205-158">Unexpected conditions can occur during action execution, in which case the specific type isn't returned.</span></span> <span data-ttu-id="78205-159">So kann beispielsweise die Modellvalidierung des Eingabeparameters einer Aktion fehlschlagen.</span><span class="sxs-lookup"><span data-stu-id="78205-159">For example, an action's input parameter may fail model validation.</span></span> <span data-ttu-id="78205-160">In diesem Fall wird üblicherweise der entsprechende `ActionResult`-Typ anstatt des spezifischen Typs zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="78205-160">In such a case, it's common to return the appropriate `ActionResult` type instead of the specific type.</span></span>

### <a name="synchronous-action"></a><span data-ttu-id="78205-161">Synchrone Aktion</span><span class="sxs-lookup"><span data-stu-id="78205-161">Synchronous action</span></span>

<span data-ttu-id="78205-162">Bei der folgenden synchronen Aktion gibt es zwei mögliche Rückgabetypen:</span><span class="sxs-lookup"><span data-stu-id="78205-162">Consider a synchronous action in which there are two possible return types:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetById&highlight=7,10)]

<span data-ttu-id="78205-163">Im vorherigen Code wird ein Statuscode 404 zurückgegeben, wenn das Produkt in der Datenbank nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="78205-163">In the preceding code, a 404 status code is returned when the product doesn't exist in the database.</span></span> <span data-ttu-id="78205-164">Ist das Produkt vorhanden, wird das entsprechende `Product`-Objekt zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="78205-164">If the product does exist, the corresponding `Product` object is returned.</span></span> <span data-ttu-id="78205-165">Vor ASP.NET Core 2.1 hätte die Zeile `return product;` stattdessen `return Ok(product);` gelautet.</span><span class="sxs-lookup"><span data-stu-id="78205-165">Before ASP.NET Core 2.1, the `return product;` line would have been `return Ok(product);`.</span></span>

> [!TIP]
> <span data-ttu-id="78205-166">Seit ASP.NET Core 2.1 wird der Rückschluss auf die Bindungsquelle des Aktionsparameters aktiviert, wenn eine Controllerklasse mit dem `[ApiController]`-Attribut ausgestattet ist.</span><span class="sxs-lookup"><span data-stu-id="78205-166">As of ASP.NET Core 2.1, action parameter binding source inference is enabled when a controller class is decorated with the `[ApiController]` attribute.</span></span> <span data-ttu-id="78205-167">Ein Parametername, der dem Namen einer Routenvorlage entspricht, wird automatisch mithilfe der Routendaten der Anforderung gebunden.</span><span class="sxs-lookup"><span data-stu-id="78205-167">A parameter name matching a name in the route template is automatically bound using the request route data.</span></span> <span data-ttu-id="78205-168">Folglich wird der `id`-Parameter der vorherigen Aktion nicht explizit mit dem Attribut [[FromRoute]](/dotnet/api/microsoft.aspnetcore.mvc.fromrouteattribute) versehen.</span><span class="sxs-lookup"><span data-stu-id="78205-168">Consequently, the preceding action's `id` parameter isn't explicitly annotated with the [[FromRoute]](/dotnet/api/microsoft.aspnetcore.mvc.fromrouteattribute) attribute.</span></span>

### <a name="asynchronous-action"></a><span data-ttu-id="78205-169">Asynchrone Aktion</span><span class="sxs-lookup"><span data-stu-id="78205-169">Asynchronous action</span></span>

<span data-ttu-id="78205-170">Bei der folgenden asynchronen Aktion gibt es zwei mögliche Rückgabetypen:</span><span class="sxs-lookup"><span data-stu-id="78205-170">Consider an asynchronous action in which there are two possible return types:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsync&highlight=8,13)]

<span data-ttu-id="78205-171">Für den Code oben gilt:</span><span class="sxs-lookup"><span data-stu-id="78205-171">In the preceding code:</span></span>

* <span data-ttu-id="78205-172">Ein Statuscode „400“ ([BadRequest](xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*)) wird durch die ASP.NET Core-Runtime zurückgegeben, wenn:</span><span class="sxs-lookup"><span data-stu-id="78205-172">A 400 status code ([BadRequest](xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*)) is returned by the ASP.NET Core runtime when:</span></span>
  * <span data-ttu-id="78205-173">Das [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)-Attribut angewendet wurde und die Modellvalidierung fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="78205-173">The [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute has been applied and model validation fails.</span></span>
  * <span data-ttu-id="78205-174">Die Produktbeschreibung „XYZ-Widget“ enthält.</span><span class="sxs-lookup"><span data-stu-id="78205-174">The product description contains "XYZ Widget".</span></span>
* <span data-ttu-id="78205-175">Ein Statuscode „201“ wird von der Methode [CreatedAtAction](xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*) generiert, wenn ein Produkt erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="78205-175">A 201 status code is generated by the [CreatedAtAction](xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*) method when a product is created.</span></span> <span data-ttu-id="78205-176">In diesem Codepfad wird das `Product`-Objekt zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="78205-176">In this code path, the `Product` object is returned.</span></span>

> [!TIP]
> <span data-ttu-id="78205-177">Seit ASP.NET Core 2.1 wird der Rückschluss auf die Bindungsquelle des Aktionsparameters aktiviert, wenn eine Controllerklasse mit dem `[ApiController]`-Attribut ausgestattet ist.</span><span class="sxs-lookup"><span data-stu-id="78205-177">As of ASP.NET Core 2.1, action parameter binding source inference is enabled when a controller class is decorated with the `[ApiController]` attribute.</span></span> <span data-ttu-id="78205-178">Komplexe Typparameter werden automatisch mithilfe des Anforderungstexts gebunden.</span><span class="sxs-lookup"><span data-stu-id="78205-178">Complex type parameters are automatically bound using the request body.</span></span> <span data-ttu-id="78205-179">Folglich wird der `product`-Parameter der vorherigen Aktion nicht explizit mit dem Attribut [[FromBody]](/dotnet/api/microsoft.aspnetcore.mvc.frombodyattribute) versehen.</span><span class="sxs-lookup"><span data-stu-id="78205-179">Consequently, the preceding action's `product` parameter isn't explicitly annotated with the [[FromBody]](/dotnet/api/microsoft.aspnetcore.mvc.frombodyattribute) attribute.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="78205-180">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="78205-180">Additional resources</span></span>

* <xref:mvc/controllers/actions>
* <xref:mvc/models/validation>
* <xref:tutorials/web-api-help-pages-using-swagger>

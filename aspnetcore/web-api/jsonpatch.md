---
title: JsonPatch in ASP.NET Core-Web-API
author: tdykstra
description: Erfahren Sie, wie JSON Patch-Anforderungen in einer ASP.NET Core-Web-API behandelt werden.
ms.author: tdykstra
ms.custom: mvc
ms.date: 03/24/2019
uid: web-api/jsonpatch
ms.openlocfilehash: 14710e6431a2a7ce60fa7f190bef184da85281a0
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64888415"
---
# <a name="jsonpatch-in-aspnet-core-web-api"></a><span data-ttu-id="f6d1c-103">JsonPatch in ASP.NET Core-Web-API</span><span class="sxs-lookup"><span data-stu-id="f6d1c-103">JsonPatch in ASP.NET Core web API</span></span>

<span data-ttu-id="f6d1c-104">Von [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="f6d1c-104">By [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="f6d1c-105">In diesem Artikel wird erläutert, wie JSON Patch-Anforderungen in einer ASP.NET Core-Web-API behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-105">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="patch-http-request-method"></a><span data-ttu-id="f6d1c-106">PATCH HTTP-Anforderungsmethode</span><span class="sxs-lookup"><span data-stu-id="f6d1c-106">PATCH HTTP request method</span></span>

<span data-ttu-id="f6d1c-107">Die Methoden PUT und [PATCH](https://tools.ietf.org/html/rfc5789) werden verwendet, um eine vorhandene Ressource zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-107">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="f6d1c-108">Der Unterschied zwischen den beiden Methoden besteht darin, dass PUT die gesamte Ressource ersetzt, während PATCH nur die Änderungen angibt.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-108">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="f6d1c-109">JSON Patch</span><span class="sxs-lookup"><span data-stu-id="f6d1c-109">JSON Patch</span></span>

<span data-ttu-id="f6d1c-110">Mit dem [JSON Patch](https://tools.ietf.org/html/rfc6902)-Format geben Sie an, dass Updates auf eine Ressource angewendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-110">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="f6d1c-111">Ein JSON Patch-Dokument verfügt über ein Array von *Vorgängen*.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-111">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="f6d1c-112">Jeder Vorgang identifiziert eine bestimmte Art von Änderung, z.B. das Hinzufügen eines Arrayelements oder das Ersetzen eines Eigenschaftswerts.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-112">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="f6d1c-113">Die folgenden JSON-Dokumente stellen beispielsweise eine Ressource, ein JSON Patch-Dokument für die Ressource und das Ergebnis der Anwendung der Patchvorgänge dar.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-113">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="f6d1c-114">Ressourcenbeispiel</span><span class="sxs-lookup"><span data-stu-id="f6d1c-114">Resource example</span></span>

[!code-csharp[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="f6d1c-115">JSON Patch-Beispiel</span><span class="sxs-lookup"><span data-stu-id="f6d1c-115">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="f6d1c-116">Für den oben stehenden JSON-Code gilt:</span><span class="sxs-lookup"><span data-stu-id="f6d1c-116">In the preceding JSON:</span></span>

* <span data-ttu-id="f6d1c-117">Die `op`-Eigenschaft gibt den Typ des Vorgangs an.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-117">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="f6d1c-118">Die `path`-Eigenschaft gibt das zu aktualisierende Element an.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-118">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="f6d1c-119">Die `value`-Eigenschaft stellt den neuen Wert bereit.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-119">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="f6d1c-120">Ressource nach dem Patch</span><span class="sxs-lookup"><span data-stu-id="f6d1c-120">Resource after patch</span></span>

<span data-ttu-id="f6d1c-121">So sieht die Ressource nach der Anwendung des voranstehenden JSON Patch-Dokuments aus:</span><span class="sxs-lookup"><span data-stu-id="f6d1c-121">Here's the resource after applying the preceding JSON Patch document:</span></span>

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

<span data-ttu-id="f6d1c-122">Die Änderungen, die durch Anwenden eines JSON Patch-Dokuments auf eine Ressource vorgenommen werden, sind unteilbar: Wenn ein Vorgang aus der Liste fehlschlägt, wird kein Vorgang aus der Liste angewendet.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-122">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="f6d1c-123">Pfadsyntax</span><span class="sxs-lookup"><span data-stu-id="f6d1c-123">Path syntax</span></span>

<span data-ttu-id="f6d1c-124">Die [path](http://tools.ietf.org/html/rfc6901)-Eigenschaft eines Vorgangsobjekts weist Schrägstriche zwischen Ebenen auf.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-124">The [path](http://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="f6d1c-125">Beispielsweise `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-125">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="f6d1c-126">Nullbasierte Indizes werden verwendet, um Arrayelemente anzugeben.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-126">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="f6d1c-127">Das erste Element des `addresses`-Arrays wäre bei `/addresses/0`.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-127">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="f6d1c-128">Zum `add` ans Ende eines Arrays verwenden Sie einen Bindestrich (-) anstelle einer Indexnummer: `/addresses/-`.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-128">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="f6d1c-129">Vorgänge</span><span class="sxs-lookup"><span data-stu-id="f6d1c-129">Operations</span></span>

<span data-ttu-id="f6d1c-130">Die folgende Tabelle zeigt unterstützt Vorgänge gemäß der [JSON Patch-Spezifikation](https://tools.ietf.org/html/rfc6902):</span><span class="sxs-lookup"><span data-stu-id="f6d1c-130">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="f6d1c-131">Vorgang</span><span class="sxs-lookup"><span data-stu-id="f6d1c-131">Operation</span></span>  | <span data-ttu-id="f6d1c-132">Hinweise</span><span class="sxs-lookup"><span data-stu-id="f6d1c-132">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="f6d1c-133">Hinzufügen einer Eigenschaft oder eines Arrayelements.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-133">Add a property or array element.</span></span> <span data-ttu-id="f6d1c-134">Für vorhandene Eigenschaft: set value.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-134">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="f6d1c-135">Entfernen einer Eigenschaft oder eines Arrayelements.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-135">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="f6d1c-136">Identisch mit `remove`, gefolgt von `add` an gleicher Stelle.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-136">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="f6d1c-137">Identisch mit `remove` aus der Quelle, gefolgt von `add` zum Ziel unter Verwendung des Werts aus der Quelle.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-137">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="f6d1c-138">Identisch mit `add` zum Ziel unter Verwendung des Werts aus der Quelle.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-138">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="f6d1c-139">Gibt Statuscode für Erfolg zurück, wenn der Wert von `path` = bereitgestellter `value`.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-139">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="f6d1c-140">JsonPatch in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f6d1c-140">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="f6d1c-141">Die ASP.NET Core-Implementierung von JSON Patch wird im [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/)-NuGet-Paket bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-141">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span> <span data-ttu-id="f6d1c-142">Das Paket ist im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) enthalten.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-142">The package is included in the [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) metapackage.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="f6d1c-143">Aktionsmethodencode</span><span class="sxs-lookup"><span data-stu-id="f6d1c-143">Action method code</span></span>

<span data-ttu-id="f6d1c-144">Eine Aktionsmethode für JSON Patch in einem API-Controller:</span><span class="sxs-lookup"><span data-stu-id="f6d1c-144">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="f6d1c-145">Ist versehen mit dem `HttpPatch`-Attribut.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-145">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="f6d1c-146">Akzeptiert ein `JsonPatchDocument<T>`, in der Regel mit [FromBody].</span><span class="sxs-lookup"><span data-stu-id="f6d1c-146">Accepts a `JsonPatchDocument<T>`, typically with [FromBody].</span></span>
* <span data-ttu-id="f6d1c-147">Ruft `ApplyTo` für das Patch-Dokument auf, um die Änderungen anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-147">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="f6d1c-148">Im Folgenden ein Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f6d1c-148">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="f6d1c-149">Dieser Code aus der Beispiel-App funktioniert mit den folgenden `Customer`-Modell.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-149">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="f6d1c-150">Die Beispielaktionsmethode:</span><span class="sxs-lookup"><span data-stu-id="f6d1c-150">The sample action method:</span></span>

* <span data-ttu-id="f6d1c-151">Erstellt ein Objekt vom Typ `Customer`.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-151">Constructs a `Customer`.</span></span>
* <span data-ttu-id="f6d1c-152">Wendet den Patch an.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-152">Applies the patch.</span></span>
* <span data-ttu-id="f6d1c-153">Gibt das Ergebnis wird im Textkörper der Antwort zurück.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-153">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="f6d1c-154">In einer realen App würde der Code die Daten aus einem Speicher wie z.B. einer Datenbank abrufen und die Datenbank nach dem Anwenden des Patchs aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-154">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="f6d1c-155">Modellstatus</span><span class="sxs-lookup"><span data-stu-id="f6d1c-155">Model state</span></span>

<span data-ttu-id="f6d1c-156">Das voranstehende Aktionsmethodenbeispiel ruft eine Überladung von `ApplyTo` auf, die den Modellstatus als einen seiner Parameter akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-156">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="f6d1c-157">Mit dieser Option können Sie Fehlermeldungen in Antworten abrufen.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-157">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="f6d1c-158">Das folgende Beispiel zeigt den Textkörper einer „400 (Ungültige Anforderung)-Antwort für einen `test` Vorgang:</span><span class="sxs-lookup"><span data-stu-id="f6d1c-158">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="f6d1c-159">Dynamische Objekte</span><span class="sxs-lookup"><span data-stu-id="f6d1c-159">Dynamic objects</span></span>

<span data-ttu-id="f6d1c-160">Das folgende Aktionsmethodenbeispiel veranschaulicht das Anwenden ein Patchs auf ein dynamisches Objekt.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-160">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="f6d1c-161">Add-Vorgang (Hinzufügen)</span><span class="sxs-lookup"><span data-stu-id="f6d1c-161">The add operation</span></span>

* <span data-ttu-id="f6d1c-162">Wenn `path` auf ein Arrayelement verweist: Fügt ein neues Element vor dem von `path` angegebenen Element ein.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-162">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="f6d1c-163">Wenn `path` auf eine Eigenschaft verweist: Legt den Eigenschaftswert fest.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-163">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="f6d1c-164">Wenn `path` auf einen nicht vorhandenen Speicherort verweist:</span><span class="sxs-lookup"><span data-stu-id="f6d1c-164">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="f6d1c-165">Wenn die Ressource, auf die der Patch angewendet werden soll, ein dynamisches Objekt ist: Fügt eine Eigenschaft hinzu.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-165">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="f6d1c-166">Wenn die Ressource, auf die der Patch angewendet werden soll, ein statisches Objekt ist: Die Anforderung schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-166">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="f6d1c-167">Das folgende Patch-Dokumentbeispiel legt den Wert von `CustomerName` fest und fügt ein `Order`-Objekt am Ende des `Orders`-Arrays hinzu.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-167">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="f6d1c-168">Remove-Vorgang (Entfernen)</span><span class="sxs-lookup"><span data-stu-id="f6d1c-168">The remove operation</span></span>

* <span data-ttu-id="f6d1c-169">Wenn `path` auf ein Arrayelement verweist: Entfernt das Element.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-169">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="f6d1c-170">Wenn `path` auf eine Eigenschaft verweist:</span><span class="sxs-lookup"><span data-stu-id="f6d1c-170">If `path` points to a property:</span></span>
  * <span data-ttu-id="f6d1c-171">Wenn die Ressource, auf die der Patch angewendet werden soll, ein dynamisches Objekt ist: Entfernt die Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-171">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="f6d1c-172">Wenn die Ressource, auf die der Patch angewendet werden soll, ein statisches Objekt ist:</span><span class="sxs-lookup"><span data-stu-id="f6d1c-172">If resource to patch is a static object:</span></span> 
    * <span data-ttu-id="f6d1c-173">Wenn die Eigenschaft NULL-Werte zulässt: auf Null festlegen.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-173">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="f6d1c-174">Wenn die Eigenschaft keine NULL-Werte zulässt: auf `default<T>` festlegen.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-174">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="f6d1c-175">Im folgenden Beispiel legt das Patch-Dokument `CustomerName` auf Null fest und löscht `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-175">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="f6d1c-176">Replace-Vorgang (Ersetzen)</span><span class="sxs-lookup"><span data-stu-id="f6d1c-176">The replace operation</span></span>

<span data-ttu-id="f6d1c-177">Dieser Vorgang ist funktionell identisch mit einem `remove`, gefolgt von einem `add`.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-177">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="f6d1c-178">Das folgende Patch-Dokumentbeispiel legt den Wert von `CustomerName` fest und ersetzt `Orders[0]` durch ein neues `Order`-Objekt.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-178">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="f6d1c-179">Move-Vorgang (Verschieben)</span><span class="sxs-lookup"><span data-stu-id="f6d1c-179">The move operation</span></span>

* <span data-ttu-id="f6d1c-180">Wenn `path` auf ein Arrayelement verweist: Kopiert das `from`-Element an den von `path` angegebenen Speicherort und führt dann einen `remove`-Vorgang für das `from`-Element aus.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-180">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="f6d1c-181">Wenn `path` auf eine Eigenschaft verweist: Kopiert den Wert der `from`-Eigenschaft in die `path`-Eigenschaft, und führt dann einen `remove`-Vorgang für die `from`-Eigenschaft aus.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-181">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="f6d1c-182">Wenn `path` auf eine nicht vorhandene Eigenschaft verweist:</span><span class="sxs-lookup"><span data-stu-id="f6d1c-182">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="f6d1c-183">Wenn die Ressource, auf die der Patch angewendet werden soll, ein statisches Objekt ist: Die Anforderung schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-183">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="f6d1c-184">Wenn die Ressource, auf die der Patch angewendet werden soll, dynamisches Objekt ist: Kopiert die `from`-Eigenschaft an den von `path` angegebenen Speicherort, und führt dann einen `remove`-Vorgang für die `from`-Eigenschaft aus.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-184">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="f6d1c-185">Das folgende Patch-Dokumentbeispiel:</span><span class="sxs-lookup"><span data-stu-id="f6d1c-185">The following sample patch document:</span></span>

* <span data-ttu-id="f6d1c-186">Kopiert den Wert der `Orders[0].OrderName` nach `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-186">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="f6d1c-187">Legt `Orders[0].OrderName` auf Null fest.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-187">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="f6d1c-188">Verschiebt `Orders[1]` vor `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-188">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="f6d1c-189">Copy-Vorgang (Kopieren)</span><span class="sxs-lookup"><span data-stu-id="f6d1c-189">The copy operation</span></span>

<span data-ttu-id="f6d1c-190">Dieser Vorgang ist funktionell identisch mit einem `move`-Vorgang ohne den abschließenden `remove`-Schritt.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-190">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="f6d1c-191">Das folgende Patch-Dokumentbeispiel:</span><span class="sxs-lookup"><span data-stu-id="f6d1c-191">The following sample patch document:</span></span>

* <span data-ttu-id="f6d1c-192">Kopiert den Wert der `Orders[0].OrderName` nach `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-192">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="f6d1c-193">Fügt eine Kopie von `Orders[1]` vor `Orders[0]` ein.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-193">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="f6d1c-194">Test-Vorgang (Testen)</span><span class="sxs-lookup"><span data-stu-id="f6d1c-194">The test operation</span></span>

<span data-ttu-id="f6d1c-195">Wenn der Wert an dem von `path` angegebenen Speicherort sich von dem in `value` bereitgestellten Wert unterscheidet, schlägt die Anforderung fehl.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-195">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="f6d1c-196">In diesem Fall schlägt die gesamte PATCH-Anforderung fehl, selbst wenn alle anderen Vorgänge im Patch-Dokument ansonsten erfolgreich ausgeführt werden könnten.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-196">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="f6d1c-197">Der `test`-Vorgang wird häufig verwendet, um ein Update zu verhindern, wenn ein Parallelitätskonflikt vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-197">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="f6d1c-198">Das folgende Patch-Dokumentbeispiel hat keine Auswirkungen, wenn der Anfangswert von `CustomerName` „John“ ist, da der Test fehlschlägt:</span><span class="sxs-lookup"><span data-stu-id="f6d1c-198">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="f6d1c-199">Abrufen des Codes</span><span class="sxs-lookup"><span data-stu-id="f6d1c-199">Get the code</span></span>

<span data-ttu-id="f6d1c-200">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2)</span><span class="sxs-lookup"><span data-stu-id="f6d1c-200">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="f6d1c-201">([Informationen zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f6d1c-201">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="f6d1c-202">Um das Beispiel zu testen, führen Sie die App aus, und senden Sie HTTP-Anforderungen mit den folgenden Einstellungen:</span><span class="sxs-lookup"><span data-stu-id="f6d1c-202">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="f6d1c-203">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="f6d1c-203">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="f6d1c-204">HTTP-Methode: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="f6d1c-204">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="f6d1c-205">Header: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="f6d1c-205">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="f6d1c-206">Hauptteil: Kopieren und Einfügen eines der JSON-Patch-Dokumentbeispiele aus dem *JSON*-Projektordner.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-206">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f6d1c-207">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="f6d1c-207">Additional resources</span></span>

* [<span data-ttu-id="f6d1c-208">IETF RFC 5789 PATCH-Methodenspezifikation</span><span class="sxs-lookup"><span data-stu-id="f6d1c-208">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="f6d1c-209">IETF RFC 6902 JSON Patch-Spezifikation</span><span class="sxs-lookup"><span data-stu-id="f6d1c-209">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="f6d1c-210">IETF RFC 6901 JSON Patch-Pfadformatspezifikation</span><span class="sxs-lookup"><span data-stu-id="f6d1c-210">IETF RFC 6901 JSON Patch path format spec</span></span>](http://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="f6d1c-211">[JSON Patch-Dokumentation](http://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="f6d1c-211">[JSON Patch documentation](http://jsonpatch.com/).</span></span> <span data-ttu-id="f6d1c-212">Enthält Links zu Ressourcen zum Erstellen von JSON Patch-Dokumenten.</span><span class="sxs-lookup"><span data-stu-id="f6d1c-212">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="f6d1c-213">ASP.NET Core JSON Patch-Quellcode</span><span class="sxs-lookup"><span data-stu-id="f6d1c-213">ASP.NET Core JSON Patch source code</span></span>](https://github.com/aspnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

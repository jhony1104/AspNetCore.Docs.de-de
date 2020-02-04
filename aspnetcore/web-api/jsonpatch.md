---
title: JsonPatch in ASP.NET Core-Web-API
author: rick-anderson
description: Erfahren Sie, wie JSON Patch-Anforderungen in einer ASP.NET Core-Web-API behandelt werden.
ms.author: riande
ms.custom: mvc
ms.date: 11/01/2019
uid: web-api/jsonpatch
ms.openlocfilehash: e57556e4b3fba55c6c187092593ffab4e31ee2d9
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76727036"
---
# <a name="jsonpatch-in-aspnet-core-web-api"></a><span data-ttu-id="83750-103">JsonPatch in ASP.NET Core-Web-API</span><span class="sxs-lookup"><span data-stu-id="83750-103">JsonPatch in ASP.NET Core web API</span></span>

<span data-ttu-id="83750-104">Von [Tom Dykstra](https://github.com/tdykstra) und [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="83750-104">By [Tom Dykstra](https://github.com/tdykstra) and [Kirk Larkin](https://github.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="83750-105">In diesem Artikel wird erläutert, wie JSON Patch-Anforderungen in einer ASP.NET Core-Web-API behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="83750-105">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="package-installation"></a><span data-ttu-id="83750-106">Paketinstallation</span><span class="sxs-lookup"><span data-stu-id="83750-106">Package installation</span></span>

<span data-ttu-id="83750-107">Support für JSON Patch kann mithilfe des `Microsoft.AspNetCore.Mvc.NewtonsoftJson`-Pakets aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="83750-107">Support for JsonPatch is enabled using the `Microsoft.AspNetCore.Mvc.NewtonsoftJson` package.</span></span> <span data-ttu-id="83750-108">Für Apps ist Folgendes erforderlich, damit dieses Feature aktiviert werden kann:</span><span class="sxs-lookup"><span data-stu-id="83750-108">To enable this feature, apps must:</span></span>

* <span data-ttu-id="83750-109">Installieren des NuGet-Pakets [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/).</span><span class="sxs-lookup"><span data-stu-id="83750-109">Install the [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package.</span></span>
* <span data-ttu-id="83750-110">Aktualisieren Sie die `Startup.ConfigureServices`-Methode des Projekts so, dass diese einen Aufruf von `AddNewtonsoftJson` enthält:</span><span class="sxs-lookup"><span data-stu-id="83750-110">Update the project's `Startup.ConfigureServices` method to include a call to `AddNewtonsoftJson`:</span></span>

  ```csharp
  services
      .AddControllersWithViews()
      .AddNewtonsoftJson();
  ```

<span data-ttu-id="83750-111">`AddNewtonsoftJson` ist mit den folgenden MVC-Dienstregistrierungsmethoden kompatibel:</span><span class="sxs-lookup"><span data-stu-id="83750-111">`AddNewtonsoftJson` is compatible with the MVC service registration methods:</span></span>

  * `AddRazorPages`
  * `AddControllersWithViews`
  * `AddControllers`

## <a name="jsonpatch-addnewtonsoftjson-and-systemtextjson"></a><span data-ttu-id="83750-112">JsonPatch, AddNewtonsoftJson und System.Text.Json</span><span class="sxs-lookup"><span data-stu-id="83750-112">JsonPatch, AddNewtonsoftJson, and System.Text.Json</span></span>
  
<span data-ttu-id="83750-113">`AddNewtonsoftJson` ersetzt die auf `System.Text.Json` basierten Eingabe- und Ausgabeformatierer, die für die Formatierung **aller** JSON-Inhalte verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="83750-113">`AddNewtonsoftJson` replaces the `System.Text.Json` based input and output formatters used for formatting **all** JSON content.</span></span> <span data-ttu-id="83750-114">Aktualisieren Sie die Methode `Startup.ConfigureServices` des Projekts folgendermaßen, wenn Sie mithilfe von `Newtonsoft.Json` Support für `JsonPatch` hinzuzufügen möchten, während alle anderen Formatierer davon unbeeinflusst bleiben:</span><span class="sxs-lookup"><span data-stu-id="83750-114">To add support for `JsonPatch` using `Newtonsoft.Json`, while leaving the other formatters unchanged, update the project's `Startup.ConfigureServices` as follows:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

<span data-ttu-id="83750-115">Für den vorangehenden Code ist ein Verweis auf [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson) und das Verwenden folgender Anweisungen erforderlich:</span><span class="sxs-lookup"><span data-stu-id="83750-115">The preceding code requires a reference to [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson) and the following using statements:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a><span data-ttu-id="83750-116">PATCH HTTP-Anforderungsmethode</span><span class="sxs-lookup"><span data-stu-id="83750-116">PATCH HTTP request method</span></span>

<span data-ttu-id="83750-117">Die Methoden PUT und [PATCH](https://tools.ietf.org/html/rfc5789) werden verwendet, um eine vorhandene Ressource zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="83750-117">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="83750-118">Der Unterschied zwischen den beiden Methoden besteht darin, dass PUT die gesamte Ressource ersetzt, während PATCH nur die Änderungen angibt.</span><span class="sxs-lookup"><span data-stu-id="83750-118">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="83750-119">JSON Patch</span><span class="sxs-lookup"><span data-stu-id="83750-119">JSON Patch</span></span>

<span data-ttu-id="83750-120">Mit dem [JSON Patch](https://tools.ietf.org/html/rfc6902)-Format geben Sie an, dass Updates auf eine Ressource angewendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="83750-120">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="83750-121">Ein JSON Patch-Dokument verfügt über ein Array von *Vorgängen*.</span><span class="sxs-lookup"><span data-stu-id="83750-121">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="83750-122">Jeder Vorgang identifiziert eine bestimmte Art von Änderung, z.B. das Hinzufügen eines Arrayelements oder das Ersetzen eines Eigenschaftswerts.</span><span class="sxs-lookup"><span data-stu-id="83750-122">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="83750-123">Die folgenden JSON-Dokumente stellen beispielsweise eine Ressource, ein JSON Patch-Dokument für die Ressource und das Ergebnis der Anwendung der Patchvorgänge dar.</span><span class="sxs-lookup"><span data-stu-id="83750-123">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="83750-124">Ressourcenbeispiel</span><span class="sxs-lookup"><span data-stu-id="83750-124">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="83750-125">JSON Patch-Beispiel</span><span class="sxs-lookup"><span data-stu-id="83750-125">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="83750-126">Für den oben stehenden JSON-Code gilt:</span><span class="sxs-lookup"><span data-stu-id="83750-126">In the preceding JSON:</span></span>

* <span data-ttu-id="83750-127">Die `op`-Eigenschaft gibt den Typ des Vorgangs an.</span><span class="sxs-lookup"><span data-stu-id="83750-127">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="83750-128">Die `path`-Eigenschaft gibt das zu aktualisierende Element an.</span><span class="sxs-lookup"><span data-stu-id="83750-128">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="83750-129">Die `value`-Eigenschaft stellt den neuen Wert bereit.</span><span class="sxs-lookup"><span data-stu-id="83750-129">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="83750-130">Ressource nach dem Patch</span><span class="sxs-lookup"><span data-stu-id="83750-130">Resource after patch</span></span>

<span data-ttu-id="83750-131">So sieht die Ressource nach der Anwendung des voranstehenden JSON Patch-Dokuments aus:</span><span class="sxs-lookup"><span data-stu-id="83750-131">Here's the resource after applying the preceding JSON Patch document:</span></span>

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

<span data-ttu-id="83750-132">Die Änderungen, die durch Anwenden eines JSON Patch-Dokuments auf eine Ressource vorgenommen werden, sind unteilbar: Wenn ein Vorgang aus der Liste fehlschlägt, wird kein Vorgang aus der Liste angewendet.</span><span class="sxs-lookup"><span data-stu-id="83750-132">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="83750-133">Pfadsyntax</span><span class="sxs-lookup"><span data-stu-id="83750-133">Path syntax</span></span>

<span data-ttu-id="83750-134">Die [path](https://tools.ietf.org/html/rfc6901)-Eigenschaft eines Vorgangsobjekts weist Schrägstriche zwischen Ebenen auf.</span><span class="sxs-lookup"><span data-stu-id="83750-134">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="83750-135">Beispielsweise `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="83750-135">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="83750-136">Nullbasierte Indizes werden verwendet, um Arrayelemente anzugeben.</span><span class="sxs-lookup"><span data-stu-id="83750-136">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="83750-137">Das erste Element des `addresses`-Arrays wäre bei `/addresses/0`.</span><span class="sxs-lookup"><span data-stu-id="83750-137">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="83750-138">Zum `add` ans Ende eines Arrays verwenden Sie einen Bindestrich (-) anstelle einer Indexnummer: `/addresses/-`.</span><span class="sxs-lookup"><span data-stu-id="83750-138">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="83750-139">Vorgänge</span><span class="sxs-lookup"><span data-stu-id="83750-139">Operations</span></span>

<span data-ttu-id="83750-140">Die folgende Tabelle zeigt unterstützt Vorgänge gemäß der [JSON Patch-Spezifikation](https://tools.ietf.org/html/rfc6902):</span><span class="sxs-lookup"><span data-stu-id="83750-140">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="83750-141">Vorgang</span><span class="sxs-lookup"><span data-stu-id="83750-141">Operation</span></span>  | <span data-ttu-id="83750-142">Hinweise</span><span class="sxs-lookup"><span data-stu-id="83750-142">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="83750-143">Hinzufügen einer Eigenschaft oder eines Arrayelements.</span><span class="sxs-lookup"><span data-stu-id="83750-143">Add a property or array element.</span></span> <span data-ttu-id="83750-144">Für vorhandene Eigenschaft: set value.</span><span class="sxs-lookup"><span data-stu-id="83750-144">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="83750-145">Entfernen einer Eigenschaft oder eines Arrayelements.</span><span class="sxs-lookup"><span data-stu-id="83750-145">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="83750-146">Identisch mit `remove`, gefolgt von `add` an gleicher Stelle.</span><span class="sxs-lookup"><span data-stu-id="83750-146">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="83750-147">Identisch mit `remove` aus der Quelle, gefolgt von `add` zum Ziel unter Verwendung des Werts aus der Quelle.</span><span class="sxs-lookup"><span data-stu-id="83750-147">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="83750-148">Identisch mit `add` zum Ziel unter Verwendung des Werts aus der Quelle.</span><span class="sxs-lookup"><span data-stu-id="83750-148">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="83750-149">Gibt Statuscode für Erfolg zurück, wenn der Wert von `path` = bereitgestellter `value`.</span><span class="sxs-lookup"><span data-stu-id="83750-149">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="83750-150">JsonPatch in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="83750-150">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="83750-151">Die ASP.NET Core-Implementierung von JSON Patch wird im [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/)-NuGet-Paket bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="83750-151">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="83750-152">Aktionsmethodencode</span><span class="sxs-lookup"><span data-stu-id="83750-152">Action method code</span></span>

<span data-ttu-id="83750-153">Eine Aktionsmethode für JSON Patch in einem API-Controller:</span><span class="sxs-lookup"><span data-stu-id="83750-153">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="83750-154">Ist versehen mit dem `HttpPatch`-Attribut.</span><span class="sxs-lookup"><span data-stu-id="83750-154">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="83750-155">Akzeptiert eine `JsonPatchDocument<T>`-Klasse, in der Regel mit `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="83750-155">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="83750-156">Ruft `ApplyTo` für das Patch-Dokument auf, um die Änderungen anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="83750-156">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="83750-157">Im Folgenden ein Beispiel:</span><span class="sxs-lookup"><span data-stu-id="83750-157">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="83750-158">Dieser Code aus der Beispiel-App funktioniert mit den folgenden `Customer`-Modell.</span><span class="sxs-lookup"><span data-stu-id="83750-158">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="83750-159">Die Beispielaktionsmethode:</span><span class="sxs-lookup"><span data-stu-id="83750-159">The sample action method:</span></span>

* <span data-ttu-id="83750-160">Erstellt ein Objekt vom Typ `Customer`.</span><span class="sxs-lookup"><span data-stu-id="83750-160">Constructs a `Customer`.</span></span>
* <span data-ttu-id="83750-161">Wendet den Patch an.</span><span class="sxs-lookup"><span data-stu-id="83750-161">Applies the patch.</span></span>
* <span data-ttu-id="83750-162">Gibt das Ergebnis wird im Textkörper der Antwort zurück.</span><span class="sxs-lookup"><span data-stu-id="83750-162">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="83750-163">In einer realen App würde der Code die Daten aus einem Speicher wie z.B. einer Datenbank abrufen und die Datenbank nach dem Anwenden des Patchs aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="83750-163">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="83750-164">Modellstatus</span><span class="sxs-lookup"><span data-stu-id="83750-164">Model state</span></span>

<span data-ttu-id="83750-165">Das voranstehende Aktionsmethodenbeispiel ruft eine Überladung von `ApplyTo` auf, die den Modellstatus als einen seiner Parameter akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="83750-165">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="83750-166">Mit dieser Option können Sie Fehlermeldungen in Antworten abrufen.</span><span class="sxs-lookup"><span data-stu-id="83750-166">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="83750-167">Das folgende Beispiel zeigt den Textkörper einer „400 (Ungültige Anforderung)-Antwort für einen `test` Vorgang:</span><span class="sxs-lookup"><span data-stu-id="83750-167">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="83750-168">Dynamische Objekte</span><span class="sxs-lookup"><span data-stu-id="83750-168">Dynamic objects</span></span>

<span data-ttu-id="83750-169">Das folgende Aktionsmethodenbeispiel veranschaulicht das Anwenden ein Patchs auf ein dynamisches Objekt.</span><span class="sxs-lookup"><span data-stu-id="83750-169">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="83750-170">Add-Vorgang (Hinzufügen)</span><span class="sxs-lookup"><span data-stu-id="83750-170">The add operation</span></span>

* <span data-ttu-id="83750-171">Wenn `path` auf ein Arrayelement verweist: Fügt ein neues Element vor dem von `path` angegebenen Element ein.</span><span class="sxs-lookup"><span data-stu-id="83750-171">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="83750-172">Wenn `path` auf eine Eigenschaft verweist: Legt den Eigenschaftswert fest.</span><span class="sxs-lookup"><span data-stu-id="83750-172">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="83750-173">Wenn `path` auf einen nicht vorhandenen Speicherort verweist:</span><span class="sxs-lookup"><span data-stu-id="83750-173">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="83750-174">Wenn die Ressource, auf die der Patch angewendet werden soll, ein dynamisches Objekt ist: Fügt eine Eigenschaft hinzu.</span><span class="sxs-lookup"><span data-stu-id="83750-174">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="83750-175">Wenn die Ressource, auf die der Patch angewendet werden soll, ein statisches Objekt ist: Die Anforderung schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="83750-175">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="83750-176">Das folgende Patch-Dokumentbeispiel legt den Wert von `CustomerName` fest und fügt ein `Order`-Objekt am Ende des `Orders`-Arrays hinzu.</span><span class="sxs-lookup"><span data-stu-id="83750-176">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="83750-177">Remove-Vorgang (Entfernen)</span><span class="sxs-lookup"><span data-stu-id="83750-177">The remove operation</span></span>

* <span data-ttu-id="83750-178">Wenn `path` auf ein Arrayelement verweist: Entfernt das Element.</span><span class="sxs-lookup"><span data-stu-id="83750-178">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="83750-179">Wenn `path` auf eine Eigenschaft verweist:</span><span class="sxs-lookup"><span data-stu-id="83750-179">If `path` points to a property:</span></span>
  * <span data-ttu-id="83750-180">Wenn die Ressource, auf die der Patch angewendet werden soll, ein dynamisches Objekt ist: Entfernt die Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="83750-180">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="83750-181">Wenn die Ressource, auf die der Patch angewendet werden soll, ein statisches Objekt ist:</span><span class="sxs-lookup"><span data-stu-id="83750-181">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="83750-182">Wenn die Eigenschaft NULL-Werte zulässt: auf Null festlegen.</span><span class="sxs-lookup"><span data-stu-id="83750-182">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="83750-183">Wenn die Eigenschaft keine NULL-Werte zulässt: auf `default<T>` festlegen.</span><span class="sxs-lookup"><span data-stu-id="83750-183">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="83750-184">Im folgenden Beispiel legt das Patch-Dokument `CustomerName` auf Null fest und löscht `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="83750-184">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="83750-185">Replace-Vorgang (Ersetzen)</span><span class="sxs-lookup"><span data-stu-id="83750-185">The replace operation</span></span>

<span data-ttu-id="83750-186">Dieser Vorgang ist funktionell identisch mit einem `remove`, gefolgt von einem `add`.</span><span class="sxs-lookup"><span data-stu-id="83750-186">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="83750-187">Das folgende Patch-Dokumentbeispiel legt den Wert von `CustomerName` fest und ersetzt `Orders[0]` durch ein neues `Order`-Objekt.</span><span class="sxs-lookup"><span data-stu-id="83750-187">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="83750-188">Move-Vorgang (Verschieben)</span><span class="sxs-lookup"><span data-stu-id="83750-188">The move operation</span></span>

* <span data-ttu-id="83750-189">Wenn `path` auf ein Arrayelement verweist: Kopiert das `from`-Element an den Speicherort des `path`-Elements und führt dann einen `remove`-Vorgang für das `from`-Element aus.</span><span class="sxs-lookup"><span data-stu-id="83750-189">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="83750-190">Wenn `path` auf eine Eigenschaft verweist: Kopiert den Wert der `from`-Eigenschaft in die `path`-Eigenschaft, und führt dann einen `remove`-Vorgang für die `from`-Eigenschaft aus.</span><span class="sxs-lookup"><span data-stu-id="83750-190">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="83750-191">Wenn `path` auf eine nicht vorhandene Eigenschaft verweist:</span><span class="sxs-lookup"><span data-stu-id="83750-191">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="83750-192">Wenn die Ressource, auf die der Patch angewendet werden soll, ein statisches Objekt ist: Die Anforderung schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="83750-192">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="83750-193">Wenn es sich bei der Ressource, die gepatcht werden soll, um ein dynamisches Objekt handelt: Kopiert die `from`-Eigenschaft in den von `path` angegebenen Speicherort, und führt dann einen `remove`-Vorgang für die `from`-Eigenschaft aus.</span><span class="sxs-lookup"><span data-stu-id="83750-193">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="83750-194">Das folgende Patch-Dokumentbeispiel:</span><span class="sxs-lookup"><span data-stu-id="83750-194">The following sample patch document:</span></span>

* <span data-ttu-id="83750-195">Kopiert den Wert der `Orders[0].OrderName` nach `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="83750-195">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="83750-196">Legt `Orders[0].OrderName` auf Null fest.</span><span class="sxs-lookup"><span data-stu-id="83750-196">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="83750-197">Verschiebt `Orders[1]` vor `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="83750-197">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="83750-198">Copy-Vorgang (Kopieren)</span><span class="sxs-lookup"><span data-stu-id="83750-198">The copy operation</span></span>

<span data-ttu-id="83750-199">Dieser Vorgang ist funktionell identisch mit einem `move`-Vorgang ohne den abschließenden `remove`-Schritt.</span><span class="sxs-lookup"><span data-stu-id="83750-199">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="83750-200">Das folgende Patch-Dokumentbeispiel:</span><span class="sxs-lookup"><span data-stu-id="83750-200">The following sample patch document:</span></span>

* <span data-ttu-id="83750-201">Kopiert den Wert der `Orders[0].OrderName` nach `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="83750-201">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="83750-202">Fügt eine Kopie von `Orders[1]` vor `Orders[0]` ein.</span><span class="sxs-lookup"><span data-stu-id="83750-202">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="83750-203">Test-Vorgang (Testen)</span><span class="sxs-lookup"><span data-stu-id="83750-203">The test operation</span></span>

<span data-ttu-id="83750-204">Wenn der Wert an dem von `path` angegebenen Speicherort sich von dem in `value` bereitgestellten Wert unterscheidet, schlägt die Anforderung fehl.</span><span class="sxs-lookup"><span data-stu-id="83750-204">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="83750-205">In diesem Fall schlägt die gesamte PATCH-Anforderung fehl, selbst wenn alle anderen Vorgänge im Patch-Dokument ansonsten erfolgreich ausgeführt werden könnten.</span><span class="sxs-lookup"><span data-stu-id="83750-205">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="83750-206">Der `test`-Vorgang wird häufig verwendet, um ein Update zu verhindern, wenn ein Parallelitätskonflikt vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="83750-206">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="83750-207">Das folgende Patch-Dokumentbeispiel hat keine Auswirkungen, wenn der Anfangswert von `CustomerName` „John“ ist, da der Test fehlschlägt:</span><span class="sxs-lookup"><span data-stu-id="83750-207">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="83750-208">Abrufen des Codes</span><span class="sxs-lookup"><span data-stu-id="83750-208">Get the code</span></span>

<span data-ttu-id="83750-209">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2)</span><span class="sxs-lookup"><span data-stu-id="83750-209">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="83750-210">([Informationen zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="83750-210">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="83750-211">Um das Beispiel zu testen, führen Sie die App aus, und senden Sie HTTP-Anforderungen mit den folgenden Einstellungen:</span><span class="sxs-lookup"><span data-stu-id="83750-211">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="83750-212">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="83750-212">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="83750-213">HTTP-Methode: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="83750-213">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="83750-214">Header: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="83750-214">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="83750-215">Hauptteil: Kopieren und Einfügen eines der JSON-Patch-Dokumentbeispiele aus dem *JSON*-Projektordner.</span><span class="sxs-lookup"><span data-stu-id="83750-215">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="83750-216">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="83750-216">Additional resources</span></span>

* [<span data-ttu-id="83750-217">IETF RFC 5789 PATCH-Methodenspezifikation</span><span class="sxs-lookup"><span data-stu-id="83750-217">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="83750-218">IETF RFC 6902 JSON Patch-Spezifikation</span><span class="sxs-lookup"><span data-stu-id="83750-218">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="83750-219">IETF RFC 6901 JSON Patch-Pfadformatspezifikation</span><span class="sxs-lookup"><span data-stu-id="83750-219">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="83750-220">[JSON Patch-Dokumentation](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="83750-220">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="83750-221">Enthält Links zu Ressourcen zum Erstellen von JSON Patch-Dokumenten.</span><span class="sxs-lookup"><span data-stu-id="83750-221">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="83750-222">ASP.NET Core JSON Patch-Quellcode</span><span class="sxs-lookup"><span data-stu-id="83750-222">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="83750-223">In diesem Artikel wird erläutert, wie JSON Patch-Anforderungen in einer ASP.NET Core-Web-API behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="83750-223">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="patch-http-request-method"></a><span data-ttu-id="83750-224">PATCH HTTP-Anforderungsmethode</span><span class="sxs-lookup"><span data-stu-id="83750-224">PATCH HTTP request method</span></span>

<span data-ttu-id="83750-225">Die Methoden PUT und [PATCH](https://tools.ietf.org/html/rfc5789) werden verwendet, um eine vorhandene Ressource zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="83750-225">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="83750-226">Der Unterschied zwischen den beiden Methoden besteht darin, dass PUT die gesamte Ressource ersetzt, während PATCH nur die Änderungen angibt.</span><span class="sxs-lookup"><span data-stu-id="83750-226">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="83750-227">JSON Patch</span><span class="sxs-lookup"><span data-stu-id="83750-227">JSON Patch</span></span>

<span data-ttu-id="83750-228">Mit dem [JSON Patch](https://tools.ietf.org/html/rfc6902)-Format geben Sie an, dass Updates auf eine Ressource angewendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="83750-228">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="83750-229">Ein JSON Patch-Dokument verfügt über ein Array von *Vorgängen*.</span><span class="sxs-lookup"><span data-stu-id="83750-229">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="83750-230">Jeder Vorgang identifiziert eine bestimmte Art von Änderung, z.B. das Hinzufügen eines Arrayelements oder das Ersetzen eines Eigenschaftswerts.</span><span class="sxs-lookup"><span data-stu-id="83750-230">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="83750-231">Die folgenden JSON-Dokumente stellen beispielsweise eine Ressource, ein JSON Patch-Dokument für die Ressource und das Ergebnis der Anwendung der Patchvorgänge dar.</span><span class="sxs-lookup"><span data-stu-id="83750-231">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="83750-232">Ressourcenbeispiel</span><span class="sxs-lookup"><span data-stu-id="83750-232">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="83750-233">JSON Patch-Beispiel</span><span class="sxs-lookup"><span data-stu-id="83750-233">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="83750-234">Für den oben stehenden JSON-Code gilt:</span><span class="sxs-lookup"><span data-stu-id="83750-234">In the preceding JSON:</span></span>

* <span data-ttu-id="83750-235">Die `op`-Eigenschaft gibt den Typ des Vorgangs an.</span><span class="sxs-lookup"><span data-stu-id="83750-235">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="83750-236">Die `path`-Eigenschaft gibt das zu aktualisierende Element an.</span><span class="sxs-lookup"><span data-stu-id="83750-236">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="83750-237">Die `value`-Eigenschaft stellt den neuen Wert bereit.</span><span class="sxs-lookup"><span data-stu-id="83750-237">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="83750-238">Ressource nach dem Patch</span><span class="sxs-lookup"><span data-stu-id="83750-238">Resource after patch</span></span>

<span data-ttu-id="83750-239">So sieht die Ressource nach der Anwendung des voranstehenden JSON Patch-Dokuments aus:</span><span class="sxs-lookup"><span data-stu-id="83750-239">Here's the resource after applying the preceding JSON Patch document:</span></span>

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

<span data-ttu-id="83750-240">Die Änderungen, die durch Anwenden eines JSON Patch-Dokuments auf eine Ressource vorgenommen werden, sind unteilbar: Wenn ein Vorgang aus der Liste fehlschlägt, wird kein Vorgang aus der Liste angewendet.</span><span class="sxs-lookup"><span data-stu-id="83750-240">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="83750-241">Pfadsyntax</span><span class="sxs-lookup"><span data-stu-id="83750-241">Path syntax</span></span>

<span data-ttu-id="83750-242">Die [path](https://tools.ietf.org/html/rfc6901)-Eigenschaft eines Vorgangsobjekts weist Schrägstriche zwischen Ebenen auf.</span><span class="sxs-lookup"><span data-stu-id="83750-242">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="83750-243">Beispielsweise `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="83750-243">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="83750-244">Nullbasierte Indizes werden verwendet, um Arrayelemente anzugeben.</span><span class="sxs-lookup"><span data-stu-id="83750-244">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="83750-245">Das erste Element des `addresses`-Arrays wäre bei `/addresses/0`.</span><span class="sxs-lookup"><span data-stu-id="83750-245">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="83750-246">Zum `add` ans Ende eines Arrays verwenden Sie einen Bindestrich (-) anstelle einer Indexnummer: `/addresses/-`.</span><span class="sxs-lookup"><span data-stu-id="83750-246">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="83750-247">Vorgänge</span><span class="sxs-lookup"><span data-stu-id="83750-247">Operations</span></span>

<span data-ttu-id="83750-248">Die folgende Tabelle zeigt unterstützt Vorgänge gemäß der [JSON Patch-Spezifikation](https://tools.ietf.org/html/rfc6902):</span><span class="sxs-lookup"><span data-stu-id="83750-248">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="83750-249">Vorgang</span><span class="sxs-lookup"><span data-stu-id="83750-249">Operation</span></span>  | <span data-ttu-id="83750-250">Hinweise</span><span class="sxs-lookup"><span data-stu-id="83750-250">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="83750-251">Hinzufügen einer Eigenschaft oder eines Arrayelements.</span><span class="sxs-lookup"><span data-stu-id="83750-251">Add a property or array element.</span></span> <span data-ttu-id="83750-252">Für vorhandene Eigenschaft: set value.</span><span class="sxs-lookup"><span data-stu-id="83750-252">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="83750-253">Entfernen einer Eigenschaft oder eines Arrayelements.</span><span class="sxs-lookup"><span data-stu-id="83750-253">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="83750-254">Identisch mit `remove`, gefolgt von `add` an gleicher Stelle.</span><span class="sxs-lookup"><span data-stu-id="83750-254">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="83750-255">Identisch mit `remove` aus der Quelle, gefolgt von `add` zum Ziel unter Verwendung des Werts aus der Quelle.</span><span class="sxs-lookup"><span data-stu-id="83750-255">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="83750-256">Identisch mit `add` zum Ziel unter Verwendung des Werts aus der Quelle.</span><span class="sxs-lookup"><span data-stu-id="83750-256">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="83750-257">Gibt Statuscode für Erfolg zurück, wenn der Wert von `path` = bereitgestellter `value`.</span><span class="sxs-lookup"><span data-stu-id="83750-257">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="83750-258">JsonPatch in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="83750-258">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="83750-259">Die ASP.NET Core-Implementierung von JSON Patch wird im [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/)-NuGet-Paket bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="83750-259">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span> <span data-ttu-id="83750-260">Das Paket ist im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) enthalten.</span><span class="sxs-lookup"><span data-stu-id="83750-260">The package is included in the [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) metapackage.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="83750-261">Aktionsmethodencode</span><span class="sxs-lookup"><span data-stu-id="83750-261">Action method code</span></span>

<span data-ttu-id="83750-262">Eine Aktionsmethode für JSON Patch in einem API-Controller:</span><span class="sxs-lookup"><span data-stu-id="83750-262">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="83750-263">Ist versehen mit dem `HttpPatch`-Attribut.</span><span class="sxs-lookup"><span data-stu-id="83750-263">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="83750-264">Akzeptiert eine `JsonPatchDocument<T>`-Klasse, in der Regel mit `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="83750-264">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="83750-265">Ruft `ApplyTo` für das Patch-Dokument auf, um die Änderungen anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="83750-265">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="83750-266">Im Folgenden ein Beispiel:</span><span class="sxs-lookup"><span data-stu-id="83750-266">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="83750-267">Dieser Code aus der Beispiel-App funktioniert mit den folgenden `Customer`-Modell.</span><span class="sxs-lookup"><span data-stu-id="83750-267">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="83750-268">Die Beispielaktionsmethode:</span><span class="sxs-lookup"><span data-stu-id="83750-268">The sample action method:</span></span>

* <span data-ttu-id="83750-269">Erstellt ein Objekt vom Typ `Customer`.</span><span class="sxs-lookup"><span data-stu-id="83750-269">Constructs a `Customer`.</span></span>
* <span data-ttu-id="83750-270">Wendet den Patch an.</span><span class="sxs-lookup"><span data-stu-id="83750-270">Applies the patch.</span></span>
* <span data-ttu-id="83750-271">Gibt das Ergebnis wird im Textkörper der Antwort zurück.</span><span class="sxs-lookup"><span data-stu-id="83750-271">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="83750-272">In einer realen App würde der Code die Daten aus einem Speicher wie z.B. einer Datenbank abrufen und die Datenbank nach dem Anwenden des Patchs aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="83750-272">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="83750-273">Modellstatus</span><span class="sxs-lookup"><span data-stu-id="83750-273">Model state</span></span>

<span data-ttu-id="83750-274">Das voranstehende Aktionsmethodenbeispiel ruft eine Überladung von `ApplyTo` auf, die den Modellstatus als einen seiner Parameter akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="83750-274">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="83750-275">Mit dieser Option können Sie Fehlermeldungen in Antworten abrufen.</span><span class="sxs-lookup"><span data-stu-id="83750-275">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="83750-276">Das folgende Beispiel zeigt den Textkörper einer „400 (Ungültige Anforderung)-Antwort für einen `test` Vorgang:</span><span class="sxs-lookup"><span data-stu-id="83750-276">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="83750-277">Dynamische Objekte</span><span class="sxs-lookup"><span data-stu-id="83750-277">Dynamic objects</span></span>

<span data-ttu-id="83750-278">Das folgende Aktionsmethodenbeispiel veranschaulicht das Anwenden ein Patchs auf ein dynamisches Objekt.</span><span class="sxs-lookup"><span data-stu-id="83750-278">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="83750-279">Add-Vorgang (Hinzufügen)</span><span class="sxs-lookup"><span data-stu-id="83750-279">The add operation</span></span>

* <span data-ttu-id="83750-280">Wenn `path` auf ein Arrayelement verweist: Fügt ein neues Element vor dem von `path` angegebenen Element ein.</span><span class="sxs-lookup"><span data-stu-id="83750-280">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="83750-281">Wenn `path` auf eine Eigenschaft verweist: Legt den Eigenschaftswert fest.</span><span class="sxs-lookup"><span data-stu-id="83750-281">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="83750-282">Wenn `path` auf einen nicht vorhandenen Speicherort verweist:</span><span class="sxs-lookup"><span data-stu-id="83750-282">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="83750-283">Wenn die Ressource, auf die der Patch angewendet werden soll, ein dynamisches Objekt ist: Fügt eine Eigenschaft hinzu.</span><span class="sxs-lookup"><span data-stu-id="83750-283">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="83750-284">Wenn die Ressource, auf die der Patch angewendet werden soll, ein statisches Objekt ist: Die Anforderung schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="83750-284">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="83750-285">Das folgende Patch-Dokumentbeispiel legt den Wert von `CustomerName` fest und fügt ein `Order`-Objekt am Ende des `Orders`-Arrays hinzu.</span><span class="sxs-lookup"><span data-stu-id="83750-285">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="83750-286">Remove-Vorgang (Entfernen)</span><span class="sxs-lookup"><span data-stu-id="83750-286">The remove operation</span></span>

* <span data-ttu-id="83750-287">Wenn `path` auf ein Arrayelement verweist: Entfernt das Element.</span><span class="sxs-lookup"><span data-stu-id="83750-287">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="83750-288">Wenn `path` auf eine Eigenschaft verweist:</span><span class="sxs-lookup"><span data-stu-id="83750-288">If `path` points to a property:</span></span>
  * <span data-ttu-id="83750-289">Wenn die Ressource, auf die der Patch angewendet werden soll, ein dynamisches Objekt ist: Entfernt die Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="83750-289">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="83750-290">Wenn die Ressource, auf die der Patch angewendet werden soll, ein statisches Objekt ist:</span><span class="sxs-lookup"><span data-stu-id="83750-290">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="83750-291">Wenn die Eigenschaft NULL-Werte zulässt: auf Null festlegen.</span><span class="sxs-lookup"><span data-stu-id="83750-291">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="83750-292">Wenn die Eigenschaft keine NULL-Werte zulässt: auf `default<T>` festlegen.</span><span class="sxs-lookup"><span data-stu-id="83750-292">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="83750-293">Im folgenden Beispiel legt das Patch-Dokument `CustomerName` auf Null fest und löscht `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="83750-293">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="83750-294">Replace-Vorgang (Ersetzen)</span><span class="sxs-lookup"><span data-stu-id="83750-294">The replace operation</span></span>

<span data-ttu-id="83750-295">Dieser Vorgang ist funktionell identisch mit einem `remove`, gefolgt von einem `add`.</span><span class="sxs-lookup"><span data-stu-id="83750-295">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="83750-296">Das folgende Patch-Dokumentbeispiel legt den Wert von `CustomerName` fest und ersetzt `Orders[0]` durch ein neues `Order`-Objekt.</span><span class="sxs-lookup"><span data-stu-id="83750-296">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="83750-297">Move-Vorgang (Verschieben)</span><span class="sxs-lookup"><span data-stu-id="83750-297">The move operation</span></span>

* <span data-ttu-id="83750-298">Wenn `path` auf ein Arrayelement verweist: Kopiert das `from`-Element an den Speicherort des `path`-Elements und führt dann einen `remove`-Vorgang für das `from`-Element aus.</span><span class="sxs-lookup"><span data-stu-id="83750-298">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="83750-299">Wenn `path` auf eine Eigenschaft verweist: Kopiert den Wert der `from`-Eigenschaft in die `path`-Eigenschaft, und führt dann einen `remove`-Vorgang für die `from`-Eigenschaft aus.</span><span class="sxs-lookup"><span data-stu-id="83750-299">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="83750-300">Wenn `path` auf eine nicht vorhandene Eigenschaft verweist:</span><span class="sxs-lookup"><span data-stu-id="83750-300">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="83750-301">Wenn die Ressource, auf die der Patch angewendet werden soll, ein statisches Objekt ist: Die Anforderung schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="83750-301">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="83750-302">Wenn es sich bei der Ressource, die gepatcht werden soll, um ein dynamisches Objekt handelt: Kopiert die `from`-Eigenschaft in den von `path` angegebenen Speicherort, und führt dann einen `remove`-Vorgang für die `from`-Eigenschaft aus.</span><span class="sxs-lookup"><span data-stu-id="83750-302">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="83750-303">Das folgende Patch-Dokumentbeispiel:</span><span class="sxs-lookup"><span data-stu-id="83750-303">The following sample patch document:</span></span>

* <span data-ttu-id="83750-304">Kopiert den Wert der `Orders[0].OrderName` nach `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="83750-304">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="83750-305">Legt `Orders[0].OrderName` auf Null fest.</span><span class="sxs-lookup"><span data-stu-id="83750-305">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="83750-306">Verschiebt `Orders[1]` vor `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="83750-306">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="83750-307">Copy-Vorgang (Kopieren)</span><span class="sxs-lookup"><span data-stu-id="83750-307">The copy operation</span></span>

<span data-ttu-id="83750-308">Dieser Vorgang ist funktionell identisch mit einem `move`-Vorgang ohne den abschließenden `remove`-Schritt.</span><span class="sxs-lookup"><span data-stu-id="83750-308">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="83750-309">Das folgende Patch-Dokumentbeispiel:</span><span class="sxs-lookup"><span data-stu-id="83750-309">The following sample patch document:</span></span>

* <span data-ttu-id="83750-310">Kopiert den Wert der `Orders[0].OrderName` nach `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="83750-310">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="83750-311">Fügt eine Kopie von `Orders[1]` vor `Orders[0]` ein.</span><span class="sxs-lookup"><span data-stu-id="83750-311">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="83750-312">Test-Vorgang (Testen)</span><span class="sxs-lookup"><span data-stu-id="83750-312">The test operation</span></span>

<span data-ttu-id="83750-313">Wenn der Wert an dem von `path` angegebenen Speicherort sich von dem in `value` bereitgestellten Wert unterscheidet, schlägt die Anforderung fehl.</span><span class="sxs-lookup"><span data-stu-id="83750-313">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="83750-314">In diesem Fall schlägt die gesamte PATCH-Anforderung fehl, selbst wenn alle anderen Vorgänge im Patch-Dokument ansonsten erfolgreich ausgeführt werden könnten.</span><span class="sxs-lookup"><span data-stu-id="83750-314">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="83750-315">Der `test`-Vorgang wird häufig verwendet, um ein Update zu verhindern, wenn ein Parallelitätskonflikt vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="83750-315">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="83750-316">Das folgende Patch-Dokumentbeispiel hat keine Auswirkungen, wenn der Anfangswert von `CustomerName` „John“ ist, da der Test fehlschlägt:</span><span class="sxs-lookup"><span data-stu-id="83750-316">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="83750-317">Abrufen des Codes</span><span class="sxs-lookup"><span data-stu-id="83750-317">Get the code</span></span>

<span data-ttu-id="83750-318">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2)</span><span class="sxs-lookup"><span data-stu-id="83750-318">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="83750-319">([Informationen zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="83750-319">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="83750-320">Um das Beispiel zu testen, führen Sie die App aus, und senden Sie HTTP-Anforderungen mit den folgenden Einstellungen:</span><span class="sxs-lookup"><span data-stu-id="83750-320">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="83750-321">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="83750-321">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="83750-322">HTTP-Methode: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="83750-322">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="83750-323">Header: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="83750-323">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="83750-324">Hauptteil: Kopieren und Einfügen eines der JSON-Patch-Dokumentbeispiele aus dem *JSON*-Projektordner.</span><span class="sxs-lookup"><span data-stu-id="83750-324">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="83750-325">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="83750-325">Additional resources</span></span>

* [<span data-ttu-id="83750-326">IETF RFC 5789 PATCH-Methodenspezifikation</span><span class="sxs-lookup"><span data-stu-id="83750-326">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="83750-327">IETF RFC 6902 JSON Patch-Spezifikation</span><span class="sxs-lookup"><span data-stu-id="83750-327">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="83750-328">IETF RFC 6901 JSON Patch-Pfadformatspezifikation</span><span class="sxs-lookup"><span data-stu-id="83750-328">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="83750-329">[JSON Patch-Dokumentation](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="83750-329">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="83750-330">Enthält Links zu Ressourcen zum Erstellen von JSON Patch-Dokumenten.</span><span class="sxs-lookup"><span data-stu-id="83750-330">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="83750-331">ASP.NET Core JSON Patch-Quellcode</span><span class="sxs-lookup"><span data-stu-id="83750-331">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end

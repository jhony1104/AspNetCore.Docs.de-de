---
<span data-ttu-id="13cbc-101">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="13cbc-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="13cbc-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-102">'Blazor'</span></span>
- <span data-ttu-id="13cbc-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="13cbc-103">'Identity'</span></span>
- <span data-ttu-id="13cbc-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="13cbc-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="13cbc-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-105">'Razor'</span></span>
- <span data-ttu-id="13cbc-106">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="13cbc-106">'SignalR' uid:</span></span> 

---

# <a name="jsonpatch-in-aspnet-core-web-api"></a><span data-ttu-id="13cbc-107">JsonPatch in ASP.NET Core-Web-API</span><span class="sxs-lookup"><span data-stu-id="13cbc-107">JsonPatch in ASP.NET Core web API</span></span>

<span data-ttu-id="13cbc-108">Von [Tom Dykstra](https://github.com/tdykstra) und [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="13cbc-108">By [Tom Dykstra](https://github.com/tdykstra) and [Kirk Larkin](https://github.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="13cbc-109">In diesem Artikel wird erläutert, wie JSON Patch-Anforderungen in einer ASP.NET Core-Web-API behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="13cbc-109">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="package-installation"></a><span data-ttu-id="13cbc-110">Paketinstallation</span><span class="sxs-lookup"><span data-stu-id="13cbc-110">Package installation</span></span>

<span data-ttu-id="13cbc-111">Führen Sie die folgenden Schritte aus, um die JSON-Patchunterstützung in der APP zu aktivieren:</span><span class="sxs-lookup"><span data-stu-id="13cbc-111">To enable JSON Patch support in your app, complete the following steps:</span></span>

1. <span data-ttu-id="13cbc-112">Installieren Sie das [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) nuget-Paket.</span><span class="sxs-lookup"><span data-stu-id="13cbc-112">Install the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package.</span></span>
1. <span data-ttu-id="13cbc-113">Aktualisieren Sie die- `Startup.ConfigureServices` Methode des Projekts, um aufzurufen <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*> .</span><span class="sxs-lookup"><span data-stu-id="13cbc-113">Update the project's `Startup.ConfigureServices` method to call <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*>.</span></span> <span data-ttu-id="13cbc-114">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="13cbc-114">For example:</span></span>

    ```csharp
    services
        .AddControllersWithViews()
        .AddNewtonsoftJson();
    ```

<span data-ttu-id="13cbc-115">`AddNewtonsoftJson` ist mit den folgenden MVC-Dienstregistrierungsmethoden kompatibel:</span><span class="sxs-lookup"><span data-stu-id="13cbc-115">`AddNewtonsoftJson` is compatible with the MVC service registration methods:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers*>

## <a name="json-patch-addnewtonsoftjson-and-systemtextjson"></a><span data-ttu-id="13cbc-116">JSON Patch, addnewtonweichjson und System. Text. JSON</span><span class="sxs-lookup"><span data-stu-id="13cbc-116">JSON Patch, AddNewtonsoftJson, and System.Text.Json</span></span>

<span data-ttu-id="13cbc-117">`AddNewtonsoftJson`ersetzt die `System.Text.Json` -basierten Eingabe-und Ausgabe Formatierer, die zum Formatieren des **gesamten** JSON-Inhalts verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="13cbc-117">`AddNewtonsoftJson` replaces the `System.Text.Json`-based input and output formatters used for formatting **all** JSON content.</span></span> <span data-ttu-id="13cbc-118">Um Unterstützung für JSON-Patch mithilfe von hinzuzufügen und `Newtonsoft.Json` die anderen Formatierer unverändert zu lassen, aktualisieren Sie die-Methode des Projekts `Startup.ConfigureServices` wie folgt:</span><span class="sxs-lookup"><span data-stu-id="13cbc-118">To add support for JSON Patch using `Newtonsoft.Json`, while leaving the other formatters unchanged, update the project's `Startup.ConfigureServices` method as follows:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

<span data-ttu-id="13cbc-119">Der vorangehende Code erfordert das `Microsoft.AspNetCore.Mvc.NewtonsoftJson` -Paket und die folgenden- `using` Anweisungen:</span><span class="sxs-lookup"><span data-stu-id="13cbc-119">The preceding code requires the `Microsoft.AspNetCore.Mvc.NewtonsoftJson` package and the following `using` statements:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a><span data-ttu-id="13cbc-120">PATCH HTTP-Anforderungsmethode</span><span class="sxs-lookup"><span data-stu-id="13cbc-120">PATCH HTTP request method</span></span>

<span data-ttu-id="13cbc-121">Die Methoden PUT und [PATCH](https://tools.ietf.org/html/rfc5789) werden verwendet, um eine vorhandene Ressource zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="13cbc-121">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="13cbc-122">Der Unterschied zwischen den beiden Methoden besteht darin, dass PUT die gesamte Ressource ersetzt, während PATCH nur die Änderungen angibt.</span><span class="sxs-lookup"><span data-stu-id="13cbc-122">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="13cbc-123">JSON Patch</span><span class="sxs-lookup"><span data-stu-id="13cbc-123">JSON Patch</span></span>

<span data-ttu-id="13cbc-124">Mit dem [JSON Patch](https://tools.ietf.org/html/rfc6902)-Format geben Sie an, dass Updates auf eine Ressource angewendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="13cbc-124">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="13cbc-125">Ein JSON Patch-Dokument verfügt über ein Array von *Vorgängen*.</span><span class="sxs-lookup"><span data-stu-id="13cbc-125">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="13cbc-126">Jeder Vorgang identifiziert eine bestimmte Art von Änderung.</span><span class="sxs-lookup"><span data-stu-id="13cbc-126">Each operation identifies a particular type of change.</span></span> <span data-ttu-id="13cbc-127">Beispiele für solche Änderungen sind das Hinzufügen eines Array Elements oder das Ersetzen eines Eigenschafts Werts.</span><span class="sxs-lookup"><span data-stu-id="13cbc-127">Examples of such changes include adding an array element or replacing a property value.</span></span>

<span data-ttu-id="13cbc-128">Die folgenden JSON-Dokumente stellen z. b. eine Ressource, ein JSON-Patch-Dokument für die Ressource und das Ergebnis der Anwendung der patchvorgänge dar.</span><span class="sxs-lookup"><span data-stu-id="13cbc-128">For example, the following JSON documents represent a resource, a JSON Patch document for the resource, and the result of applying the Patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="13cbc-129">Ressourcenbeispiel</span><span class="sxs-lookup"><span data-stu-id="13cbc-129">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="13cbc-130">JSON Patch-Beispiel</span><span class="sxs-lookup"><span data-stu-id="13cbc-130">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="13cbc-131">Für den oben stehenden JSON-Code gilt:</span><span class="sxs-lookup"><span data-stu-id="13cbc-131">In the preceding JSON:</span></span>

* <span data-ttu-id="13cbc-132">Die `op`-Eigenschaft gibt den Typ des Vorgangs an.</span><span class="sxs-lookup"><span data-stu-id="13cbc-132">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="13cbc-133">Die `path`-Eigenschaft gibt das zu aktualisierende Element an.</span><span class="sxs-lookup"><span data-stu-id="13cbc-133">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="13cbc-134">Die `value`-Eigenschaft stellt den neuen Wert bereit.</span><span class="sxs-lookup"><span data-stu-id="13cbc-134">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="13cbc-135">Ressource nach dem Patch</span><span class="sxs-lookup"><span data-stu-id="13cbc-135">Resource after patch</span></span>

<span data-ttu-id="13cbc-136">So sieht die Ressource nach der Anwendung des voranstehenden JSON Patch-Dokuments aus:</span><span class="sxs-lookup"><span data-stu-id="13cbc-136">Here's the resource after applying the preceding JSON Patch document:</span></span>

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

<span data-ttu-id="13cbc-137">Die Änderungen, die durch Anwenden eines JSON-Patch-Dokuments auf eine Ressource vorgenommen werden, sind atomarisch.</span><span class="sxs-lookup"><span data-stu-id="13cbc-137">The changes made by applying a JSON Patch document to a resource are atomic.</span></span> <span data-ttu-id="13cbc-138">Wenn ein Vorgang in der Liste fehlschlägt, wird kein Vorgang in der Liste angewendet.</span><span class="sxs-lookup"><span data-stu-id="13cbc-138">If any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="13cbc-139">Pfadsyntax</span><span class="sxs-lookup"><span data-stu-id="13cbc-139">Path syntax</span></span>

<span data-ttu-id="13cbc-140">Die [path](https://tools.ietf.org/html/rfc6901)-Eigenschaft eines Vorgangsobjekts weist Schrägstriche zwischen Ebenen auf.</span><span class="sxs-lookup"><span data-stu-id="13cbc-140">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="13cbc-141">Beispiel: `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="13cbc-141">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="13cbc-142">Nullbasierte Indizes werden verwendet, um Arrayelemente anzugeben.</span><span class="sxs-lookup"><span data-stu-id="13cbc-142">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="13cbc-143">Das erste Element des `addresses`-Arrays wäre bei `/addresses/0`.</span><span class="sxs-lookup"><span data-stu-id="13cbc-143">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="13cbc-144">Bis `add` zum Ende eines Arrays verwenden Sie einen Bindestrich ( `-` ) anstelle einer Indexnummer: `/addresses/-` .</span><span class="sxs-lookup"><span data-stu-id="13cbc-144">To `add` to the end of an array, use a hyphen (`-`) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="13cbc-145">Operationen (Operations)</span><span class="sxs-lookup"><span data-stu-id="13cbc-145">Operations</span></span>

<span data-ttu-id="13cbc-146">Die folgende Tabelle zeigt unterstützt Vorgänge gemäß der [JSON Patch-Spezifikation](https://tools.ietf.org/html/rfc6902):</span><span class="sxs-lookup"><span data-stu-id="13cbc-146">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="13cbc-147">Vorgang</span><span class="sxs-lookup"><span data-stu-id="13cbc-147">Operation</span></span>  | <span data-ttu-id="13cbc-148">Hinweise</span><span class="sxs-lookup"><span data-stu-id="13cbc-148">Notes</span></span> |
|---
<span data-ttu-id="13cbc-149">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="13cbc-149">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="13cbc-150">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-150">'Blazor'</span></span>
- <span data-ttu-id="13cbc-151">'Identity'</span><span class="sxs-lookup"><span data-stu-id="13cbc-151">'Identity'</span></span>
- <span data-ttu-id="13cbc-152">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="13cbc-152">'Let's Encrypt'</span></span>
- <span data-ttu-id="13cbc-153">'Razor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-153">'Razor'</span></span>
- <span data-ttu-id="13cbc-154">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="13cbc-154">'SignalR' uid:</span></span> 

-
<span data-ttu-id="13cbc-155">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="13cbc-155">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="13cbc-156">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-156">'Blazor'</span></span>
- <span data-ttu-id="13cbc-157">'Identity'</span><span class="sxs-lookup"><span data-stu-id="13cbc-157">'Identity'</span></span>
- <span data-ttu-id="13cbc-158">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="13cbc-158">'Let's Encrypt'</span></span>
- <span data-ttu-id="13cbc-159">'Razor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-159">'Razor'</span></span>
- <span data-ttu-id="13cbc-160">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="13cbc-160">'SignalR' uid:</span></span> 

-
<span data-ttu-id="13cbc-161">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="13cbc-161">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="13cbc-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-162">'Blazor'</span></span>
- <span data-ttu-id="13cbc-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="13cbc-163">'Identity'</span></span>
- <span data-ttu-id="13cbc-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="13cbc-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="13cbc-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-165">'Razor'</span></span>
- <span data-ttu-id="13cbc-166">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="13cbc-166">'SignalR' uid:</span></span> 

<span data-ttu-id="13cbc-167">------|---
Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="13cbc-167">------|---
title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="13cbc-168">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-168">'Blazor'</span></span>
- <span data-ttu-id="13cbc-169">'Identity'</span><span class="sxs-lookup"><span data-stu-id="13cbc-169">'Identity'</span></span>
- <span data-ttu-id="13cbc-170">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="13cbc-170">'Let's Encrypt'</span></span>
- <span data-ttu-id="13cbc-171">'Razor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-171">'Razor'</span></span>
- <span data-ttu-id="13cbc-172">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="13cbc-172">'SignalR' uid:</span></span> 

-
<span data-ttu-id="13cbc-173">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="13cbc-173">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="13cbc-174">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-174">'Blazor'</span></span>
- <span data-ttu-id="13cbc-175">'Identity'</span><span class="sxs-lookup"><span data-stu-id="13cbc-175">'Identity'</span></span>
- <span data-ttu-id="13cbc-176">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="13cbc-176">'Let's Encrypt'</span></span>
- <span data-ttu-id="13cbc-177">'Razor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-177">'Razor'</span></span>
- <span data-ttu-id="13cbc-178">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="13cbc-178">'SignalR' uid:</span></span> 

-
<span data-ttu-id="13cbc-179">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="13cbc-179">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="13cbc-180">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-180">'Blazor'</span></span>
- <span data-ttu-id="13cbc-181">'Identity'</span><span class="sxs-lookup"><span data-stu-id="13cbc-181">'Identity'</span></span>
- <span data-ttu-id="13cbc-182">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="13cbc-182">'Let's Encrypt'</span></span>
- <span data-ttu-id="13cbc-183">'Razor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-183">'Razor'</span></span>
- <span data-ttu-id="13cbc-184">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="13cbc-184">'SignalR' uid:</span></span> 

-
<span data-ttu-id="13cbc-185">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="13cbc-185">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="13cbc-186">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-186">'Blazor'</span></span>
- <span data-ttu-id="13cbc-187">'Identity'</span><span class="sxs-lookup"><span data-stu-id="13cbc-187">'Identity'</span></span>
- <span data-ttu-id="13cbc-188">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="13cbc-188">'Let's Encrypt'</span></span>
- <span data-ttu-id="13cbc-189">'Razor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-189">'Razor'</span></span>
- <span data-ttu-id="13cbc-190">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="13cbc-190">'SignalR' uid:</span></span> 

-
<span data-ttu-id="13cbc-191">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="13cbc-191">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="13cbc-192">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-192">'Blazor'</span></span>
- <span data-ttu-id="13cbc-193">'Identity'</span><span class="sxs-lookup"><span data-stu-id="13cbc-193">'Identity'</span></span>
- <span data-ttu-id="13cbc-194">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="13cbc-194">'Let's Encrypt'</span></span>
- <span data-ttu-id="13cbc-195">'Razor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-195">'Razor'</span></span>
- <span data-ttu-id="13cbc-196">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="13cbc-196">'SignalR' uid:</span></span> 

-
<span data-ttu-id="13cbc-197">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="13cbc-197">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="13cbc-198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-198">'Blazor'</span></span>
- <span data-ttu-id="13cbc-199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="13cbc-199">'Identity'</span></span>
- <span data-ttu-id="13cbc-200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="13cbc-200">'Let's Encrypt'</span></span>
- <span data-ttu-id="13cbc-201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-201">'Razor'</span></span>
- <span data-ttu-id="13cbc-202">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="13cbc-202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="13cbc-203">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="13cbc-203">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="13cbc-204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-204">'Blazor'</span></span>
- <span data-ttu-id="13cbc-205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="13cbc-205">'Identity'</span></span>
- <span data-ttu-id="13cbc-206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="13cbc-206">'Let's Encrypt'</span></span>
- <span data-ttu-id="13cbc-207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-207">'Razor'</span></span>
- <span data-ttu-id="13cbc-208">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="13cbc-208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="13cbc-209">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="13cbc-209">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="13cbc-210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-210">'Blazor'</span></span>
- <span data-ttu-id="13cbc-211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="13cbc-211">'Identity'</span></span>
- <span data-ttu-id="13cbc-212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="13cbc-212">'Let's Encrypt'</span></span>
- <span data-ttu-id="13cbc-213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-213">'Razor'</span></span>
- <span data-ttu-id="13cbc-214">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="13cbc-214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="13cbc-215">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="13cbc-215">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="13cbc-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-216">'Blazor'</span></span>
- <span data-ttu-id="13cbc-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="13cbc-217">'Identity'</span></span>
- <span data-ttu-id="13cbc-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="13cbc-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="13cbc-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-219">'Razor'</span></span>
- <span data-ttu-id="13cbc-220">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="13cbc-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="13cbc-221">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="13cbc-221">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="13cbc-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-222">'Blazor'</span></span>
- <span data-ttu-id="13cbc-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="13cbc-223">'Identity'</span></span>
- <span data-ttu-id="13cbc-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="13cbc-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="13cbc-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-225">'Razor'</span></span>
- <span data-ttu-id="13cbc-226">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="13cbc-226">'SignalR' uid:</span></span> 

-
<span data-ttu-id="13cbc-227">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="13cbc-227">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="13cbc-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-228">'Blazor'</span></span>
- <span data-ttu-id="13cbc-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="13cbc-229">'Identity'</span></span>
- <span data-ttu-id="13cbc-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="13cbc-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="13cbc-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-231">'Razor'</span></span>
- <span data-ttu-id="13cbc-232">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="13cbc-232">'SignalR' uid:</span></span> 

-
<span data-ttu-id="13cbc-233">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="13cbc-233">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="13cbc-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-234">'Blazor'</span></span>
- <span data-ttu-id="13cbc-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="13cbc-235">'Identity'</span></span>
- <span data-ttu-id="13cbc-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="13cbc-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="13cbc-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-237">'Razor'</span></span>
- <span data-ttu-id="13cbc-238">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="13cbc-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="13cbc-239">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="13cbc-239">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="13cbc-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-240">'Blazor'</span></span>
- <span data-ttu-id="13cbc-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="13cbc-241">'Identity'</span></span>
- <span data-ttu-id="13cbc-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="13cbc-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="13cbc-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-243">'Razor'</span></span>
- <span data-ttu-id="13cbc-244">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="13cbc-244">'SignalR' uid:</span></span> 

-
<span data-ttu-id="13cbc-245">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="13cbc-245">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="13cbc-246">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-246">'Blazor'</span></span>
- <span data-ttu-id="13cbc-247">'Identity'</span><span class="sxs-lookup"><span data-stu-id="13cbc-247">'Identity'</span></span>
- <span data-ttu-id="13cbc-248">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="13cbc-248">'Let's Encrypt'</span></span>
- <span data-ttu-id="13cbc-249">'Razor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-249">'Razor'</span></span>
- <span data-ttu-id="13cbc-250">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="13cbc-250">'SignalR' uid:</span></span> 

<span data-ttu-id="13cbc-251">----------------| | `add`     | Fügen Sie eine Eigenschaft oder ein Array Element hinzu.</span><span class="sxs-lookup"><span data-stu-id="13cbc-251">----------------| | `add`     | Add a property or array element.</span></span> <span data-ttu-id="13cbc-252">Für vorhandene Eigenschaft: Wert festlegen. | | `remove`  | Entfernen Sie eine Eigenschaft oder ein Array Element.</span><span class="sxs-lookup"><span data-stu-id="13cbc-252">For existing property: set value.| | `remove`  | Remove a property or array element.</span></span> <span data-ttu-id="13cbc-253">| | `replace` | Identisch mit `remove` gefolgt von `add` am gleichen Speicherort.</span><span class="sxs-lookup"><span data-stu-id="13cbc-253">| | `replace` | Same as `remove` followed by `add` at same location.</span></span> <span data-ttu-id="13cbc-254">| | `move`    | Identisch `remove` mit der Quelle, gefolgt von `add` zum Ziel, wobei der Wert aus der Quelle verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="13cbc-254">| | `move`    | Same as `remove` from source followed by `add` to destination using value from source.</span></span> <span data-ttu-id="13cbc-255">| | `copy`    | Identisch `add` mit dem Ziel mit dem Wert aus der Quelle.</span><span class="sxs-lookup"><span data-stu-id="13cbc-255">| | `copy`    | Same as `add` to destination using value from source.</span></span> <span data-ttu-id="13cbc-256">| | `test`    | Rückgabe des Erfolgsstatus Codes, wenn der Wert bei `path` = bereitgestellt wird `value` . |</span><span class="sxs-lookup"><span data-stu-id="13cbc-256">| | `test`    | Return success status code if value at `path` = provided `value`.|</span></span>

## <a name="json-patch-in-aspnet-core"></a><span data-ttu-id="13cbc-257">JSON-Patch in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="13cbc-257">JSON Patch in ASP.NET Core</span></span>

<span data-ttu-id="13cbc-258">Die ASP.NET Core-Implementierung von JSON Patch wird im [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/)-NuGet-Paket bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="13cbc-258">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="13cbc-259">Aktionsmethodencode</span><span class="sxs-lookup"><span data-stu-id="13cbc-259">Action method code</span></span>

<span data-ttu-id="13cbc-260">Eine Aktionsmethode für JSON Patch in einem API-Controller:</span><span class="sxs-lookup"><span data-stu-id="13cbc-260">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="13cbc-261">Ist versehen mit dem `HttpPatch`-Attribut.</span><span class="sxs-lookup"><span data-stu-id="13cbc-261">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="13cbc-262">Akzeptiert eine `JsonPatchDocument<T>`-Klasse, in der Regel mit `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="13cbc-262">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="13cbc-263">Ruft `ApplyTo` für das Patch-Dokument auf, um die Änderungen anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="13cbc-263">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="13cbc-264">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="13cbc-264">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="13cbc-265">Dieser Code aus der Beispiel-APP funktioniert mit dem folgenden `Customer` Modell:</span><span class="sxs-lookup"><span data-stu-id="13cbc-265">This code from the sample app works with the following `Customer` model:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="13cbc-266">Die Beispielaktionsmethode:</span><span class="sxs-lookup"><span data-stu-id="13cbc-266">The sample action method:</span></span>

* <span data-ttu-id="13cbc-267">Erstellt ein Objekt vom Typ `Customer`.</span><span class="sxs-lookup"><span data-stu-id="13cbc-267">Constructs a `Customer`.</span></span>
* <span data-ttu-id="13cbc-268">Wendet den Patch an.</span><span class="sxs-lookup"><span data-stu-id="13cbc-268">Applies the patch.</span></span>
* <span data-ttu-id="13cbc-269">Gibt das Ergebnis wird im Textkörper der Antwort zurück.</span><span class="sxs-lookup"><span data-stu-id="13cbc-269">Returns the result in the body of the response.</span></span>

<span data-ttu-id="13cbc-270">In einer realen App würde der Code die Daten aus einem Speicher wie z.B. einer Datenbank abrufen und die Datenbank nach dem Anwenden des Patchs aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="13cbc-270">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="13cbc-271">Modellstatus</span><span class="sxs-lookup"><span data-stu-id="13cbc-271">Model state</span></span>

<span data-ttu-id="13cbc-272">Das voranstehende Aktionsmethodenbeispiel ruft eine Überladung von `ApplyTo` auf, die den Modellstatus als einen seiner Parameter akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="13cbc-272">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="13cbc-273">Mit dieser Option können Sie Fehlermeldungen in Antworten abrufen.</span><span class="sxs-lookup"><span data-stu-id="13cbc-273">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="13cbc-274">Das folgende Beispiel zeigt den Textkörper einer „400 (Ungültige Anforderung)-Antwort für einen `test` Vorgang:</span><span class="sxs-lookup"><span data-stu-id="13cbc-274">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="13cbc-275">Dynamische Objekte</span><span class="sxs-lookup"><span data-stu-id="13cbc-275">Dynamic objects</span></span>

<span data-ttu-id="13cbc-276">Im folgenden Aktionsmethoden Beispiel wird gezeigt, wie ein Patch auf ein dynamisches Objekt angewendet wird:</span><span class="sxs-lookup"><span data-stu-id="13cbc-276">The following action method example shows how to apply a patch to a dynamic object:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="13cbc-277">Add-Vorgang (Hinzufügen)</span><span class="sxs-lookup"><span data-stu-id="13cbc-277">The add operation</span></span>

* <span data-ttu-id="13cbc-278">Wenn `path` auf ein Arrayelement verweist: Fügt ein neues Element vor dem von `path` angegebenen Element ein.</span><span class="sxs-lookup"><span data-stu-id="13cbc-278">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="13cbc-279">Wenn `path` auf eine Eigenschaft verweist: Legt den Eigenschaftswert fest.</span><span class="sxs-lookup"><span data-stu-id="13cbc-279">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="13cbc-280">Wenn `path` auf einen nicht vorhandenen Speicherort verweist:</span><span class="sxs-lookup"><span data-stu-id="13cbc-280">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="13cbc-281">Wenn die Ressource, auf die der Patch angewendet werden soll, ein dynamisches Objekt ist: Fügt eine Eigenschaft hinzu.</span><span class="sxs-lookup"><span data-stu-id="13cbc-281">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="13cbc-282">Wenn die Ressource, auf die der Patch angewendet werden soll, ein statisches Objekt ist: Die Anforderung schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="13cbc-282">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="13cbc-283">Das folgende Patch-Dokumentbeispiel legt den Wert von `CustomerName` fest und fügt ein `Order`-Objekt am Ende des `Orders`-Arrays hinzu.</span><span class="sxs-lookup"><span data-stu-id="13cbc-283">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="13cbc-284">Remove-Vorgang (Entfernen)</span><span class="sxs-lookup"><span data-stu-id="13cbc-284">The remove operation</span></span>

* <span data-ttu-id="13cbc-285">Wenn `path` auf ein Arrayelement verweist: Entfernt das Element.</span><span class="sxs-lookup"><span data-stu-id="13cbc-285">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="13cbc-286">Wenn `path` auf eine Eigenschaft verweist:</span><span class="sxs-lookup"><span data-stu-id="13cbc-286">If `path` points to a property:</span></span>
  * <span data-ttu-id="13cbc-287">Wenn die Ressource, auf die der Patch angewendet werden soll, ein dynamisches Objekt ist: Entfernt die Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="13cbc-287">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="13cbc-288">Wenn die Ressource, auf die der Patch angewendet werden soll, ein statisches Objekt ist:</span><span class="sxs-lookup"><span data-stu-id="13cbc-288">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="13cbc-289">Wenn die Eigenschaft NULL-Werte zulässt: auf Null festlegen.</span><span class="sxs-lookup"><span data-stu-id="13cbc-289">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="13cbc-290">Wenn die Eigenschaft keine NULL-Werte zulässt: auf `default<T>` festlegen.</span><span class="sxs-lookup"><span data-stu-id="13cbc-290">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="13cbc-291">Im folgenden Beispiel-Patch-Dokument wird `CustomerName` auf NULL festgelegt und löscht Folgendes `Orders[0]` :</span><span class="sxs-lookup"><span data-stu-id="13cbc-291">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="13cbc-292">Replace-Vorgang (Ersetzen)</span><span class="sxs-lookup"><span data-stu-id="13cbc-292">The replace operation</span></span>

<span data-ttu-id="13cbc-293">Dieser Vorgang ist funktionell identisch mit einem `remove`, gefolgt von einem `add`.</span><span class="sxs-lookup"><span data-stu-id="13cbc-293">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="13cbc-294">Im folgenden Beispiel-Patch-Dokument wird der Wert von festgelegt `CustomerName` und `Orders[0]` durch ein neues- `Order` Objekt ersetzt:</span><span class="sxs-lookup"><span data-stu-id="13cbc-294">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="13cbc-295">Move-Vorgang (Verschieben)</span><span class="sxs-lookup"><span data-stu-id="13cbc-295">The move operation</span></span>

* <span data-ttu-id="13cbc-296">Wenn `path` auf ein Arrayelement verweist: Kopiert das `from`-Element an den Speicherort des `path`-Elements und führt dann einen `remove`-Vorgang für das `from`-Element aus.</span><span class="sxs-lookup"><span data-stu-id="13cbc-296">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="13cbc-297">Wenn `path` auf eine Eigenschaft verweist: Kopiert den Wert der `from`-Eigenschaft in die `path`-Eigenschaft, und führt dann einen `remove`-Vorgang für die `from`-Eigenschaft aus.</span><span class="sxs-lookup"><span data-stu-id="13cbc-297">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="13cbc-298">Wenn `path` auf eine nicht vorhandene Eigenschaft verweist:</span><span class="sxs-lookup"><span data-stu-id="13cbc-298">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="13cbc-299">Wenn die Ressource, auf die der Patch angewendet werden soll, ein statisches Objekt ist: Die Anforderung schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="13cbc-299">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="13cbc-300">Wenn es sich bei der Ressource, die gepatcht werden soll, um ein dynamisches Objekt handelt: Kopiert die `from`-Eigenschaft in den von `path` angegebenen Speicherort, und führt dann einen `remove`-Vorgang für die `from`-Eigenschaft aus.</span><span class="sxs-lookup"><span data-stu-id="13cbc-300">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="13cbc-301">Das folgende Patch-Dokumentbeispiel:</span><span class="sxs-lookup"><span data-stu-id="13cbc-301">The following sample patch document:</span></span>

* <span data-ttu-id="13cbc-302">Kopiert den Wert der `Orders[0].OrderName` nach `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="13cbc-302">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="13cbc-303">Legt `Orders[0].OrderName` auf Null fest.</span><span class="sxs-lookup"><span data-stu-id="13cbc-303">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="13cbc-304">Verschiebt `Orders[1]` vor `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="13cbc-304">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="13cbc-305">Copy-Vorgang (Kopieren)</span><span class="sxs-lookup"><span data-stu-id="13cbc-305">The copy operation</span></span>

<span data-ttu-id="13cbc-306">Dieser Vorgang ist funktionell identisch mit einem `move`-Vorgang ohne den abschließenden `remove`-Schritt.</span><span class="sxs-lookup"><span data-stu-id="13cbc-306">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="13cbc-307">Das folgende Patch-Dokumentbeispiel:</span><span class="sxs-lookup"><span data-stu-id="13cbc-307">The following sample patch document:</span></span>

* <span data-ttu-id="13cbc-308">Kopiert den Wert der `Orders[0].OrderName` nach `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="13cbc-308">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="13cbc-309">Fügt eine Kopie von `Orders[1]` vor `Orders[0]` ein.</span><span class="sxs-lookup"><span data-stu-id="13cbc-309">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="13cbc-310">Test-Vorgang (Testen)</span><span class="sxs-lookup"><span data-stu-id="13cbc-310">The test operation</span></span>

<span data-ttu-id="13cbc-311">Wenn der Wert an dem von `path` angegebenen Speicherort sich von dem in `value` bereitgestellten Wert unterscheidet, schlägt die Anforderung fehl.</span><span class="sxs-lookup"><span data-stu-id="13cbc-311">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="13cbc-312">In diesem Fall schlägt die gesamte PATCH-Anforderung fehl, selbst wenn alle anderen Vorgänge im Patch-Dokument ansonsten erfolgreich ausgeführt werden könnten.</span><span class="sxs-lookup"><span data-stu-id="13cbc-312">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="13cbc-313">Der `test`-Vorgang wird häufig verwendet, um ein Update zu verhindern, wenn ein Parallelitätskonflikt vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="13cbc-313">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="13cbc-314">Das folgende Patch-Dokumentbeispiel hat keine Auswirkungen, wenn der Anfangswert von `CustomerName` „John“ ist, da der Test fehlschlägt:</span><span class="sxs-lookup"><span data-stu-id="13cbc-314">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="13cbc-315">Beziehen des Codes</span><span class="sxs-lookup"><span data-stu-id="13cbc-315">Get the code</span></span>

<span data-ttu-id="13cbc-316">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples)</span><span class="sxs-lookup"><span data-stu-id="13cbc-316">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples).</span></span> <span data-ttu-id="13cbc-317">([Informationen zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="13cbc-317">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="13cbc-318">Um das Beispiel zu testen, führen Sie die App aus, und senden Sie HTTP-Anforderungen mit den folgenden Einstellungen:</span><span class="sxs-lookup"><span data-stu-id="13cbc-318">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="13cbc-319">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="13cbc-319">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="13cbc-320">HTTP-Methode: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="13cbc-320">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="13cbc-321">Header: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="13cbc-321">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="13cbc-322">Text: Kopieren Sie eine der JSON-patchdokumentbeispiele, und fügen Sie Sie aus dem *JSON* -Projektordner ein.</span><span class="sxs-lookup"><span data-stu-id="13cbc-322">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="13cbc-323">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="13cbc-323">Additional resources</span></span>

* [<span data-ttu-id="13cbc-324">IETF RFC 5789 PATCH-Methodenspezifikation</span><span class="sxs-lookup"><span data-stu-id="13cbc-324">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="13cbc-325">IETF RFC 6902 JSON Patch-Spezifikation</span><span class="sxs-lookup"><span data-stu-id="13cbc-325">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="13cbc-326">IETF RFC 6901 JSON Patch-Pfadformatspezifikation</span><span class="sxs-lookup"><span data-stu-id="13cbc-326">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="13cbc-327">[JSON Patch-Dokumentation](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="13cbc-327">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="13cbc-328">Enthält Links zu Ressourcen zum Erstellen von JSON Patch-Dokumenten.</span><span class="sxs-lookup"><span data-stu-id="13cbc-328">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="13cbc-329">ASP.NET Core JSON Patch-Quellcode</span><span class="sxs-lookup"><span data-stu-id="13cbc-329">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="13cbc-330">In diesem Artikel wird erläutert, wie JSON Patch-Anforderungen in einer ASP.NET Core-Web-API behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="13cbc-330">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="patch-http-request-method"></a><span data-ttu-id="13cbc-331">PATCH HTTP-Anforderungsmethode</span><span class="sxs-lookup"><span data-stu-id="13cbc-331">PATCH HTTP request method</span></span>

<span data-ttu-id="13cbc-332">Die Methoden PUT und [PATCH](https://tools.ietf.org/html/rfc5789) werden verwendet, um eine vorhandene Ressource zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="13cbc-332">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="13cbc-333">Der Unterschied zwischen den beiden Methoden besteht darin, dass PUT die gesamte Ressource ersetzt, während PATCH nur die Änderungen angibt.</span><span class="sxs-lookup"><span data-stu-id="13cbc-333">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="13cbc-334">JSON Patch</span><span class="sxs-lookup"><span data-stu-id="13cbc-334">JSON Patch</span></span>

<span data-ttu-id="13cbc-335">Mit dem [JSON Patch](https://tools.ietf.org/html/rfc6902)-Format geben Sie an, dass Updates auf eine Ressource angewendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="13cbc-335">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="13cbc-336">Ein JSON Patch-Dokument verfügt über ein Array von *Vorgängen*.</span><span class="sxs-lookup"><span data-stu-id="13cbc-336">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="13cbc-337">Jeder Vorgang identifiziert eine bestimmte Art von Änderung, z.B. das Hinzufügen eines Arrayelements oder das Ersetzen eines Eigenschaftswerts.</span><span class="sxs-lookup"><span data-stu-id="13cbc-337">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="13cbc-338">Die folgenden JSON-Dokumente stellen beispielsweise eine Ressource, ein JSON Patch-Dokument für die Ressource und das Ergebnis der Anwendung der Patchvorgänge dar.</span><span class="sxs-lookup"><span data-stu-id="13cbc-338">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="13cbc-339">Ressourcenbeispiel</span><span class="sxs-lookup"><span data-stu-id="13cbc-339">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="13cbc-340">JSON Patch-Beispiel</span><span class="sxs-lookup"><span data-stu-id="13cbc-340">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="13cbc-341">Für den oben stehenden JSON-Code gilt:</span><span class="sxs-lookup"><span data-stu-id="13cbc-341">In the preceding JSON:</span></span>

* <span data-ttu-id="13cbc-342">Die `op`-Eigenschaft gibt den Typ des Vorgangs an.</span><span class="sxs-lookup"><span data-stu-id="13cbc-342">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="13cbc-343">Die `path`-Eigenschaft gibt das zu aktualisierende Element an.</span><span class="sxs-lookup"><span data-stu-id="13cbc-343">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="13cbc-344">Die `value`-Eigenschaft stellt den neuen Wert bereit.</span><span class="sxs-lookup"><span data-stu-id="13cbc-344">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="13cbc-345">Ressource nach dem Patch</span><span class="sxs-lookup"><span data-stu-id="13cbc-345">Resource after patch</span></span>

<span data-ttu-id="13cbc-346">So sieht die Ressource nach der Anwendung des voranstehenden JSON Patch-Dokuments aus:</span><span class="sxs-lookup"><span data-stu-id="13cbc-346">Here's the resource after applying the preceding JSON Patch document:</span></span>

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

<span data-ttu-id="13cbc-347">Die Änderungen, die durch Anwenden eines JSON Patch-Dokuments auf eine Ressource vorgenommen werden, sind unteilbar: Wenn ein Vorgang aus der Liste fehlschlägt, wird kein Vorgang aus der Liste angewendet.</span><span class="sxs-lookup"><span data-stu-id="13cbc-347">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="13cbc-348">Pfadsyntax</span><span class="sxs-lookup"><span data-stu-id="13cbc-348">Path syntax</span></span>

<span data-ttu-id="13cbc-349">Die [path](https://tools.ietf.org/html/rfc6901)-Eigenschaft eines Vorgangsobjekts weist Schrägstriche zwischen Ebenen auf.</span><span class="sxs-lookup"><span data-stu-id="13cbc-349">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="13cbc-350">Beispiel: `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="13cbc-350">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="13cbc-351">Nullbasierte Indizes werden verwendet, um Arrayelemente anzugeben.</span><span class="sxs-lookup"><span data-stu-id="13cbc-351">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="13cbc-352">Das erste Element des `addresses`-Arrays wäre bei `/addresses/0`.</span><span class="sxs-lookup"><span data-stu-id="13cbc-352">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="13cbc-353">Zum `add` ans Ende eines Arrays verwenden Sie einen Bindestrich (-) anstelle einer Indexnummer: `/addresses/-`.</span><span class="sxs-lookup"><span data-stu-id="13cbc-353">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="13cbc-354">Operationen (Operations)</span><span class="sxs-lookup"><span data-stu-id="13cbc-354">Operations</span></span>

<span data-ttu-id="13cbc-355">Die folgende Tabelle zeigt unterstützt Vorgänge gemäß der [JSON Patch-Spezifikation](https://tools.ietf.org/html/rfc6902):</span><span class="sxs-lookup"><span data-stu-id="13cbc-355">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="13cbc-356">Vorgang</span><span class="sxs-lookup"><span data-stu-id="13cbc-356">Operation</span></span>  | <span data-ttu-id="13cbc-357">Hinweise</span><span class="sxs-lookup"><span data-stu-id="13cbc-357">Notes</span></span> |
|---
<span data-ttu-id="13cbc-358">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="13cbc-358">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="13cbc-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-359">'Blazor'</span></span>
- <span data-ttu-id="13cbc-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="13cbc-360">'Identity'</span></span>
- <span data-ttu-id="13cbc-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="13cbc-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="13cbc-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-362">'Razor'</span></span>
- <span data-ttu-id="13cbc-363">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="13cbc-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="13cbc-364">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="13cbc-364">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="13cbc-365">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-365">'Blazor'</span></span>
- <span data-ttu-id="13cbc-366">'Identity'</span><span class="sxs-lookup"><span data-stu-id="13cbc-366">'Identity'</span></span>
- <span data-ttu-id="13cbc-367">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="13cbc-367">'Let's Encrypt'</span></span>
- <span data-ttu-id="13cbc-368">'Razor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-368">'Razor'</span></span>
- <span data-ttu-id="13cbc-369">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="13cbc-369">'SignalR' uid:</span></span> 

-
<span data-ttu-id="13cbc-370">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="13cbc-370">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="13cbc-371">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-371">'Blazor'</span></span>
- <span data-ttu-id="13cbc-372">'Identity'</span><span class="sxs-lookup"><span data-stu-id="13cbc-372">'Identity'</span></span>
- <span data-ttu-id="13cbc-373">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="13cbc-373">'Let's Encrypt'</span></span>
- <span data-ttu-id="13cbc-374">'Razor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-374">'Razor'</span></span>
- <span data-ttu-id="13cbc-375">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="13cbc-375">'SignalR' uid:</span></span> 

<span data-ttu-id="13cbc-376">------|---
Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="13cbc-376">------|---
title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="13cbc-377">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-377">'Blazor'</span></span>
- <span data-ttu-id="13cbc-378">'Identity'</span><span class="sxs-lookup"><span data-stu-id="13cbc-378">'Identity'</span></span>
- <span data-ttu-id="13cbc-379">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="13cbc-379">'Let's Encrypt'</span></span>
- <span data-ttu-id="13cbc-380">'Razor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-380">'Razor'</span></span>
- <span data-ttu-id="13cbc-381">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="13cbc-381">'SignalR' uid:</span></span> 

-
<span data-ttu-id="13cbc-382">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="13cbc-382">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="13cbc-383">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-383">'Blazor'</span></span>
- <span data-ttu-id="13cbc-384">'Identity'</span><span class="sxs-lookup"><span data-stu-id="13cbc-384">'Identity'</span></span>
- <span data-ttu-id="13cbc-385">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="13cbc-385">'Let's Encrypt'</span></span>
- <span data-ttu-id="13cbc-386">'Razor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-386">'Razor'</span></span>
- <span data-ttu-id="13cbc-387">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="13cbc-387">'SignalR' uid:</span></span> 

-
<span data-ttu-id="13cbc-388">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="13cbc-388">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="13cbc-389">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-389">'Blazor'</span></span>
- <span data-ttu-id="13cbc-390">'Identity'</span><span class="sxs-lookup"><span data-stu-id="13cbc-390">'Identity'</span></span>
- <span data-ttu-id="13cbc-391">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="13cbc-391">'Let's Encrypt'</span></span>
- <span data-ttu-id="13cbc-392">'Razor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-392">'Razor'</span></span>
- <span data-ttu-id="13cbc-393">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="13cbc-393">'SignalR' uid:</span></span> 

-
<span data-ttu-id="13cbc-394">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="13cbc-394">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="13cbc-395">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-395">'Blazor'</span></span>
- <span data-ttu-id="13cbc-396">'Identity'</span><span class="sxs-lookup"><span data-stu-id="13cbc-396">'Identity'</span></span>
- <span data-ttu-id="13cbc-397">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="13cbc-397">'Let's Encrypt'</span></span>
- <span data-ttu-id="13cbc-398">'Razor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-398">'Razor'</span></span>
- <span data-ttu-id="13cbc-399">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="13cbc-399">'SignalR' uid:</span></span> 

-
<span data-ttu-id="13cbc-400">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="13cbc-400">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="13cbc-401">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-401">'Blazor'</span></span>
- <span data-ttu-id="13cbc-402">'Identity'</span><span class="sxs-lookup"><span data-stu-id="13cbc-402">'Identity'</span></span>
- <span data-ttu-id="13cbc-403">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="13cbc-403">'Let's Encrypt'</span></span>
- <span data-ttu-id="13cbc-404">'Razor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-404">'Razor'</span></span>
- <span data-ttu-id="13cbc-405">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="13cbc-405">'SignalR' uid:</span></span> 

-
<span data-ttu-id="13cbc-406">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="13cbc-406">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="13cbc-407">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-407">'Blazor'</span></span>
- <span data-ttu-id="13cbc-408">'Identity'</span><span class="sxs-lookup"><span data-stu-id="13cbc-408">'Identity'</span></span>
- <span data-ttu-id="13cbc-409">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="13cbc-409">'Let's Encrypt'</span></span>
- <span data-ttu-id="13cbc-410">'Razor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-410">'Razor'</span></span>
- <span data-ttu-id="13cbc-411">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="13cbc-411">'SignalR' uid:</span></span> 

-
<span data-ttu-id="13cbc-412">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="13cbc-412">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="13cbc-413">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-413">'Blazor'</span></span>
- <span data-ttu-id="13cbc-414">'Identity'</span><span class="sxs-lookup"><span data-stu-id="13cbc-414">'Identity'</span></span>
- <span data-ttu-id="13cbc-415">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="13cbc-415">'Let's Encrypt'</span></span>
- <span data-ttu-id="13cbc-416">'Razor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-416">'Razor'</span></span>
- <span data-ttu-id="13cbc-417">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="13cbc-417">'SignalR' uid:</span></span> 

-
<span data-ttu-id="13cbc-418">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="13cbc-418">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="13cbc-419">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-419">'Blazor'</span></span>
- <span data-ttu-id="13cbc-420">'Identity'</span><span class="sxs-lookup"><span data-stu-id="13cbc-420">'Identity'</span></span>
- <span data-ttu-id="13cbc-421">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="13cbc-421">'Let's Encrypt'</span></span>
- <span data-ttu-id="13cbc-422">'Razor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-422">'Razor'</span></span>
- <span data-ttu-id="13cbc-423">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="13cbc-423">'SignalR' uid:</span></span> 

-
<span data-ttu-id="13cbc-424">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="13cbc-424">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="13cbc-425">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-425">'Blazor'</span></span>
- <span data-ttu-id="13cbc-426">'Identity'</span><span class="sxs-lookup"><span data-stu-id="13cbc-426">'Identity'</span></span>
- <span data-ttu-id="13cbc-427">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="13cbc-427">'Let's Encrypt'</span></span>
- <span data-ttu-id="13cbc-428">'Razor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-428">'Razor'</span></span>
- <span data-ttu-id="13cbc-429">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="13cbc-429">'SignalR' uid:</span></span> 

-
<span data-ttu-id="13cbc-430">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="13cbc-430">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="13cbc-431">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-431">'Blazor'</span></span>
- <span data-ttu-id="13cbc-432">'Identity'</span><span class="sxs-lookup"><span data-stu-id="13cbc-432">'Identity'</span></span>
- <span data-ttu-id="13cbc-433">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="13cbc-433">'Let's Encrypt'</span></span>
- <span data-ttu-id="13cbc-434">'Razor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-434">'Razor'</span></span>
- <span data-ttu-id="13cbc-435">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="13cbc-435">'SignalR' uid:</span></span> 

-
<span data-ttu-id="13cbc-436">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="13cbc-436">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="13cbc-437">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-437">'Blazor'</span></span>
- <span data-ttu-id="13cbc-438">'Identity'</span><span class="sxs-lookup"><span data-stu-id="13cbc-438">'Identity'</span></span>
- <span data-ttu-id="13cbc-439">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="13cbc-439">'Let's Encrypt'</span></span>
- <span data-ttu-id="13cbc-440">'Razor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-440">'Razor'</span></span>
- <span data-ttu-id="13cbc-441">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="13cbc-441">'SignalR' uid:</span></span> 

-
<span data-ttu-id="13cbc-442">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="13cbc-442">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="13cbc-443">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-443">'Blazor'</span></span>
- <span data-ttu-id="13cbc-444">'Identity'</span><span class="sxs-lookup"><span data-stu-id="13cbc-444">'Identity'</span></span>
- <span data-ttu-id="13cbc-445">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="13cbc-445">'Let's Encrypt'</span></span>
- <span data-ttu-id="13cbc-446">'Razor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-446">'Razor'</span></span>
- <span data-ttu-id="13cbc-447">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="13cbc-447">'SignalR' uid:</span></span> 

-
<span data-ttu-id="13cbc-448">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="13cbc-448">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="13cbc-449">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-449">'Blazor'</span></span>
- <span data-ttu-id="13cbc-450">'Identity'</span><span class="sxs-lookup"><span data-stu-id="13cbc-450">'Identity'</span></span>
- <span data-ttu-id="13cbc-451">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="13cbc-451">'Let's Encrypt'</span></span>
- <span data-ttu-id="13cbc-452">'Razor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-452">'Razor'</span></span>
- <span data-ttu-id="13cbc-453">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="13cbc-453">'SignalR' uid:</span></span> 

-
<span data-ttu-id="13cbc-454">Title: Autor: Beschreibung: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="13cbc-454">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="13cbc-455">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-455">'Blazor'</span></span>
- <span data-ttu-id="13cbc-456">'Identity'</span><span class="sxs-lookup"><span data-stu-id="13cbc-456">'Identity'</span></span>
- <span data-ttu-id="13cbc-457">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="13cbc-457">'Let's Encrypt'</span></span>
- <span data-ttu-id="13cbc-458">'Razor'</span><span class="sxs-lookup"><span data-stu-id="13cbc-458">'Razor'</span></span>
- <span data-ttu-id="13cbc-459">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="13cbc-459">'SignalR' uid:</span></span> 

<span data-ttu-id="13cbc-460">----------------| | `add`     | Fügen Sie eine Eigenschaft oder ein Array Element hinzu.</span><span class="sxs-lookup"><span data-stu-id="13cbc-460">----------------| | `add`     | Add a property or array element.</span></span> <span data-ttu-id="13cbc-461">Für vorhandene Eigenschaft: Wert festlegen. | | `remove`  | Entfernen Sie eine Eigenschaft oder ein Array Element.</span><span class="sxs-lookup"><span data-stu-id="13cbc-461">For existing property: set value.| | `remove`  | Remove a property or array element.</span></span> <span data-ttu-id="13cbc-462">| | `replace` | Identisch mit `remove` gefolgt von `add` am gleichen Speicherort.</span><span class="sxs-lookup"><span data-stu-id="13cbc-462">| | `replace` | Same as `remove` followed by `add` at same location.</span></span> <span data-ttu-id="13cbc-463">| | `move`    | Identisch `remove` mit der Quelle, gefolgt von `add` zum Ziel, wobei der Wert aus der Quelle verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="13cbc-463">| | `move`    | Same as `remove` from source followed by `add` to destination using value from source.</span></span> <span data-ttu-id="13cbc-464">| | `copy`    | Identisch `add` mit dem Ziel mit dem Wert aus der Quelle.</span><span class="sxs-lookup"><span data-stu-id="13cbc-464">| | `copy`    | Same as `add` to destination using value from source.</span></span> <span data-ttu-id="13cbc-465">| | `test`    | Rückgabe des Erfolgsstatus Codes, wenn der Wert bei `path` = bereitgestellt wird `value` . |</span><span class="sxs-lookup"><span data-stu-id="13cbc-465">| | `test`    | Return success status code if value at `path` = provided `value`.|</span></span>

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="13cbc-466">JsonPatch in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="13cbc-466">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="13cbc-467">Die ASP.NET Core-Implementierung von JSON Patch wird im [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/)-NuGet-Paket bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="13cbc-467">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span> <span data-ttu-id="13cbc-468">Das Paket ist im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) enthalten.</span><span class="sxs-lookup"><span data-stu-id="13cbc-468">The package is included in the [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) metapackage.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="13cbc-469">Aktionsmethodencode</span><span class="sxs-lookup"><span data-stu-id="13cbc-469">Action method code</span></span>

<span data-ttu-id="13cbc-470">Eine Aktionsmethode für JSON Patch in einem API-Controller:</span><span class="sxs-lookup"><span data-stu-id="13cbc-470">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="13cbc-471">Ist versehen mit dem `HttpPatch`-Attribut.</span><span class="sxs-lookup"><span data-stu-id="13cbc-471">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="13cbc-472">Akzeptiert eine `JsonPatchDocument<T>`-Klasse, in der Regel mit `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="13cbc-472">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="13cbc-473">Ruft `ApplyTo` für das Patch-Dokument auf, um die Änderungen anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="13cbc-473">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="13cbc-474">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="13cbc-474">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="13cbc-475">Dieser Code aus der Beispiel-App funktioniert mit den folgenden `Customer`-Modell.</span><span class="sxs-lookup"><span data-stu-id="13cbc-475">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="13cbc-476">Die Beispielaktionsmethode:</span><span class="sxs-lookup"><span data-stu-id="13cbc-476">The sample action method:</span></span>

* <span data-ttu-id="13cbc-477">Erstellt ein Objekt vom Typ `Customer`.</span><span class="sxs-lookup"><span data-stu-id="13cbc-477">Constructs a `Customer`.</span></span>
* <span data-ttu-id="13cbc-478">Wendet den Patch an.</span><span class="sxs-lookup"><span data-stu-id="13cbc-478">Applies the patch.</span></span>
* <span data-ttu-id="13cbc-479">Gibt das Ergebnis wird im Textkörper der Antwort zurück.</span><span class="sxs-lookup"><span data-stu-id="13cbc-479">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="13cbc-480">In einer realen App würde der Code die Daten aus einem Speicher wie z.B. einer Datenbank abrufen und die Datenbank nach dem Anwenden des Patchs aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="13cbc-480">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="13cbc-481">Modellstatus</span><span class="sxs-lookup"><span data-stu-id="13cbc-481">Model state</span></span>

<span data-ttu-id="13cbc-482">Das voranstehende Aktionsmethodenbeispiel ruft eine Überladung von `ApplyTo` auf, die den Modellstatus als einen seiner Parameter akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="13cbc-482">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="13cbc-483">Mit dieser Option können Sie Fehlermeldungen in Antworten abrufen.</span><span class="sxs-lookup"><span data-stu-id="13cbc-483">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="13cbc-484">Das folgende Beispiel zeigt den Textkörper einer „400 (Ungültige Anforderung)-Antwort für einen `test` Vorgang:</span><span class="sxs-lookup"><span data-stu-id="13cbc-484">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="13cbc-485">Dynamische Objekte</span><span class="sxs-lookup"><span data-stu-id="13cbc-485">Dynamic objects</span></span>

<span data-ttu-id="13cbc-486">Das folgende Aktionsmethodenbeispiel veranschaulicht das Anwenden ein Patchs auf ein dynamisches Objekt.</span><span class="sxs-lookup"><span data-stu-id="13cbc-486">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="13cbc-487">Add-Vorgang (Hinzufügen)</span><span class="sxs-lookup"><span data-stu-id="13cbc-487">The add operation</span></span>

* <span data-ttu-id="13cbc-488">Wenn `path` auf ein Arrayelement verweist: Fügt ein neues Element vor dem von `path` angegebenen Element ein.</span><span class="sxs-lookup"><span data-stu-id="13cbc-488">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="13cbc-489">Wenn `path` auf eine Eigenschaft verweist: Legt den Eigenschaftswert fest.</span><span class="sxs-lookup"><span data-stu-id="13cbc-489">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="13cbc-490">Wenn `path` auf einen nicht vorhandenen Speicherort verweist:</span><span class="sxs-lookup"><span data-stu-id="13cbc-490">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="13cbc-491">Wenn die Ressource, auf die der Patch angewendet werden soll, ein dynamisches Objekt ist: Fügt eine Eigenschaft hinzu.</span><span class="sxs-lookup"><span data-stu-id="13cbc-491">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="13cbc-492">Wenn die Ressource, auf die der Patch angewendet werden soll, ein statisches Objekt ist: Die Anforderung schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="13cbc-492">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="13cbc-493">Das folgende Patch-Dokumentbeispiel legt den Wert von `CustomerName` fest und fügt ein `Order`-Objekt am Ende des `Orders`-Arrays hinzu.</span><span class="sxs-lookup"><span data-stu-id="13cbc-493">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="13cbc-494">Remove-Vorgang (Entfernen)</span><span class="sxs-lookup"><span data-stu-id="13cbc-494">The remove operation</span></span>

* <span data-ttu-id="13cbc-495">Wenn `path` auf ein Arrayelement verweist: Entfernt das Element.</span><span class="sxs-lookup"><span data-stu-id="13cbc-495">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="13cbc-496">Wenn `path` auf eine Eigenschaft verweist:</span><span class="sxs-lookup"><span data-stu-id="13cbc-496">If `path` points to a property:</span></span>
  * <span data-ttu-id="13cbc-497">Wenn die Ressource, auf die der Patch angewendet werden soll, ein dynamisches Objekt ist: Entfernt die Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="13cbc-497">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="13cbc-498">Wenn die Ressource, auf die der Patch angewendet werden soll, ein statisches Objekt ist:</span><span class="sxs-lookup"><span data-stu-id="13cbc-498">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="13cbc-499">Wenn die Eigenschaft NULL-Werte zulässt: auf Null festlegen.</span><span class="sxs-lookup"><span data-stu-id="13cbc-499">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="13cbc-500">Wenn die Eigenschaft keine NULL-Werte zulässt: auf `default<T>` festlegen.</span><span class="sxs-lookup"><span data-stu-id="13cbc-500">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="13cbc-501">Im folgenden Beispiel legt das Patch-Dokument `CustomerName` auf Null fest und löscht `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="13cbc-501">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="13cbc-502">Replace-Vorgang (Ersetzen)</span><span class="sxs-lookup"><span data-stu-id="13cbc-502">The replace operation</span></span>

<span data-ttu-id="13cbc-503">Dieser Vorgang ist funktionell identisch mit einem `remove`, gefolgt von einem `add`.</span><span class="sxs-lookup"><span data-stu-id="13cbc-503">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="13cbc-504">Das folgende Patch-Dokumentbeispiel legt den Wert von `CustomerName` fest und ersetzt `Orders[0]` durch ein neues `Order`-Objekt.</span><span class="sxs-lookup"><span data-stu-id="13cbc-504">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="13cbc-505">Move-Vorgang (Verschieben)</span><span class="sxs-lookup"><span data-stu-id="13cbc-505">The move operation</span></span>

* <span data-ttu-id="13cbc-506">Wenn `path` auf ein Arrayelement verweist: Kopiert das `from`-Element an den Speicherort des `path`-Elements und führt dann einen `remove`-Vorgang für das `from`-Element aus.</span><span class="sxs-lookup"><span data-stu-id="13cbc-506">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="13cbc-507">Wenn `path` auf eine Eigenschaft verweist: Kopiert den Wert der `from`-Eigenschaft in die `path`-Eigenschaft, und führt dann einen `remove`-Vorgang für die `from`-Eigenschaft aus.</span><span class="sxs-lookup"><span data-stu-id="13cbc-507">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="13cbc-508">Wenn `path` auf eine nicht vorhandene Eigenschaft verweist:</span><span class="sxs-lookup"><span data-stu-id="13cbc-508">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="13cbc-509">Wenn die Ressource, auf die der Patch angewendet werden soll, ein statisches Objekt ist: Die Anforderung schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="13cbc-509">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="13cbc-510">Wenn es sich bei der Ressource, die gepatcht werden soll, um ein dynamisches Objekt handelt: Kopiert die `from`-Eigenschaft in den von `path` angegebenen Speicherort, und führt dann einen `remove`-Vorgang für die `from`-Eigenschaft aus.</span><span class="sxs-lookup"><span data-stu-id="13cbc-510">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="13cbc-511">Das folgende Patch-Dokumentbeispiel:</span><span class="sxs-lookup"><span data-stu-id="13cbc-511">The following sample patch document:</span></span>

* <span data-ttu-id="13cbc-512">Kopiert den Wert der `Orders[0].OrderName` nach `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="13cbc-512">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="13cbc-513">Legt `Orders[0].OrderName` auf Null fest.</span><span class="sxs-lookup"><span data-stu-id="13cbc-513">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="13cbc-514">Verschiebt `Orders[1]` vor `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="13cbc-514">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="13cbc-515">Copy-Vorgang (Kopieren)</span><span class="sxs-lookup"><span data-stu-id="13cbc-515">The copy operation</span></span>

<span data-ttu-id="13cbc-516">Dieser Vorgang ist funktionell identisch mit einem `move`-Vorgang ohne den abschließenden `remove`-Schritt.</span><span class="sxs-lookup"><span data-stu-id="13cbc-516">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="13cbc-517">Das folgende Patch-Dokumentbeispiel:</span><span class="sxs-lookup"><span data-stu-id="13cbc-517">The following sample patch document:</span></span>

* <span data-ttu-id="13cbc-518">Kopiert den Wert der `Orders[0].OrderName` nach `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="13cbc-518">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="13cbc-519">Fügt eine Kopie von `Orders[1]` vor `Orders[0]` ein.</span><span class="sxs-lookup"><span data-stu-id="13cbc-519">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="13cbc-520">Test-Vorgang (Testen)</span><span class="sxs-lookup"><span data-stu-id="13cbc-520">The test operation</span></span>

<span data-ttu-id="13cbc-521">Wenn der Wert an dem von `path` angegebenen Speicherort sich von dem in `value` bereitgestellten Wert unterscheidet, schlägt die Anforderung fehl.</span><span class="sxs-lookup"><span data-stu-id="13cbc-521">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="13cbc-522">In diesem Fall schlägt die gesamte PATCH-Anforderung fehl, selbst wenn alle anderen Vorgänge im Patch-Dokument ansonsten erfolgreich ausgeführt werden könnten.</span><span class="sxs-lookup"><span data-stu-id="13cbc-522">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="13cbc-523">Der `test`-Vorgang wird häufig verwendet, um ein Update zu verhindern, wenn ein Parallelitätskonflikt vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="13cbc-523">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="13cbc-524">Das folgende Patch-Dokumentbeispiel hat keine Auswirkungen, wenn der Anfangswert von `CustomerName` „John“ ist, da der Test fehlschlägt:</span><span class="sxs-lookup"><span data-stu-id="13cbc-524">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="13cbc-525">Beziehen des Codes</span><span class="sxs-lookup"><span data-stu-id="13cbc-525">Get the code</span></span>

<span data-ttu-id="13cbc-526">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2)</span><span class="sxs-lookup"><span data-stu-id="13cbc-526">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="13cbc-527">([Informationen zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="13cbc-527">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="13cbc-528">Um das Beispiel zu testen, führen Sie die App aus, und senden Sie HTTP-Anforderungen mit den folgenden Einstellungen:</span><span class="sxs-lookup"><span data-stu-id="13cbc-528">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="13cbc-529">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="13cbc-529">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="13cbc-530">HTTP-Methode: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="13cbc-530">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="13cbc-531">Header: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="13cbc-531">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="13cbc-532">Text: Kopieren Sie eine der JSON-patchdokumentbeispiele, und fügen Sie Sie aus dem *JSON* -Projektordner ein.</span><span class="sxs-lookup"><span data-stu-id="13cbc-532">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="13cbc-533">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="13cbc-533">Additional resources</span></span>

* [<span data-ttu-id="13cbc-534">IETF RFC 5789 PATCH-Methodenspezifikation</span><span class="sxs-lookup"><span data-stu-id="13cbc-534">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="13cbc-535">IETF RFC 6902 JSON Patch-Spezifikation</span><span class="sxs-lookup"><span data-stu-id="13cbc-535">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="13cbc-536">IETF RFC 6901 JSON Patch-Pfadformatspezifikation</span><span class="sxs-lookup"><span data-stu-id="13cbc-536">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="13cbc-537">[JSON Patch-Dokumentation](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="13cbc-537">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="13cbc-538">Enthält Links zu Ressourcen zum Erstellen von JSON Patch-Dokumenten.</span><span class="sxs-lookup"><span data-stu-id="13cbc-538">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="13cbc-539">ASP.NET Core JSON Patch-Quellcode</span><span class="sxs-lookup"><span data-stu-id="13cbc-539">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end

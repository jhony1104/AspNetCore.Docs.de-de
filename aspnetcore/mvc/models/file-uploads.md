---
title: Dateiuploads in ASP.NET Core
author: ardalis
description: Verwenden von Modellbindung und Streaming zum Hochladen von Dateien in ASP.NET Core MVC
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
uid: mvc/models/file-uploads
ms.openlocfilehash: 831f0c84f0ff062e9e24ccbf4ca81b7143c66e66
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59468552"
---
# <a name="file-uploads-in-aspnet-core"></a><span data-ttu-id="3dd3a-103">Dateiuploads in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3dd3a-103">File uploads in ASP.NET Core</span></span>

<span data-ttu-id="3dd3a-104">Von [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="3dd3a-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="3dd3a-105">ASP.NET MVC-Aktionen unterstützen das Hochladen von mindestens einer kleinen Datei über eine einfache Modellbindung bzw. das Streaming von großen Dateien.</span><span class="sxs-lookup"><span data-stu-id="3dd3a-105">ASP.NET MVC actions support uploading of one or more files using simple model binding for smaller files or streaming for larger files.</span></span>

<span data-ttu-id="3dd3a-106">[Beispiel anzeigen oder von GitHub herunterladen](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/models/file-uploads/sample/FileUploadSample).</span><span class="sxs-lookup"><span data-stu-id="3dd3a-106">[View or download sample from GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/models/file-uploads/sample/FileUploadSample)</span></span>

## <a name="uploading-small-files-with-model-binding"></a><span data-ttu-id="3dd3a-107">Hochladen von kleinen Dateien mittels Modellbindung</span><span class="sxs-lookup"><span data-stu-id="3dd3a-107">Uploading small files with model binding</span></span>

<span data-ttu-id="3dd3a-108">Wenn Sie kleine Dateien hochladen möchten, können Sie ein mehrteiliges HTML-Formular verwenden oder über JavaScript eine POST-Anforderung erstellen.</span><span class="sxs-lookup"><span data-stu-id="3dd3a-108">To upload small files, you can use a multi-part HTML form or construct a POST request using JavaScript.</span></span> <span data-ttu-id="3dd3a-109">Nachfolgend ist ein Beispielformular dargestellt, das Razor verwendet und das Hochladen von mehreren Dateien unterstützt:</span><span class="sxs-lookup"><span data-stu-id="3dd3a-109">An example form using Razor, which supports multiple uploaded files, is shown below:</span></span>

```html
<form method="post" enctype="multipart/form-data" asp-controller="UploadFiles" asp-action="Index">
    <div class="form-group">
        <div class="col-md-10">
            <p>Upload one or more files using this form:</p>
            <input type="file" name="files" multiple>
        </div>
    </div>
    <div class="form-group">
        <div class="col-md-10">
            <input type="submit" value="Upload">
        </div>
    </div>
</form>
```

<span data-ttu-id="3dd3a-110">HTML-Formulare müssen einen `enctype` von `multipart/form-data` angeben, damit Dateiuploads unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="3dd3a-110">In order to support file uploads, HTML forms must specify an `enctype` of `multipart/form-data`.</span></span> <span data-ttu-id="3dd3a-111">Das oben dargestellte `files`-Eingabeelement unterstützt das Hochladen von mehreren Dateien.</span><span class="sxs-lookup"><span data-stu-id="3dd3a-111">The `files` input element shown above supports uploading multiple files.</span></span> <span data-ttu-id="3dd3a-112">Lassen Sie das `multiple`-Attribut bei diesem Eingabeelement aus, wenn nur eine Datei hochgeladen werden soll.</span><span class="sxs-lookup"><span data-stu-id="3dd3a-112">Omit the `multiple` attribute on this input element to allow just a single file to be uploaded.</span></span> <span data-ttu-id="3dd3a-113">Das obenstehende Markup wird im Browser wie folgt gerendert:</span><span class="sxs-lookup"><span data-stu-id="3dd3a-113">The above markup renders in a browser as:</span></span>

![Dateiuploadformular](file-uploads/_static/upload-form.png)

<span data-ttu-id="3dd3a-115">Auf die einzelnen Dateien, die auf den Server geladen werden, kann über eine [Modellbindung](xref:mvc/models/model-binding) mittels einer [IFormFile](/dotnet/api/microsoft.aspnetcore.http.iformfile)-Schnittstelle zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="3dd3a-115">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using the [IFormFile](/dotnet/api/microsoft.aspnetcore.http.iformfile) interface.</span></span> <span data-ttu-id="3dd3a-116">`IFormFile` weist folgende Struktur auf:</span><span class="sxs-lookup"><span data-stu-id="3dd3a-116">`IFormFile` has the following structure:</span></span>

```csharp
public interface IFormFile
{
    string ContentType { get; }
    string ContentDisposition { get; }
    IHeaderDictionary Headers { get; }
    long Length { get; }
    string Name { get; }
    string FileName { get; }
    Stream OpenReadStream();
    void CopyTo(Stream target);
    Task CopyToAsync(Stream target, CancellationToken cancellationToken = null);
}
```

> [!WARNING]
> <span data-ttu-id="3dd3a-117">Verlassen Sie sich nicht ohne Überprüfung auf die `FileName`-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="3dd3a-117">Don't rely on or trust the `FileName` property without validation.</span></span> <span data-ttu-id="3dd3a-118">Die `FileName`-Eigenschaft sollte nur verwendet werden, um etwas anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="3dd3a-118">The `FileName` property should only be used for display purposes.</span></span>

<span data-ttu-id="3dd3a-119">Wenn Dateien mittels Modellbindung und der `IFormFile`-Schnittstelle hochgeladen werden, kann die Aktionsmethode entweder eine einzelne `IFormFile` oder `IEnumerable<IFormFile>` bzw. `List<IFormFile>` akzeptieren, die für mehrere Dateien stehen.</span><span class="sxs-lookup"><span data-stu-id="3dd3a-119">When uploading files using model binding and the `IFormFile` interface, the action method can accept either a single `IFormFile` or an `IEnumerable<IFormFile>` (or `List<IFormFile>`) representing several files.</span></span> <span data-ttu-id="3dd3a-120">Mithilfe des folgenden Beispiels können Sie eine Schleife durch mindestens eine hochgeladene Datei ausführen, diese im lokalen Dateisystem speichern und die Gesamtanzahl und die Größe der hochgeladen Dateien abfragen.</span><span class="sxs-lookup"><span data-stu-id="3dd3a-120">The following example loops through one or more uploaded files, saves them to the local file system, and returns the total number and size of files uploaded.</span></span>

[!INCLUDE [GetTempFileName](../../includes/GetTempFileName.md)]

[!code-csharp[](file-uploads/sample/FileUploadSample/Controllers/UploadFilesController.cs?name=snippet1)]

<span data-ttu-id="3dd3a-121">Dateien, die über die `IFormFile`-Technik hochgeladen werden, werden im Arbeitsspeicher oder auf einem Datenträger auf dem Webserver vor der Verarbeitung gepuffert.</span><span class="sxs-lookup"><span data-stu-id="3dd3a-121">Files uploaded using the `IFormFile` technique are buffered in memory or on disk on the web server before being processed.</span></span> <span data-ttu-id="3dd3a-122">Innerhalb der Aktionsmethode können Sie über einen Stream auf die `IFormFile`-Inhalte zugreifen.</span><span class="sxs-lookup"><span data-stu-id="3dd3a-122">Inside the action method, the `IFormFile` contents are accessible as a stream.</span></span> <span data-ttu-id="3dd3a-123">Dateien können nicht nur auf dem lokalen Dateisystem gestreamt werden, sondern auch auf dem [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs) oder dem [Entity Framework](/ef/core/index).</span><span class="sxs-lookup"><span data-stu-id="3dd3a-123">In addition to the local file system, files can be streamed to [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs) or [Entity Framework](/ef/core/index).</span></span>

<span data-ttu-id="3dd3a-124">Zum Speichern von Binärdateidaten in einer Datenbank über das Entity Framework müssen Sie eine Eigenschaft des Typs `byte[]` auf der Entität definieren:</span><span class="sxs-lookup"><span data-stu-id="3dd3a-124">To store binary file data in a database using Entity Framework, define a property of type `byte[]` on the entity:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public byte[] AvatarImage { get; set; }
}
```

<span data-ttu-id="3dd3a-125">Geben Sie eine Ansichtsmodelleigenschaft vom Typ `IFormFile` an:</span><span class="sxs-lookup"><span data-stu-id="3dd3a-125">Specify a viewmodel property of type `IFormFile`:</span></span>

```csharp
public class RegisterViewModel
{
    // other properties omitted

    public IFormFile AvatarImage { get; set; }
}
```

> [!NOTE]
> <span data-ttu-id="3dd3a-126">`IFormFile` kann wie oben dargestellt direkt als Parameter einer Aktionsmethode oder als Ansichtsmodelleigenschaft verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="3dd3a-126">`IFormFile` can be used directly as an action method parameter or as a viewmodel property, as shown above.</span></span>

<span data-ttu-id="3dd3a-127">Kopieren Sie die `IFormFile`-Datei in einen Stream, und speichern Sie sie im Bytearray:</span><span class="sxs-lookup"><span data-stu-id="3dd3a-127">Copy the `IFormFile` to a stream and save it to the byte array:</span></span>

```csharp
// POST: /Account/Register
[HttpPost]
[AllowAnonymous]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Register(RegisterViewModel model)
{
    ViewData["ReturnUrl"] = returnUrl;
    if  (ModelState.IsValid)
    {
        var user = new ApplicationUser 
        {
            UserName = model.Email,
            Email = model.Email
        };
        using (var memoryStream = new MemoryStream())
        {
            await model.AvatarImage.CopyToAsync(memoryStream);
            user.AvatarImage = memoryStream.ToArray();
        }
    // additional logic omitted

    // Don't rely on or trust the model.AvatarImage.FileName property 
    // without validation.
}
```

> [!NOTE]
> <span data-ttu-id="3dd3a-128">Speichern Sie Binärdaten in relationalen Datenbanken mit Bedacht, da sie Auswirkungen auf die Leistung haben können.</span><span class="sxs-lookup"><span data-stu-id="3dd3a-128">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>

## <a name="uploading-large-files-with-streaming"></a><span data-ttu-id="3dd3a-129">Hochladen von großen Dateien mittels Streaming</span><span class="sxs-lookup"><span data-stu-id="3dd3a-129">Uploading large files with streaming</span></span>

<span data-ttu-id="3dd3a-130">Wenn es zu Ressourcenproblemen mit der App kommt, weil zu viele Dateien hochgeladen werden oder die Dateien zu groß sind, sollten Sie darüber nachdenken, den Dateiupload zu streamen, anstatt ihn wie zuvor erläutert mittels Modellbindung vollständig zu puffern.</span><span class="sxs-lookup"><span data-stu-id="3dd3a-130">If the size or frequency of file uploads is causing resource problems for the app, consider streaming the file upload rather than buffering it in its entirety, as the model binding approach shown above does.</span></span> <span data-ttu-id="3dd3a-131">Die Verwendung von `IFormFile` und die Modellbindung stellen recht einfache Lösung dar. Für das Streaming müssen Sie hingegen eine Reihe verschiedener Schritte ausführen, um eine einwandfreie Implementierung zu garantieren.</span><span class="sxs-lookup"><span data-stu-id="3dd3a-131">While using `IFormFile` and model binding is a much simpler solution, streaming requires a number of steps to implement properly.</span></span>

> [!NOTE]
> <span data-ttu-id="3dd3a-132">Jede gepufferte Datei, die größer als 64 KB ist, wird von RAM in eine temporäre Datei auf dem Datenträger auf dem Server verschoben.</span><span class="sxs-lookup"><span data-stu-id="3dd3a-132">Any single buffered file exceeding 64KB will be moved from RAM to a temp file on disk on the server.</span></span> <span data-ttu-id="3dd3a-133">Welche Ressourcen (Datenträger oder RAM) für den Dateiupload verwendet werden, ist von der Anzahl und der Größe der gleichzeitig hochgeladenen Dateien abhängig.</span><span class="sxs-lookup"><span data-stu-id="3dd3a-133">The resources (disk, RAM) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="3dd3a-134">Beim Streaming geht es nicht um Leistung, sondern um die Staffelung der Uploads.</span><span class="sxs-lookup"><span data-stu-id="3dd3a-134">Streaming isn't so much about perf, it's about scale.</span></span> <span data-ttu-id="3dd3a-135">Wenn Sie versuchen, zu viele Uploads zu puffern, stürzt Ihre Website ab, wenn der Arbeitsspeicher oder der Speicherplatz auf dem Datenträger ausgelastet ist.</span><span class="sxs-lookup"><span data-stu-id="3dd3a-135">If you try to buffer too many uploads, your site will crash when it runs out of memory or disk space.</span></span>

<span data-ttu-id="3dd3a-136">Im folgenden Beispiel wird dargestellt, wie Sie JavaScript bzw. Angular verwendet können, um einen Stream auf eine Controlleraktion durchzuführen.</span><span class="sxs-lookup"><span data-stu-id="3dd3a-136">The following example demonstrates using JavaScript/Angular to stream to a controller action.</span></span> <span data-ttu-id="3dd3a-137">Das Antifälschungstoken einer Datei wird mithilfe von Filterattributen generiert und an HTTP-Header anstelle von Anforderungstexten übergeben.</span><span class="sxs-lookup"><span data-stu-id="3dd3a-137">The file's antiforgery token is generated using a custom filter attribute and passed in HTTP headers instead of in the request body.</span></span> <span data-ttu-id="3dd3a-138">Da die Aktionsmethode die hochgeladenen Daten direkt verarbeitet, wird die Modellbindung von einem anderen Filter deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="3dd3a-138">Because the action method processes the uploaded data directly, model binding is disabled by another filter.</span></span> <span data-ttu-id="3dd3a-139">Innerhalb der Aktion werden die Inhalte des Formulars über `MultipartReader` gelesen. Dieses Element liest jede einzelne `MultipartSection`-Klasse, wodurch die Datei verarbeitet wird oder die Inhalte angemessen gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="3dd3a-139">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="3dd3a-140">Sobald alle Abschnitte gelesen wurden, führt die Aktion ihre eigene Modellbindung aus.</span><span class="sxs-lookup"><span data-stu-id="3dd3a-140">Once all sections have been read, the action performs its own model binding.</span></span>

<span data-ttu-id="3dd3a-141">Die erste Aktion lädt das Formular und speichert das Antifälschungstoken (über das `GenerateAntiforgeryTokenCookieForAjax`-Attribut) in einem Cookie:</span><span class="sxs-lookup"><span data-stu-id="3dd3a-141">The initial action loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieForAjax` attribute):</span></span>

```csharp
[HttpGet]
[GenerateAntiforgeryTokenCookieForAjax]
public IActionResult Index()
{
    return View();
}
```

<span data-ttu-id="3dd3a-142">Das Attribut verwendet die in ASP.NET Core integrierte [Antifälschungsunterstützung](xref:security/anti-request-forgery), um ein Cookie mit einem Anforderungstoken festzulegen:</span><span class="sxs-lookup"><span data-stu-id="3dd3a-142">The attribute uses ASP.NET Core's built-in [Antiforgery](xref:security/anti-request-forgery) support to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/sample/FileUploadSample/Filters/GenerateAntiforgeryTokenCookieForAjaxAttribute.cs?name=snippet1)]

<span data-ttu-id="3dd3a-143">Angular übergibt automatisch ein Antifälschungstoken an einen Anforderungsheader mit dem Namen `X-XSRF-TOKEN`.</span><span class="sxs-lookup"><span data-stu-id="3dd3a-143">Angular automatically passes an antiforgery token in a request header named `X-XSRF-TOKEN`.</span></span> <span data-ttu-id="3dd3a-144">Die ASP.NET Core MVC-App wird so konfiguriert, dass sie auf diesen Header in Ihrer Konfiguration in *Startup.cs* verweist:</span><span class="sxs-lookup"><span data-stu-id="3dd3a-144">The ASP.NET Core MVC app is configured to refer to this header in its configuration in *Startup.cs*:</span></span>

[!code-csharp[](file-uploads/sample/FileUploadSample/Startup.cs?name=snippet1)]

<span data-ttu-id="3dd3a-145">Das nachfolgend dargestellte `DisableFormValueModelBinding`-Attribut wird verwendet, um die Modellbindung für die `Upload`-Aktionsmethode zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="3dd3a-145">The `DisableFormValueModelBinding` attribute, shown below, is used to disable model binding for the `Upload` action method.</span></span>

[!code-csharp[](file-uploads/sample/FileUploadSample/Filters/DisableFormValueModelBindingAttribute.cs?name=snippet1)]

<span data-ttu-id="3dd3a-146">Da die Modellbindung deaktiviert ist, akzeptiert die `Upload`-Aktionsmethode keine Parameter.</span><span class="sxs-lookup"><span data-stu-id="3dd3a-146">Since model binding is disabled, the `Upload` action method doesn't accept parameters.</span></span> <span data-ttu-id="3dd3a-147">Sie funktioniert direkt mit der `Request`-Eigenschaft von `ControllerBase` zusammen.</span><span class="sxs-lookup"><span data-stu-id="3dd3a-147">It works directly with the `Request` property of `ControllerBase`.</span></span> <span data-ttu-id="3dd3a-148">Ein `MultipartReader` wird verwendet, um die verschiedenen Abschnitte zu lesen.</span><span class="sxs-lookup"><span data-stu-id="3dd3a-148">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="3dd3a-149">Die Datei wird zusammen mit einem GUID-Dateinamen und die Schlüssel- bzw. Wertdaten werden in einem `KeyValueAccumulator` gespeichert.</span><span class="sxs-lookup"><span data-stu-id="3dd3a-149">The file is saved with a GUID filename and the key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="3dd3a-150">Sobald alle Abschnitte gelesen wurden, werden die Inhalte von `KeyValueAccumulator` verwendet, um die Formulardaten an einen Modelltyp zu binden.</span><span class="sxs-lookup"><span data-stu-id="3dd3a-150">Once all sections have been read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="3dd3a-151">Die vollständige `Upload`-Methode wird im Folgenden Beispiel dargestellt:</span><span class="sxs-lookup"><span data-stu-id="3dd3a-151">The complete `Upload` method is shown below:</span></span>

[!INCLUDE [GetTempFileName](../../includes/GetTempFileName.md)]

[!code-csharp[](file-uploads/sample/FileUploadSample/Controllers/StreamingController.cs?name=snippet1)]

## <a name="troubleshooting"></a><span data-ttu-id="3dd3a-152">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="3dd3a-152">Troubleshooting</span></span>

<span data-ttu-id="3dd3a-153">Nachfolgend werden einige häufig auftretenden Probleme aufgeführt, die entstehen können, wenn Dateien hochgeladen werden. Außerdem wird erläutert, wie Sie diese Probleme beheben können.</span><span class="sxs-lookup"><span data-stu-id="3dd3a-153">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="unexpected-not-found-error-with-iis"></a><span data-ttu-id="3dd3a-154">Unerwarteter „Nicht gefunden“-Fehler mit IIS</span><span class="sxs-lookup"><span data-stu-id="3dd3a-154">Unexpected Not Found error with IIS</span></span>

<span data-ttu-id="3dd3a-155">Der folgende Fehler wird angezeigt, wenn Ihr Dateiupload die in `maxAllowedContentLength` konfigurierte Größe des Servers überschreitet:</span><span class="sxs-lookup"><span data-stu-id="3dd3a-155">The following error indicates your file upload exceeds the server's configured `maxAllowedContentLength`:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="3dd3a-156">Die Standardeinstellung lautet `30000000`, also in etwa 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="3dd3a-156">The default setting is `30000000`, which is approximately 28.6MB.</span></span> <span data-ttu-id="3dd3a-157">Sie können diesen Wert bearbeiten, indem Sie die *web.config*-Datei bearbeiten:</span><span class="sxs-lookup"><span data-stu-id="3dd3a-157">The value can be customized by editing *web.config*:</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <!-- This will handle requests up to 50MB -->
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="3dd3a-158">Diese Einstellung gilt nur für IIS.</span><span class="sxs-lookup"><span data-stu-id="3dd3a-158">This setting only applies to IIS.</span></span> <span data-ttu-id="3dd3a-159">Beim Hosting unter Kestrel gehört dieses Verhalten nicht zum Standard.</span><span class="sxs-lookup"><span data-stu-id="3dd3a-159">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="3dd3a-160">Weitere Informationen finden Sie unter [Request Limits \<requestLimits\> (Anforderungseinschränkungen)](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="3dd3a-160">For more information, see [Request Limits \<requestLimits\>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="3dd3a-161">Ausnahme bei möglichem NULL-Verweis mit IFormFile</span><span class="sxs-lookup"><span data-stu-id="3dd3a-161">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="3dd3a-162">Wenn Ihr Controller es zulässt, dass Dateien über `IFormFile` hochgeladen werden, Sie allerdings feststellen, dass der Wert immer NULL ist, überprüfen Sie, ob Ihr HTML-Formular einen `enctype`-Wert von `multipart/form-data` angibt.</span><span class="sxs-lookup"><span data-stu-id="3dd3a-162">If your controller is accepting uploaded files using `IFormFile` but you find that the value is always null, confirm that your HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="3dd3a-163">Wenn dieses Attribut für das `<form>`-Element festgelegt ist, können keine Dateien hochgeladen werden, und jedes gebundene `IFormFile`-Argument gibt NULL zurück.</span><span class="sxs-lookup"><span data-stu-id="3dd3a-163">If this attribute isn't set on the `<form>` element, the file upload won't occur and any bound `IFormFile` arguments will be null.</span></span>

---
title: Dateiuploads in ASP.NET Core
author: ardalis
description: Verwenden von Modellbindung und Streaming zum Hochladen von Dateien in ASP.NET Core MVC
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
uid: mvc/models/file-uploads
ms.openlocfilehash: 831f0c84f0ff062e9e24ccbf4ca81b7143c66e66
ms.sourcegitcommit: 948e533e02c2a7cb6175ada20b2c9cabb7786d0b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/10/2019
ms.locfileid: "59468552"
---
# <a name="file-uploads-in-aspnet-core"></a>Dateiuploads in ASP.NET Core

Von [Steve Smith](https://ardalis.com/)

ASP.NET MVC-Aktionen unterstützen das Hochladen von mindestens einer kleinen Datei über eine einfache Modellbindung bzw. das Streaming von großen Dateien.

[Beispiel anzeigen oder von GitHub herunterladen](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/models/file-uploads/sample/FileUploadSample)

## <a name="uploading-small-files-with-model-binding"></a>Hochladen von kleinen Dateien mittels Modellbindung

Wenn Sie kleine Dateien hochladen möchten, können Sie ein mehrteiliges HTML-Formular verwenden oder über JavaScript eine POST-Anforderung erstellen. Nachfolgend ist ein Beispielformular dargestellt, das Razor verwendet und das Hochladen von mehreren Dateien unterstützt:

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

HTML-Formulare müssen einen `enctype` von `multipart/form-data` angeben, damit Dateiuploads unterstützt werden. Das oben dargestellte `files`-Eingabeelement unterstützt das Hochladen von mehreren Dateien. Lassen Sie das `multiple`-Attribut bei diesem Eingabeelement aus, wenn nur eine Datei hochgeladen werden soll. Das obenstehende Markup wird im Browser wie folgt gerendert:

![Dateiuploadformular](file-uploads/_static/upload-form.png)

Auf die einzelnen Dateien, die auf den Server geladen werden, kann über eine [Modellbindung](xref:mvc/models/model-binding) mittels einer [IFormFile](/dotnet/api/microsoft.aspnetcore.http.iformfile)-Schnittstelle zugegriffen werden. `IFormFile` weist folgende Struktur auf:

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
> Verlassen Sie sich nicht ohne Überprüfung auf die `FileName`-Eigenschaft. Die `FileName`-Eigenschaft sollte nur verwendet werden, um etwas anzuzeigen.

Wenn Dateien mittels Modellbindung und der `IFormFile`-Schnittstelle hochgeladen werden, kann die Aktionsmethode entweder eine einzelne `IFormFile` oder `IEnumerable<IFormFile>` bzw. `List<IFormFile>` akzeptieren, die für mehrere Dateien stehen. Mithilfe des folgenden Beispiels können Sie eine Schleife durch mindestens eine hochgeladene Datei ausführen, diese im lokalen Dateisystem speichern und die Gesamtanzahl und die Größe der hochgeladen Dateien abfragen.

[!INCLUDE [GetTempFileName](../../includes/GetTempFileName.md)]

[!code-csharp[](file-uploads/sample/FileUploadSample/Controllers/UploadFilesController.cs?name=snippet1)]

Dateien, die über die `IFormFile`-Technik hochgeladen werden, werden im Arbeitsspeicher oder auf einem Datenträger auf dem Webserver vor der Verarbeitung gepuffert. Innerhalb der Aktionsmethode können Sie über einen Stream auf die `IFormFile`-Inhalte zugreifen. Dateien können nicht nur auf dem lokalen Dateisystem gestreamt werden, sondern auch auf dem [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs) oder dem [Entity Framework](/ef/core/index).

Zum Speichern von Binärdateidaten in einer Datenbank über das Entity Framework müssen Sie eine Eigenschaft des Typs `byte[]` auf der Entität definieren:

```csharp
public class ApplicationUser : IdentityUser
{
    public byte[] AvatarImage { get; set; }
}
```

Geben Sie eine Ansichtsmodelleigenschaft vom Typ `IFormFile` an:

```csharp
public class RegisterViewModel
{
    // other properties omitted

    public IFormFile AvatarImage { get; set; }
}
```

> [!NOTE]
> `IFormFile` kann wie oben dargestellt direkt als Parameter einer Aktionsmethode oder als Ansichtsmodelleigenschaft verwendet werden.

Kopieren Sie die `IFormFile`-Datei in einen Stream, und speichern Sie sie im Bytearray:

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
> Speichern Sie Binärdaten in relationalen Datenbanken mit Bedacht, da sie Auswirkungen auf die Leistung haben können.

## <a name="uploading-large-files-with-streaming"></a>Hochladen von großen Dateien mittels Streaming

Wenn es zu Ressourcenproblemen mit der App kommt, weil zu viele Dateien hochgeladen werden oder die Dateien zu groß sind, sollten Sie darüber nachdenken, den Dateiupload zu streamen, anstatt ihn wie zuvor erläutert mittels Modellbindung vollständig zu puffern. Die Verwendung von `IFormFile` und die Modellbindung stellen recht einfache Lösung dar. Für das Streaming müssen Sie hingegen eine Reihe verschiedener Schritte ausführen, um eine einwandfreie Implementierung zu garantieren.

> [!NOTE]
> Jede gepufferte Datei, die größer als 64 KB ist, wird von RAM in eine temporäre Datei auf dem Datenträger auf dem Server verschoben. Welche Ressourcen (Datenträger oder RAM) für den Dateiupload verwendet werden, ist von der Anzahl und der Größe der gleichzeitig hochgeladenen Dateien abhängig. Beim Streaming geht es nicht um Leistung, sondern um die Staffelung der Uploads. Wenn Sie versuchen, zu viele Uploads zu puffern, stürzt Ihre Website ab, wenn der Arbeitsspeicher oder der Speicherplatz auf dem Datenträger ausgelastet ist.

Im folgenden Beispiel wird dargestellt, wie Sie JavaScript bzw. Angular verwendet können, um einen Stream auf eine Controlleraktion durchzuführen. Das Antifälschungstoken einer Datei wird mithilfe von Filterattributen generiert und an HTTP-Header anstelle von Anforderungstexten übergeben. Da die Aktionsmethode die hochgeladenen Daten direkt verarbeitet, wird die Modellbindung von einem anderen Filter deaktiviert. Innerhalb der Aktion werden die Inhalte des Formulars über `MultipartReader` gelesen. Dieses Element liest jede einzelne `MultipartSection`-Klasse, wodurch die Datei verarbeitet wird oder die Inhalte angemessen gespeichert werden. Sobald alle Abschnitte gelesen wurden, führt die Aktion ihre eigene Modellbindung aus.

Die erste Aktion lädt das Formular und speichert das Antifälschungstoken (über das `GenerateAntiforgeryTokenCookieForAjax`-Attribut) in einem Cookie:

```csharp
[HttpGet]
[GenerateAntiforgeryTokenCookieForAjax]
public IActionResult Index()
{
    return View();
}
```

Das Attribut verwendet die in ASP.NET Core integrierte [Antifälschungsunterstützung](xref:security/anti-request-forgery), um ein Cookie mit einem Anforderungstoken festzulegen:

[!code-csharp[](file-uploads/sample/FileUploadSample/Filters/GenerateAntiforgeryTokenCookieForAjaxAttribute.cs?name=snippet1)]

Angular übergibt automatisch ein Antifälschungstoken an einen Anforderungsheader mit dem Namen `X-XSRF-TOKEN`. Die ASP.NET Core MVC-App wird so konfiguriert, dass sie auf diesen Header in Ihrer Konfiguration in *Startup.cs* verweist:

[!code-csharp[](file-uploads/sample/FileUploadSample/Startup.cs?name=snippet1)]

Das nachfolgend dargestellte `DisableFormValueModelBinding`-Attribut wird verwendet, um die Modellbindung für die `Upload`-Aktionsmethode zu deaktivieren.

[!code-csharp[](file-uploads/sample/FileUploadSample/Filters/DisableFormValueModelBindingAttribute.cs?name=snippet1)]

Da die Modellbindung deaktiviert ist, akzeptiert die `Upload`-Aktionsmethode keine Parameter. Sie funktioniert direkt mit der `Request`-Eigenschaft von `ControllerBase` zusammen. Ein `MultipartReader` wird verwendet, um die verschiedenen Abschnitte zu lesen. Die Datei wird zusammen mit einem GUID-Dateinamen und die Schlüssel- bzw. Wertdaten werden in einem `KeyValueAccumulator` gespeichert. Sobald alle Abschnitte gelesen wurden, werden die Inhalte von `KeyValueAccumulator` verwendet, um die Formulardaten an einen Modelltyp zu binden.

Die vollständige `Upload`-Methode wird im Folgenden Beispiel dargestellt:

[!INCLUDE [GetTempFileName](../../includes/GetTempFileName.md)]

[!code-csharp[](file-uploads/sample/FileUploadSample/Controllers/StreamingController.cs?name=snippet1)]

## <a name="troubleshooting"></a>Problembehandlung

Nachfolgend werden einige häufig auftretenden Probleme aufgeführt, die entstehen können, wenn Dateien hochgeladen werden. Außerdem wird erläutert, wie Sie diese Probleme beheben können.

### <a name="unexpected-not-found-error-with-iis"></a>Unerwarteter „Nicht gefunden“-Fehler mit IIS

Der folgende Fehler wird angezeigt, wenn Ihr Dateiupload die in `maxAllowedContentLength` konfigurierte Größe des Servers überschreitet:

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

Die Standardeinstellung lautet `30000000`, also in etwa 28,6 MB. Sie können diesen Wert bearbeiten, indem Sie die *web.config*-Datei bearbeiten:

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

Diese Einstellung gilt nur für IIS. Beim Hosting unter Kestrel gehört dieses Verhalten nicht zum Standard. Weitere Informationen finden Sie unter [Request Limits \<requestLimits\> (Anforderungseinschränkungen)](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).

### <a name="null-reference-exception-with-iformfile"></a>Ausnahme bei möglichem NULL-Verweis mit IFormFile

Wenn Ihr Controller es zulässt, dass Dateien über `IFormFile` hochgeladen werden, Sie allerdings feststellen, dass der Wert immer NULL ist, überprüfen Sie, ob Ihr HTML-Formular einen `enctype`-Wert von `multipart/form-data` angibt. Wenn dieses Attribut für das `<form>`-Element festgelegt ist, können keine Dateien hochgeladen werden, und jedes gebundene `IFormFile`-Argument gibt NULL zurück.

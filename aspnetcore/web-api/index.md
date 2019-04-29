---
title: Erstellen von Web-APIs mit ASP.NET Core
author: scottaddie
description: Erfahren Sie mehr über die Grundlagen zum Erstellen einer Web-API in ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 04/11/2019
uid: web-api/index
ms.openlocfilehash: 334e5732269921a62356e7854824deccc051c291
ms.sourcegitcommit: 8a84ce880b4c40d6694ba6423038f18fc2eb5746
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60165171"
---
# <a name="create-web-apis-with-aspnet-core"></a>Erstellen von Web-APIs mit ASP.NET Core

Von [Scott Addie](https://github.com/scottaddie) und [Tom Dykstra](https://github.com/tdykstra)

ASP.NET Core unterstützt das Erstellen von RESTful-Diensten, die auch als Web-APIs bezeichnet werden, unter Verwendung von C#. Eine Web-API verwendet Controller, um Anforderungen zu verarbeiten. *Controller* in einer Web-API sind von `ControllerBase` abgeleitete Klassen. In diesem Artikel wird beschrieben, wie Sie Controller für die Verarbeitung von API-Anforderungen verwenden.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/index/samples) ([Informationen zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="controllerbase-class"></a>ControllerBase-Klasse

Eine Web-API verfügt über mindestens eine Controllerklasse, die von <xref:Microsoft.AspNetCore.Mvc.ControllerBase> abgeleitet ist. Durch die Web-API-Projektvorlage wird beispielsweise ein Values-Controller erstellt:

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_Signature&highlight=3)]

Erstellen Sie keinen von der <xref:Microsoft.AspNetCore.Mvc.Controller>-Basisklasse abgeleiteten Web-API-Controller. `Controller` wird von `ControllerBase` abgeleitet und fügt Unterstützung für Ansichten hinzu, wird also für die Verarbeitung von Webseiten verwendet und nicht für Web-API-Anforderungen.  Es gibt eine Ausnahme von dieser Regel: Wenn Sie denselben Controller sowohl für Ansichten als auch für APIs verwenden möchten, leiten Sie ihn von `Controller` ab.

Die `ControllerBase`-Klasse bietet viele Eigenschaften und Methoden, die für die Verarbeitung von HTTP-Anforderungen hilfreich sind. `ControllerBase.CreatedAtAction` gibt beispielsweise Statuscode 201 zurück:

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_400And201&highlight=8-9)]

 Hier einige weitere Beispiele für die von `ControllerBase` bereitgestellten Methoden.

|Methode  |Hinweise  |
|---------|---------|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>| Gibt Statuscode 400 zurück.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> |Gibt Statuscode 404 zurück.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.PhysicalFile*>|Gibt eine Datei zurück.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>|Ruft die [Modellbindung](xref:mvc/models/model-binding) auf.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryValidateModel*>|Ruft die [Modellvalidierung](xref:mvc/models/validation) auf.|

Eine Liste aller verfügbaren Methoden und Eigenschaften finden Sie unter <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.

## <a name="attributes"></a>Attribute

Der <xref:Microsoft.AspNetCore.Mvc>-Namespace enthält Attribute, mit denen das Verhalten von Web-API-Controllern und Aktionsmethoden konfiguriert werden kann. Im folgenden Beispiel werden mithilfe von Attributen die akzeptierte HTTP-Methode und die zurückgegebenen Statuscodes angegeben:

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_400And201&highlight=1-3)]

Hier einige weitere Beispiele für die verfügbaren Attribute.

|Attribut|Hinweise|
|---------|-----|
|[[Route]](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)      |Gibt ein URL-Muster für einen Controller oder eine Aktion an.|
|[[Bind]](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)        |Gibt Präfixe und Eigenschaften an, die in die Modellbindung einbezogen werden sollen.|
|[[HttpGet]](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)  |Identifiziert eine Aktion, die die HTTP GET-Methode unterstützt.|
|[[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)|Gibt die von einer Aktion akzeptierten Datentypen an.|
|[[Produces]](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)|Gibt die von einer Aktion zurückgegebenen Datentypen an.|

Eine Liste mit den verfügbaren Attributen finden Sie im <xref:Microsoft.AspNetCore.Mvc>-Namespace.

## <a name="apicontroller-attribute"></a>ApiController-Attribut

Das [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)-Attribut kann auf eine Controllerklasse angewendet werden, um API-spezifische Verhalten zu aktivieren:

* [Anforderung für das Attributrouting](#attribute-routing-requirement)
* [Automatische HTTP 400-Antworten](#automatic-http-400-responses)
* [Rückschluss auf Bindungsquellparameter](#binding-source-parameter-inference)
* [Ableiten der Multipart/form-data-Anforderung](#multipartform-data-request-inference)
* [Problemdetails für Fehlerstatuscodes](#problem-details-for-error-status-codes)

Diese Features erfordern mindestens [Kompatibilitätsversion](<xref:mvc/compatibility-version>) 2.1.

### <a name="apicontroller-on-specific-controllers"></a>ApiController für bestimmte Controller

Das `[ApiController]`-Attribut kann, wie im folgenden Beispiel der Projektvorlage ersichtlich, auf bestimmte Controller angewendet werden:

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_Signature&highlight=2)]

### <a name="apicontroller-on-multiple-controllers"></a>ApiController für mehrere Controller

Eine Möglichkeit, das Attribut für mehr als einen Controller zu verwenden, besteht darin, eine benutzerdefinierte Basiscontrollerklasse zu erstellen, die mit dem `[ApiController]`-Attribut kommentiert ist. Hier ein Beispiel für eine benutzerdefinierte Basisklasse und einen Controller, der von ihr abgeleitet wird:

[!code-csharp[](index/samples/2.x/Controllers/MyControllerBase.cs?name=snippet_MyControllerBase)]

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_Inherit)]

### <a name="apicontroller-on-an-assembly"></a>ApiController für eine Assembly

Wenn die [Kompatibilitätsversion](<xref:mvc/compatibility-version>) auf 2.2 oder höher festgelegt ist, kann das `[ApiController]`-Attribut auf eine Assembly angewendet werden. Auf diese Weise wendet die Anmerkung das Web-API-Verhalten auf alle Controller in der Assembly an. Es gibt keine Möglichkeit, einzelne Controller auszuschließen. Wenden Sie das Attribut auf Assemblyebene auf die `Startup`-Klasse an, wie im folgenden Beispiel gezeigt:

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

## <a name="attribute-routing-requirement"></a>Anforderung für das Attributrouting

Durch das `ApiController`-Attribut wird das Attributrouting zu einer Anforderung. Beispiel:

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_Signature&highlight=1)]

Sie können über [konventionelle Routen](xref:mvc/controllers/routing#conventional-routing), die durch <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> oder <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> in `Startup.Configure` definiert sind, nicht auf Aktionen zugreifen.

## <a name="automatic-http-400-responses"></a>Automatische HTTP 400-Antwort

Durch das `ApiController`-Attribut wird bei Modellvalidierungsfehlern automatisch eine HTTP 400-Antwort ausgelöst. Deshalb ist der folgende Code in einer Aktionsmethode überflüssig:

```csharp
if (!ModelState.IsValid)
{
    return BadRequest(ModelState);
}
```

### <a name="default-badrequest-response"></a>Standardmäßige BadRequest-Antwort 

Bei Kompatibilitätsversion 2.2 oder höher wird für HTTP 400-Antworten standardmäßig der Antworttyp <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> verwendet. Der Typ `ValidationProblemDetails` entspricht der [RFC 7807-Spezifikation](https://tools.ietf.org/html/rfc7807).

Um die Standardantwort in <xref:Microsoft.AspNetCore.Mvc.SerializableError> zu ändern, legen Sie die `SuppressUseValidationProblemDetailsForInvalidModelStateResponses`-Eigenschaft auf `true` in `Startup.ConfigureServices` fest, wie im folgenden Beispiel gezeigt:

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,9)]

### <a name="customize-badrequest-response"></a>Anpassen der BadRequest-Antwort

Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory>, um die aus einem Validierungsfehler resultierende Antwort anzupassen. Fügen Sie nach `services.AddMvc().SetCompatibilityVersion` folgenden hervorgehobenen Code hinzu:

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureBadRequestResponse&highlight=3-20)]

### <a name="disable-automatic-400"></a>Deaktivieren des automatischen Verhaltens bei Statuscode 400

Um das automatische Verhalten bei Statuscode 400 zu deaktivieren, legen Sie die <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter>-Eigenschaft auf `true` fest. Fügen Sie in `Startup.ConfigureServices` nach `services.AddMvc().SetCompatibilityVersion` folgenden hervorgehobenen Code hinzu:

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,7)]

## <a name="binding-source-parameter-inference"></a>Rückschluss auf Bindungsquellparameter

Ein Bindungsquellenattribut definiert den Speicherort, an dem der Wert eines Aktionsparameters vorhanden ist. Es gibt die folgenden Bindungsquellattribute:

|Attribut|Bindungsquelle |
|---------|---------|
|[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)     | Anforderungstext |
|[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)     | Formulardaten im Anforderungstext |
|[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) | Anforderungsheader |
|[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)   | Abfragezeichenfolge-Parameter der Anforderung |
|[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)   | Routendaten aus aktuellen Anforderungen |
|[[FromServices]](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices) | Der als Aktionsparameter eingefügte Anforderungsdienst. |

> [!WARNING]
> Verwenden Sie `[FromRoute]` nicht, wenn Werte möglicherweise `%2f` enthalten (d.h. `/`). `%2f` wird nicht durch Entfernen von Escapezeichen zu `/`. Verwenden Sie `[FromQuery]`, wenn der Wert `%2f` enthalten könnte.

Ohne das `[ApiController]`-Attribut oder Bindungsquellenattribute wie `[FromQuery]` versucht die ASP.NET Core-Runtime, die Modellbindung für komplexe Objekte zu verwenden. Bei der Modellbindung für komplexe Objekte werden Daten aus Wertanbietern in einer festgelegten Reihenfolge abgerufen.

Im folgenden Beispiel gibt das `[FromQuery]`-Attribut an, dass der Parameterwert `discontinuedOnly` in der Abfragezeichenfolge der Anforderungs-URL bereitgestellt wird:

[!code-csharp[](index/samples/2.x/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

Das `[ApiController]`-Attribut wendet Rückschlussregeln auf die Standarddatenquellen von Aktionsparametern an. Da durch diese Regeln Attribute auf die Aktionsparameter angewendet werden, müssen Sie Bindungsquellen nicht manuell identifizieren. Die Rückschlussregeln für Bindungsquellen verhalten sich wie folgt:

* `[FromBody]` wird für komplexe Typparameter abgeleitet. Jeder komplexe integrierte Typ mit spezieller Bedeutung, z. B. <xref:Microsoft.AspNetCore.Http.IFormCollection> und <xref:System.Threading.CancellationToken>, stellt eine Ausnahme von der `[FromBody]`-Rückschlussregel dar. Der Rückschlusscode der Bindungsquelle ignoriert diese Typen. 
* `[FromForm]` wird für Aktionsparameter des Typs <xref:Microsoft.AspNetCore.Http.IFormFile> und <xref:Microsoft.AspNetCore.Http.IFormFileCollection> abgeleitet. Es wird für keine einfachen oder benutzerdefinierte Typen abgeleitet.
* `[FromRoute]` wird für jeden Namen von Aktionsparametern abgeleitet, der mit einem Parameter in der Routenvorlage übereinstimmt. Wenn mehr als eine Route mit einem Aktionsparameter übereinstimmt, gilt jeder Routenwert als `[FromRoute]`.
* `[FromQuery]` wird für alle anderen Aktionsparameter abgeleitet.

### <a name="frombody-inference-notes"></a>Hinweise zum FromBody-Rückschluss

`[FromBody]` wird für einfache Typen wie `string` oder `int` nicht abgeleitet. Deshalb sollte das `[FromBody]`-Attribut für einfache Typen verwendet werden, wenn diese Funktionsweise benötigt wird.

Wenn an eine Aktion mehr als ein Parameter aus dem Anforderungstext gebunden ist, wird eine Ausnahme ausgelöst. Beispielsweise lösen alle Signaturen der folgenden Aktionsmethoden eine Ausnahme aus:

* `[FromBody]` wird für beide abgeleitet, da sie komplexe Typen darstellen.

  ```csharp
  [HttpPost]
  public IActionResult Action1(Product product, Order order)
  ```

* Das `[FromBody]`-Attribut für einen Typ wird für den anderen abgeleitet, da es sich um einen komplexen Typ handelt.

  ```csharp
  [HttpPost]
  public IActionResult Action2(Product product, [FromBody] Order order)
  ```

* Das `[FromBody]`-Attribut für beide.

  ```csharp
  [HttpPost]
  public IActionResult Action3([FromBody] Product product, [FromBody] Order order)
  ```

> [!NOTE]
> In ASP.NET Core 2.1 werden Sammlungstypparameter wie Listen und Arrays fälschlicherweise als `[FromQuery]` abgeleitet. Für diese Parameter sollte das `[FromBody]`-Attribut verwendet werden, wenn sie aus dem Anforderungstext gebunden werden sollen. Dieses Verhalten wurde bei ASP.NET Core 2.2 oder höher behoben. Hier werden Sammlungstypparameter standardmäßig als „[FromBody]“ abgeleitet.

### <a name="disable-inference-rules"></a>Deaktivieren von Rückschlussregeln

Legen Sie <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> auf `true` fest, um den Rückschluss auf Bindungsquellen zu deaktivieren. Fügen Sie den folgenden Code zu `Startup.ConfigureServices` hinter `services.AddMvc().SetCompatibilityVersion` hinzu:

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,6)]

## <a name="multipartform-data-request-inference"></a>Ableiten der Multipart/form-data-Anforderung

Das `[ApiController]`-Attribut wendet eine Rückschlussregel an, wenn ein Aktionsparameter mit dem [[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)-Attribut kommentiert ist: Der Inhaltstyp der `multipart/form-data`-Anforderung wird abgeleitet.

Um das Standardverhalten zu deaktivieren, legen Sie <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> auf `true` in `Startup.ConfigureServices` fest, wie im folgenden Beispiel gezeigt:

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,5)]

## <a name="problem-details-for-error-status-codes"></a>Problemdetails für Fehlerstatuscodes

Bei Kompatibilitätsversion 2.2 oder höher wandelt MVC ein Fehlerergebnis (ein Ergebnis mit dem Statuscode 400 oder höher) in ein Ergebnis mit <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> um. Die `ProblemDetails`-Typ basiert auf der [RFC 7807-Spezifikation](https://tools.ietf.org/html/rfc7807) für die Bereitstellung maschinenlesbarer Fehlerdetails in einer HTTP-Antwort.

Betrachten Sie den folgenden Code in einer Controlleraktion:

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_ProblemDetailsStatusCode)]

Die HTTP-Antwort für `NotFound` hat den Statuscode 404 mit einem `ProblemDetails`-Körper. Beispiel:

```json
{
    type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
    title: "Not Found",
    status: 404,
    traceId: "0HLHLV31KRN83:00000001"
}
```

### <a name="customize-problemdetails-response"></a>Anpassen der ProblemDetails-Antwort

Verwenden Sie die `ClientErrorMapping`-Eigenschaft zum Konfigurieren des Inhalts der `ProblemDetails`-Antwort. Der folgenden Code aktualisiert beispielsweise die `type`-Eigenschaft für 404-Antworten:

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=10-11)]

### <a name="disable-problemdetails-response"></a>Deaktivieren der ProblemDetails-Antwort

Die automatische Erstellung von `ProblemDetails` wird deaktiviert, wenn die `SuppressMapClientErrors`-Eigenschaft auf `true` festgelegt ist. Fügen Sie den folgenden Code zu `Startup.ConfigureServices` hinzu:

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,8)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen 

* <xref:web-api/action-return-types>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>

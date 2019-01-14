---
title: Erstellen von Web-APIs mit ASP.NET Core
author: scottaddie
description: Lernen Sie die verfügbaren Features zum Erstellen einer Web-API in ASP.NET Core kennen, und erhalten Sie Informationen zur Verwendung dieser Features.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/11/2019
uid: web-api/index
ms.openlocfilehash: a826bdecdd3a25eb23597123166695c169ba4229
ms.sourcegitcommit: ec71fd5a988f927ae301813aae5ff764feb3bb6a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2019
ms.locfileid: "54249437"
---
# <a name="build-web-apis-with-aspnet-core"></a>Erstellen von Web-APIs mit ASP.NET Core

Von [Scott Addie](https://github.com/scottaddie)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/define-controller/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

In diesem Dokument wird erläutert, wie eine Web-API in ASP.NET Core erstellt werden kann und wann jedes Feature am besten verwendet werden kann.

## <a name="derive-class-from-controllerbase"></a>Ableiten einer Klasse von ControllerBase

Leiten Sie Inhalte der <xref:Microsoft.AspNetCore.Mvc.ControllerBase>-Klasse in einem Controller ab, der als Web-API fungieren soll. Beispiel:

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](define-controller/samples/WebApiSample.Api.21/Controllers/PetsController.cs?name=snippet_PetsController&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](define-controller/samples/WebApiSample.Api.Pre21/Controllers/PetsController.cs?name=snippet_PetsController&highlight=3)]

::: moniker-end

Die `ControllerBase`-Klasse ermöglicht den Zugriff auf mehrere Eigenschaften und Methoden. Zu den Beispielen im vorherigen Code zählen <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest(Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary)> und <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction(System.String,System.Object,System.Object)>. Diese Methoden werden innerhalb von Aktionsmethoden aufgerufen und geben dann jeweils die Statuscodes HTTP 400 und HTTP 201 zurück. Ein Zugriff erfolgt auf die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState>, die ebenfalls von `ControllerBase` bereitgestellt wird, um die Anforderungsvalidierung für das Modell zu verarbeiten.

::: moniker range=">= aspnetcore-2.1"

## <a name="annotation-with-apicontroller-attribute"></a>Anmerkungen mit dem ApiController-Attribut

In ASP.NET Core 2.1 wird das Attribut [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) eingeführt, um eine Klasse des Web-API-Controllers anzugeben. Beispiel:

[!code-csharp[](define-controller/samples/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_ControllerSignature&highlight=2)]

Eine Kompatibilitätsversion von 2.1 oder höher, die über <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> festgelegt wird, ist für die Verwendung dieses Attributs auf Controllerebene erforderlich. Beispielsweise legt der hervorgehobene Code in `Startup.ConfigureServices` das Kompatibilitätsflag 2.1 fest:

[!code-csharp[](define-controller/samples/WebApiSample.Api.21/Startup.cs?name=snippet_SetCompatibilityVersion&highlight=2)]

Weitere Informationen finden Sie unter <xref:mvc/compatibility-version>.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

Das Attribut `[ApiController]` kann in ASP.NET Core 2.2 oder höher auf eine Assembly angewendet werden. Auf diese Weise wendet die Anmerkung das Web-API-Verhalten auf alle Controller in der Assembly an. Beachten Sie, dass Sie einzelne Controller davon nicht ausnehmen können. Es wird empfohlen, Attribute auf Assemblyebene auf die Klasse `Startup` anzuwenden:

[!code-csharp[](define-controller/samples/WebApiSample.Api.22/Startup.cs?name=snippet_ApiControllerAttributeOnAssembly&highlight=1)]

Eine Kompatibilitätsversion von 2.2 oder höher, die über <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> festgelegt wird, ist für die Verwendung dieses Attributs auf Assemblyebene erforderlich.

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

Das `[ApiController]`-Attribut ist in der Regel mit `ControllerBase` gekoppelt, um das REST-spezifische Verhalten für Controller zu ermöglichen. Mit `ControllerBase` kann auf Methoden wie <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> und <xref:Microsoft.AspNetCore.Mvc.ControllerBase.File*> zugegriffen werden.

Eine weitere Vorgehensweise ist das Erstellen einer benutzerdefinierten Basiscontrollerklasse, für die das Attribut `[ApiController]` angegeben ist:

[!code-csharp[](define-controller/samples/WebApiSample.Api.21/Controllers/MyBaseController.cs?name=snippet_ControllerSignature)]

In den folgenden Abschnitten werden praktische Features beschrieben, die vom Attribut hinzugefügt wurden.

### <a name="automatic-http-400-responses"></a>Automatische HTTP 400-Antwort

Modellvalidierungsfehler lösen automatisch eine HTTP 400-Antwort aus. Deshalb wird der nachfolgende Code in Ihren Aktionen überflüssig:

[!code-csharp[](define-controller/samples/WebApiSample.Api.Pre21/Controllers/PetsController.cs?name=snippet_ModelStateIsValidCheck)]

Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> zum Anpassen der Antwortausgabe.

Es kann nützlich sein, dass Standardverhalten zu deaktivieren, wenn Ihre Aktion nach einem Modellvalidierungsfehler wiederhergestellt werden kann. Das Standardverhalten wird deaktiviert, sobald die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> auf `true` festgelegt wird. Fügen Sie den folgenden Code zu `Startup.ConfigureServices` hinter `services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_<version_number>);` hinzu:

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](define-controller/samples/WebApiSample.Api.22/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=7)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](define-controller/samples/WebApiSample.Api.21/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=5)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

Mit dem Kompatibilitätsflag 2.2 oder höher wird für Antworten mit HTTP-Statuscode 400 standardmäßig der Typ <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> zurückgegeben. Der Typ `ValidationProblemDetails` entspricht der [RFC 7807-Spezifikation](https://tools.ietf.org/html/rfc7807). Legen Sie die `SuppressUseValidationProblemDetailsForInvalidModelStateResponses`-Eigenschaft auf `true` fest, um stattdessen <xref:Microsoft.AspNetCore.Mvc.SerializableError> im ASP.NET Core 2.1-Fehlerformat zurückzugeben. Fügen Sie den folgenden Code zu `Startup.ConfigureServices` hinzu:

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

### <a name="binding-source-parameter-inference"></a>Rückschluss auf Bindungsquellparameter

Ein Bindungsquellenattribut definiert den Speicherort, an dem der Wert eines Aktionsparameters vorhanden ist. Es gibt die folgenden Bindungsquellattribute:

|Attribut|Bindungsquelle |
|---------|---------|
|**[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)**     | Anforderungstext |
|**[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)**     | Formulardaten im Anforderungstext |
|**[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute)** | Anforderungsheader |
|**[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)**   | Abfragezeichenfolge-Parameter der Anforderung |
|**[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)**   | Routendaten aus aktuellen Anforderungen |
|**[[FromServices]](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices)** | Der als Aktionsparameter eingefügte Anforderungsdienst. |

> [!WARNING]
> Verwenden Sie `[FromRoute]` nicht, wenn Werte möglicherweise `%2f` enthalten (d.h. `/`). `%2f` wird nicht durch Entfernen von Escapezeichen zu `/`. Verwenden Sie `[FromQuery]`, wenn der Wert `%2f` enthalten könnte.

Ohne das `[ApiController]`-Attribut werden Bindungsquellattribute explizit definiert. Im folgenden Beispiel gibt das `[FromQuery]`-Attribut an, dass der Parameterwert `discontinuedOnly` in der Abfragezeichenfolge der Anforderungs-URL bereitgestellt wird:

[!code-csharp[](define-controller/samples/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

Rückschlussregeln werden auf die Standarddatenquellen der Aktionsparameter angewendet. Diese Regeln konfigurieren die Bindungsquellen, die Sie sonst am ehesten manuell auf die Aktionsparameter anwenden. Die Bindungsquellenattribute verhalten sich wie folgt:

* Für komplexe Typparameter wird **[FromBody]** abgeleitet. Eine Ausnahme von der Regel ist jeder komplexe integrierte Typ mit spezieller Bedeutung, z.B. <xref:Microsoft.AspNetCore.Http.IFormCollection> und <xref:System.Threading.CancellationToken>. Der Rückschlusscode der Bindungsquelle ignoriert diese Typen. `[FromBody]` wird für einfache Typen wie `string` oder `int` nicht abgeleitet. Deshalb sollte das `[FromBody]`-Attribut für einfache Typen verwendet werden, wenn diese Funktionsweise benötigt wird. Wenn für eine Aktion mehr als ein Parameter explizit angegeben ist (über `[FromBody]`) oder als vom Anforderungstext gebunden hergeleitet wird, wird eine Ausnahme ausgelöst. Die folgenden Aktionssignaturen lösen beispielsweise eine Ausnahme aus:

    [!code-csharp[](define-controller/samples/WebApiSample.Api.21/Controllers/TestController.cs?name=snippet_ActionsCausingExceptions)]

    > [!NOTE]
    > In ASP.NET Core 2.1 werden Sammlungstypparameter wie z.B. Listen und Arrays fälschlicherweise als [[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) abgeleitet. Für diese Parameter sollte [[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) verwendet werden, wenn sie aus dem Anforderungstext gebunden werden sollen. Dieses Verhalten wurde für ASP.NET Core 2.2 oder höher behoben. Hier werden Sammlungstypparameter standardmäßig als „[FromBody]“ abgeleitet.

* **[FromForm]** wird für Aktionsparameter des Typs <xref:Microsoft.AspNetCore.Http.IFormFile> und <xref:Microsoft.AspNetCore.Http.IFormFileCollection> abgeleitet. Es wird für keine einfachen oder benutzerdefinierte Typen abgeleitet.
* **[FromRoute]** wird für jeden Namen von Aktionsparametern abgeleitet, die mit einem Parameter in der Routenvorlage übereinstimmen. Wenn mehr als eine Route mit einem Aktionsparameter übereinstimmt, gilt jeder Routenwert als `[FromRoute]`.
* **[FromQuery]** wird für alle anderen Aktionsparameter abgeleitet.

Die standardmäßig festgelegten Rückschlussregeln werden deaktiviert, sobald die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> auf `true` festgelegt wird. Fügen Sie den folgenden Code zu `Startup.ConfigureServices` hinter `services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_<version_number>);` hinzu:

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](define-controller/samples/WebApiSample.Api.22/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=6)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](define-controller/samples/WebApiSample.Api.21/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=4)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

### <a name="multipartform-data-request-inference"></a>Ableiten der Multipart/form-data-Anforderung

Wenn ein Aktionsparameter mit dem [[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)-Attribut kommentiert wird, wird der Anforderungsinhaltstyp `multipart/form-data` abgeleitet.

Das Standardverhalten wird deaktiviert, sobald die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> auf `true` festgelegt wird.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

Fügen Sie den folgenden Code zu `Startup.ConfigureServices` hinzu:

[!code-csharp[](define-controller/samples/WebApiSample.Api.22/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=5)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

Fügen Sie den folgenden Code zu `Startup.ConfigureServices` hinter `services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);` hinzu:

[!code-csharp[](define-controller/samples/WebApiSample.Api.21/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

### <a name="attribute-routing-requirement"></a>Anforderung für das Attributrouting

Das Attributrouting wird zu einer Anforderung. Beispiel:

[!code-csharp[](define-controller/samples/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_ControllerSignature&highlight=1)]

Sie können über [konventionelle Routen](xref:mvc/controllers/routing#conventional-routing), die in <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> oder von <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> in `Startup.Configure` definiert sind, nicht auf Aktionen zugreifen.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="problem-details-responses-for-error-status-codes"></a>Problemdetailantworten für Fehlerstatuscodes

MVC wandelt in ASP.NET Core 2.2 oder höher ein Fehlerergebnis (ein Ergebnis mit dem Statuscode 400 oder höher) in ein Ergebnis mit <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> um. `ProblemDetails` ist:

* ein Typ, der auf der [RCF 7807-Spezifikation](https://tools.ietf.org/html/rfc7807) basiert.
* ein standardisiertes Format zum Angeben maschinenlesbarer Fehlerdetails in einer HTTP-Antwort.

Betrachten Sie den folgenden Code in einer Controlleraktion:

[!code-csharp[](define-controller/samples/WebApiSample.Api.22/Controllers/ProductsController.cs?name=snippet_ProblemDetailsStatusCode)]

Die HTTP-Antwort für `NotFound` hat den Statuscode 404 mit einem `ProblemDetails`-Körper. Beispiel:

```json
{
    type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
    title: "Not Found",
    status: 404,
    traceId: "0HLHLV31KRN83:00000001"
}
```

Das Feature für Problemdetails erfordert das Kompatibilitätsflag 2.2 oder höher. Das Standardverhalten wird deaktiviert, sobald die Eigenschaft `SuppressMapClientErrors` auf `true` festgelegt wird. Fügen Sie den folgenden Code zu `Startup.ConfigureServices` hinzu:

[!code-csharp[](define-controller/samples/WebApiSample.Api.22/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=8)]

Verwenden Sie die `ClientErrorMapping`-Eigenschaft zum Konfigurieren des Inhalts der `ProblemDetails`-Antwort. Der folgenden Code aktualisiert beispielsweise die `type`-Eigenschaft für 404-Antworten:

[!code-csharp[](define-controller/samples/WebApiSample.Api.22/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=10-11)]

::: moniker-end

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:web-api/action-return-types>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>

---
title: Formatieren von Antwortdaten in Web-APIs in ASP.NET Core
author: ardalis
description: Informationen zum Formatieren von Antwortdaten in Web-APIS in ASP.NET Core
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 05/29/2019
uid: web-api/advanced/formatting
ms.openlocfilehash: 8bee4efdae5341ddab5bd3aec278ecfef37f0c08
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082346"
---
<!-- DO NOT EDIT BEFORE https://github.com/aspnet/AspNetCore.Docs/pull/12077 MERGES -->
# <a name="format-response-data-in-aspnet-core-web-api"></a>Formatieren von Antwortdaten in Web-APIs in ASP.NET Core

Von [Steve Smith](https://ardalis.com/)

ASP.NET Core MVC verfügt über integrierte Unterstützung zum Formatieren von Antwortdaten mithilfe von festen Formaten oder als Antwort auf Clientspezifikationen.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="format-specific-action-results"></a>Formatspezifische Aktionsergebnisse

Einige Aktionsergebnistypen sind für ein bestimmtes Format spezifisch, wie z.B. `JsonResult` und `ContentResult`. Aktionen können bestimmte Ergebnisse zurückgeben, die immer auf bestimmte Weise formatiert werden. Beim Zurückgeben eines `JsonResult` werden beispielsweise unabhängig von den Clienteinstellungen JSON-formatierte Daten zurückgegeben. Dementsprechend werden beim Zurückgeben von `ContentResult` Zeichenfolgendaten im Textformat zurückgegeben (wie auch beim Zurückgeben einer Zeichenfolge).

> [!NOTE]
> Eine Aktion muss keinen bestimmten Typ zurückgeben, da MVC jeden beliebigen Objektrückgabewert unterstützt. Gibt eine Aktion eine `IActionResult`-Implementierung zurück und erbt der Controller von `Controller`, verfügen Entwickler über zahlreiche Hilfsmethoden, die vielen der Auswahlmöglichkeiten entsprechen. Ergebnisse von Aktionen, die Objekte zurückgeben, die nicht dem `IActionResult`-Typ angehören, werden mit der entsprechenden `IOutputFormatter`-Implementierung serialisiert.

Sollen Daten in einem bestimmten Format von einem Controller zurückgegeben werden, der von der `Controller`-Basisklasse erbt, verwenden Sie die integrierte Hilfsmethode `Json`, um JSON-Daten zurückzugeben, sowie `Content` für Textdaten. Die Aktionsmethode sollte entweder den spezifischen Ergebnistyp (z.B. `JsonResult`) oder `IActionResult` zurückgeben.

Zurückgeben von JSON-formatierten Daten:

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=21-26)]

Beispielantwort dieser Aktion:

![Registerkarte „Netzwerk“ von Developer Tools in Microsoft Edge mit „application/json“ als Inhaltstyp der Antwort](formatting/_static/json-response.png)

Beachten Sie, dass der Inhaltstyp der Antwort `application/json` ist. Dies wird sowohl in der Liste der Netzwerkanforderungen als auch im Abschnitt „Antwortheader“ gezeigt. Beachten Sie auch die Liste der Optionen, die vom Browser (hier Microsoft Edge) im Accept-Header im Abschnitt „Anforderungsheader“ angezeigt wird. Das aktuelle Verfahren sieht vor, dass dieser Header ignoriert wird. Informationen zur Berücksichtigung des Headers finden Sie weiter unten.

Wenn Sie Daten im Textformat zurückgeben möchten, verwenden Sie `ContentResult` und das `Content`-Hilfsprogramm:

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=47-52)]

Eine Antwort dieser Aktion:

![Registerkarte „Netzwerk“ von Developer Tools in Microsoft Edge mit „text/plain“ als Inhaltstyp der Antwort](formatting/_static/text-response.png)

Beachten Sie, dass der zurückgegebene `Content-Type` in diesem Fall `text/plain` ist. Dasselbe Verhalten erreichen Sie auch mit einer Zeichenfolge als Antworttyp:

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=54-59)]

>[!TIP]
> Für nicht triviale Aktionen mit mehreren Rückgabetypen oder Optionen (wie z.B. verschiedene HTTP-Statuscodes abhängig vom Ergebnis der ausgeführten Vorgänge) sollten Sie `IActionResult` als Rückgabetyp wählen.

## <a name="content-negotiation"></a>Inhaltsaushandlung

Die Inhaltsaushandlung (auch *Conneg* genannt, kurz für „Content negotiation“) tritt auf, wenn der Client einen [Accept-Header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html) angibt. Das von ASP.NET Core MVC verwendete Standardformat ist JSON. Die Inhaltsaushandlung wird durch `ObjectResult` implementiert. Sie ist auch in die Statuscode-spezifischen Aktionsergebnisse integriert, die von den Hilfsmethoden zurückgegeben werden (die alle auf `ObjectResult` basieren). Sie können auch einen Modelltyp zurückgeben (eine Klasse, die Sie zuvor als Datenübertragungstyp definiert haben). Dadurch werden die Daten automatisch vom Framework in einem `ObjectResult` umschlossen.

Die folgende Aktionsmethode verwendet die Hilfsmethoden `Ok` und `NotFound`:

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=8,10&range=28-38)]

Sofern kein anderes Format angefordert wurde und der Server das angeforderte Format zurückgeben kann, wird eine Antwort im JSON-Format zurückgegeben. Sie können ein Tool wie [Fiddler](https://www.telerik.com/fiddler) verwenden, um eine Anforderung mit einem Accept-Header zu erstellen und ein anderes Format anzugeben. Verfügt der Server in diesem Fall über ein *Formatierungsprogramm*, das eine Antwort im angeforderten Format erstellen kann, wird das Ergebnis im vom Client gewünschten Format zurückgegeben.

![Fiddler-Konsole mit einer manuell erstellten GET-Anforderung mit dem Accept-Headerwert „application/xml“](formatting/_static/fiddler-composer.png)

Im obigen Screenshot wurde Fiddler Composer verwendet, um eine Anforderung mit der Angabe `Accept: application/xml` zu erstellen. Standardmäßig unterstützt ASP.NET Core MVC ausschließlich JSON. Selbst wenn ein anderes Format angegeben wird, wird das Ergebnis dennoch im JSON-Format zurückgegeben. Informationen zum Hinzufügen von zusätzlichen Formatierungsprogrammen finden Sie im nächsten Abschnitt.

Controlleraktionen können POCOs (Plain Old CLR Objects) zurückgeben. In diesem Fall erstellt ASP.NET Core MVC automatisch ein `ObjectResult`, das das Objekt umschließt. Der Client erhält das formatierte serialisierte Objekt (das Standardformat ist JSON, Sie können auch XML oder andere Formaten konfigurieren). Ist das zurückgegebene Objekt `null`, gibt das Framework die Antwort `204 No Content` zurück.

Zurückgeben eines Objekttyps:

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3&range=40-45)]

In diesem Beispiel erhält eine Anforderung für einen gültigen Autoralias die Antwort „200 OK“ mit den Daten des Autors. Eine Anforderung für einen ungültigen Alias erhält die Antwort „204 Kein Inhalt“. Weiter unten finden Sie Screenshots mit der Antwort im XML- und JSON-Format.

### <a name="content-negotiation-process"></a>Prozess der Inhaltsaushandlung

Eine *Aushandlung* des Inhalts erfolgt nur, wenn in der Anforderung ein `Accept`-Header angezeigt wird. Enthält eine Anforderung einen Accept-Header, listet das Framework die Medientypen im Accept-Header nach Priorität auf und sucht ein Formatierungsprogramm, das eine Antwort in einem der im Accept-Header angegebenen Formate erstellen kann. Wird kein Formatierungsprogramm gefunden, das der Clientanforderung entspricht, sucht das Framework das erste Formatierungsprogramm, das eine Antwort erstellen kann (sofern der Entwickler unter `MvcOptions` nicht stattdessen die Option für die Rückgabe von „406 Nicht akzeptabel“ konfiguriert hat). Wird in der Anforderung das XML-Format angegeben, wurde das XML-Formatierungsprogramm jedoch nicht konfiguriert, wird stattdessen das JSON-Formatierungsprogramm verwendet. Allgemein ausgedrückt: Wurde kein Formatierungsprogramm konfiguriert, das das angeforderte Format bereitstellen kann, wird das erste Formatierungsprogramm verwendet, mit dem das Objekt formatiert werden kann. Ist kein Header angegeben, wird das erste Formatierungsprogramm zum Serialisieren der Antwort verwendet, das das zurückzugebende Objekt bearbeiten kann. In diesem Fall findet keine Aushandlung statt, sondern der Server legt das Format fest, das verwendet werden soll.

> [!NOTE]
> Enthält der Accept-Header `*/*`, wird der Header ignoriert, sofern `RespectBrowserAcceptHeader` unter `MvcOptions` nicht auf TRUE festgelegt ist.

### <a name="browsers-and-content-negotiation"></a>Browser und Inhaltsaushandlung

Im Gegensatz zu typischen API-Clients unterstützen Webbrowser oft `Accept`-Header, die eine Vielzahl von Formaten, einschließlich Platzhaltern, enthalten. Erkennt das Framework, dass die Anforderung von einem Browser stammt, wird standardmäßig der `Accept`-Header ignoriert und der Inhalt stattdessen im konfigurierten Standardformat der Anwendung zurückgegeben (sofern nicht anders konfiguriert im JSON-Format). Dadurch gestaltet sich die Verwendung von unterschiedlichen Browsern bei der Nutzung von APIs einheitlicher.

Wenn Sie möchten, dass Ihre Anwendung Accept-Header berücksichtigt, können Sie dies als Teil der MVC-Konfiguration unter *Startup.cs* durch Festlegen von `RespectBrowserAcceptHeader` auf `true` in der `ConfigureServices`-Methode konfigurieren.

```csharp
services.AddMvc(options =>
{
    options.RespectBrowserAcceptHeader = true; // false by default
});
```

## <a name="configuring-formatters"></a>Konfigurieren von Formatierungsprogrammen

Wenn Ihre Anwendung neben dem Standardformat JSON zusätzliche Formate unterstützen soll, können Sie NuGet-Pakete hinzufügen und MVC so konfigurieren, dass diese unterstützt werden. Es gibt unterschiedliche Formatierungsprogramme für Ein- und für Ausgaben. Eingabeformatierer werden bei der [Modellbindung](xref:mvc/models/model-binding) verwendet, Ausgabeformatierer zur Formatierung von Antworten. Sie können auch [benutzerdefinierte Formatierer](xref:web-api/advanced/custom-formatters) konfigurieren.

::: moniker range=">= aspnetcore-3.0"

### <a name="configure-systemtextjson-based-formatters"></a>Konfigurieren von System.Text.Json-basierten Formatierern

Funktionen für die `System.Text.Json`-basierten Formatierer können mithilfe von `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions` konfiguriert werden.

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.SerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.SerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

Optionen zur Ausgabeserialisierung können aktionsweise mithilfe von `JsonResult` konfiguriert werden. Beispiel:

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        options.WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a>Hinzufügen von Newtonsoft.Json-basierter Unterstützung für das JSON-Format

Vor ASP.NET Core 3.0 wurden von MVC standardmäßig die JSON-Formatierer verwendet, die mithilfe des `Newtonsoft.Json`-Pakets implementiert wurden. In ASP.NET Core 3.0 oder höher basieren die Standardformatierer für JSON auf `System.Text.Json`. Wenn Sie das NuGet-Paket [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) installieren und in `Startup.ConfigureServices` konfigurieren, erhalten Sie die Unterstützung für `Newtonsoft.Json`-basierte Formatierer und Features.

```csharp
services.AddControllers()
    .AddNewtonsoftJson();
```

Einige Features funktionieren mit `System.Text.Json`-basierten Formatierern möglicherweise nicht gut und erfordern einen Verweis auf die `Newtonsoft.Json`-basierten Formatierer für das Release von ASP.NET Core 3.0. Verwenden Sie weiterhin die `Newtonsoft.Json`-basierten Formatierer, wenn Ihre ASP.NET Core-App für Version 3.0 oder höher

* `Newtonsoft.Json`-Attribute verwendet (z. B. `[JsonProperty]` oder `[JsonIgnore]`), die Serialisierungseinstellungen anpasst oder auf Features beruht, die von `Newtonsoft.Json` bereitstellt werden.
* `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings` konfiguriert. Vor ASP.NET Core 3.0 akzeptiert `JsonResult.SerializerSettings` eine Instanz von `JsonSerializerSettings`, die für `Newtonsoft.Json` spezifisch ist.
* die [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>)-Dokumentation generiert.

Funktionen für die `Newtonsoft.Json`-basierten Formatierer können mithilfe von `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings` konfiguriert werden:

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefautlContractResolver();

    // Configure a custom converter
    options.SerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

Optionen zur Ausgabeserialisierung können aktionsweise mithilfe von `JsonResult` konfiguriert werden. Beispiel:

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerSettings
    {
        options.Formatting = Formatting.Indented,
    });
}
```

::: moniker-end

### <a name="add-xml-format-support"></a>Hinzufügen von Unterstützung für das XML-Format

::: moniker range="<= aspnetcore-2.2"

Installieren Sie das NuGet-Paket [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/), um die Unterstützung für das XML-Format in ASP.NET Core 2.2 hinzuzufügen.

::: moniker-end

XML-Formatierer, die mithilfe von `System.Xml.Serialization.XmlSerializer` implementiert wurden, können durch einen Aufruf von <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> in `Startup.ConfigureServices` konfiguriert werden:

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet1&highlight=2)]

Alternativ können XML-Formatierer, die mithilfe von `System.Runtime.Serialization.DataContractSerializer` implementiert wurden, durch einen Aufruf von <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlDataContractSerializerFormatters*> in `Startup.ConfigureServices` konfiguriert werden:

```csharp
services.AddMvc()
    .AddXmlDataContractSerializerFormatters();
```

Sobald Sie Unterstützung für das XML-Format hinzugefügt haben, geben die Controllermethoden das entsprechende Format wie in diesem Fiddler-Beispiel veranschaulicht basierend auf dem `Accept`-Header der Anforderung zurück:

![Fiddler-Konsole: Auf der Registerkarte „Raw“ (Rohdaten) wird die Anforderung mit dem Accept-Headerwert „application/xml“ angezeigt. Registerkarte „Raw“ für die Antwort mit dem Inhaltstyp-Headerwert „application/xml“](formatting/_static/xml-response.png)

Auf der Registerkarte „Inspektoren“ wird deutlich, dass die GET-Anforderung unter „Raw“ mit einem festgelegten `Accept: application/xml`-Header erstellt wurde. Im Antwortbereich wird der `Content-Type: application/xml`-Header angezeigt, und das `Author`-Objekt in XML wurde serialisiert.

Verwenden Sie die Registerkarte „Composer“, um für die Anforderung `application/json` im `Accept`-Header anzugeben. Führen Sie die Anforderung aus. Die Antwort wird nun als JSON formatiert:

![Fiddler-Konsole: Auf der Registerkarte „Raw“ (Rohdaten) wird die Anforderung mit dem Accept-Headerwert „application/json“ angezeigt. Registerkarte „Raw“ für die Antwort mit dem Headerwert „application/json“ mit dem Typ „Inhalt“](formatting/_static/json-response-fiddler.png)

In diesem Screenshot wird deutlich, dass die Anforderung einen Header für `Accept: application/json` festlegt und die Antwort denselben Header als `Content-Type` angibt. Das `Author`-Objekt wird im Text der Antwort im JSON-Format dargestellt.

### <a name="forcing-a-particular-format"></a>Erzwingen eines bestimmten Formats

Wenn Sie die Antwortformate für eine bestimmte Aktion einschränken möchten, können Sie den `[Produces]`-Filter anwenden. Der `[Produces]`-Filter gibt die Antwortformate für eine bestimmte Aktion (oder einen Controller) an. Wie die meisten [Filter](xref:mvc/controllers/filters) kann auch dieser Filter auf die Aktion, den Controller oder im globalen Gültigkeitsbereich angewendet werden.

```csharp
[Produces("application/json")]
public class AuthorsController
```

Der `[Produces]`-Filter erzwingt bei allen Aktionen im `AuthorsController`, dass Antworten im JSON-Format zurückgegeben werden. Dies geschieht selbst, wenn für die Anwendung andere Formatierungsprogramme konfiguriert wurden und der Client einen `Accept`-Header bereitgestellt hat, der ein anderes verfügbares Format anfordert. Weitere Informationen, einschließlich der globalen Anwendung von Filtern, finden Sie unter [Filter](xref:mvc/controllers/filters).

### <a name="special-case-formatters"></a>Spezielle Formatierungsprogramme

Einige besondere Fälle werden mithilfe von integrierten Formatierungsprogrammen implementiert. Standardmäßig werden `string`-Rückgabetypen als *text/plain* formatiert (als *text/html*, wenn sie über den `Accept`-Header angefordert werden). Dieses Verhalten kann durch Entfernen des `TextOutputFormatter` entfernt werden. Formatierungsprogramme werden in der `Configure`-Methode in *Startup.cs* entfernt (siehe unten). Aktionen mit einem Modellobjekt-Rückgabetyp geben die Antwort „204 Kein Inhalt“ beim Zurückgeben von `null` zurück. Dieses Verhalten kann durch Entfernen des `HttpNoContentOutputFormatter` entfernt werden. Der folgende Code entfernt `TextOutputFormatter` und `HttpNoContentOutputFormatter`.

```csharp
services.AddMvc(options =>
{
    options.OutputFormatters.RemoveType<TextOutputFormatter>();
    options.OutputFormatters.RemoveType<HttpNoContentOutputFormatter>();
});
```

Ohne `TextOutputFormatter` geben `string`-Rückgabetypen beispielsweise „406 Nicht akzeptabel“ zurück. Beachten Sie, dass ein XML-Formatierer `string`-Rückgabetypen formatiert, wenn der `TextOutputFormatter` entfernt wird.

Ohne `HttpNoContentOutputFormatter` werden NULL-Objekte mithilfe des konfigurierten Formatierungsprogramms formatiert. Beispielsweise gibt das JSON-Formatierungsprogramm einfach eine Antwort mit dem Text `null` zurück, während das XML-Formatierungsprogramm ein leeres XML-Element mit dem festgelegten Attribut `xsi:nil="true"` zurückgibt.

## <a name="response-format-url-mappings"></a>Antwortformat bei URL-Zuordnungen

Clients können ein bestimmtes Format als Teil der URL anfordern, wie z.B. in der Abfragezeichenfolge, als Teil des Pfads oder mithilfe einer formatspezifischen Dateierweiterung, wie etwa XML oder JSON. Die Zuordnung des Anforderungspfads sollte in der Route angegeben werden, die die API verwendet. Beispiel:

```csharp
[FormatFilter]
public class ProductsController
{
    [Route("[controller]/[action]/{id}.{format?}")]
    public Product GetById(int id)
```

Mit dieser Route kann das angeforderte Format als optionale Dateierweiterung angegeben werden. Das `[FormatFilter]`-Attribut überprüft, ob in den `RouteData` ein Formatwert vorhanden ist und ordnet das Antwortformat beim Erstellen der Antwort dem entsprechenden Formatierungsprogramm zu.

|           Route            |             Formatierungsprogramm              |
|----------------------------|------------------------------------|
|   `/products/GetById/5`    |    Standard-Ausgabeformatierungsprogramm    |
| `/products/GetById/5.json` | JSON-Formatierungsprogramm (falls konfiguriert) |
| `/products/GetById/5.xml`  | XML-Formatierungsprogramm (falls konfiguriert)  |

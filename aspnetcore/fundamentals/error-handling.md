---
title: Fehlerbehandlung in ASP.NET Core
author: tdykstra
description: Erfahren Sie mehr über die Fehlerbehandlung in ASP.NET Core-Apps.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 03/05/2019
uid: fundamentals/error-handling
ms.openlocfilehash: d809c70b3fae6b2d21d5ec0871298d905b873d5d
ms.sourcegitcommit: 191d21c1e37b56f0df0187e795d9a56388bbf4c7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/08/2019
ms.locfileid: "57665362"
---
# <a name="handle-errors-in-aspnet-core"></a>Fehlerbehandlung in ASP.NET Core

Von [Tom Dykstra](https://github.com/tdykstra/), [Luke Latham](https://github.com/guardrex) und [Steve Smith](https://ardalis.com/)

Dieser Artikel beschreibt grundsätzliche Vorgehensweisen zur Behandlung von Fehlern in ASP.NET Core-Anwendungen.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/2.x) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="developer-exception-page"></a>Seite mit Ausnahmen für Entwickler

Verwenden Sie die *Seite mit Ausnahmen für Entwickler*, um eine App so zu konfigurieren, dass sie eine Seite anzeigt, die ausführliche Informationen zu Anforderungsausnahmen enthält. Die Seite wird vom [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/)-Paket zur Verfügung gestellt. Dieses ist im [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app) enthalten. Fügen Sie der `Startup.Configure`-Methode eine Zeile hinzu, wenn die App in der [Entwicklungsumgebung](xref:fundamentals/environments) ausgeführt wird:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_UseDeveloperExceptionPage)]

Fügen Sie den Aufruf von <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> vor Middleware ein, wo Sie Ausnahmen abfangen möchten.

> [!WARNING]
> Aktivieren Sie die Seite mit Ausnahmen für Entwickler **nur dann, wenn die App in der Entwicklungsumgebung ausgeführt wird**. Wenn die App in der Produktionsumgebung ausgeführt wird, sollten Sie keine detaillierten Ausnahmeinformationen öffentlich teilen. Weitere Informationen zum Konfigurieren der Umgebungen finden Sie unter <xref:fundamentals/environments>.

Wenn Sie die Seite mit Ausnahmen für Entwickler anzeigen möchten, führen Sie die Beispiel-App mit der auf `Development` festgelegten Umgebung aus, und fügen Sie `?throw=true` zur Basis-URL der App hinzu. Die Seite enthält die folgenden Informationen zu der Ausnahme und der Anforderung:

* Stapelüberwachung
* Abfragezeichenfolgeparameter (sofern vorhanden)
* Cookies (sofern vorhanden)
* Header

## <a name="configure-a-custom-exception-handling-page"></a>Konfigurieren einer benutzerdefinierten Seite zur Ausnahmebehandlung

Wenn die App nicht in der Entwicklungsumgebung ausgeführt wird, rufen Sie die <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>-Erweiterungsmethode auf, um die Middleware zur Ausnahmebehandlung hinzuzufügen. Die Middleware:

* Fängt Ausnahmen ab.
* Protokolliert Ausnahmen.
* Führt die Anforderung für die angegebene Seite oder den Controller in einer anderen Pipeline erneut aus. Die Anforderung wird nicht erneut ausgeführt, wenn die Antwort gestartet wurde.

Im folgenden Beispiel aus der Beispiel-App fügt <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> die Middleware zur Ausnahmebehandlung in Nichtentwicklungsumgebungen hinzu. Die Erweiterungsmethode gibt am `/Error`-Endpunkt für erneut ausgeführte Anforderungen eine Fehlerseite oder einen Controller an, nachdem Ausnahmen abgefangen und protokolliert wurden:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_UseExceptionHandler1)]

Die Razor Pages-App-Vorlage stellt eine Fehlerseite (*.cshtml*) und <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel>-Klasse (`ErrorModel`) im Ordner „Pages“ bereit.

In einer MVC-App ist die folgende Fehlerhandlermethode in der MVC-App-Vorlage enthalten und wird im Home-Controller angezeigt:

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

Die Aktionsmethode zur Fehlerbehandlung wird nicht durch HTTP-Methodenattribute wie `HttpGet` ergänzt. Durch explizite Verben könnte bei einigen Anforderungen verhindert werden, dass diese Methode zum Einsatz kommt. Lassen Sie den anonymen Zugriff auf die Methode zu, damit nicht authentifizierte Benutzer die Fehleransicht empfangen können.

## <a name="access-the-exception"></a>Zugreifen auf die Ausnahme

Verwenden Sie <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature>, um auf die Ausnahme oder den Pfad der ursprünglichen Anforderung in einem Controller oder auf einer Seite zuzugreifen:

* Der Pfad steht über die <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature.Path>-Eigenschaft zur Verfügung.
* Lesen Sie <xref:System.Exception?displayProperty=fullName> aus der geerbten Eigenschaft [IExceptionHandlerFeature.Error](xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature.Error).

```csharp
// using Microsoft.AspNetCore.Diagnostics;

var exceptionHandlerPathFeature = 
    HttpContext.Features.Get<IExceptionHandlerPathFeature>();
var path = exceptionHandlerPathFeature?.Path;
var error = exceptionHandlerPathFeature?.Error;
```

> [!WARNING]
> Stellen Sie **keine** vertraulichen Fehlerinformationen aus <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> oder <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> für Clients bereit. Die Bereitstellung von Fehlern ist ein Sicherheitsrisiko.

## <a name="configure-custom-exception-handling-code"></a>Konfigurieren eines benutzerdefinierten Ausnahmeverarbeitungscodes

Eine Alternative zur Bereitstellung eines Endpunkts für Fehler bei einer [benutzerdefinierten Ausnahmebehandlungsseite](#configure-a-custom-exception-handling-page) besteht darin, einen Lambda-Ausdruck für <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> anzugeben. Die Verwendung eines Lambda-Ausdrucks mit <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> ermöglicht den Zugriff auf den Fehler, bevor die Antwort zurückgegeben wird.

Die Beispiel-App veranschaulicht benutzerdefinierten Ausnahmebehandlungscode in `Startup.Configure`. Lösen Sie eine Ausnahme mit dem Link **Ausnahme auslösen** auf der Indexseite aus. Der folgende Lambda-Ausdruck wird ausgeführt:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_UseExceptionHandler2)]

> [!WARNING]
> Stellen Sie **keine** vertraulichen Fehlerinformationen aus <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> oder <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> für Clients bereit. Die Bereitstellung von Fehlern ist ein Sicherheitsrisiko.

## <a name="configure-status-code-pages"></a>Konfigurieren von Statuscodeseiten

Ihre ASP.NET Core-App stellt für HTTP-Statuscodes wie *404 – Nicht gefunden* standardmäßig keine Statuscodeseite zur Verfügung. Die App gibt einen Statuscode und einen leeren Antworttext zurück. Verwenden Sie zum Bereitstellen von Statuscodeseiten Middleware für Statuscodeseiten.

Die Middleware wird vom [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/)-Paket zur Verfügung gestellt. Dieses ist im [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app) enthalten.

Fügen Sie der `Startup.Configure`-Methode eine Zeile hinzu:

```csharp
app.UseStatusCodePages();
```

Rufen Sie die <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*>-Methode vor Middleware für die Anforderungsverarbeitung auf (z.B. Middleware für statische Dateien und MVC-Middleware).

Standardmäßig fügt die Middleware „Status Code Pages“ Handler im Textformat für gängige Statuscodes wie *404 – nicht gefunden* hinzu:

```
Status Code: 404; Not Found
```

Die Middleware unterstützt verschiedene Erweiterungsmethoden, mit denen Sie das Verhalten anpassen können.

Eine Überladung von <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> verwendet einen Lambdaausdruck, den Sie verwenden können, um benutzerdefinierte Fehlerbehandlungs-Programmlogik zu verarbeiten und manuell die Antwort zu schreiben:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

Eine Überladung von <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> verwendet einen Inhaltstyp und eine Formatzeichenfolge, womit Sie Inhaltstyp und Antworttext anpassen können:

```csharp
app.UseStatusCodePages("text/plain", "Status code page, status code: {0}");
```

### <a name="redirect-and-re-execute-extension-methods"></a>Erweiterungsmethoden zum Umleiten und erneuten Ausführen

<xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*>:

* Sendet den Statuscode *302 Found* (Gefunden) an den Client.
* Der Client wird an den in der URL-Vorlage angegebenen Standort umgeleitet.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*> wird häufig verwendet, wenn die App Folgendes tun sollte:

* Den Client an einen anderen Endpunkt umleiten, in der Regel in Fällen, in denen eine andere App den Fehler verarbeitet. Für Web-Apps gibt die Adressleiste des Browsers des Clients den umgeleiteten Endpunkt wieder.
* Den ursprünglichen Statuscode mit der anfänglichen Umleitungsantwort nicht beibehalten und zurückgeben.

<xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*>:

* Gibt den ursprünglichen Statuscode an den Client zurück.
* Generiert den Antworttext durch die erneute Ausführung der Anforderungspipeline mithilfe eines alternativen Pfads.

```csharp
app.UseStatusCodePagesWithReExecute("/Error/{0}");
```

<xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*> wird häufig verwendet, wenn die App Folgendes tun sollte:

* Die Anforderung ohne Umleitung an einen anderen Endpunkt verarbeiten. Für Web-Apps gibt die Adressleiste des Browsers des Clients den ursprünglich angeforderten Endpunkt wieder.
* Den ursprünglichen Statuscode mit der Antwort beibehalten und zurückgeben.

Vorlagen können einen `{0}`-Platzhalter für den Statuscode enthalten. Der Vorlagenpfad muss mit einem Schrägstrich (`/`) beginnen. Wenn Sie einen Platzhalter verwenden, vergewissern Sie sich, dass der Endpunkt (Seite oder Controller) das Pfadsegment verarbeiten kann. Eine Razor Page für Fehler sollte z.B. den Wert des optionalen Pfadsegments mit der `@page`-Anweisung akzeptieren:

```cshtml
@page "{code?}"
```

Statuscodeseiten können für bestimmte Anforderungen in der Handlermethode einer Razor-Seite oder in einem MVC-Controller deaktiviert werden. Versuchen Sie, das <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> aus der Sammlung [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features) der Anforderung abzurufen und das Feature zu deaktivieren (falls es verfügbar ist), um Statuscodeseiten zu deaktivieren:

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

Um eine <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*>-Überladung zu verwenden, die auf einen Endpunkt in der App verweist, müssen Sie eine MVC-Ansicht oder Razor Page für den Endpunkt erstellen. Eine Razor Pages-App-Vorlage erstellt beispielsweise die folgende Seite und Seitenmodellklasse:

*Error.cshtml*:

::: moniker range=">= aspnetcore-2.2"

```cshtml
@page
@model ErrorModel
@{
    ViewData["Title"] = "Error";
}

<h1 class="text-danger">Error.</h1>
<h2 class="text-danger">An error occurred while processing your request.</h2>

@if (Model.ShowRequestId)
{
    <p>
        <strong>Request ID:</strong> <code>@Model.RequestId</code>
    </p>
}

<h3>Development Mode</h3>
<p>
    Swapping to the <strong>Development</strong> environment displays 
    detailed information about the error that occurred.
</p>
<p>
    <strong>The Development environment shouldn't be enabled for deployed 
    applications.</strong> It can result in displaying sensitive information 
    from exceptions to end users. For local debugging, enable the 
    <strong>Development</strong> environment by setting the 
    <strong>ASPNETCORE_ENVIRONMENT</strong> environment variable to 
    <strong>Development</strong> and restarting the app.
</p>
```

*Error.cshtml.cs*:

```csharp
[ResponseCache(Duration = 0, Location = ResponseCacheLocation.None, NoStore = true)]
public class ErrorModel : PageModel
{
    public string RequestId { get; set; }

    public bool ShowRequestId => !string.IsNullOrEmpty(RequestId);

    public void OnGet()
    {
        RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier;
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```cshtml
@page
@model ErrorModel
@{
    ViewData["Title"] = "Error";
}

<h1 class="text-danger">Error.</h1>
<h2 class="text-danger">An error occurred while processing your request.</h2>

@if (Model.ShowRequestId)
{
    <p>
        <strong>Request ID:</strong> <code>@Model.RequestId</code>
    </p>
}

<h3>Development Mode</h3>
<p>
    Swapping to <strong>Development</strong> environment will display more detailed 
    information about the error that occurred.
</p>
<p>
    <strong>Development environment should not be enabled in deployed applications
    </strong>, as it can result in sensitive information from exceptions being 
    displayed to end users. For local debugging, development environment can be 
    enabled by setting the <strong>ASPNETCORE_ENVIRONMENT</strong> environment 
    variable to <strong>Development</strong>, and restarting the application.
</p>
```

*Error.cshtml.cs*:

```csharp
public class ErrorModel : PageModel
{
    public string RequestId { get; set; }

    public bool ShowRequestId => !string.IsNullOrEmpty(RequestId);

    [ResponseCache(Duration = 0, Location = ResponseCacheLocation.None, 
        NoStore = true)]
    public void OnGet()
    {
        RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier;
    }
}
```

::: moniker-end

## <a name="exception-handling-code"></a>Ausnahmebehandlungscode

Code auf Seiten zur Ausnahmebehandlung kann Ausnahmen auslösen. Es ist häufig empfehlenswert, wenn Produktionsfehlerseiten nur statische Inhalte aufweisen.

Achten Sie nach dem Senden der Header für eine Antwort auch auf Folgendes:

* Die App kann den Statuscode der Antwort nicht ändern.
* Weder Ausnahmeseiten noch Handler können ausgeführt werden. Die Antwort muss abgeschlossen oder die Verbindung unterbrochen werden.

## <a name="server-exception-handling"></a>Sichere Ausnahmebehandlung

Neben der Ausnahmebehandlungslogik in Ihrer App kann die [Serverimplementierung](xref:fundamentals/servers/index) einige Ausnahmebehandlungen durchführen. Wenn der Server eine Ausnahme erfasst, bevor die Antwortheader gesendet werden, sendet der Server die Antwort *500 – Interner Serverfehler* ohne Antworttext. Wenn der Server eine Ausnahme auffängt, nachdem die Antwortheader gesendet wurden, schließt der Server die Verbindung. Anforderungen, die nicht von Ihrer App verarbeitet werden, werden vom Server verarbeitet. Jede Ausnahme, die bei der Anforderungsverarbeitung durch den Server auftritt, wird durch die Ausnahmebehandlung des Servers verarbeitet. Die benutzerdefinierten Fehlerseiten der App, Middleware zur Fehlerbehandlung und Filter haben keine Auswirkungen auf dieses Verhalten.

## <a name="startup-exception-handling"></a>Fehlerbehandlung während des Starts

Nur auf Hostebene können Ausnahmen behandelt werden, die während des Starts einer App auftreten. Sie können mithilfe des [Webhosts](xref:fundamentals/host/web-host) [ konfigurieren, wie der Host während des Starts auf Fehler reagiert](xref:fundamentals/host/web-host#detailed-errors), indem Sie die Schlüssel `captureStartupErrors` und `detailedErrors` verwenden.

Das Hosting kann nur dann eine Fehlerseite für einen erfassten Fehler beim Start anzeigen, wenn der Fehler nach der Bindung der Hostadresse bzw. des Ports auftritt. Wenn bei einer Bindung aus beliebigem Grund ein Fehler auftritt, geschieht Folgendes:

* Die Hostebene protokolliert eine kritische Ausnahme.
* Der DotNet-Prozess stürzt ab.
* Wenn die App auf dem [Kestrel](xref:fundamentals/servers/kestrel)-Server ausgeführt wird, wird keine Fehlerseite angezeigt.

Wenn sie auf [IIS](/iis) oder [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) ausgeführt wird, wird ein *Prozessfehler 502.5* vom [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) zurückgegeben, wenn der Prozess nicht starten kann. Weitere Informationen finden Sie unter <xref:host-and-deploy/iis/troubleshoot>. Weitere Informationen zum Beheben von Startproblemen mit Azure App Service finden Sie unter <xref:host-and-deploy/azure-apps/troubleshoot>.

## <a name="aspnet-core-mvc-error-handling"></a>ASP.NET Core MVC-Fehlerbehandlung

[MVC](xref:mvc/overview)-Apps verfügen über einige zusätzliche Optionen für die Fehlerbehandlung, z.B. das Konfigurieren von Ausnahmefiltern und das Durchführen einer Modellvalidierung.

### <a name="exception-filters"></a>Ausnahmefilter

Ausnahmefilter können in einer MVC-App global oder auf der Grundlage eines Controllers oder einer Aktion konfiguriert werden. Diese Filter verarbeiten jede nicht behandelte Ausnahme, die während der Ausführung eine Controlleraktion oder eines anderen Filters auftritt. Diese Dateien werden andernfalls nicht aufgerufen. Weitere Informationen finden Sie unter <xref:mvc/controllers/filters#exception-filters>.

> [!TIP]
> Ausnahmefilter eignen sich zum Auffangen von Ausnahmen, die in MVC-Aktionen auftreten. Sie sind jedoch nicht so flexibel wie Middleware für die Ausnahmebehandlung. Es wird empfohlen, die Middleware zu verwenden. Verwenden Sie Filter nur dann, wenn Sie für die Fehlerbehandlung auf Grundlage einer ausgewählten MVC-Aktion eine *andere* Strategie anwenden müssen.

### <a name="handle-model-state-errors"></a>Behandeln von Modellstatusfehlern

Die [Modellvalidierung](xref:mvc/models/validation) tritt vor jeder ausgelösten Controlleraktion auf. Die Aktionsmethode ist dafür verantwortlich, [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) zu untersuchen und angemessen zu reagieren.

Einige Apps wählen eine Standardkonvention aus, um [Modellvalidierungsfehler](xref:mvc/models/validation) zu behandeln. Dann kann ein [Filter](xref:mvc/controllers/filters) hilfreich sein, um eine solche Richtlinie zu implementieren. Sie sollten testen, wie sich Ihre Aktionen mit ungültigen Modellstatus verhalten. Weitere Informationen finden Sie unter <xref:mvc/controllers/testing>.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:host-and-deploy/iis/troubleshoot>
* <xref:host-and-deploy/azure-apps/troubleshoot>

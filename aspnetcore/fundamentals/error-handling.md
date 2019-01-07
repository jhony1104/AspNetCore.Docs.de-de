---
title: Fehlerbehandlung in ASP.NET Core
author: tdykstra
description: Erfahren Sie mehr über die Fehlerbehandlung in ASP.NET Core-Apps.
ms.author: tdykstra
ms.custom: mvc
ms.date: 12/18/2018
uid: fundamentals/error-handling
ms.openlocfilehash: f4358cba81d2aa47a26f90a8d5f4e77310bcad00
ms.sourcegitcommit: 816f39e852a8f453e8682081871a31bc66db153a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/19/2018
ms.locfileid: "53637637"
---
# <a name="handle-errors-in-aspnet-core"></a>Fehlerbehandlung in ASP.NET Core

Von [Steve Smith](https://ardalis.com/) und [Tom Dykstra](https://github.com/tdykstra/)

Dieser Artikel beschreibt grundsätzliche Vorgehensweisen zur Behandlung von Fehlern in ASP.NET Core-Anwendungen.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/2.x/ErrorHandlingSample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="the-developer-exception-page"></a>Die Seite mit Ausnahmen für Entwickler

::: moniker range=">= aspnetcore-2.1"

Verwenden Sie die *Seite mit Ausnahmen für Entwickler*, um eine App so zu konfigurieren, dass sie eine Seite anzeigt, die ausführliche Informationen zu Ausnahmen enthält. Die Seite wird vom [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/)-Paket zur Verfügung gestellt. Dieses ist im [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app) enthalten. Fügen Sie der `Startup.Configure`-Methode eine Zeile hinzu:

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Verwenden Sie die *Seite mit Ausnahmen für Entwickler*, um eine App so zu konfigurieren, dass sie eine Seite anzeigt, die ausführliche Informationen zu Ausnahmen enthält. Die Seite wird vom [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/)-Paket zur Verfügung gestellt. Dieses ist im [Microsoft.AspNetCore.All-Metapaket](xref:fundamentals/metapackage) enthalten. Fügen Sie der `Startup.Configure`-Methode eine Zeile hinzu:

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Verwenden Sie die *Seite mit Ausnahmen für Entwickler*, um eine App so zu konfigurieren, dass sie eine Seite anzeigt, die ausführliche Informationen zu Ausnahmen enthält. Die Seite wird durch Hinzufügen eines Paketverweises auf das [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/)-Paket in der Projektdatei verfügbar gemacht. Fügen Sie der `Startup.Configure`-Methode eine Zeile hinzu:

::: moniker-end

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevExceptionPage&highlight=7)]

Fügen Sie den Aufruf von [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) vor Middleware ein, vor der Sie Ausnahmen abfangen möchten, z.B. `app.UseMvc`.

> [!WARNING]
> Aktivieren Sie die Seite mit Ausnahmen für Entwickler **nur dann, wenn die App in der Entwicklungsumgebung ausgeführt wird**. Wenn die App in der Produktionsumgebung ausgeführt wird, sollten Sie keine detaillierten Ausnahmeinformationen öffentlich teilen. [Weitere Informationen zum Konfigurieren von Umgebungen](xref:fundamentals/environments).

Wenn Sie die Seite mit Ausnahmen für Entwickler anzeigen möchten, führen Sie die Beispiel-App mit der auf `Development` festgelegten Umgebung aus, und fügen Sie `?throw=true` zur Basis-URL der App hinzu. Die Seite enthält mehrere Registerkarten mit Informationen zu der Ausnahme und der Anforderung. Die erste Registerkarte enthält eine Stapelüberwachung:

![Stapelüberwachung](error-handling/_static/developer-exception-page.png)

Auf der nächsten Registerkarte werden, sofern vorhanden, Abfragezeichenfolgeparameter angezeigt:

![Abfragezeichenfolgeparameter](error-handling/_static/developer-exception-page-query.png)

Wenn die Anforderung Cookies besitzt, werden diese auf der Registerkarte **Cookies** angezeigt. Header werden auf der letzten Registerkarte angezeigt:

![Header](error-handling/_static/developer-exception-page-headers.png)

## <a name="configure-a-custom-exception-handling-page"></a>Konfigurieren einer benutzerdefinierten Seite zur Ausnahmebehandlung

Konfigurieren Sie eine Seite zur Ausnahmebehandlung, die verwendet werden kann, wenn die App nicht in der `Development`-Umgebung ausgeführt wird:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevExceptionPage&highlight=11)]

In einer Razor Pages-App enthält die Vorlage [dotnet new](/dotnet/core/tools/dotnet-new) von Razor Pages eine Fehlerseite und die Fehlerklasse `PageModel` im Ordner *Pages*.

Die Aktionsmethode zur Fehlerbehandlung wird in einer MVC-App nicht durch HTTP-Methodenattribute wie `HttpGet` ergänzt. Durch explizite Verben könnte bei einigen Anforderungen verhindert werden, dass diese Methode zum Einsatz kommt. Lassen Sie den anonymen Zugriff auf die Methode zu, damit nicht authentifizierte Benutzer die Fehleransicht empfangen können.

Folgende Fehlerhandlermethode wird beispielsweise in der MVC-Vorlage [dotnet new](/dotnet/core/tools/dotnet-new) bereitgestellt und im Home-Controller angezeigt:

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

## <a name="configure-status-code-pages"></a>Konfigurieren von Statuscodeseiten

Ihre App stellt für HTTP-Statuscodes wie *404 – Nicht gefunden* standardmäßig keine ausführliche Statuscodeseite zur Verfügung. Verwenden Sie zum Bereitstellen von Statuscodeseiten Middleware für Statuscodeseiten.

::: moniker range=">= aspnetcore-2.1"

Die Middleware wird vom [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/)-Paket zur Verfügung gestellt. Dieses ist im [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app) enthalten.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Die Middleware wird vom [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/)-Paket zur Verfügung gestellt. Dieses ist im [Microsoft.AspNetCore.All-Metapaket](xref:fundamentals/metapackage) enthalten.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Die Middleware wird durch Hinzufügen eines Paketverweises auf das [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/)-Paket in der Projektdatei verfügbar gemacht.

::: moniker-end

Fügen Sie der `Startup.Configure`-Methode eine Zeile hinzu:

```csharp
app.UseStatusCodePages();
```

<xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> sollte vor Middleware für die Anforderungsverarbeitung in der Pipeline aufgerufen werden (z.B. Middleware für statische Dateien und MVC-Middleware).

Standardmäßig fügt die Middleware „Satus Code Pages“ Handler im Textformat für gängige Statuscodes wie 404 hinzu:

![404-Seite](error-handling/_static/default-404-status-code.png)

Die Middleware unterstützt zahlreiche Erweiterungsmethoden. Eine Methode verwendet einen Lambdaausdruck:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

Eine Überladung von `UseStatusCodePages` verwendet einen Inhaltstyp und eine Formatzeichenfolge:

```csharp
app.UseStatusCodePages("text/plain", "Status code page, status code: {0}");
```
### <a name="redirect-re-execute-extension-methods"></a>Umleitungen von Erweiterungsmethoden für ein erneutes Ausführen

<xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*>:

* Sendet den Statuscode *302 Found* (Gefunden) an den Client.
* Der Client wird an den in der URL-Vorlage angegebenen Standort umgeleitet. 

Die Vorlage kann einen `{0}`-Platzhalter für den Statuscode enthalten. Der Vorlagenpfad muss mit einem Schrägstrich (`/`) beginnen.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*>:

* Gibt den ursprünglichen Statuscode an den Client zurück.
* Gibt an, dass der Antworttext durch die erneute Ausführung der Anforderungspipeline mithilfe eines alternativen Pfads erstellt werden soll. 

Die Vorlage kann einen `{0}`-Platzhalter für den Statuscode enthalten. Der Vorlagenpfad muss mit einem Schrägstrich (`/`) beginnen.

```csharp
app.UseStatusCodePagesWithReExecute("/error/{0}");
```

Statuscodeseiten können für bestimmte Anforderungen in der Handlermethode einer Razor-Seite oder in einem MVC-Controller deaktiviert werden. Versuchen Sie, [IStatusCodePagesFeature](/dotnet/api/microsoft.aspnetcore.diagnostics.istatuscodepagesfeature) aus der Sammlung [HttpContext.Features](/dotnet/api/microsoft.aspnetcore.http.httpcontext.features) der Anforderung abzurufen und das Feature zu deaktivieren (falls es verfügbar ist), um Statuscodeseiten zu deaktivieren:

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

Um eine `UseStatusCodePages*`-Überladung zu verwenden, die auf einen Endpunkt in der App verweist, müssen Sie eine MVC-Ansicht oder Razor Page für den Endpunkt erstellen. Mit der Vorlage [dotnet new](/dotnet/core/tools/dotnet-new) für eine Razor Pages-App werden beispielsweise die folgende Seite und die folgende Seitenmodellklasse erstellt:

*Error.cshtml*:

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

## <a name="exception-handling-code"></a>Ausnahmebehandlungscode

Code auf Seiten zur Ausnahmebehandlung kann Ausnahmen auslösen. Es ist häufig empfehlenswert, wenn Produktionsfehlerseiten nur statische Inhalte aufweisen.

Bedenken Sie zudem, dass Sie den Statuscode einer Antwort nicht ändern können, sobald Sie die Header für diese Antwort gesendet haben, und dass weder Ausnahmeseiten noch Handler ausgeführt werden können. Die Antwort muss abgeschlossen oder die Verbindung unterbrochen werden.

## <a name="server-exception-handling"></a>Sichere Ausnahmebehandlung

Neben der Ausnahmebehandlungslogik in Ihrer App werden auf dem [Server](xref:fundamentals/servers/index), der Ihre App hostet, einige Ausnahmebehandlungen durchgeführt. Wenn der Server eine Ausnahme erfasst, bevor die Header gesendet werden, sendet der Server die Antwort *500 – Interner Serverfehler* ohne Text. Wenn der Server eine Ausnahme auffängt, nachdem die Header gesendet wurden, schließt der Server die Verbindung. Anforderungen, die nicht von Ihrer App verarbeitet werden, werden vom Server verarbeitet. Jede auftretende Ausnahme wird durch die Ausnahmebehandlung des Servers behandelt. Konfigurierte benutzerdefinierte Fehlerseiten, Middleware oder Filter zur Fehlerbehandlung haben keine Auswirkungen auf dieses Verhalten.

## <a name="startup-exception-handling"></a>Fehlerbehandlung während des Starts

Nur auf Hostebene können Ausnahmen behandelt werden, die während des Starts einer App auftreten. Sie können mithilfe des [Webhosts](xref:fundamentals/host/web-host) [konfigurieren, wie der Host während des Starts auf Fehler reagiert](xref:fundamentals/host/web-host#detailed-errors), indem Sie die Schlüssel `captureStartupErrors` und `detailedErrors` verwenden.

Das Hosting kann nur dann eine Fehlerseite für einen erfassten Fehler beim Start anzeigen, wenn der Fehler nach der Bindung der Hostadresse bzw. des Ports auftritt. Wenn eine Bindung aus irgendeinem Grund fehlschlägt, wird auf der Hostebene eine kritische Ausnahme protokolliert, der .NET-Prozess stürzt ab, und es wird keine Fehlerseite angezeigt, wenn die App auf dem [Kestrel](xref:fundamentals/servers/kestrel)-Server ausgeführt wird.

Wenn sie auf [IIS](/iis) oder [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) ausgeführt wird, wird ein *Prozessfehler 502.5* vom [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) zurückgegeben, wenn der Prozess nicht gestartet werden kann. Weitere Informationen zum Beheben von Startproblemen beim Hosten mit den IIS finden Sie unter <xref:host-and-deploy/iis/troubleshoot>. Weitere Informationen zum Beheben von Startproblemen mit Azure App Service finden Sie unter <xref:host-and-deploy/azure-apps/troubleshoot>.

## <a name="aspnet-core-mvc-error-handling"></a>ASP.NET Core MVC-Fehlerbehandlung

[MVC](xref:mvc/overview)-Apps verfügen über einige zusätzliche Optionen für die Fehlerbehandlung, z.B. das Konfigurieren von Ausnahmefiltern und das Durchführen einer Modellvalidierung.

### <a name="exception-filters"></a>Ausnahmefilter

Ausnahmefilter können in einer MVC-App global oder auf der Grundlage eines Controllers oder einer Aktion konfiguriert werden. Diese Filter verarbeiten jede nicht behandelte Ausnahme, die während der Ausführung eine Controlleraktion oder eines anderen Filters auftritt. Diese Dateien werden andernfalls nicht aufgerufen. Weitere Informationen dazu finden Sie unter [Filter](xref:mvc/controllers/filters).

> [!TIP]
> Ausnahmefilter eignen sich zum Auffangen von Ausnahmen, die in MVC-Aktionen auftreten. Sie sind jedoch nicht so flexibel wie Middleware für die Fehlerbehandlung. Ziehen Sie im Allgemeinen die Verwendung der Middleware für allgemeine Fälle vor, und verwenden Sie Filter nur dann, wenn Sie die Fehlerbehandlung *auf einem anderen Weg* angehen müssen, je nachdem, welche MVC-Aktion gewählt wurde.

### <a name="handling-model-state-errors"></a>Behandeln von Modellstatusfehlern

Die [Modellvalidierung](xref:mvc/models/validation) tritt vor jeder ausgelösten Controlleraktion auf. Die Aktionsmethode ist dafür verantwortlich, `ModelState.IsValid` zu untersuchen und angemessen zu reagieren.

Einige Apps wählen eine Standardkonvention aus, um Modellvalidierungsfehler zu behandeln. Dann kann ein [Filter](xref:mvc/controllers/filters) hilfreich sein, um eine solche Richtlinie zu implementieren. Sie sollten testen, wie sich Ihre Aktionen mit ungültigen Modellstatus verhalten. Weitere Informationen finden Sie unter [Testen von Controllerlogik](xref:mvc/controllers/testing).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:host-and-deploy/iis/troubleshoot>
* <xref:host-and-deploy/azure-apps/troubleshoot>

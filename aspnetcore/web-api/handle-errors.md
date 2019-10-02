---
title: Fehlerbehandlung in ASP.NET Core-Web-APIs
author: pranavkm
description: Erfahren Sie, wie Sie Fehler in ASP.NET Core-Web-APIs beheben können.
monikerRange: '>= aspnetcore-2.1'
ms.author: prkrishn
ms.custom: mvc
ms.date: 09/25/2019
uid: web-api/handle-errors
ms.openlocfilehash: 9c5dd2f89e7351f386d1f0633c831952dc58e568
ms.sourcegitcommit: 994da92edb0abf856b1655c18880028b15a28897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71278732"
---
# <a name="handle-errors-in-aspnet-core-web-apis"></a>Fehlerbehandlung in ASP.NET Core-Web-APIs

Dieser Artikel beschreibt die Verarbeitung und Anpassung der Fehlerbehandlung bei ASP.NET Core-Web-APIs.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="developer-exception-page"></a>Seite mit Ausnahmen für Entwickler

Die [Seite mit Ausnahmen für Entwickler](xref:fundamentals/error-handling) ist ein nützliches Tool, um detaillierte Stapelüberwachungen für Serverfehler zu erhalten.

Die Seite mit Ausnahmen für Entwickler zeigt eine Nur-Text-Antwort an, wenn der Client keine HTML-formatierte Ausgabe akzeptiert. Beispiel:

```
> curl https://localhost:5001/weatherforecast
System.ArgumentException: count
   at errorhandling.Controllers.WeatherForecastController.Get(Int32 x) in D:\work\Samples\samples\aspnetcore\mvc\errorhandling\Controllers\WeatherForecastController.cs:line 35
   at lambda_method(Closure , Object , Object[] )
   at Microsoft.Extensions.Internal.ObjectMethodExecutor.Execute(Object target, Object[] parameters)
...
```

> [!WARNING]
> Aktivieren Sie die Seite mit Ausnahmen für Entwickler **nur dann, wenn die App in der Entwicklungsumgebung ausgeführt wird**. Wenn die App in der Produktionsumgebung ausgeführt wird, sollten Sie keine detaillierten Ausnahmeinformationen öffentlich teilen. Weitere Informationen zum Konfigurieren der Umgebungen finden Sie unter <xref:fundamentals/environments>.

## <a name="exception-handler"></a>Ausnahmehandler

In nicht zur Entwicklung dienenden Umgebungen kann [Middleware zur Ausnahmebehandlung](xref:fundamentals/error-handling) verwendet werden, um eine Fehlernutzlast zu erzeugen:

1. Rufen Sie <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> in `Startup.Configure` auf, um die Middleware zu verwenden:

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

1. Konfigurieren Sie eine Controlleraktion, um auf die `/error`-Route zu antworten:

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

Die oben stehende `Error`-Aktion sendet eine mit [RFC7807](https://tools.ietf.org/html/rfc7807) kompatible Nutzlast an den Client.

Middleware zur Ausnahmebehandlung kann auch eine detailliertere Ausgabe der Inhaltsaushandlung in der lokalen Entwicklungsumgebung bieten. Führen Sie die folgenden Schritte aus, um für ein konsistentes Nutzlastformat in Entwicklungs- und Produktionsumgebungen zu sorgen:

1. Registrieren Sie umgebungsspezifische Middleware zur Ausnahmebehandlung in `Startup.Configure`:

    ::: moniker range=">= aspnetcore-3.0"

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseExceptionHandler("/error-local-development");
        }
        else
        {
            app.UseExceptionHandler("/error");
        }
    }
    ```

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseExceptionHandler("/error-local-development");
        }
        else
        {
            app.UseExceptionHandler("/error");
        }
    }
    ```

    ::: moniker-end

    Im oben stehenden Code ist die Middleware mit Folgendem registriert:

    * Route `/error-local-development` in der Entwicklungsumgebung.
    * Route `/error` in einer nicht zur Entwicklung dienenden Umgebung.
    
1. Wenden Sie das Attributrouting auf Controlleraktionen an:

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

## <a name="use-exceptions-to-modify-the-response"></a>Verwenden von Ausnahmen zum Ändern der Antwort

Der Inhalt der Antwort kann außerhalb des Controllers geändert werden. In der ASP.NET 4.x-Web-API war die einzige Möglichkeit hierfür die Verwendung des Typs <xref:System.Web.Http.HttpResponseException>. ASP.NET Core enthält keinen entsprechenden Typ. Die Unterstützung für `HttpResponseException` kann mit folgenden Schritten hinzugefügt werden:

1. Erstellen Sie einen bekannten Ausnahmetyp namens `HttpResponseException`:

    [!code-csharp[](handle-errors/samples/3.x/Exceptions/HttpResponseException.cs?name=snippet_HttpResponseException)]

1. Erstellen Sie einen Aktionsfilter namens `HttpResponseExceptionFilter`:

    [!code-csharp[](handle-errors/samples/3.x/Filters/HttpResponseExceptionFilter.cs?name=snippet_HttpResponseExceptionFilter)]

1. Fügen Sie den Aktionsfilter in `Startup.ConfigureServices` der Filtersammlung hinzu:

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.2"
    
    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.1"

    [!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

## <a name="validation-failure-error-response"></a>Antwort auf Überprüfungsfehler

Bei Web-API-Controllern antwortet MVC mit dem Antworttyp <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>, wenn bei der Modellüberprüfung ein Fehler auftritt. MVC verwendet die Ergebnisse von <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory>, um die Fehlerantwort auf einen Überprüfungsfehler zu erstellen. Das folgende Beispiel verwendet die Factory, um den Standardantworttyp von <xref:Microsoft.AspNetCore.Mvc.SerializableError> in `Startup.ConfigureServices` zu ändern:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=4-13)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=5-14)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=6-15)]

::: moniker-end

## <a name="client-error-response"></a>Fehlerantwort des Clients

Ein *Fehlerergebnis* ist als Ergebnis mit dem HTTP-Statuscode 400 oder höher definiert. Bei Web-API-Controllern transformiert MVC ein Fehlerergebnis in ein Ergebnis mit <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.

::: moniker range=">= aspnetcore-3.0"

Die Fehlerantwort kann auf eine der folgenden Arten konfiguriert werden:

1. [Implementieren der ProblemDetailsFactory](#implement-problemdetailsfactory)
1. [Verwenden von ApiBehaviorOptions.ClientErrorMapping](#use-apibehavioroptionsclienterrormapping)

### <a name="implement-problemdetailsfactory"></a>Implementieren der ProblemDetailsFactory

MVC verwendet die `Microsoft.AspNetCore.Mvc.ProblemDetailsFactory`, um alle Instanzen von <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> und <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> zu generieren. Hierzu gehören Clientfehlerantworten, Antworten auf Überprüfungsfehler sowie die Hilfsmethoden `Microsoft.AspNetCore.Mvc.ControllerBase.Problem` und <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem>.

Um die Antwort mit Problemdetails anzupassen, registrieren Sie eine benutzerdefinierte Implementierung von `ProblemDetailsFactory` in `Startup.ConfigureServices`:

```csharp
public void ConfigureServices(IServiceCollection serviceCollection)
{
    services.AddControllers();
    services.AddTransient<ProblemDetailsFactory, CustomProblemDetailsFactory>();
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Die Fehlerantwort kann so konfiguriert werden wie im Abschnitt [Verwenden von ApiBehaviorOptions.ClientErrorMapping](#use-apibehavioroptionsclienterrormapping) beschrieben.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="use-apibehavioroptionsclienterrormapping"></a>Verwenden von ApiBehaviorOptions.ClientErrorMapping

Verwenden Sie die <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping*>-Eigenschaft zum Konfigurieren des Inhalts der `ProblemDetails`-Antwort. Der folgende Code in `Startup.ConfigureServices` aktualisiert beispielsweise die `type`-Eigenschaft für 404-Antworten:

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=8-9)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=9-10)]

::: moniker-end

---
title: Protokollierung und Diagnose in GrpC unter .net
author: jamesnk
description: Erfahren Sie, wie Sie die Diagnose von ihrer GrpC-app in .net sammeln.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/23/2019
uid: grpc/diagnostics
ms.openlocfilehash: 17607b734e6d777de9516aa14e81c97f87b61023
ms.sourcegitcommit: 80286715afb93c4d13c931b008016d6086c0312b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2020
ms.locfileid: "77074522"
---
# <a name="logging-and-diagnostics-in-grpc-on-net"></a>Protokollierung und Diagnose in GrpC unter .net

Von [James Newton-King](https://twitter.com/jamesnk)

Dieser Artikel enthält Anleitungen zum Sammeln von Diagnoseinformationen aus einer GrpC-APP, um Probleme bei der Problembehandlung zu beheben. Folgende Themen werden behandelt:

* **Protokollierung** : strukturierte Protokolle, die in [.net Core-Protokollierung](xref:fundamentals/logging/index)geschrieben werden <xref:Microsoft.Extensions.Logging.ILogger> wird von App-Frameworks verwendet, um Protokolle zu schreiben, und von Benutzern für die eigene Protokollierung in einer App.
* Ablauf **Verfolgung** : Ereignisse im Zusammenhang mit einem Vorgang, der mit `DiaganosticSource` und `Activity`geschrieben wurde. Ablauf Verfolgungen aus der Diagnose Quelle werden häufig verwendet, um App-Telemetriedaten nach Bibliotheken wie [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) und [opentelemetry](https://github.com/open-telemetry/opentelemetry-dotnet)zu erfassen.
* **Metriken** : Darstellung von Daten Messungen über Zeiträume, z. b. Anforderungen pro Sekunde. Metriken werden mithilfe von `EventCounter` ausgegeben und können mithilfe des Befehlszeilen Tools " [dotnet-Counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) " oder mit [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters)beobachtet werden.

## <a name="logging"></a>Protokollierung

GrpC-Dienste und der GrpC-Client schreiben Protokolle mithilfe der [.net Core-Protokollierung](xref:fundamentals/logging/index). Protokolle sind ein guter Ausgangspunkt, wenn Sie unerwartetes Verhalten in ihren apps Debuggen müssen.

### <a name="grpc-services-logging"></a>GrpC-Dienst Protokollierung

> [!WARNING]
> Server seitige Protokolle können vertrauliche Informationen aus Ihrer APP enthalten. Veröffentlichen Sie **niemals** unformatierte Protokolle von Produktions-apps in öffentlichen Foren wie GitHub.

Da GrpC-Dienste auf ASP.net Core gehostet werden, wird das ASP.net Core Protokollierungs System verwendet. In der Standardkonfiguration protokolliert GrpC nur wenige Informationen, aber dies kann konfiguriert werden. Ausführliche Informationen zum Konfigurieren ASP.net Core Protokollierung finden Sie in der Dokumentation zur [ASP.net Core Protokollierung](xref:fundamentals/logging/index#configuration) .

GrpC fügt Protokolle unter der Kategorie `Grpc` hinzu. Um detaillierte Protokolle von GrpC zu aktivieren, konfigurieren Sie die `Grpc` Präfixe auf `Debug` Ebene in der Datei *appSettings. JSON* , indem Sie die folgenden Elemente zum `LogLevel` unter Abschnitt in `Logging`hinzufügen:

[!code-json[](diagnostics/sample/logging-config.json?highlight=7)]

Sie können dies auch in *Startup.cs* mit `ConfigureLogging`konfigurieren:

[!code-csharp[](diagnostics/sample/logging-config-code.cs?highlight=5)]

Wenn Sie keine JSON-basierte Konfiguration verwenden, legen Sie den folgenden Konfigurations Wert in Ihrem Konfigurationssystem fest:

* `Logging:LogLevel:Grpc` = `Debug`

Überprüfen Sie die Dokumentation Ihres Konfigurations Systems, um zu bestimmen, wie Sie die Werte für die in der Struktur eingested Wenn z. b. Umgebungsvariablen verwendet werden, werden anstelle der `:` zwei `_` Zeichen verwendet (z. b. `Logging__LogLevel__Grpc`).

Es wird empfohlen, die `Debug` Ebene zu verwenden, wenn Sie eine detailliertere Diagnose für Ihre APP sammeln. Die `Trace` Ebene erzeugt eine sehr geringe Diagnose und wird nur selten zum Diagnostizieren von Problemen in Ihrer APP benötigt.

#### <a name="sample-logging-output"></a>Beispiel einer Protokollierungsausgabe

Im folgenden finden Sie ein Beispiel für die Konsolenausgabe auf `Debug` Ebene eines GrpC-Dienstanbieter:

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
dbug: Grpc.AspNetCore.Server.ServerCallHandler[1]
      Reading message.
info: GrpcService.GreeterService[0]
      Hello World
dbug: Grpc.AspNetCore.Server.ServerCallHandler[6]
      Sending message.
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 1.4113ms 200 application/grpc
```

### <a name="access-server-side-logs"></a>Zugreifen auf serverseitige Protokolle

Wie Sie auf serverseitige Protokolle zugreifen, hängt von der Umgebung ab, in der Sie ausgeführt werden.

#### <a name="as-a-console-app"></a>Als Konsolen-App

Wenn Sie in einer Konsolen-app ausgeführt werden, sollte die [Konsolen](xref:fundamentals/logging/index#console-provider) Protokollierung standardmäßig aktiviert werden. GrpC-Protokolle werden in der-Konsole angezeigt.

#### <a name="other-environments"></a>Andere Umgebungen

Wenn die app in einer anderen Umgebung (z. b. Docker, Kubernetes oder Windows-Dienst) bereitgestellt wird, finden Sie unter <xref:fundamentals/logging/index> Weitere Informationen zum Konfigurieren von Protokollierungs Anbietern, die für die Umgebung geeignet sind.

### <a name="grpc-client-logging"></a>GrpC-Client Protokollierung

> [!WARNING]
> Client seitige Protokolle können vertrauliche Informationen aus Ihrer APP enthalten. Veröffentlichen Sie **niemals** unformatierte Protokolle von Produktions-apps in öffentlichen Foren wie GitHub.

Wenn Sie Protokolle vom .NET-Client erhalten möchten, können Sie die `GrpcChannelOptions.LoggerFactory`-Eigenschaft festlegen, wenn der Client Kanal erstellt wird. Wenn Sie einen GrpC-Dienst aus einer ASP.net Core-App aufrufen, kann die Protokollierungs Factory aus der Abhängigkeitsinjektion (di) aufgelöst werden:

[!code-csharp[](diagnostics/sample/net-client-dependency-injection.cs?highlight=7,16)]

Eine alternative Möglichkeit zum Aktivieren der Client Protokollierung ist die Verwendung der [GrpC-clientfactory](xref:grpc/clientfactory) zum Erstellen des Clients. Ein GrpC-Client, der bei der clientfactory registriert und von di aufgelöst wird, verwendet automatisch die konfigurierte Protokollierung der app.

Wenn Ihre APP nicht di verwendet, können Sie mit [loggerfactory. Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*)eine neue `ILoggerFactory`-Instanz erstellen. Um auf diese Methode zuzugreifen, fügen Sie der APP das Paket [Microsoft. Extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) hinzu.

[!code-csharp[](diagnostics/sample/net-client-loggerfactory-create.cs?highlight=1,8)]

#### <a name="grpc-client-log-scopes"></a>GrpC-Client Protokoll Bereiche

Der GrpC-Client fügt Protokolle, die während eines GrpC-Aufrufes aufgerufen wurden, einen [Protokollierungs Bereich](https://docs.microsoft.com/aspnet/core/fundamentals/logginglog-scopes) Der Bereich enthält Metadaten im Zusammenhang mit dem GrpC-Befehl:

* **Grpcmethodtype** : der Typ der GrpC-Methode. Mögliche Werte sind Namen aus `Grpc.Core.MethodType` Enumeration, z. b. unäres
* **Grpcursor** : der relative URI der GrpC-Methode, z. b./Greet. Greeter/sayhellos

#### <a name="sample-logging-output"></a>Beispiel einer Protokollierungsausgabe

Im folgenden finden Sie ein Beispiel für die Konsolenausgabe auf der `Debug` Ebene eines GrpC-Clients:

```console
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Starting gRPC call. Method type: 'Unary', URI: 'https://localhost:5001/Greet.Greeter/SayHello'.
dbug: Grpc.Net.Client.Internal.GrpcCall[6]
      Sending message.
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Reading message.
dbug: Grpc.Net.Client.Internal.GrpcCall[4]
      Finished gRPC call.
```

## <a name="tracing"></a>Ablaufverfolgung

GrpC-Dienste und der GrpC-Client stellen Informationen zu GrpC-aufrufen mithilfe von [diagnosticsource](https://docs.microsoft.com/dotnet/api/system.diagnostics.diagnosticsource) und [Aktivität](https://docs.microsoft.com/dotnet/api/system.diagnostics.activity)bereit.

* .Net-GrpC verwendet eine Aktivität zur Darstellung eines GrpC-Aufrufes.
* Ablauf Verfolgungs Ereignisse werden am Anfang und am Ende der Aktivität "GrpC-Anrufe" in die Diagnose Quelle geschrieben.
* Die Ablauf Verfolgung erfasst keine Informationen darüber, wann Nachrichten über die Lebensdauer von GrpC Streaming-aufrufen gesendet werden.

### <a name="grpc-service-tracing"></a>GrpC-Dienst Ablauf Verfolgung

GrpC-Dienste werden auf ASP.net Core gehostet, die Ereignisse über eingehende HTTP-Anforderungen meldet. GrpC-spezifische Metadaten werden der vorhandenen HTTP-Anforderungs Diagnose hinzugefügt, die ASP.net Core bereitstellt.

* Der Name der Diagnose Quelle ist `Microsoft.AspNetCore`.
* Der Aktivitäts Name ist `Microsoft.AspNetCore.Hosting.HttpRequestIn`.
  * Der Name der vom GrpC-Aufruf aufgerufenen GrpC-Methode wird als Tag mit dem Namen `grpc.method`hinzugefügt.
  * Der Status Code des GrpC-Aufrufens, wenn er fertig ist, wird als Tag mit dem Namen `grpc.status_code`hinzugefügt.

### <a name="grpc-client-tracing"></a>GrpC-Client Ablauf Verfolgung

Der .net-GrpC-Client verwendet `HttpClient`, um GrpC-Aufrufe durchführen. Obwohl `HttpClient` Diagnose Ereignisse schreibt, stellt der .net-GrpC-Client eine benutzerdefinierte Diagnose Quelle, Aktivität und Ereignisse bereit, sodass umfassende Informationen zu einem GrpC-Rückruf gesammelt werden können.

* Der Name der Diagnose Quelle ist `Grpc.Net.Client`.
* Der Aktivitäts Name ist `Grpc.Net.Client.GrpcOut`.
  * Der Name der vom GrpC-Aufruf aufgerufenen GrpC-Methode wird als Tag mit dem Namen `grpc.method`hinzugefügt.
  * Der Status Code des GrpC-Aufrufens, wenn er fertig ist, wird als Tag mit dem Namen `grpc.status_code`hinzugefügt.

### <a name="collecting-tracing"></a>Erfassen der Ablauf Verfolgung

Die einfachste Möglichkeit, `DiagnosticSource` zu verwenden, besteht darin, eine telemetriebibliothek wie [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) oder [opentelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) in der APP zu konfigurieren. Die Bibliothek verarbeitet Informationen zu GrpC-aufrufen neben anderen APP-Telemetriedaten.

Die Ablauf Verfolgung kann in einem verwalteten Dienst wie Application Insights angezeigt werden, oder Sie können ein eigenes verteiltes Ablauf Verfolgungssystem ausführen. Opentelemetry unterstützt das Exportieren von Ablauf Verfolgungs Daten in [Jaeger](https://www.jaegertracing.io/) und [Zipkin](https://zipkin.io/).

`DiagnosticSource` können Ablauf Verfolgungs Ereignisse im Code mithilfe `DiagnosticListener`verarbeiten. Informationen zum lauschen auf eine Diagnose Quelle mit Code finden Sie im [Benutzerhandbuch für diagnosticsource](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).

> [!NOTE]
> Mit telemetriebibliotheken werden zurzeit keine GrpC-spezifischen `Grpc.Net.Client.GrpcOut` Telemetriedaten erfasst. Arbeiten, um telemetriebibliotheken zu verbessern, die diese Ablauf Verfolgung erfassen

## <a name="metrics"></a>metrics

Metriken sind eine Darstellung von Daten Messungen über Zeiträume, z. b. Anforderungen pro Sekunde. Metrikdaten ermöglichen die Überwachung des Zustands einer APP auf hoher Ebene. .Net-GrpC-Metriken werden mithilfe von `EventCounter`ausgegeben.

### <a name="grpc-service-metrics"></a>GrpC-dienstmetriken

GrpC-servermetriken werden auf `Grpc.AspNetCore.Server` Ereignis Quelle gemeldet.

| Name                      | BESCHREIBUNG                   |
| --------------------------|-------------------------------|
| `total-calls`             | Aufrufe gesamt                   |
| `current-calls`           | Aktuelle Aufrufe                 |
| `calls-failed`            | Fehlgeschlagene Aufrufe gesamt            |
| `calls-deadline-exceeded` | Stichtag für Anrufe überschritten. |
| `messages-sent`           | Gesamtzahl der gesendeten Nachrichten“           |
| `messages-received`       | Empfangene Nachrichten insgesamt       |
| `calls-unimplemented`     | Nicht implementierte Aufrufe gesamt     |

ASP.net Core bietet auch eine eigene Metrik für `Microsoft.AspNetCore.Hosting` Ereignis Quelle.

### <a name="grpc-client-metrics"></a>GrpC-Clientmetriken

GrpC-Clientmetriken werden auf `Grpc.Net.Client` Ereignis Quelle gemeldet.

| Name                      | BESCHREIBUNG                   |
| --------------------------|-------------------------------|
| `total-calls`             | Aufrufe gesamt                   |
| `current-calls`           | Aktuelle Aufrufe                 |
| `calls-failed`            | Fehlgeschlagene Aufrufe gesamt            |
| `calls-deadline-exceeded` | Stichtag für Anrufe überschritten. |
| `messages-sent`           | Gesamtzahl der gesendeten Nachrichten“           |
| `messages-received`       | Empfangene Nachrichten insgesamt       |

### <a name="observe-metrics"></a>Metriken beobachten

[dotnet-Counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) ist ein Leistungs Überwachungs Tool für die Ad-hoc-Integritäts Überwachung und Leistungs Untersuchung auf erster Ebene. Überwachen Sie eine .net-App mit `Grpc.AspNetCore.Server` oder `Grpc.Net.Client` als Anbieter Namen.

```console
> dotnet-counters monitor --process-id 1902 Grpc.AspNetCore.Server

Press p to pause, r to resume, q to quit.
    Status: Running
[Grpc.AspNetCore.Server]
    Total Calls                                 300
    Current Calls                               5
    Total Calls Failed                          0
    Total Calls Deadline Exceeded               0
    Total Messages Sent                         295
    Total Messages Received                     300
    Total Calls Unimplemented                   0
```

Eine weitere Möglichkeit, um GrpC-Metriken zu beobachten, besteht darin, Indikator Daten mithilfe des [Microsoft. applicationinsights. eventcountercollector-Pakets](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters)von Application Insights aufzuzeichnen. Nach der Einrichtung sammelt Application Insights allgemeine .NET-Leistungsindikatoren zur Laufzeit. die Leistungsindikatoren von GrpC werden standardmäßig nicht erfasst, App Insights kann jedoch so angepasst werden, dass [zusätzliche Indikatoren enthalten](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected)sind.

Geben Sie die zu sammelnden GrpC-Indikatoren für Application Insight in *Startup.cs*an:

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                // Configure App Insights to collect gRPC counters gRPC services hosted in an ASP.NET Core app
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "current-calls"));
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "total-calls"));
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "calls-failed"));
            }
        );
    }
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>

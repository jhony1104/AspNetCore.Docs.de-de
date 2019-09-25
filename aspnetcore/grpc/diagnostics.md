---
title: Protokollierung und Diagnose in GrpC unter .net
author: jamesnk
description: Erfahren Sie, wie Sie die Diagnose von ihrer GrpC-app in .net sammeln.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/23/2019
uid: grpc/diagnostics
ms.openlocfilehash: 7194e91b40a08c4a7ee619b8f207900af2683aa1
ms.sourcegitcommit: fae6f0e253f9d62d8f39de5884d2ba2b4b2a6050
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71250735"
---
# <a name="logging-and-diagnostics-in-grpc-on-net"></a>Protokollierung und Diagnose in GrpC unter .net

Von [James Newton-King](https://twitter.com/jamesnk)

Dieser Artikel enthält Anleitungen zum Sammeln von Diagnoseinformationen aus ihrer GrpC-APP, um Probleme bei der Problembehandlung zu beheben.

## <a name="grpc-services-logging"></a>GrpC-Dienst Protokollierung

> [!WARNING]
> Server seitige Protokolle können vertrauliche Informationen aus Ihrer APP enthalten. Veröffentlichen Sie **niemals** unformatierte Protokolle von Produktions-apps in öffentlichen Foren wie GitHub.

Da GrpC-Dienste auf ASP.net Core gehostet werden, wird das ASP.net Core Protokollierungs System verwendet. In der Standardkonfiguration protokolliert GrpC nur wenige Informationen, aber dies kann konfiguriert werden. Ausführliche Informationen zum Konfigurieren ASP.net Core Protokollierung finden Sie in der Dokumentation zur [ASP.net Core Protokollierung](xref:fundamentals/logging/index#configuration) .

GrpC fügt Protokolle unter der `Grpc` Kategorie hinzu. Um detaillierte Protokolle von GrpC zu aktivieren, konfigurieren `Grpc` Sie die Präfixe `Debug` auf der Ebene in der Datei *appSettings. JSON* , `LogLevel` indem Sie die folgenden Elemente zum unter Abschnitt `Logging`in hinzufügen:

[!code-json[](diagnostics/sample/logging-config.json?highlight=7)]

Sie können dies auch in *Startup.cs* mit `ConfigureLogging`folgenden Einstellungen konfigurieren:

[!code-csharp[](diagnostics/sample/logging-config-code.cs?highlight=5)]

Wenn Sie keine JSON-basierte Konfiguration verwenden, legen Sie den folgenden Konfigurations Wert in Ihrem Konfigurationssystem fest:

* `Logging:LogLevel:Grpc` = `Debug`

Überprüfen Sie die Dokumentation Ihres Konfigurations Systems, um zu bestimmen, wie Sie die Werte für die in der Struktur eingested Wenn z. b. Umgebungsvariablen verwendet werden `_` , werden anstelle `:` von zwei Zeichen verwendet (z `Logging__LogLevel__Grpc`. b.).

Es wird empfohlen, `Debug` die Ebene zu verwenden, wenn Sie eine detailliertere Diagnose für Ihre APP sammeln. Die `Trace` Ebene erzeugt eine sehr geringe Diagnose und wird nur selten zum Diagnostizieren von Problemen in Ihrer APP benötigt.

### <a name="sample-logging-output"></a>Beispiel einer Protokollierungsausgabe

Im folgenden finden Sie ein Beispiel für die Konsolen `Debug` Ausgabe auf der Ebene eines GrpC-Dienstanbieter:

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

## <a name="access-server-side-logs"></a>Zugreifen auf serverseitige Protokolle

Wie Sie auf serverseitige Protokolle zugreifen, hängt von der Umgebung ab, in der Sie ausgeführt werden.

### <a name="as-a-console-app"></a>Als Konsolen-App

Wenn Sie in einer Konsolen-app ausgeführt werden, sollte die [Konsolen](xref:fundamentals/logging/index#console-provider) Protokollierung standardmäßig aktiviert werden. GrpC-Protokolle werden in der-Konsole angezeigt.

### <a name="other-environments"></a>Andere Umgebungen

Wenn die app in einer anderen Umgebung (z. b. Docker, Kubernetes oder Windows-Dienst) bereitgestellt <xref:fundamentals/logging/index> wird, finden Sie weitere Informationen zum Konfigurieren von Protokollierungs Anbietern, die für die Umgebung geeignet sind.

## <a name="grpc-client-logging"></a>GrpC-Client Protokollierung

> [!WARNING]
> Client seitige Protokolle können vertrauliche Informationen aus Ihrer APP enthalten. Veröffentlichen Sie **niemals** unformatierte Protokolle von Produktions-apps in öffentlichen Foren wie GitHub.

Wenn Sie Protokolle vom .NET-Client erhalten möchten, können Sie `GrpcChannelOptions.LoggerFactory` die-Eigenschaft festlegen, wenn der Client Kanal erstellt wird. Wenn Sie einen GrpC-Dienst aus einer ASP.net Core-App aufrufen, kann die Protokollierungs Factory aus der Abhängigkeitsinjektion (di) aufgelöst werden:

[!code-csharp[](diagnostics/sample/net-client-dependency-injection.cs?highlight=7,16)]

Eine alternative Möglichkeit zum Aktivieren der Client Protokollierung ist die Verwendung der [GrpC-clientfactory](xref:grpc/clientfactory) zum Erstellen des Clients. Ein GrpC-Client, der bei der clientfactory registriert und von di aufgelöst wird, verwendet automatisch die konfigurierte Protokollierung der app.

Wenn Ihre APP nicht di verwendet, können Sie mit `ILoggerFactory` [loggerfactory. Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*)eine neue Instanz erstellen. Um auf diese Methode zuzugreifen, fügen Sie der APP das Paket [Microsoft. Extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) hinzu.

[!code-csharp[](diagnostics/sample/net-client-loggerfactory-create.cs?highlight=1,8)]

### <a name="sample-logging-output"></a>Beispiel einer Protokollierungsausgabe

Im folgenden finden Sie ein Beispiel für die Konsolen `Debug` Ausgabe auf der Ebene eines GrpC-Clients:

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

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>

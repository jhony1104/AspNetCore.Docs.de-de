---
title: Problembehandlung bei GrpC auf .net Core
author: jamesnk
description: Beheben von Fehlern bei der Verwendung von GrpC auf .net Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 08/12/2019
uid: grpc/troubleshoot
ms.openlocfilehash: ad74bfa57d2dde316734d55d86075f463e78ee56
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2019
ms.locfileid: "69029033"
---
# <a name="troubleshoot-grpc-on-net-core"></a>Problembehandlung bei GrpC auf .net Core

Von [James Newton-King](https://twitter.com/jamesnk)

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a>Keine Übereinstimmung zwischen Client-und Dienst-SSL/TLS-Konfiguration

Die GrpC-Vorlage und-Beispiele verwenden [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) , um GrpC-Dienste standardmäßig zu sichern. GrpC-Clients müssen eine sichere Verbindung verwenden, um gesicherte GrpC-Dienste erfolgreich aufzurufen.

Sie können überprüfen, ob der ASP.net Core GrpC-Dienst TLS in den in App-Start geschriebenen Protokollen verwendet. Der Dienst lauscht an einem HTTPS-Endpunkt:

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

Der .net Core-Client muss `https` in der Server Adresse verwenden, um Aufrufe mit einer gesicherten Verbindung durchführen zu können:

```csharp
static async Task Main(string[] args)
{
    var httpClient = new HttpClient();
    // The port number(5001) must match the port of the gRPC server.
    httpClient.BaseAddress = new Uri("https://localhost:5001");
    var client = GrpcClient.Create<Greeter.GreeterClient>(httpClient);
}
```

Alle GrpC-Client Implementierungen unterstützen TLS. für GrpC-Clients aus anderen Sprachen ist in der Regel `SslCredentials`der mit konfigurierte Kanal erforderlich. `SslCredentials`Gibt das Zertifikat an, das vom Client verwendet wird, und es muss anstelle unsicherer Anmelde Informationen verwendet werden. Beispiele für das Konfigurieren der verschiedenen GrpC-Client Implementierungen für die Verwendung von TLS finden Sie unter [GrpC-Authentifizierung](https://www.grpc.io/docs/guides/auth/).

## <a name="call-insecure-grpc-services-with-net-core-client"></a>Anrufen unsicherer GrpC-Dienste mit .net Core-Client

Eine zusätzliche Konfiguration ist erforderlich, um unsichere GrpC-Dienste mit dem .net Core-Client aufzurufen. Der GrpC-Client muss den `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` Switch auf `true` festlegen und `http` in der Server Adresse verwenden:

```csharp
// This switch must be set before creating the HttpClient.
AppContext.SetSwitch("System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

var httpClient = new HttpClient();
// The port number(5000) must match the port of the gRPC server.
httpClient.BaseAddress = new Uri("http://localhost:5000");
var client = GrpcClient.Create<Greeter.GreeterClient>(httpClient);
```

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a>ASP.net Core GrpC-app unter macOS konnte nicht gestartet werden.

Kestrel unterstützt HTTP/2 mit TLS unter macOS und älteren Windows-Versionen wie Windows 7 nicht. Die ASP.net Core GrpC-Vorlage und-Beispiele verwenden standardmäßig TLS. Wenn Sie versuchen, den GrpC-Server zu starten, wird die folgende Fehlermeldung angezeigt:

> Bindung https://localhost:5001 an an der IPv4-Loopback Schnittstelle nicht möglich: HTTP/2 über TLS wird unter macOS aufgrund fehlender alpn-Unterstützung nicht unterstützt.

Um dieses Problem zu umgehen, konfigurieren Sie Kestrel und den GrpC-Client für die Verwendung von http/2 *ohne* TLS. Dies sollte nur während der Entwicklung durchzuführen sein. Die Verwendung von TLS führt dazu, dass GrpC-Nachrichten ohne Verschlüsselung gesendet werden.

Kestrel muss einen http/2-Endpunkt ohne TLS in *Program.cs*konfigurieren:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                // Setup a HTTP/2 endpoint without TLS.
                options.ListenLocalhost(5000, o => o.Protocols = HttpProtocols.Http2);
            });
            webBuilder.UseStartup<Startup>();
        });
```

Der GrpC-Client muss auch für die Verwendung von TLS konfiguriert werden. Weitere Informationen finden Sie unter [callunsichere GrpC-Dienste mit .net Core-Client](#call-insecure-grpc-services-with-net-core-client).

> [!WARNING]
> HTTP/2 ohne TLS sollte nur während der APP-Entwicklung verwendet werden. Produktions-apps sollten immer Transportsicherheit verwenden. Weitere Informationen finden Sie unter [Sicherheitsüberlegungen in GrpC für ASP.net Core](xref:grpc/security#transport-security).

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a>GrpC C# -Assets werden nicht aus  *\*. proto* -Dateien generiert.

die GrpC-Codegenerierung von konkreten Clients und Dienst Basisklassen erfordert, dass protobuf-Dateien und-Tools von einem Projekt referenziert werden. Sie müssen Folgendes einschließen:

* *. proto* -Dateien, die Sie in der `<Protobuf>` Element Gruppe verwenden möchten. Auf [importierte *. proto* -Dateien](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) muss vom Projekt verwiesen werden.
* Paket Verweis auf das GrpC-Toolpaket " [GrpC. Tools](https://www.nuget.org/packages/Grpc.Tools/)".

Weitere Informationen zum Erstellen von GrpC C# -Assets finden <xref:grpc/basics>Sie unter.

Standardmäßig generiert ein `<Protobuf>` Verweis einen konkreten Client und eine Dienst Basisklasse. Das-Attribut des `GrpcServices` Reference-Elements kann verwendet werden C# , um die Medienobjekt Generierung einzuschränken. Gültige `GrpcServices` Optionen sind:

* `Both`(Standardwert, wenn nicht vorhanden)
* `Server`
* `Client`
* `None`

Eine ASP.net Core Web-App, die GrpC-Dienste gehostet, benötigt nur die generierte Dienst Basisklasse:

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

Für eine GrpC-Client-App, die GrpC-Anrufe erstellt, muss nur der konkrete Client generiert werden

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```

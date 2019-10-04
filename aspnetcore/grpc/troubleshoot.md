---
title: Problembehandlung bei GrpC auf .net Core
author: jamesnk
description: Beheben von Fehlern bei der Verwendung von GrpC auf .net Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 09/21/2019
uid: grpc/troubleshoot
ms.openlocfilehash: c31f499b008cdec9d759e804b18965156ca99f30
ms.sourcegitcommit: d8b12cc1716ee329d7bd2300e201b61e15d506ac
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/04/2019
ms.locfileid: "71942894"
---
# <a name="troubleshoot-grpc-on-net-core"></a>Problembehandlung bei GrpC auf .net Core

Von [James Newton-King](https://twitter.com/jamesnk)

In diesem Dokument werden häufig auftretende Probleme bei der Entwicklung von GrpC-apps in .net erläutert.

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
    // The port number(5001) must match the port of the gRPC server.
    var channel = GrpcChannel.ForAddress("https://localhost:5001");
    var client = new Greet.GreeterClient(channel);
}
```

Alle GrpC-Client Implementierungen unterstützen TLS. für GrpC-Clients aus anderen Sprachen ist in der Regel `SslCredentials`der mit konfigurierte Kanal erforderlich. `SslCredentials`Gibt das Zertifikat an, das vom Client verwendet wird, und es muss anstelle unsicherer Anmelde Informationen verwendet werden. Beispiele für das Konfigurieren der verschiedenen GrpC-Client Implementierungen für die Verwendung von TLS finden Sie unter [GrpC-Authentifizierung](https://www.grpc.io/docs/guides/auth/).

## <a name="call-a-grpc-service-with-an-untrustedinvalid-certificate"></a>Einen GrpC-Dienst mit einem nicht vertrauenswürdigen/ungültigen Zertifikat aufzurufen.

Der .net-GrpC-Client erfordert, dass der Dienst über ein vertrauenswürdiges Zertifikat verfügt. Die folgende Fehlermeldung wird zurückgegeben, wenn ein GrpC-Dienst ohne vertrauenswürdiges Zertifikat aufgerufen wird:

> Ausnahmefehler. System .net. http. httprequestexception: Die SSL-Verbindung konnte nicht hergestellt werden. siehe innere Ausnahme.
> ---> System. Security. Authentication. AuthenticationException: Das Remote Zertifikat ist gemäß dem Validierungsverfahren ungültig.

Dieser Fehler wird möglicherweise angezeigt, wenn Sie Ihre APP lokal testen und das ASP.net Core HTTPS-Entwicklungs Zertifikat nicht vertrauenswürdig ist. Anweisungen zum Beheben dieses Problems finden Sie unter [Einstufen des ASP.NET Core-HTTPS-Entwicklungszertifikats als vertrauenswürdig unter Windows und macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).

Wenn Sie einen GrpC-Dienst auf einem anderen Computer aufrufen und dem Zertifikat nicht vertrauen können, kann der GrpC-Client so konfiguriert werden, dass das ungültige Zertifikat ignoriert wird. Der folgende Code verwendet [httpclienthandler. servercertifitorecustomvalidationcallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) , um Aufrufe ohne ein vertrauenswürdiges Zertifikat zuzulassen:

```csharp
var httpClientHandler = new HttpClientHandler();
// Return `true` to allow certificates that are untrusted/invalid
httpClientHandler.ServerCertificateCustomValidationCallback = 
    HttpClientHandler.DangerousAcceptAnyServerCertificateValidator;
var httpClient = new HttpClient(httpClientHandler);

var channel = GrpcChannel.ForAddress("https://localhost:5001",
    new GrpcChannelOptions { HttpClient = httpClient });
var client = new Greet.GreeterClient(channel);
```

> [!WARNING]
> Nicht vertrauenswürdige Zertifikate sollten nur während der APP-Entwicklung verwendet werden. In Produktions-apps sollten immer gültige Zertifikate verwendet werden.

## <a name="call-insecure-grpc-services-with-net-core-client"></a>Anrufen unsicherer GrpC-Dienste mit .net Core-Client

Eine zusätzliche Konfiguration ist erforderlich, um unsichere GrpC-Dienste mit dem .net Core-Client aufzurufen. Der GrpC-Client muss den `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` Switch auf `true` festlegen und `http` in der Server Adresse verwenden:

```csharp
// This switch must be set before creating the GrpcChannel/HttpClient.
AppContext.SetSwitch(
    "System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

// The port number(5000) must match the port of the gRPC server.
var channel = GrpcChannel.ForAddress("http://localhost:5000");
var client = new Greet.GreeterClient(channel);
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
                options.ListenLocalhost(5000, o => o.Protocols = 
                    HttpProtocols.Http2);
            });
            webBuilder.UseStartup<Startup>();
        });
```

Wenn ein http/2-Endpunkt ohne TLS konfiguriert ist, müssen die [listenoptions.-Protokolle](xref:fundamentals/servers/kestrel#listenoptionsprotocols) des Endpunkts `HttpProtocols.Http2`auf festgelegt werden. `HttpProtocols.Http1AndHttp2`kann nicht verwendet werden, da TLS zum Aushandeln von http/2 erforderlich ist. Ohne TLS werden alle Verbindungen mit dem Endpunkt standardmäßig auf HTTP/1.1, und GrpC-Aufrufe schlagen fehl.

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

## <a name="wpf-projects-unable-to-generated-grpc-c-assets-from-proto-files"></a>WPF-Projekte können keine GrpC C# -Assets aus *@no__t -2. proto* -Dateien generieren.

WPF-Projekte haben ein [bekanntes Problem](https://github.com/dotnet/wpf/issues/810) , das verhindert, dass die GrpC-Codegenerierung ordnungsgemäß funktioniert. Alle in einem WPF-Projekt generierten GrpC-Typen, indem auf `Grpc.Tools`-und *. proto* -Dateien verwiesen wird, erstellen bei der Verwendung Kompilierungsfehler:

> Fehler CS0246: Der Typ-oder Namespace Name "mygrpcservices" konnte nicht gefunden werden. (fehlt eine using-Direktive oder ein Assemblyverweis?)

Sie können dieses Problem umgehen, indem Sie folgende Schritte ausführen:

1. Erstellen Sie ein neues .net Core-Klassen Bibliotheksprojekt.
2. Fügen Sie im neuen Projekt Verweise hinzu, um die [ C# Codegenerierung aus *@no__t -3. proto* -Dateienzu aktivieren:
    * Fügen Sie dem [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/)-Paket einen Paketverweis hinzu.
    * Fügen Sie der `<Protobuf>`-Element Gruppe *@no__t -1. proto* -Dateien hinzu.
3. Fügen Sie in der WPF-Anwendung einen Verweis auf das neue Projekt hinzu.

Die WPF-Anwendung kann die von GrpC generierten Typen aus dem neuen Klassen Bibliotheksprojekt verwenden.

[!INCLUDE[](~/includes/gRPCazure.md)]

---
title: gRPC-Dienste mit ASP.NET Core
author: juntaoluo
description: Lernen Sie die grundlegenden Konzepte kennen, wenn Sie GrpC-Dienste mit ASP.net Core schreiben.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/03/2019
uid: grpc/aspnetcore
ms.openlocfilehash: 18a6dd2ddd4f3c3c4466e3b96dd1748fd0972e39
ms.sourcegitcommit: fae6f0e253f9d62d8f39de5884d2ba2b4b2a6050
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71250802"
---
# <a name="grpc-services-with-aspnet-core"></a>gRPC-Dienste mit ASP.NET Core

In diesem Dokument werden die ersten Schritte mit den GrpC-Diensten mithilfe von ASP.net Core erläutert.

## <a name="prerequisites"></a>Erforderliche Komponenten

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a>Erste Schritte mit dem gRPC-Dienst in ASP.NET Core

[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Ausführliche Anweisungen zum Erstellen eines GrpC-Projekts finden [Sie unter Get Started with GrpC Services](xref:tutorials/grpc/grpc-start) .

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio für Mac](#tab/visual-studio-code+visual-studio-mac)

Führen Sie `dotnet new grpc -o GrpcGreeter` über die Befehlszeile aus.

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a>Hinzufügen von GrpC-Diensten zu einer ASP.net Core-App

GrpC erfordert das Paket " [GrpC. aspnetcore](https://www.nuget.org/packages/Grpc.AspNetCore) ".

### <a name="configure-grpc"></a>Konfigurieren von GrpC

In *Startup.cs*:

* GrpC ist mit der `AddGrpc` -Methode aktiviert.
* Jeder GrpC-Dienst wird mithilfe der `MapGrpcService` -Methode zur Routing Pipeline hinzugefügt.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]

ASP.net Core Middlewares und Features nutzen die Routing Pipeline gemeinsam, daher kann eine APP so konfiguriert werden, dass Sie zusätzliche Anforderungs Handler bereitstellt. Die zusätzlichen Anforderungs Handler, z. b. MVC-Controller, arbeiten parallel mit den konfigurierten GrpC-Diensten.

### <a name="configure-kestrel"></a>Konfigurieren von Kestrel

Kestrel-GrpC-Endpunkte:

* Erfordert http/2.
* Muss mit [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246)gesichert werden.

#### <a name="http2"></a>HTTP/2

GrpC erfordert http/2. GrpC für ASP.net Core überprüft, dass [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/2`den Wert hat.

Kestrel [unterstützt HTTP/2](xref:fundamentals/servers/kestrel#http2-support) auf den meisten modernen Betriebssystemen. Kestrel-Endpunkte werden so konfiguriert, dass HTTP/1.1-und http/2-Verbindungen standardmäßig unterstützt werden.

#### <a name="tls"></a>TLS

Kestrel-Endpunkte, die für GrpC verwendet werden, sollten mit TLS gesichert werden. In der Entwicklung wird ein Endpunkt, der mit TLS gesichert ist `https://localhost:5001` , automatisch erstellt, wenn das ASP.net Core-Entwicklungs Zertifikat vorhanden ist. Es ist keine Konfiguration erforderlich. Ein `https` Präfix überprüft, ob der Kestrel-Endpunkt TLS verwendet.

In der Produktionsumgebung muss TLS explizit konfiguriert werden. Im folgenden *appSettings. JSON* -Beispiel wird ein http/2-Endpunkt bereitgestellt, der mit TLS gesichert ist:

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

Alternativ können Kestrel-Endpunkte in *Program.cs*konfiguriert werden:

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

#### <a name="protocol-negotiation"></a>Protokoll Aushandlung

TLS wird für mehr als das Sichern der Kommunikation verwendet. Der Rahmen der TLS [-Protokoll Aushandlung (Application Layer Protocol Aushandlung, alpn)](https://tools.ietf.org/html/rfc7301#section-3) wird verwendet, um das Verbindungsprotokoll zwischen dem Client und dem Server auszuhandeln, wenn ein Endpunkt mehrere Protokolle unterstützt. Diese Aushandlung bestimmt, ob die Verbindung http/1.1 oder http/2 verwendet.

Wenn ein http/2-Endpunkt ohne TLS konfiguriert ist, müssen die [listenoptions.-Protokolle](xref:fundamentals/servers/kestrel#listenoptionsprotocols) des Endpunkts `HttpProtocols.Http2`auf festgelegt werden. Ein Endpunkt mit mehreren Protokollen (z `HttpProtocols.Http1AndHttp2`. b.) kann ohne TLS nicht verwendet werden, da keine Aushandlung vorhanden ist. Alle Verbindungen mit dem ungesicherten Endpunkt werden standardmäßig auf HTTP/1.1 und GrpC-Aufrufe nicht bestanden.

Weitere Informationen zum Aktivieren von http/2 und TLS mit Kestrel finden Sie unter [Kestrel-Endpunkt Konfiguration](xref:fundamentals/servers/kestrel#endpoint-configuration).

> [!NOTE]
> macOS unterstützt ASP.NET Core gRPC mit TLS nicht. Zum erfolgreichen Ausführen von gRPC-Diensten unter macOS ist eine zusätzliche Konfiguration erforderlich. Weitere Informationen finden Sie unter [ASP.NET Core gRPC-App kann unter macOS nicht gestartet werden](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).

## <a name="integration-with-aspnet-core-apis"></a>Integration mit ASP.net Core-APIs

GrpC-Dienste verfügen über Vollzugriff auf die ASP.net Core Features wie z. b. [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (di) und [Protokollierung](xref:fundamentals/logging/index). Die Dienst Implementierung kann z. b. einen Protokollierungs Dienst aus dem di-Container über den-Konstruktor auflösen:

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

Standardmäßig kann die GrpC-Dienst Implementierung andere di-Dienste mit beliebiger Lebensdauer (Singleton, Bereich oder vorübergehend) auflösen.

### <a name="resolve-httpcontext-in-grpc-methods"></a>Auflösen von HttpContext in den GrpC-Methoden

Die GrpC-API ermöglicht den Zugriff auf einige http/2-Nachrichten Daten, wie z. b. die Methode, den Host, den Header und die Nachspann. Der Zugriff erfolgt über `ServerCallContext` das Argument, das an jede GrpC-Methode übermittelt wird:

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

`ServerCallContext`bietet keinen Vollzugriff auf `HttpContext` in allen ASP.NET-APIs. Die `GetHttpContext` Erweiterungsmethode bietet vollen Zugriff auf das `HttpContext` , das die zugrunde liegende http/2-Nachricht in ASP.NET-APIs darstellt:

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>

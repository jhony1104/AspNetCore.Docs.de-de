---
title: gRPC-Dienste mit ASP.NET Core
author: juntaoluo
description: Erfahren Sie, die grundlegenden Konzepten gRPC-Dienste mit ASP.NET Core zu schreiben.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 03/08/2019
uid: grpc/aspnetcore
ms.openlocfilehash: 1f019fac23982a95fa37d43099522f4b3e9d107a
ms.sourcegitcommit: 5d384db2fa9373a93b5d15e985fb34430e49ad7a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66039284"
---
# <a name="grpc-services-with-aspnet-core"></a>gRPC-Dienste mit ASP.NET Core

Diesem Dokument wird erläutert, wie Sie die ersten Schritte mit gRPC-Services mithilfe von ASP.NET Core.

[!INCLUDE[](~/includes/net-core-prereqs-all-3.0.md)]

## <a name="get-started-with-grpc-service-in-aspnet-core"></a>Erste Schritte mit dem gRPC-Dienst in ASP.NET Core

[!INCLUDE[View or download sample code](~/includes/grpc/download.md)]

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Finden Sie unter [erste Schritte mit gRPC Services](xref:tutorials/grpc/grpc-start) ausführliche Anleitungen zum Erstellen eines Projekts gRPC.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio für Mac](#tab/visual-studio-code+visual-studio-mac)

Führen Sie `dotnet new grpc -o GrpcGreeter` über die Befehlszeile aus.

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a>Hinzufügen von gRPC-Services zu einer ASP.NET Core-app

gRPC erfordert die folgenden Pakete:

* [Grpc.AspNetCore.Server](https://www.nuget.org/packages/Grpc.AspNetCore.Server)
* [Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/) Protobuf-Nachricht APIs.
* [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/)

### <a name="configure-grpc"></a>GRPC konfigurieren

gRPC aktiviert ist, mit der `AddGrpc` Methode:

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeter/Startup.cs?name=snippet&highlight=5)]

Jeder gRPC-Dienst wird hinzugefügt, die routing-Pipeline über die `MapGrpcService` Methode:

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeter/Startup.cs?name=snippet&highlight=21)]

Freigeben von ASP.NET Core-Middleware und Funktionen der routing-Pipeline, die aus diesem Grund kann eine app konfiguriert werden, um zusätzliche Anforderungshandler zu verarbeiten. Die zusätzliche Anforderung-Handler, z. B. MVC-Controller, arbeiten parallel mit den konfigurierten gRPC-Diensten.

## <a name="integration-with-aspnet-core-apis"></a>Integration in ASP.NET Core-APIs

gRPC-Dienste verfügen über Vollzugriff auf die ASP.NET Core-Features wie z. B. [Dependency Injection](xref:fundamentals/dependency-injection) (DI) und [Protokollierung](xref:fundamentals/logging/index). Die Implementierung des Diensts kann beispielsweise einen Dienst Protokollierung von DI-Container über den Konstruktor beheben:

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

Standardmäßig kann die dienstimplementierung gRPC andere DI-Dienste mit jedem Lebensdauer (Singleton, Scoped oder vorübergehend) beheben.

### <a name="resolve-httpcontext-in-grpc-methods"></a>Beheben von "HttpContext" gRPC-Methoden

Die gRPC-API ermöglicht den Zugriff auf einige Daten für das HTTP/2-Nachricht, z. B. die Methode, Host, Header und Nachspänne. Der Zugriff erfolgt über die `ServerCallContext` Argument an jede gRPC-Methode übergeben:

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeter/Services/GreeterService.cs?highlight=3-4&name=snippet)]

`ServerCallContext` bietet keine vollen Zugriff auf `HttpContext` in allen ASP.NET-APIs. Die `GetHttpContext` Erweiterungsmethode bietet umfassenden Zugriff auf die `HttpContext` , die die zugrunde liegende HTTP/2-Nachricht in ASP.NET-APIs darstellt:

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeter/Services/GreeterService.cs?name=snippet1)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>

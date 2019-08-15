---
title: gRPC-Dienste mit ASP.NET Core
author: juntaoluo
description: Lernen Sie die grundlegenden Konzepte kennen, wenn Sie GrpC-Dienste mit ASP.net Core schreiben.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 08/07/2019
uid: grpc/aspnetcore
ms.openlocfilehash: 38111c152c581c50767f9cd4e5fa257bd3fd804e
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2019
ms.locfileid: "69022316"
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

GrpC ist mit der `AddGrpc` -Methode aktiviert:

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7)]

Jeder GrpC-Dienst wird mithilfe der `MapGrpcService` -Methode zur Routing Pipeline hinzugefügt:

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=24)]

ASP.net Core Middlewares und Features nutzen die Routing Pipeline gemeinsam, daher kann eine APP so konfiguriert werden, dass Sie zusätzliche Anforderungs Handler bereitstellt. Die zusätzlichen Anforderungs Handler, z. b. MVC-Controller, arbeiten parallel mit den konfigurierten GrpC-Diensten.

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
* <xref:grpc/migration>

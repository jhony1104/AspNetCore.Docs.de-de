---
title: Migrieren von GrpC-Diensten von C-Core zu ASP.net Core
author: juntaoluo
description: Erfahren Sie, wie Sie eine vorhandene, auf C-Core basierende GrpC-App verschieben, die auf ASP.net Core Stapel ausgeführt wird.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/25/2019
uid: grpc/migration
ms.openlocfilehash: 596eca0f510387a18472eb353672980e0a8e0d24
ms.sourcegitcommit: eb4fcdeb2f9e8413117624de42841a4997d1d82d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697999"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a>Migrieren von GrpC-Diensten von C-Core zu ASP.net Core

Von [John Luo](https://github.com/juntaoluo)

Aufgrund der Implementierung des zugrunde liegenden Stapels funktionieren nicht alle Features auf die gleiche Weise wie zwischen [C-Core-basierten GrpC-](https://grpc.io/blog/grpc-stacks) apps und ASP.net Core basierten apps. In diesem Dokument werden die wichtigsten Unterschiede bei der Migration zwischen den beiden Stapeln hervorgehoben.

## <a name="grpc-service-implementation-lifetime"></a>Lebensdauer der GrpC-Dienst Implementierung

Im ASP.net Core Stapel werden GrpC-Dienste standardmäßig mit einer Bereichs bezogenen Gültigkeits [Dauer](xref:fundamentals/dependency-injection#service-lifetimes)erstellt. Im Gegensatz dazu bindet GrpC C-Core standardmäßig an einen Dienst mit einer [Singleton Lebensdauer](xref:fundamentals/dependency-injection#service-lifetimes).

Eine Bereichs bezogene Lebensdauer ermöglicht es der Dienst Implementierung, andere Dienste mit Bereichs bezogener Lebensdauer aufzulösen. Beispielsweise kann eine Gültigkeitsdauer mit Gültigkeitsbereich auch `DbContext` vom di-Container durch Konstruktorinjektion aufgelöst werden. Verwenden der Gültigkeitsdauer:

* Für jede Anforderung wird eine neue Instanz der Dienst Implementierung erstellt.
* Es ist nicht möglich, den Status zwischen Anforderungen über Instanzmember für den Implementierungstyp freizugeben.
* Die Annahme besteht darin, freigegebene Zustände in einem Singleton-Dienst im di-Container zu speichern. Die gespeicherten freigegebenen Zustände werden im Konstruktor der GrpC-Dienst Implementierung aufgelöst.

Weitere Informationen zur Dienst Lebensdauer finden Sie unter <xref:fundamentals/dependency-injection#service-lifetimes>.

### <a name="add-a-singleton-service"></a>Einen Singleton-Dienst hinzufügen

Um den Übergang von einer GrpC-C-Core-Implementierung zu ASP.net Core zu vereinfachen, ist es möglich, die Dienst Lebensdauer der Dienst Implementierung von einem Bereich in einen Singleton zu ändern. Dies umfasst das Hinzufügen einer Instanz der Dienst Implementierung zum di-Container:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

Allerdings kann eine Dienst Implementierung mit einer Singleton-Lebensdauer keine Bereichs bezogenen Dienste mehr durch Konstruktorinjektion auflösen.

## <a name="configure-grpc-services-options"></a>Konfigurieren von GrpC-Dienst Optionen

In C-Core-basierten apps werden Einstellungen wie `grpc.max_receive_message_length` und `grpc.max_send_message_length` beim [Erstellen der Server Instanz](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__)mit `ChannelOption` konfiguriert.

In ASP.net Core bietet GrpC eine Konfiguration über den `GrpcServiceOptions`-Typ. So kann z. b. die maximale Größe eingehender Nachrichten für einen GrpC-Dienst über `AddGrpc` konfiguriert werden. Im folgenden Beispiel wird der Standard `MaxReceiveMessageSize` von 4 MB in 16 MB geändert:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 16 * 1024 * 1024; // 16 MB
    });
}
```

Weitere Informationen zur Konfiguration finden Sie unter <xref:grpc/configuration>.

## <a name="logging"></a>Protokollierung

Auf C-Core basierende apps basieren auf dem `GrpcEnvironment`, um die Protokollierung zu Debuggingzwecken zu [Konfigurieren](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) . Der ASP.net Core Stapel stellt diese Funktion über die [Protokollierungs-API](xref:fundamentals/logging/index)bereit. Beispielsweise kann eine Protokollierung mithilfe von konstruktorinjection zum GrpC-Dienst hinzugefügt werden:

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a>HTTPS

Auf C-Core basierende apps konfigurieren HTTPS über die [Server. Ports-Eigenschaft](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports). Ein ähnliches Konzept wird verwendet, um Server in ASP.net Core zu konfigurieren. Kestrel verwendet beispielsweise die [Endpunkt Konfiguration](xref:fundamentals/servers/kestrel#endpoint-configuration) für diese Funktion.

## <a name="interceptors-and-middleware"></a>Interceptors und Middleware

ASP.net Core [Middleware](xref:fundamentals/middleware/index) bietet im Vergleich zu Interceptors in C-Core-basierten GrpC-apps ähnliche Funktionen. Middleware und Interceptors sind konzeptionell identisch mit beiden, um eine Pipeline zu erstellen, die eine GrpC-Anforderung verarbeitet. Beide ermöglichen die Ausführung von Arbeiten vor oder nach der nächsten Komponente in der Pipeline. ASP.net Core Middleware arbeitet jedoch mit den zugrunde liegenden http/2-Nachrichten, während Interceptors mithilfe von [servercallcontext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html)auf der GrpC-Abstraktions Ebene arbeiten.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>

---
title: Migrieren von gRPC-Dienste von C-Core in ASP.NET Core
author: juntaoluo
description: Informationen Sie zum Verschieben einer vorhandenen C-Core-Basis gRPC-app auf ASP.NET Core-Stapel ausgeführt.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 03/31/2019
uid: grpc/migration
ms.openlocfilehash: 4d489b5aecf2e15fbbe3ac472b991a4365cd47c1
ms.sourcegitcommit: 57a974556acd09363a58f38c26f74dc21e0d4339
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59672618"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a>Migrieren von gRPC-Dienste von C-Core in ASP.NET Core

Von [John Luo](https://github.com/juntaoluo)

Aufgrund der Implementierung der zugrunde liegenden Stapel nicht alle Funktionen, die auf die gleiche Weise zwischen funktionieren [C-Core-basierte gRPC](https://grpc.io/blog/grpc-stacks) -apps und ASP.NET Core-basierten apps. In diesem Dokument werden die wichtigsten Unterschiede für die Migration zwischen beiden Stapeln hervorgehoben.

## <a name="grpc-service-implementation-lifetime"></a>gRPC Implementierung Lebensdauer

Im ASP.NET Core-Stapel gRPC-Services, in der Standardeinstellung werden mit erstellt eine [bereichslebensdauer](xref:fundamentals/dependency-injection#service-lifetimes). Im Gegensatz dazu gRPC C-Core standardmäßig bindet an einen Dienst mit einem [Singleton-Lebensdauer](xref:fundamentals/dependency-injection#service-lifetimes).

Eine Bereichsbezogene Lebensdauer ermöglicht die dienstimplementierung zum Auflösen von anderen Diensten mit Bereichsbezogene Lebensdauer. Eine Bereichsbezogene Lebensdauer kann z. B. auch beheben `DBContext` vom DI-Container über Konstruktorinjektion. Verwenden die bereichsbezogenen Lebensdauer:

* Eine neue Instanz der dienstimplementierung, die für jede Anforderung erstellt wird.
* Es ist nicht möglich, zwischen Anforderungen über Instanzmember auf den Implementierungstyp möglich.
* Zum Speichern von Freigabestatus in ein Singleton-Dienst in der DI-Container wird erwartet. Die gespeicherten freigegebenen Status werden im Konstruktor der dienstimplementierung gRPC aufgelöst.

Weitere Informationen zu dienstlebensdauer, finden Sie unter <xref:fundamentals/dependency-injection#service-lifetimes>.

### <a name="add-a-singleton-service"></a>Fügen Sie einen Singleton-Dienst

Um den Übergang von einer gRPC-C-Core-Implementierung zu ASP.NET Core zu erleichtern, ist es möglich, ändern die Lebensdauer des Diensts der dienstimplementierung von Singleton zugeordnet. Dies umfasst die DI-Container eine Instanz der dienstimplementierung hinzuzufügen:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

Eine dienstimplementierung mit einer Singleton-Lebensdauer ist jedoch nicht mehr Bereichsbezogene Dienste über Konstruktorinjektion auflösen.

## <a name="configure-grpc-services-options"></a>Konfigurieren Sie Optionen für gRPC-Dienste

In C-Core-basierte apps, Einstellungen wie z. B. `grpc.max_receive_message_length` und `grpc.max_send_message_length` konfiguriert sind, mit `ChannelOption` beim [erstellen die Server-Instanz](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).

In ASP.NET Core `GrpcServiceOptions` bietet eine Möglichkeit, diese Einstellungen zu konfigurieren. Die Einstellungen können global für alle gRPC-Services oder eine einzelne Dienstimplementierungstyp angewendet werden. Globale Einstellungen konfiguriert, überschreiben Optionen, die für einzelne Implementierung Diensttypen angegeben.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services
        .AddGrpc(globalOptions =>
        {
            // Global settings
            globalOptions.SendMaxMessageSize = 4096
            globalOptions.ReceiveMaxMessageSize = 4096
        })
        .AddServiceOptions<GreeterService>(greeterOptions =>
        {
            // GreeterService settings. These will override global settings
            globalOptions.SendMaxMessageSize = 2048
            globalOptions.ReceiveMaxMessageSize = 2048
        })
}
```

## <a name="logging"></a>Protokollierung

C-Core-basierte apps verwenden die `GrpcEnvironment` zu [konfigurieren Sie die Protokollierung](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) zu Debugzwecken. Die ASP.NET Core-Stapel bietet folgende Funktionen über die [Protokollierungs-API](xref:fundamentals/logging/index). Beispielsweise kann der gRPC-Dienst per Konstruktorinjektion eine Protokollierung hinzugefügt werden:

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a>HTTPS

C-Core-basierte apps Konfigurieren von HTTPS über den [Server.Ports Eigenschaft](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports). Ein ähnliches Konzept dient zum Konfigurieren von Servern in ASP.NET Core. Kestrel verwendet z. B. [Endpunktkonfiguration](xref:fundamentals/servers/kestrel#endpoint-configuration) für diese Funktionalität.

## <a name="interceptors-and-middleware"></a>Dienste und Middleware

ASP.NET Core [Middleware](xref:fundamentals/middleware/index) bietet ähnliche Funktionen, die im Vergleich zu Interceptors in C-Core-basierte gRPC-apps. Middleware und Dienste sind konzeptionell identisch, da beide verwendet werden, um eine Pipeline zu erstellen, die eine gRPC-Anforderung verarbeitet. Beide können vor oder nach dem die nächste Komponente in der Pipeline ausgeführt werden müssen. ASP.NET Core-Middleware wirkt sich jedoch auf die zugrunde liegenden HTTP/2-Nachrichten, während Interceptors für die gRPC-Ebene der Abstraktion Verwendung verwendet werden die [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>

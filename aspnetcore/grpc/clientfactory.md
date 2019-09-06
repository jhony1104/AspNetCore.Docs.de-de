---
title: Integration von GrpC-Client-Factory in .net Core
author: jamesnk
description: Erfahren Sie, wie Sie mithilfe der clientfactory GrpC-Clients erstellen.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/21/2019
uid: grpc/clientfactory
ms.openlocfilehash: a52fd397a7ed3e327938b0847af7f4e6a4a79400
ms.sourcegitcommit: 8b36f75b8931ae3f656e2a8e63572080adc78513
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/05/2019
ms.locfileid: "70310607"
---
# <a name="grpc-client-factory-integration-in-net-core"></a>Integration von GrpC-Client-Factory in .net Core

die GrpC- `HttpClientFactory` Integration mit bietet eine zentralisierte Möglichkeit zum Erstellen von GrpC-Clients. Sie kann als Alternative zum [Konfigurieren eigenständiger GrpC-Client Instanzen](xref:grpc/client)verwendet werden. Die Factory-Integration steht im nuget-Paket von [GrpC .net. clientfactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) zur Verfügung.

Die Factory bietet die folgenden Vorteile:

* Stellt einen zentralen Ort zum Konfigurieren logischer GrpC-Client Instanzen bereit.
* Verwaltet die Lebensdauer des zugrunde liegenden`HttpClientMessageHandler`
* Automatische Weitergabe von Stichtag und Abbruch in einem ASP.net Core GrpC-Dienst

## <a name="register-grpc-clients"></a>Registrieren von GrpC-Clients

Zum Registrieren eines GrpC-Clients kann die `AddGrpcClient` generische Erweiterungsmethode in verwendet werden `Startup.ConfigureServices`, wobei die von GrpC typisierte Client Klasse und Dienst Adresse angegeben werden:

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("http://localhost:5001");
});
```

Der GrpC-Clienttyp wird als vorübergehender Wert mit Abhängigkeitsinjektion (di) registriert. Der Client kann nun direkt in von di erstellten Typen eingefügt und genutzt werden. ASP.net Core MVC-Controllern sind signalr Hubs und GrpC-Dienste Orte, an denen die GrpC-Clients automatisch eingefügt werden können:

```csharp
public class AggregatorService : Aggregator.AggregatorBase
{
    private readonly Greeter.GreeterClient _client;

    public AggregatorService(Greeter.GreeterClient client)
    {
        _client = client;
    }

    public override async Task SayHellos(HelloRequest request,
        IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
    {
        // Forward the call on to the greeter service
        using (var call = _client.SayHellos(request))
        {
            await foreach (var response in call.ResponseStream.ReadAllAsync())
            {
                await responseStream.WriteAsync(response);
            }
        }
    }
}
```

## <a name="configure-httpclient"></a>HttpClient konfigurieren

`HttpClientFactory`erstellt den `HttpClient` , der vom GrpC-Client verwendet wird. Standard `HttpClientFactory` Methoden können verwendet werden, um eine ausgehende Anforderungs Middleware hinzuzufügen oder `HttpClientHandler` um die `HttpClient`zugrunde liegende zu konfigurieren:

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.BaseAddress = new Uri("http://localhost:5001");
    })
    .ConfigurePrimaryHttpMessageHandler(() =>
    {
        var handler = new HttpClientHandler();
        handler.ClientCertificates.Add(LoadCertificate());
        return handler;
    });
```

Weitere Informationen finden Sie unter [make http Requests using ihttpclientfactory](xref:fundamentals/http-requests).

## <a name="configure-channel-and-interceptors"></a>Konfigurieren von Kanälen und Interceptors

GrpC-spezifische Methoden stehen für Folgendes zur Verfügung:

* Konfigurieren Sie den zugrunde liegenden Channel eines GrpC-Clients.
* Fügen `Interceptor` Sie Instanzen hinzu, die vom Client beim Ausführen von GrpC-Aufrufen verwendet werden.

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("http://localhost:5001");
    })
    .AddInterceptor(() => new LoggingInterceptor())
    .ConfigureChannel(o =>
    {
        o.Credentials = new CustomCredentials();
    });
```

## <a name="deadline-and-cancellation-propagation"></a>Propagierung von Stichtag und Abbruch

GrpC-Clients, die von der Factory in einem GrpC-Dienst erstellt `EnableCallContextPropagation()` wurden, können mit konfiguriert werden, um den Stichtag und das Abbruch Token automatisch an untergeordnete Anrufe weiterzuleiten Die `EnableCallContextPropagation()` Erweiterungsmethode ist im nuget-Paket " [GrpC. aspnetcore. Server. clientfactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) " verfügbar.

Die Aufruf Kontext Weitergabe funktioniert, indem der Stichtag und das Abbruch Token aus dem aktuellen GrpC-Anforderungs Kontext gelesen und automatisch an ausgehende Aufrufe durch den GrpC-Client weitergegeben werden. Die weitergabekontexweitergabe ist eine hervorragende Möglichkeit, um sicherzustellen, dass komplexe, in einem Szenario eingefügte GrpC-Szenarios immer

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("http://localhost:5001");
    })
    .EnableCallContextPropagation();
```

Weitere Informationen zu Stichtage und RPC-Abbruch finden Sie unter [RPC-Lebenszyklus](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:grpc/client>
* <xref:fundamentals/http-requests>

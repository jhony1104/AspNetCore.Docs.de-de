---
title: 'title: author: description: monikerRange: ms.author: ms.date: no-loc:'
author: jamesnk
description: "'Blazor'"
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 05/26/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/clientfactory
ms.openlocfilehash: ecf2e4ce4375f9e5292f317a15f3b8016ac7daec
ms.sourcegitcommit: 58722eb309767e462bdbf3082bd38737a4ef168f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84106220"
---
# <a name="grpc-client-factory-integration-in-net-core"></a>'Identity'

'Let's Encrypt' 'Razor' 'SignalR' uid:

gRPC-Integration von Clientfactory in .NET Core

* Die gRPC-Integration mit `HttpClientFactory` bietet eine zentralisierte Möglichkeit, gRPC-Clients zu erstellen.
* Diese Möglichkeit kann als Alternative zum [Konfigurieren eigenständiger gRPC-Clientinstanzen](xref:grpc/client) verwendet werden.
* Die Factoryintegration ist im NuGet-Paket [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) verfügbar.

## <a name="register-grpc-clients"></a>Die Factory bietet die folgenden Vorteile:

Ein zentraler Ort für das Konfigurieren logischer gRPC-Clientinstanzen

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

Optionen für die Verwaltung der Lebensdauer des zugrunde liegenden `HttpClientMessageHandler` Automatische Weitergabe von Stichtagen und Absagen in einem ASP.NET Core-gRPC-Dienst Registrierung von gRPC-Clients

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

## <a name="configure-httpclient"></a>Zum Registrieren eines gRPC-Clients kann die generische Erweiterungsmethode `AddGrpcClient` innerhalb von `Startup.ConfigureServices` verwendet werden, die die typisierte Klasse des gRPC-Clients und die Dienstadresse angibt:

Der gRPC-Clienttyp wird als vorübergehender Typ mit einer Abhängigkeitsinjektion (Dependency Injection, DI) registriert. Der Client kann nun injiziert und direkt in von der DI erstellten Typen genutzt werden.

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .ConfigurePrimaryHttpMessageHandler(() =>
    {
        var handler = new HttpClientHandler();
        handler.ClientCertificates.Add(LoadCertificate());
        return handler;
    });
```

Bei ASP.NET Core-MVC-Controllern, SignalR-Hubs und gRPC-Diensten handelt es sich um Orte, an denen gRPC-Clients direkt injiziert werden können:

## <a name="configure-channel-and-interceptors"></a>Konfigurieren von HttpClient

`HttpClientFactory` erstellt die `HttpClient`-Klasse, die vom gRPC-Client verwendet wird.

* `HttpClientFactory`-Standardmethoden können verwendet werden ,um Middleware für ausgehende Anforderungen hinzuzufügen oder den zugrunde liegenden `HttpClientHandler` der `HttpClient`-Klasse zu konfigurieren:
* Weitere Informationen erhalten Sie unter [Stellen von HTTP-Anforderungen mithilfe von IHttpClientFactory in ASP.NET Core](xref:fundamentals/http-requests).

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .AddInterceptor(() => new LoggingInterceptor())
    .ConfigureChannel(o =>
    {
        o.Credentials = new CustomCredentials();
    });
```

## <a name="deadline-and-cancellation-propagation"></a>Konfigurieren von Kanal und Interceptoren

Für Folgendes sind gRPC-spezifische Methoden verfügbar: Konfigurieren des einem gRPC-Client zugrunde liegenden Kanals

Hinzufügen von `Interceptor`-Instanzen, die der Client für gRPC-Aufrufe verwendet Weitergabe von Stichtagen und Absagen

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

gRPC-Clients, die von der Factory in einem gRPC-Dienst erstellt wurden, können mit `EnableCallContextPropagation()` so konfiguriert werden, dass sie die Tokens für Stichtage und Absagen automatisch an untergeordnete Aufrufe weitergeben. Die Erweiterungsmethode `EnableCallContextPropagation()` ist im NuGet-Paket [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) verfügbar. Die Weitergabe von Aufrufkontext funktioniert, in dem die Tokens für Stichtage und Absagen aus dem aktuellen gRPC-Anforderungskontext gelesen und automatisch an ausgehende Aufrufe weitergegeben werden, die vom gRPC-Client ausgeführt werden.

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation(o => o.SuppressContextNotFoundErrors = true);
```

Die Weitergabe von Aufrufkontext ist eine sehr gute Möglichkeit, sicherzustellen, dass komplexe und verschachtelte gRPC-Szenarios immer den Stichtag und die Absage weitergeben.

## <a name="additional-resources"></a>`EnableCallContextPropagation` löst standardmäßig einen Fehler aus, wenn der Client außerhalb von gRPC-Aufrufen verwendet wird.

* <xref:grpc/client>
* <xref:fundamentals/http-requests>

---
title: Host ASP.net Core SignalR in Hintergrund Diensten
author: bradygaster
description: Erfahren Sie, wie Sie nach SignalR richten von .net Core-backgroundservice-Klassen an Clients senden.
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/background-services
ms.openlocfilehash: d5f1668d601f520939956985e46c62f3a5bdfcfa
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777292"
---
# <a name="host-aspnet-core-signalr-in-background-services"></a>Host ASP.net Core SignalR in Hintergrund Diensten

Von [Brady Gastern](https://twitter.com/bradygaster)

Dieser Artikel enthält Anleitungen für:

* Hosten SignalR von Hubs mit einem Hintergrund Arbeitsprozess, der mit ASP.net Core gehostet wird.
* Senden von Nachrichten an verbundene Clients aus einem .net Core- [backgroundservice](xref:Microsoft.Extensions.Hosting.BackgroundService).

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(Vorgehensweise zum herunterladen)](xref:index#how-to-download-a-sample)

## <a name="enable-signalr-in-startup"></a>Beim SignalR Start aktivieren

::: moniker range=">= aspnetcore-3.0"

Das Hosting SignalR von ASP.net Core Hubs im Kontext eines hintergrundworkerprozesses ist identisch mit dem Hosting eines Hubs in einer ASP.net Core Web-App. In der `Startup.ConfigureServices` -Methode fügt `services.AddSignalR` der Aufruf von die erforderlichen Dienste der Ebene der ASP.net Core Abhängigkeitsinjektion (di SignalR) hinzu, um zu unterstützen. In `Startup.Configure`wird die `MapHub` -Methode im `UseEndpoints` Rückruf aufgerufen, um die Hub-Endpunkte in der ASP.net Core Anforderungs Pipeline zu verbinden.

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSignalR();
        services.AddHostedService<Worker>();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.UseRouting();
        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHub<ClockHub>("/hubs/clock");
        });
    }
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Das Hosting SignalR von ASP.net Core Hubs im Kontext eines hintergrundworkerprozesses ist identisch mit dem Hosting eines Hubs in einer ASP.net Core Web-App. In der `Startup.ConfigureServices` -Methode fügt `services.AddSignalR` der Aufruf von die erforderlichen Dienste der Ebene der ASP.net Core Abhängigkeitsinjektion (di SignalR) hinzu, um zu unterstützen. In `Startup.Configure`wird die `UseSignalR` -Methode aufgerufen, um die Hub-Endpunkte in der ASP.net Core Anforderungs Pipeline zu verbinden.

[!code-csharp[Startup](background-service/sample/Server/Startup.cs?name=Startup)]

::: moniker-end

Im vorherigen Beispiel implementiert die `ClockHub` -Klasse die `Hub<T>` -Klasse, um einen stark typisierten Hub zu erstellen. Der `ClockHub` wurde in der `Startup` -Klasse so konfiguriert, dass er auf Anforderungen am `/hubs/clock`Endpunkt antwortet.

Weitere Informationen zu stark typisierten Hubs finden Sie unter [Verwenden von SignalR Hubs in für ASP.net Core](xref:signalr/hubs#strongly-typed-hubs).

> [!NOTE]
> Diese Funktion ist nicht auf die [Hub\<t->](xref:Microsoft.AspNetCore.SignalR.Hub`1) Klasse beschränkt. Jede Klasse, die von [Hub](xref:Microsoft.AspNetCore.SignalR.Hub)erbt, z. [b. dynamichub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), funktioniert ebenfalls.

[!code-csharp[Startup](background-service/sample/Server/ClockHub.cs?name=ClockHub)]

Die vom stark typisierten `ClockHub` verwendete Schnittstelle ist `IClock` die-Schnittstelle.

[!code-csharp[Startup](background-service/sample/HubServiceInterfaces/IClock.cs?name=IClock)]

## <a name="call-a-signalr-hub-from-a-background-service"></a>Einen SignalR Hub über einen Hintergrunddienst aufzurufen

Während des Starts wird `Worker` die Klasse, `BackgroundService`a, mithilfe `AddHostedService`von aktiviert.

```csharp
services.AddHostedService<Worker>();
```

Da SignalR auch in der `Startup` Phase aktiviert ist, in der jeder Hub an einen einzelnen Endpunkt in der HTTP-Anforderungs Pipeline ASP.net Core angefügt ist, wird jeder Hub durch ein `IHubContext<T>` auf dem Server dargestellt. Mithilfe der di-Features von ASP.net Core können andere Klassen, die von der hostingschicht `BackgroundService` instanziiert werden (z. b Razor . Klassen, MVC-Controller Klassen oder Seiten Modelle), Verweise auf `IHubContext<ClockHub, IClock>` serverseitige Hubs erhalten, indem Sie Instanzen von während der Konstruktion akzeptieren.

[!code-csharp[Startup](background-service/sample/Server/Worker.cs?name=Worker)]

Wenn die `ExecuteAsync` -Methode im Hintergrunddienst iterativ aufgerufen wird, werden das aktuelle Datum und die aktuelle Uhrzeit des Servers über das `ClockHub`an die verbundenen Clients gesendet.

## <a name="react-to-signalr-events-with-background-services"></a>Reagieren auf SignalR Ereignisse mit Hintergrund Diensten

Wie eine Einzelseiten SignalR -APP <xref:signalr/dotnet-client>, die den JavaScript-Client für oder eine .net-Desktop-App mithilfe von verwenden `BackgroundService` kann `IHostedService` , kann eine-oder-Implementierung auch SignalR verwendet werden, um eine Verbindung mit Hubs herzustellen und auf Ereignisse zu reagieren.

Die `ClockHubClient` -Klasse implementiert die `IClock` -Schnittstelle `IHostedService` und die-Schnittstelle. Auf diese Weise kann Sie während `Startup` der kontinuierlichen Durchführung von aktiviert werden und auf Hub-Ereignisse vom Server reagieren.

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

Während der `ClockHubClient` Initialisierung wird von eine Instanz von erstellt `HubConnection` und die `IClock.ShowTime` -Methode als Handler für das- `ShowTime` Ereignis des Hubs aktiviert.

[!code-csharp[The ClockHubClient constructor](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

In der `IHostedService.StartAsync` -Implementierung wird `HubConnection` der asynchron gestartet.

[!code-csharp[StartAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

Während der `IHostedService.StopAsync` -Methode wird `HubConnection` der asynchron freigegeben.

[!code-csharp[StopAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Erste Schritte](xref:tutorials/signalr)
* [Hubs](xref:signalr/hubs)
* [Veröffentlichen in Azure](xref:signalr/publish-to-azure-web-app)
* [Stark typisierte Hubs](xref:signalr/hubs#strongly-typed-hubs)

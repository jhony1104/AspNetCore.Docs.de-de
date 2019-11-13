---
title: Host ASP.net Core SignalR in Hintergrund Diensten
author: bradygaster
description: Erfahren Sie, wie Sie Nachrichten von .net Core-backgroundservice-Klassen an SignalR Clients senden.
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/background-services
ms.openlocfilehash: 000732115153eeafed3948c2a07acf77ffc34218
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73964043"
---
# <a name="host-aspnet-core-opno-locsignalr-in-background-services"></a>Host ASP.net Core SignalR in Hintergrund Diensten

Von [Brady Gastern](https://twitter.com/bradygaster)

Dieser Artikel enthält Anleitungen für:

* Hosten von SignalR Hubs mit einem Hintergrund Arbeitsprozess, der mit ASP.net Core gehostet wird.
* Senden von Nachrichten an verbundene Clients aus einem .net Core- [backgroundservice](xref:Microsoft.Extensions.Hosting.BackgroundService).

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(Vorgehensweise zum herunterladen)](xref:index#how-to-download-a-sample)

## <a name="wire-up-opno-locsignalr-during-startup"></a>SignalR während des Starts verknüpfen

::: moniker range=">= aspnetcore-3.0"

Das Hosting ASP.net Core SignalR Hubs im Kontext eines hintergrundworkerprozesses ist identisch mit dem Hosting eines Hubs in einer ASP.net Core Web-App. In der `Startup.ConfigureServices`-Methode fügt der Aufruf `services.AddSignalR` die erforderlichen Dienste der ASP.net Core-Abhängigkeitsinjektion (di)-Ebene hinzu, um SignalRzu unterstützen. In `Startup.Configure`wird die `MapHub`-Methode im `UseEndpoints` Rückruf aufgerufen, um die Hub-Endpunkte in der ASP.net Core Anforderungs Pipeline zu verknüpfen.

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

Das Hosting ASP.net Core SignalR Hubs im Kontext eines hintergrundworkerprozesses ist identisch mit dem Hosting eines Hubs in einer ASP.net Core Web-App. In der `Startup.ConfigureServices`-Methode fügt der Aufruf `services.AddSignalR` die erforderlichen Dienste der ASP.net Core-Abhängigkeitsinjektion (di)-Ebene hinzu, um SignalRzu unterstützen. In `Startup.Configure`wird die `UseSignalR`-Methode aufgerufen, um die Hub-Endpunkte in der ASP.net Core Anforderungs Pipeline zu verknüpfen.

[!code-csharp[Startup](background-service/sample/Server/Startup.cs?name=Startup)]

::: moniker-end

Im vorherigen Beispiel implementiert die `ClockHub`-Klasse die `Hub<T>`-Klasse, um einen stark typisierten Hub zu erstellen. Der `ClockHub` wurde in der `Startup`-Klasse konfiguriert, um auf Anforderungen am Endpunkt `/hubs/clock`zu reagieren.

Weitere Informationen zu stark typisierten Hubs finden Sie unter [Verwenden von Hubs in SignalR für ASP.net Core](xref:signalr/hubs#strongly-typed-hubs).

> [!NOTE]
> Diese Funktion ist nicht auf die [Hub-\<t >](xref:Microsoft.AspNetCore.SignalR.Hub`1) Klasse beschränkt. Jede Klasse, die von [Hub](xref:Microsoft.AspNetCore.SignalR.Hub)erbt, z. [b. dynamichub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), funktioniert ebenfalls.

[!code-csharp[Startup](background-service/sample/Server/ClockHub.cs?name=ClockHub)]

Die Schnittstelle, die von der stark typisierten `ClockHub` verwendet wird, ist die `IClock` Schnittstelle.

[!code-csharp[Startup](background-service/sample/HubServiceInterfaces/IClock.cs?name=IClock)]

## <a name="call-a-opno-locsignalr-hub-from-a-background-service"></a>SignalR Hub von einem Hintergrunddienst aus abrufen

Während des Starts wird die `Worker`-Klasse, eine `BackgroundService`, mit `AddHostedService`verknüpft.

```csharp
services.AddHostedService<Worker>();
```

Da SignalR auch während der `Startup` Phase eingebunden wird, in der jeder Hub an einen einzelnen Endpunkt in der HTTP-Anforderungs Pipeline ASP.net Core angefügt ist, wird jeder Hub durch eine `IHubContext<T>` auf dem Server dargestellt. Mithilfe der di-Features von ASP.net Core können andere Klassen, die von der hostingschicht instanziiert werden (z. b. `BackgroundService` Klassen, MVC-Controller Klassen oder Razor Page Models) Verweise auf serverseitige Hubs erhalten, indem während der Erstellung Instanzen von `IHubContext<ClockHub, IClock>` akzeptiert werden.

[!code-csharp[Startup](background-service/sample/Server/Worker.cs?name=Worker)]

Da die `ExecuteAsync`-Methode im Hintergrunddienst iterativ aufgerufen wird, werden das aktuelle Datum und die aktuelle Uhrzeit des Servers mithilfe des `ClockHub`an die verbundenen Clients gesendet.

## <a name="react-to-opno-locsignalr-events-with-background-services"></a>Reagieren auf SignalR Ereignisse mit Hintergrund Diensten

Wie eine Einzelseiten-APP, die den JavaScript-Client für SignalR oder eine .net-Desktop-App verwendet, kann die mithilfe des <xref:signalr/dotnet-client>verwenden, eine `BackgroundService` oder `IHostedService` Implementierung kann auch verwendet werden, um eine Verbindung mit SignalR Hubs herzustellen und auf Ereignisse zu reagieren.

Die `ClockHubClient`-Klasse implementiert sowohl die `IClock`-Schnittstelle als auch die `IHostedService`-Schnittstelle. Auf diese Weise kann es während `Startup` eingebunden werden, um fortlaufend auszuführen und auf Hub-Ereignisse vom Server zu reagieren.

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

Während der Initialisierung erstellt der `ClockHubClient` eine Instanz eines `HubConnection` und verbindet die `IClock.ShowTime`-Methode als Handler für das `ShowTime` Ereignis des Hubs.

[!code-csharp[The ClockHubClient constructor](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

In der `IHostedService.StartAsync`-Implementierung wird der `HubConnection` asynchron gestartet.

[!code-csharp[StartAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

Während der `IHostedService.StopAsync`-Methode wird der `HubConnection` asynchron verworfen.

[!code-csharp[StopAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Erste Schritte](xref:tutorials/signalr)
* [Hubs](xref:signalr/hubs)
* [Veröffentlichen in Azure](xref:signalr/publish-to-azure-web-app)
* [Stark typisierte Hubs](xref:signalr/hubs#strongly-typed-hubs)

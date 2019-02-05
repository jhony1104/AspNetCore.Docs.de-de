---
title: Host ASP.NET Core SignalR in Diensten im Hintergrund
author: bradygaster
description: Erfahren Sie, wie Nachrichten von .NET Core BackgroundService-Klassen für SignalR-Clients gesendet.
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 02/04/2019
uid: signalr/background-services
ms.openlocfilehash: b359bd7f6b0667aeb8d9c8f5eb450637b1347b19
ms.sourcegitcommit: e418cb9cddeb3de06fa0cb4fdb5529da03ff6d63
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2019
ms.locfileid: "55739669"
---
# <a name="host-aspnet-core-signalr-in-background-services"></a>Host ASP.NET Core SignalR in Diensten im Hintergrund

Durch [Brady Gaster](https://twitter.com/bradygaster)

Dieser Artikel enthält Anleitungen für:

* SignalR-Hubs mit einem mit ASP.NET Core gehosteten Hintergrund-Arbeitsprozess gehostet werden.
* Senden von Nachrichten an verbundene Clients in ein .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(Herunterladen von)](xref:index#how-to-download-a-sample)

## <a name="wire-up-signalr-during-startup"></a>Während des Starts SignalR verknüpfen

Hosten von ASP.NET Core SignalR-Hubs im Kontext eines Arbeitsprozesses für den Hintergrund ist zum Hosten der Hub in einer ASP.NET Core-Web-app identisch. In der `Startup.ConfigureServices` -Methode, die aufrufende `services.AddSignalR` fügt die erforderlichen Dienste, die ASP.NET Core Dependency Injection (DI)-Ebene, die SignalR unterstützen. In `Startup.Configure`, `UseSignalR` Methode wird aufgerufen, die Hub-Endpunkte in der ASP.NET Core-Anforderungspipeline verknüpfen.

[!code-csharp[Startup](background-service/sample/Server/Startup.cs?name=Startup)]

Im vorherigen Beispiel das `ClockHub` -Klasse implementiert die `Hub<T>` Klasse, um einen stark typisierten Hub zu erstellen. Die `ClockHub` konfiguriert wurde die `Startup` Klasse zum Reagieren auf Anforderungen an den Endpunkt `/hubs/clock`.

Weitere Informationen zu stark typisierte Hubs finden Sie unter [verwenden Hubs in SignalR für ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).

> [!NOTE]
> Diese Funktionen sind nicht auf die [Hub\<T >](xref:Microsoft.AspNetCore.SignalR.Hub`1) Klasse. Jede Klasse, die von erbt [Hub](xref:Microsoft.AspNetCore.SignalR.Hub), z. B. [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), funktionieren auch.

[!code-csharp[Startup](background-service/sample/Server/ClockHub.cs?name=ClockHub)]

Die Schnittstelle, durch den stark typisierten `ClockHub` ist die `IClock` Schnittstelle.

[!code-csharp[Startup](background-service/sample/HubServiceInterfaces/IClock.cs?name=IClock)]

## <a name="call-a-signalr-hub-from-a-background-service"></a>Rufen Sie einen Hintergrunddienst einen SignalR-Hub

Während des Starts der `Worker` -Klasse, eine `BackgroundService`, läuft mit `AddHostedService`.

```csharp
services.AddHostedService<Worker>();
```

Da SignalR auch während der vernetzt ist die `Startup` phase, in dem jeder Hub an einen einzelnen Endpunkt in ASP.NET Core-HTTP-Anforderungspipeline angefügt ist, wird jeder Hub durch dargestellt eine `IHubContext<T>` auf dem Server. Mithilfe von ASP.NET Core DI features, andere Klassen instanziiert, indem der hostingebene, wie z. B. `BackgroundService` Klassen, MVC-Controller-Klassen oder Razor Pages-Modellen, können Verweise auf die serverseitigen Hubs abrufen, indem Sie Instanzen von akzeptieren `IHubContext<ClockHub, IClock>` während der Erstellung.

[!code-csharp[Startup](background-service/sample/Server/Worker.cs?name=Worker)]

Als die `ExecuteAsync` -Methode im Hintergrunddienst wiederholt aufgerufen wird, aktuelles Datum und Uhrzeit des Servers gesendet werden an die verbundenen Clients, die mit der `ClockHub`.

## <a name="react-to-signalr-events-with-background-services"></a>Reagieren Sie auf Ereignisse von SignalR mit Diensten im Hintergrund

Wie eine Einzelseiten-App mithilfe von JavaScript-Client für SignalR oder eine .NET desktop-app ausführen kann mithilfe der von der <xref:signalr/dotnet-client>, `BackgroundService` oder `IHostedService` Implementierung kann auch verwendet werden, um eine Verbindung mit SignalR-Hubs herstellen und auf Ereignisse reagieren.

Die `ClockHubClient` -Klasse implementiert sowohl die `IClock` Schnittstelle und die `IHostedService` Schnittstelle. Auf diese Weise können sie während der eingerichtet werden `Startup` kontinuierlich ausgeführt werden, und reagieren auf Hub-Ereignisse vom Server. 

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

Während der Initialisierung der `ClockHubClient` erstellt eine Instanz eine `HubConnection` und verbindet die `IClock.ShowTime` Methode als Ereignishandler für des Hubs `ShowTime` Ereignis.

[!code-csharp[The ClockHubClient constructor](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

In der `IHostedService.StartAsync` -Implementierung der `HubConnection` wird asynchron gestartet.

[!code-csharp[StartAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

Während der `IHostedService.StopAsync` -Methode, die `HubConnection` asynchron verworfen wird.

[!code-csharp[StopAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Erste Schritte](xref:tutorials/signalr)
* [Hubs](xref:signalr/hubs)
* [Veröffentlichen in Azure](xref:signalr/publish-to-azure-web-app)
* [Stark typisierte Hubs](xref:signalr/hubs#strongly-typed-hubs)

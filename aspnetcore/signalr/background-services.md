---
title: Hosten von ASP.net Core signalr in background Services
author: bradygaster
description: Erfahren Sie, wie Sie Nachrichten von .net Core-backgroundservice-Klassen an signalr-Clients senden.
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 02/04/2019
uid: signalr/background-services
ms.openlocfilehash: 23a53f33a03ce3b76cf6846c3f214a6cad055209
ms.sourcegitcommit: f65d8765e4b7c894481db9b37aa6969abc625a48
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773936"
---
# <a name="host-aspnet-core-signalr-in-background-services"></a>Hosten von ASP.net Core signalr in background Services

Von [Brady Gastern](https://twitter.com/bradygaster)

Dieser Artikel enthält Anleitungen für:

* Hosten von signalr-Hubs mit einem Hintergrund Arbeitsprozess, der mit ASP.net Core gehostet wird.
* Senden von Nachrichten an verbundene Clients aus einem .net Core- [backgroundservice](xref:Microsoft.Extensions.Hosting.BackgroundService).

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(herunterladen)](xref:index#how-to-download-a-sample)

## <a name="wire-up-signalr-during-startup"></a>Übertragen von signalr während des Starts

::: moniker range=">= aspnetcore-3.0"

Das Hosting von ASP.net Core signalr Hubs im Kontext eines hintergrundworkerprozesses ist identisch mit dem Hosting eines Hubs in einer ASP.net Core Web-App. In der `Startup.ConfigureServices` -Methode fügt `services.AddSignalR` der Aufruf von die erforderlichen Dienste zur Unterstützung von signalr der Ebene der ASP.net Core-Abhängigkeitsinjektion (di) hinzu. In `Startup.Configure`wird die `MapHub` - `UseEndpoints` Methode im Rückruf aufgerufen, um die Hub-Endpunkt (s) in der ASP.net Core Anforderungs Pipeline zu verknüpfen.

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

Das Hosting von ASP.net Core signalr Hubs im Kontext eines hintergrundworkerprozesses ist identisch mit dem Hosting eines Hubs in einer ASP.net Core Web-App. In der `Startup.ConfigureServices` -Methode fügt `services.AddSignalR` der Aufruf von die erforderlichen Dienste zur Unterstützung von signalr der Ebene der ASP.net Core-Abhängigkeitsinjektion (di) hinzu. In `Startup.Configure`wird die `UseSignalR` -Methode aufgerufen, um den Hub-Endpunkt (s) in der ASP.net Core Anforderungs Pipeline zu verknüpfen.

[!code-csharp[Startup](background-service/sample/Server/Startup.cs?name=Startup)]

::: moniker-end

Im vorherigen Beispiel implementiert die `ClockHub` -Klasse die `Hub<T>` -Klasse, um einen stark typisierten Hub zu erstellen. Der `ClockHub` wurde in der `Startup` -Klasse so konfiguriert, dass er auf Anforderungen am `/hubs/clock`Endpunkt antwortet.

Weitere Informationen zu stark typisierten Hubs finden Sie unter [Verwenden von Hubs in signalr für ASP.net Core](xref:signalr/hubs#strongly-typed-hubs).

> [!NOTE]
> Diese Funktion ist nicht auf die [Hub\<t->](xref:Microsoft.AspNetCore.SignalR.Hub`1) Klasse beschränkt. Jede Klasse, die von [Hub](xref:Microsoft.AspNetCore.SignalR.Hub)erbt, z. [b. dynamichub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), funktioniert ebenfalls.

[!code-csharp[Startup](background-service/sample/Server/ClockHub.cs?name=ClockHub)]

Die vom stark typisierten `ClockHub` verwendete Schnittstelle ist die `IClock` -Schnittstelle.

[!code-csharp[Startup](background-service/sample/HubServiceInterfaces/IClock.cs?name=IClock)]

## <a name="call-a-signalr-hub-from-a-background-service"></a>Einen signalr-Hub über einen Hintergrunddienst aufzurufen

Während des Starts wird `Worker` die Klasse, `BackgroundService`a, mithilfe `AddHostedService`von verdrahtet.

```csharp
services.AddHostedService<Worker>();
```

Da signalr auch in der `Startup` Phase eingebunden ist, in der jeder Hub an einen einzelnen Endpunkt in der HTTP-Anforderungs Pipeline ASP.net Core angefügt ist, wird jeder Hub durch eine `IHubContext<T>` auf dem Server repräsentiert. Mithilfe der di-Features von ASP.net Core können andere Klassen, die von der hostingschicht `BackgroundService` instanziiert werden (z. b. Klassen, MVC-Controller Klassen oder Razor Page Models), Verweise auf `IHubContext<ClockHub, IClock>` serverseitige Hubs erhalten, indem Sie während der Erstellung Instanzen von akzeptieren.

[!code-csharp[Startup](background-service/sample/Server/Worker.cs?name=Worker)]

Wenn die `ExecuteAsync` -Methode im Hintergrunddienst iterativ aufgerufen wird, werden das aktuelle Datum und die aktuelle Uhrzeit des Servers über das `ClockHub`an die verbundenen Clients gesendet.

## <a name="react-to-signalr-events-with-background-services"></a>Reagieren auf signalr-Ereignisse mit Hintergrund Diensten

Wie eine Einzelseiten-APP <xref:signalr/dotnet-client>, die den JavaScript-Client für signalr oder eine .net-Desktop-App mithilfe von verwenden kann, kann eine-oder `IHostedService` -Implementierung auch verwendet werden, um eine `BackgroundService` Verbindung mit signalr Hubs herzustellen und auf Ereignisse zu reagieren.

Die `ClockHubClient` -Klasse implementiert `IClock` die-Schnittstelle `IHostedService` und die-Schnittstelle. Auf diese Weise können Sie während `Startup` der fortlaufenden arbeiten und auf Hub-Ereignisse vom Server reagieren.

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

Während der `ClockHubClient` Initialisierung wird `HubConnection` von eine Instanz von erstellt, und die `IClock.ShowTime` -Methode wird als Handler für das- `ShowTime` Ereignis des Hubs erstellt.

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

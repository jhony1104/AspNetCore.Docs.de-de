---
title: ASP.NET Core SignalR .NET Client
author: bradygaster
description: Informationen zum ASP.NET Core SignalR .NET Client
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/17/2019
uid: signalr/dotnet-client
ms.openlocfilehash: 97c553874cb1e4b678fa0e5cd65074f135193861
ms.sourcegitcommit: 4ef0362ef8b6e5426fc5af18f22734158fe587e1
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67153116"
---
# <a name="aspnet-core-signalr-net-client"></a>ASP.NET Core SignalR .NET Client

Der ASP.NET Core SignalR .NET Client Library können Sie die Kommunikation mit SignalR-Hubs in .NET apps.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Das Codebeispiel in diesem Artikel ist eine WPF-app, die ASP.NET Core SignalR .NET Client verwendet.

## <a name="install-the-signalr-net-client-package"></a>Installieren Sie das SignalR .NET Client-Paket

Die `Microsoft.AspNetCore.SignalR.Client` für .NET-Clients zur Verbindung mit SignalR-Hubs ist ein Paket erforderlich. Um die Clientbibliothek zu installieren, führen Sie den folgenden Befehl der **-Paket-Manager-Konsole** Fenster:

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

## <a name="connect-to-a-hub"></a>Verbinden mit einem hub

Um eine Verbindung herzustellen, erstellen Sie eine `HubConnectionBuilder` , und rufen Sie `Build`. Die Hub-URL, Protokoll, Transporttyp, Protokollebene, Header und anderen Optionen können beim Erstellen einer Verbindungs konfiguriert werden. Konfigurieren Sie alle erforderlichen Optionen durch Einfügen eines der `HubConnectionBuilder` Methoden `Build`. Starten Sie die Verbindung mit `StartAsync`.

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a>Behandeln Sie die Verbindung wurde unterbrochen

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>Automatisch erneut eine Verbindung herstellen

Die <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection> kann konfiguriert werden, um die Verbindung automatisch wiederherzustellen, verwenden die `WithAutomaticReconnect` Methode für die <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>. Es wird nicht standardmäßig automatisch wiederherzustellen.

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect()
    .Build();
```

Ohne Parameter `WithAutomaticReconnect()` konfiguriert den Client, um zu warten, 0, 2, 10 und 30 Sekunden bzw. jedem Versuch eine erneute Verbindung beenden nach vier fehlgeschlagenen versuchen.

Vor jedem erneuten Verbindungsversuche, die `HubConnection` wird der Übergang in die `HubConnectionState.Reconnecting` Status und ausgelöst werden die `Reconnecting` Ereignis.  Dies bietet die Möglichkeit, Benutzer zu warnen, dass die Verbindung unterbrochen wurde und UI-Elemente zu deaktivieren. Nicht interaktive apps können beginnen, Warteschlangen, oder Löschen von Nachrichten.

```csharp
connection.Reconnecting += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Reconnecting);

    // Notify users the connection was lost and the client is reconnecting.
    // Start queuing or dropping messages.

    return Task.CompletedTask;
};
```

Wenn der Client erfolgreich in die ersten vier Versuche, erneut eine Verbindung herstellt der `HubConnection` erfolgt ein Wechsel zurück in die `Connected` Status und ausgelöst werden die `Reconnected` Ereignis. Dies bietet die Möglichkeit, Benutzer zu informieren, die Verbindung wurde wieder hergestellt wurde, und Entfernen aus der Warteschlange Nachrichten in der Warteschlange.

Da die Verbindung vollständig neu auf dem Server, sieht eine neue `ConnectionId` bereitgestellt der `Reconnected` -Ereignishandler.

> [!WARNING]
> Die `Reconnected` -Ereignishandler des `connectionId` Parameter ist null, wenn die `HubConnection` wurde so konfiguriert, dass [überspringen Aushandlung](xref:signalr/configuration#configure-client-options).

```csharp
connection.Reconnected += connectionId =>
{
    Debug.Assert(connection.State == HubConnectionState.Connected);

    // Notify users the connection was reestablished.
    // Start dequeuing messages queued while reconnecting if any.

    return Task.CompletedTask;
};
```

`WithAutomaticReconnect()` Konfigurieren Sie wird nicht die `HubConnection` zum Wiederholen von Fehlern der ersten Start, sodass Fehler beim Starten von manuell verarbeitet werden müssen:

```csharp
public static async Task<bool> ConnectWithRetryAsync(HubConnection connection, CancellationToken token)
{
    // Keep trying to until we can start or the token is canceled.
    while (true)
    {
        try
        {
            await connection.StartAsync(token);
            Debug.Assert(connection.State == HubConnectionState.Connected);
            return true;
        }
        catch when (token.IsCancellationRequested)
        {
            return false;
        }
        catch
        {
            // Failed to connect, trying again in 5000 ms.
            Debug.Assert(connection.State == HubConnectionState.Disconnected);
            await Task.Delay(5000);
        }
    }
}
```

Wenn der Client erfolgreich in die ersten vier Versuche, erneut eine Verbindung herstellen nicht die `HubConnection` wird der Übergang in die `Disconnected` Status und ausgelöst werden die <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> Ereignis. Dies bietet die Möglichkeit, die versuchen, starten Sie die Verbindung manuell neu, oder Benutzer zu informieren, dass die Verbindung dauerhaft verloren gegangen ist.

```csharp
connection.Closed += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Disconnected);

    // Notify users the connection has been closed or manually try to restart the connection.

    return Task.CompletedTask;
};
```

Um eine benutzerdefinierte Anzahl von verbindungswiederherstellungsversuchen vor dem Trennen der Verbindung zu konfigurieren oder ändern die zeitplanung eine erneute Verbindung `WithAutomaticReconnect` akzeptiert ein Array von Zahlen, die, die die Verzögerung in Millisekunden darstellt, die Wartezeit vor dem Starten von jedem Versuch eine erneute Verbindung.

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.Zero, TimeSpan.FromSeconds(10) })
    .Build();

    // .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.FromSeconds(2), TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(30) }) yields the default behavior.
```

Im vorhergehende Beispiel konfiguriert die `HubConnection` zu versuchen, Verbindungen, unmittelbar nachdem die Verbindung unterbrochen wird. Dies gilt auch für die Standardkonfiguration.

Wenn eine erneute Verbindung beim erste Versuch fehlschlägt, wird die zweite Versuch für eine erneute Verbindung auch sofort starten, anstatt abzuwarten, 2 Sekunden, wie in der Standardkonfiguration.

Wenn eine erneute Verbindung beim zweite Versuch ein Fehler auftritt, startet der dritte Versuch für eine erneute Verbindung innerhalb von 10 Sekunden, die wiederum wie die Standardkonfiguration.

Das benutzerdefinierte Verhalten weicht klicken Sie dann erneut das Standardverhalten von stoppt, wenn Sie versuchen, das dritte Verbindung wiederherstellen Fehler. In der Standardkonfiguration es würde werden eine mehr erneut eine Verbindung herzustellen versucht, die in einer anderen 30 Sekunden.

Wenn Sie möchten noch mehr Kontrolle über Zeitpunkt und Anzahl automatisch erneut eine Verbindung herzustellen versucht, `WithAutomaticReconnect` akzeptiert ein Objekt, das die `IRetryPolicy` -Schnittstelle, die eine einzige namens Methode `NextRetryDelay`.

`NextRetryDelay` akzeptiert ein einzelnes Argument mit dem Typ `RetryContext`. Die `RetryContext` verfügt über drei Eigenschaften: `PreviousRetryCount`, `ElapsedTime` und `RetryReason` die sind eine `long`, `TimeSpan` und `Exception` bzw. Vor dem ersten Versuch eine erneute Verbindung sowohl `PreviousRetryCount` und `ElapsedTime` werden 0 (null), und die `RetryReason` wird die Ausnahme, durch die Verbindung unterbrochen, werden. Nach den fehlerhaften Wiederholungsversuchen beim Senden `PreviousRetryCount` wird werden um eins erhöht, `ElapsedTime` wird aktualisiert, um die Zeitspanne bis jetzt Wiederherstellen der Verbindung entsprechen und die `RetryReason` wird die Ausnahme, durch die letzte Verbindung abgebrochen, werden.

`NextRetryDelay` muss entweder eine Zeitspanne, die den Zeitpunkt zu warten, bevor der nächste Versuch für eine erneute Verbindung zurückgeben oder `null` Wenn die `HubConnection` beendet werden sollte erneut eine Verbindung herzustellen.

```csharp
public class RandomRetryPolicy : IRetryPolicy
{
    private readonly Random _random = new Random();

    public TimeSpan? NextRetryDelay(RetryContext retryContext)
    {
        // If we've been reconnecting for less than 60 seconds so far,
        // wait between 0 and 10 seconds before the next reconnect attempt.
        if (retryContext.ElapsedTime < TimeSpan.FromSeconds(60))
        {
            return TimeSpan.FromSeconds(_random.Next() * 10);
        }
        else
        {
            // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
            return null;
        }
    }
}
```

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect(new RandomRetryPolicy())
    .Build();
```

Alternativ können Sie schreiben Code, der den Client manuell, wie in wiederhergestellt [manuell erneut verbinden](#manually-reconnect).

::: moniker-end

### <a name="manually-reconnect"></a>Manuell erneut verbinden

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> Vor 3.0 wiederherzustellen nicht der .NET Client für SignalR automatisch. Sie müssen Code schreiben, die den Client manuell neu verbunden wird.

::: moniker-end

Verwenden der <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> Ereignis reagieren, wenn die Verbindung wurde unterbrochen. Beispielsweise empfiehlt es sich zum erneuten Herstellen einer Verbindung zu automatisieren.

Die `Closed` Ereignis erfordert ein Delegat, der zurückgegeben eine `Task`, wodurch Async-Code für die Ausführung ohne Verwendung `async void`. Erfüllen Sie die Signatur des Delegaten in einen `Closed` -Ereignishandler, der ausgeführt wird synchron zurückgeben `Task.CompletedTask`:

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

Der Hauptgrund für die Async-Unterstützung ist, damit Sie die Verbindung neu starten können. Starten eine Verbindung ist eine asynchrone Aktion.

In einem `Closed` Handler, der die Verbindung neu gestartet. warten Sie einige zufällige Verzögerung, um zu verhindern, Überlastung des Servers, berücksichtigen, wie im folgenden Beispiel gezeigt:

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a>Aufrufen von Hub-Methoden von client

`InvokeAsync` Ruft Methoden für den Hub. Übergeben Sie den Namen des Hubs-Methode und alle Argumente, die in die hubmethode, die definiert `InvokeAsync`. SignalR ist asynchron, verwenden Sie daher `async` und `await` bei den aufrufen.

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

Die `InvokeAsync` Methode gibt eine `Task` die abgeschlossen wird, wenn die Rückgabe der Servermethode. Der Rückgabewert, sofern vorhanden, dient als das Ergebnis der `Task`. Von der Methode auf dem Server ausgelösten Ausnahmen erzeugen eines fehlerhaften `Task`. Verwendung `await` Syntax für das warten, bis der Server-Methode abgeschlossen und `try...catch` Syntax, um Fehler zu behandeln.

Die `SendAsync` Methode gibt eine `Task` die abgeschlossen wird, wenn die Nachricht an den Server gesendet wurde. Kein Rückgabewert wird bereitgestellt, da dies `Task` nicht gewartet, bis die Server-Methode abgeschlossen ist. Alle Ausnahmen, die auf dem Client ausgelöst werden, beim Senden der Nachricht erzeugt einen Fehler `Task`. Verwendung `await` und `try...catch` senden Syntax zum Behandeln von Fehlern.

> [!NOTE]
> Bei Verwendung von Azure SignalR Service in *serverlose Modus*, Sie können nicht von einem Client hubmethoden aufrufen. Weitere Informationen finden Sie unter den [SignalR Service-Dokumentation](/azure/azure-signalr/signalr-concept-serverless-development-config).

## <a name="call-client-methods-from-hub"></a>Rufen Sie Client-Methoden von Hub-Instanz

Definieren Sie Methoden, die der Hub Aufrufe mithilfe von `connection.On` nach der Erstellung, jedoch vor dem Starten der Verbindung.

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

Der vorangehende Code in `connection.On` ausgeführt wird, wenn Sie serverseitigen Code aufruft, mit der `SendAsync` Methode.

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a>Fehlerbehandlung und Protokollierung

Behandeln von Fehlern mit einem Try / Catch-Anweisung. Überprüfen Sie die `Exception` Objekt, um zu bestimmen, die entsprechende Aktion aus, um aufzuzeichnen, wenn ein Fehler auftritt.

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Hubs](xref:signalr/hubs)
* [JavaScript-Client](xref:signalr/javascript-client)
* [Veröffentlichen in Azure](xref:signalr/publish-to-azure-web-app)
* [Serverlose Azure SignalR Service-Dokumentation](/azure/azure-signalr/signalr-concept-serverless-development-config)

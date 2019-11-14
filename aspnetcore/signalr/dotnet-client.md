---
title: ASP.net Core SignalR .NET-Client
author: bradygaster
description: Informationen zum ASP.net Core SignalR .NET-Client
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/dotnet-client
ms.openlocfilehash: 28e8fcf808406cd0251ba94e2ef97ab04841fcd0
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963972"
---
# <a name="aspnet-core-opno-locsignalr-net-client"></a>ASP.net Core SignalR .NET-Client

Mit der ASP.net Core SignalR .NET-Client Bibliothek können Sie über .net-apps mit SignalR Hubs kommunizieren.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Das Codebeispiel in diesem Artikel ist eine WPF-App, die den ASP.net Core SignalR .NET-Client verwendet.

## <a name="install-the-opno-locsignalr-net-client-package"></a>Installieren des SignalR .NET-Client Pakets

[Microsoft. aspnetcore.SignalR. Das Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) Paket ist erforderlich, damit .NET-Clients eine Verbindung mit SignalR Hubs herstellen können.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Um die Client Bibliothek zu installieren, führen Sie den folgenden Befehl im Fenster **Paket-Manager-Konsole** aus:

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Um die Client Bibliothek zu installieren, führen Sie den folgenden Befehl in einer Befehlsshell aus:

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="connect-to-a-hub"></a>Herstellen einer Verbindung mit einem Hub

Erstellen Sie eine `HubConnectionBuilder`, und rufen Sie `Build`auf, um eine Verbindung herzustellen. Die Hub-URL, das Protokoll, der Transporttyp, die Protokollebene, Header und andere Optionen können beim Herstellen einer Verbindung konfiguriert werden. Konfigurieren Sie alle erforderlichen Optionen, indem Sie eine der `HubConnectionBuilder` Methoden in `Build`einfügen. Starten Sie die Verbindung mit `StartAsync`.

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a>Handle für verlorene Verbindung

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>Automatisch wiederherstellen der Verbindung

Der <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection> kann so konfiguriert werden, dass er mit der `WithAutomaticReconnect`-Methode auf dem <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>automatisch erneut eine Verbindung herstellt. Standardmäßig wird die Verbindung nicht automatisch wieder hergestellt.

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect()
    .Build();
```

Ohne Parameter konfiguriert `WithAutomaticReconnect()` den Client so, dass 0, 2, 10 und 30 Sekunden gewartet wird, bevor versucht wird, jeden erneuten Verbindungsversuch durchzusetzen, wobei nach vier fehlgeschlagenen Versuchen angehalten wird.

Vor dem Starten der erneuten Verbindungsversuche wechselt der `HubConnection` in den `HubConnectionState.Reconnecting`-Zustand und lösen das `Reconnecting`-Ereignis aus.  Dies bietet die Möglichkeit, die Benutzer zu warnen, dass die Verbindung unterbrochen wurde, und Benutzeroberflächen Elemente zu deaktivieren. Nicht interaktive Apps können Nachrichten in die Warteschlange einreihen oder löschen.

```csharp
connection.Reconnecting += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Reconnecting);

    // Notify users the connection was lost and the client is reconnecting.
    // Start queuing or dropping messages.

    return Task.CompletedTask;
};
```

Wenn der Client innerhalb der ersten vier Versuche erfolgreich erneut eine Verbindung herstellt, wechselt der `HubConnection` zurück in den `Connected` Zustand und löst das `Reconnected` Ereignis aus. Dies bietet die Möglichkeit, den Benutzern mitzuteilen, dass die Verbindung wieder hergestellt wurde, und alle Nachrichten in der Warteschlange aus der Warteschlange entfernt

Da die Verbindung mit dem Server vollständig neu ist, wird der `Reconnected` Ereignis Handlern eine neue `ConnectionId` bereitgestellt.

> [!WARNING]
> Der `connectionId` Parameter des `Reconnected` Ereignis Handlers ist NULL, wenn die `HubConnection` zum über [springen der Aushandlung](xref:signalr/configuration#configure-client-options)konfiguriert wurde.

```csharp
connection.Reconnected += connectionId =>
{
    Debug.Assert(connection.State == HubConnectionState.Connected);

    // Notify users the connection was reestablished.
    // Start dequeuing messages queued while reconnecting if any.

    return Task.CompletedTask;
};
```

`WithAutomaticReconnect()` die `HubConnection` nicht so konfigurieren, dass anfängliche Start Fehler erneut auftreten, müssen Start Fehler manuell behandelt werden:

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

Wenn der Client nicht innerhalb der ersten vier Versuche erneut eine Verbindung herstellt, wechselt der `HubConnection` in den `Disconnected` Zustand und auslöst das <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> Ereignis. Auf diese Weise können Sie versuchen, die Verbindung manuell neu zu starten oder den Benutzern mitzuteilen, dass die Verbindung dauerhaft verloren gegangen ist.

```csharp
connection.Closed += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Disconnected);

    // Notify users the connection has been closed or manually try to restart the connection.

    return Task.CompletedTask;
};
```

Um eine benutzerdefinierte Anzahl von Wiederholungs versuchen zu konfigurieren, bevor die Verbindung getrennt wird, oder um die Zeit für die Wiederherstellung der Verbindung zu ändern, nimmt `WithAutomaticReconnect` ein Array von Zahlen an, das die Wartezeit in Millisekunden angibt, die vor dem Start der einzelnen Wiederholungs Versuche

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.Zero, TimeSpan.FromSeconds(10) })
    .Build();

    // .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.FromSeconds(2), TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(30) }) yields the default behavior.
```

Im vorherigen Beispiel wird der `HubConnection` so konfiguriert, dass nach dem Verlust der Verbindung erneut versucht wird, die Verbindung herzustellen. Dies gilt auch für die Standardkonfiguration.

Wenn der erste Wiederholungsversuch fehlschlägt, wird der zweite Wiederholungsversuch auch sofort gestartet, anstatt zwei Sekunden zu warten, wie in der Standardkonfiguration.

Wenn der zweite Wiederholungsversuch fehlschlägt, wird der dritte Versuch der erneuten Verbindungs Herstellung in 10 Sekunden gestartet. Dies entspricht der Standardkonfiguration.

Das benutzerdefinierte Verhalten weicht dann erneut vom Standardverhalten ab, indem nach dem dritten Wiederholungsversuch der Verbindung angehalten wird. In der Standardkonfiguration gibt es einen weiteren erneuten Verbindungsversuch in weiteren 30 Sekunden.

Wenn Sie noch mehr Kontrolle über die zeitliche Steuerung und die Anzahl der automatischen Wiederholungs Versuche benötigen, akzeptiert `WithAutomaticReconnect` ein Objekt, das die `IRetryPolicy`-Schnittstelle implementiert, die über eine einzige Methode mit dem Namen `NextRetryDelay`verfügt.

`NextRetryDelay` nimmt ein einzelnes Argument mit dem Typ `RetryContext`. Der `RetryContext` verfügt über drei Eigenschaften: `PreviousRetryCount`, `ElapsedTime` und `RetryReason` die eine `long`, eine `TimeSpan` bzw. eine `Exception` sind. Vor dem ersten Versuch der erneuten Verbindungs Herstellung sind sowohl `PreviousRetryCount` als auch `ElapsedTime` NULL, und die `RetryReason` ist die Ausnahme, die bewirkt hat, dass die Verbindung unterbrochen wurde. Nach jedem fehlgeschlagenen Wiederholungsversuch werden `PreviousRetryCount` um eins inkrementiert. `ElapsedTime` wird aktualisiert, um die Zeitspanne für eine erneute Verbindungs Herstellung widerzuspiegeln, und die `RetryReason` ist die Ausnahme, die bewirkt hat, dass der letzte Wiederholungsversuch fehlgeschlagen ist.

`NextRetryDelay` müssen entweder einen TimeSpan-Wert zurückgeben, der die Zeit angibt, die gewartet werden soll, bevor der nächste Verbindungs `null` Versuch wieder hergestellt `HubConnection` wird.

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

Alternativ dazu können Sie auch Code schreiben, mit dem der Client manuell erneut eine Verbindung herstellt, wie in [Manuelles Wiederherstellen der Verbindung](#manually-reconnect)gezeigt.

::: moniker-end

### <a name="manually-reconnect"></a>Verbindung manuell wiederherstellen

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> Vor 3,0 wird die Verbindung des .NET-Clients für SignalR nicht automatisch wieder hergestellt. Sie müssen Code schreiben, mit dem der Client manuell erneut eine Verbindung herstellt.

::: moniker-end

Verwenden Sie das <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed>-Ereignis, um auf eine verlorene Verbindung zu reagieren. Beispielsweise möchten Sie möglicherweise die erneute Verbindung automatisieren.

Das `Closed`-Ereignis erfordert einen Delegaten, der eine `Task`zurückgibt, die das Ausführen von asynchronem Code ohne Verwendung von `async void`ermöglicht. Um die Delegatsignatur in einem `Closed` Ereignishandler zu erfüllen, der synchron ausgeführt wird, geben Sie `Task.CompletedTask`zurück:

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

Der Hauptgrund für die Async-Unterstützung ist, dass Sie die Verbindung neu starten können. Das Starten einer Verbindung ist eine asynchrone Aktion.

In einem `Closed` Handler, der die Verbindung neu startet, sollten Sie auf eine zufällige Verzögerung warten, um zu verhindern, dass der Server überladen wird, wie im folgenden Beispiel gezeigt:

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a>Hub-Methoden vom Client abrufen

`InvokeAsync` ruft Methoden auf dem Hub auf. Übergeben Sie den Namen der Hub-Methode und alle in der Hub-Methode definierten Argumente an `InvokeAsync`. SignalR asynchron ist, verwenden Sie beim Ausführen der Aufrufe `async` und `await`.

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

Die `InvokeAsync`-Methode gibt eine `Task` zurück, die abgeschlossen wird, wenn die Server Methode zurückgibt. Der Rückgabewert wird ggf. als Ergebnis des `Task`bereitgestellt. Alle Ausnahmen, die von der-Methode auf dem Server ausgelöst werden, führen zu einem Fehler `Task`. Verwenden Sie `await`-Syntax, um auf den Abschluss der Server Methode zu warten, und `try...catch` Syntax, um Fehler zu behandeln.

Die `SendAsync`-Methode gibt eine `Task` zurück, die abgeschlossen wird, wenn die Nachricht an den Server gesendet wurde. Es wird kein Rückgabewert bereitgestellt, da dieser `Task` nicht wartet, bis die Server Methode abgeschlossen ist. Alle Ausnahmen, die beim Senden der Nachricht auf dem Client ausgelöst werden, führen zu einem Fehler `Task`. Verwenden Sie `await`-und `try...catch`-Syntax, um Sende Fehler zu behandeln.

> [!NOTE]
> Wenn Sie Azure SignalR Service im *Server losen Modus*verwenden, können Sie keine hubmethoden von einem Client aus abrufen. Weitere Informationen finden Sie in der [Dokumentation zumSignalR-Dienst](/azure/azure-signalr/signalr-concept-serverless-development-config).

## <a name="call-client-methods-from-hub"></a>Client Methoden aus Hub abrufen

Definieren von Methoden, die der Hub mithilfe von `connection.On` nach dem Aufbau, aber vor dem Starten der Verbindung aufruft.

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

Der vorangehende Code in `connection.On` wird ausgeführt, wenn der serverseitige Code ihn mit der `SendAsync`-Methode aufruft.

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a>Fehlerbehandlung und Protokollierung

Behandeln Sie Fehler mit einer try-catch-Anweisung. Überprüfen Sie das `Exception` Objekt, um die ordnungsgemäße Aktion zu ermitteln, die nach einem Fehler ausgeführt werden soll.

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Hubs](xref:signalr/hubs)
* [JavaScript-Client](xref:signalr/javascript-client)
* [Veröffentlichen in Azure](xref:signalr/publish-to-azure-web-app)
* [Server lose Dokumentation zu Azure SignalR Service](/azure/azure-signalr/signalr-concept-serverless-development-config)

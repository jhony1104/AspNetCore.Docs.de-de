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
# <a name="aspnet-core-signalr-net-client"></a><span data-ttu-id="24624-103">ASP.NET Core SignalR .NET Client</span><span class="sxs-lookup"><span data-stu-id="24624-103">ASP.NET Core SignalR .NET Client</span></span>

<span data-ttu-id="24624-104">Der ASP.NET Core SignalR .NET Client Library können Sie die Kommunikation mit SignalR-Hubs in .NET apps.</span><span class="sxs-lookup"><span data-stu-id="24624-104">The ASP.NET Core SignalR .NET client library lets you communicate with SignalR hubs from .NET apps.</span></span>

<span data-ttu-id="24624-105">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="24624-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="24624-106">Das Codebeispiel in diesem Artikel ist eine WPF-app, die ASP.NET Core SignalR .NET Client verwendet.</span><span class="sxs-lookup"><span data-stu-id="24624-106">The code sample in this article is a WPF app that uses the ASP.NET Core SignalR .NET client.</span></span>

## <a name="install-the-signalr-net-client-package"></a><span data-ttu-id="24624-107">Installieren Sie das SignalR .NET Client-Paket</span><span class="sxs-lookup"><span data-stu-id="24624-107">Install the SignalR .NET client package</span></span>

<span data-ttu-id="24624-108">Die `Microsoft.AspNetCore.SignalR.Client` für .NET-Clients zur Verbindung mit SignalR-Hubs ist ein Paket erforderlich.</span><span class="sxs-lookup"><span data-stu-id="24624-108">The `Microsoft.AspNetCore.SignalR.Client` package is needed for .NET clients to connect to SignalR hubs.</span></span> <span data-ttu-id="24624-109">Um die Clientbibliothek zu installieren, führen Sie den folgenden Befehl der **-Paket-Manager-Konsole** Fenster:</span><span class="sxs-lookup"><span data-stu-id="24624-109">To install the client library, run the following command in the **Package Manager Console** window:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

## <a name="connect-to-a-hub"></a><span data-ttu-id="24624-110">Verbinden mit einem hub</span><span class="sxs-lookup"><span data-stu-id="24624-110">Connect to a hub</span></span>

<span data-ttu-id="24624-111">Um eine Verbindung herzustellen, erstellen Sie eine `HubConnectionBuilder` , und rufen Sie `Build`.</span><span class="sxs-lookup"><span data-stu-id="24624-111">To establish a connection, create a `HubConnectionBuilder` and call `Build`.</span></span> <span data-ttu-id="24624-112">Die Hub-URL, Protokoll, Transporttyp, Protokollebene, Header und anderen Optionen können beim Erstellen einer Verbindungs konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="24624-112">The hub URL, protocol, transport type, log level, headers, and other options can be configured while building a connection.</span></span> <span data-ttu-id="24624-113">Konfigurieren Sie alle erforderlichen Optionen durch Einfügen eines der `HubConnectionBuilder` Methoden `Build`.</span><span class="sxs-lookup"><span data-stu-id="24624-113">Configure any required options by inserting any of the `HubConnectionBuilder` methods into `Build`.</span></span> <span data-ttu-id="24624-114">Starten Sie die Verbindung mit `StartAsync`.</span><span class="sxs-lookup"><span data-stu-id="24624-114">Start the connection with `StartAsync`.</span></span>

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a><span data-ttu-id="24624-115">Behandeln Sie die Verbindung wurde unterbrochen</span><span class="sxs-lookup"><span data-stu-id="24624-115">Handle lost connection</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="24624-116">Automatisch erneut eine Verbindung herstellen</span><span class="sxs-lookup"><span data-stu-id="24624-116">Automatically reconnect</span></span>

<span data-ttu-id="24624-117">Die <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection> kann konfiguriert werden, um die Verbindung automatisch wiederherzustellen, verwenden die `WithAutomaticReconnect` Methode für die <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span><span class="sxs-lookup"><span data-stu-id="24624-117">The <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection> can be configured to automatically reconnect using the `WithAutomaticReconnect` method on the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="24624-118">Es wird nicht standardmäßig automatisch wiederherzustellen.</span><span class="sxs-lookup"><span data-stu-id="24624-118">It won't automatically reconnect by default.</span></span>

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect()
    .Build();
```

<span data-ttu-id="24624-119">Ohne Parameter `WithAutomaticReconnect()` konfiguriert den Client, um zu warten, 0, 2, 10 und 30 Sekunden bzw. jedem Versuch eine erneute Verbindung beenden nach vier fehlgeschlagenen versuchen.</span><span class="sxs-lookup"><span data-stu-id="24624-119">Without any parameters, `WithAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="24624-120">Vor jedem erneuten Verbindungsversuche, die `HubConnection` wird der Übergang in die `HubConnectionState.Reconnecting` Status und ausgelöst werden die `Reconnecting` Ereignis.</span><span class="sxs-lookup"><span data-stu-id="24624-120">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire the `Reconnecting` event.</span></span>  <span data-ttu-id="24624-121">Dies bietet die Möglichkeit, Benutzer zu warnen, dass die Verbindung unterbrochen wurde und UI-Elemente zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="24624-121">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span> <span data-ttu-id="24624-122">Nicht interaktive apps können beginnen, Warteschlangen, oder Löschen von Nachrichten.</span><span class="sxs-lookup"><span data-stu-id="24624-122">Non-interactive apps can start queuing or dropping messages.</span></span>

```csharp
connection.Reconnecting += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Reconnecting);

    // Notify users the connection was lost and the client is reconnecting.
    // Start queuing or dropping messages.

    return Task.CompletedTask;
};
```

<span data-ttu-id="24624-123">Wenn der Client erfolgreich in die ersten vier Versuche, erneut eine Verbindung herstellt der `HubConnection` erfolgt ein Wechsel zurück in die `Connected` Status und ausgelöst werden die `Reconnected` Ereignis.</span><span class="sxs-lookup"><span data-stu-id="24624-123">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire the `Reconnected` event.</span></span> <span data-ttu-id="24624-124">Dies bietet die Möglichkeit, Benutzer zu informieren, die Verbindung wurde wieder hergestellt wurde, und Entfernen aus der Warteschlange Nachrichten in der Warteschlange.</span><span class="sxs-lookup"><span data-stu-id="24624-124">This provides an opportunity to inform users the connection has been reestablished and dequeue any queued messages.</span></span>

<span data-ttu-id="24624-125">Da die Verbindung vollständig neu auf dem Server, sieht eine neue `ConnectionId` bereitgestellt der `Reconnected` -Ereignishandler.</span><span class="sxs-lookup"><span data-stu-id="24624-125">Since the connection looks entirely new to the server, a new `ConnectionId` will be provided to the `Reconnected` event handlers.</span></span>

> [!WARNING]
> <span data-ttu-id="24624-126">Die `Reconnected` -Ereignishandler des `connectionId` Parameter ist null, wenn die `HubConnection` wurde so konfiguriert, dass [überspringen Aushandlung](xref:signalr/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="24624-126">The `Reconnected` event handler's `connectionId` parameter will be null if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```csharp
connection.Reconnected += connectionId =>
{
    Debug.Assert(connection.State == HubConnectionState.Connected);

    // Notify users the connection was reestablished.
    // Start dequeuing messages queued while reconnecting if any.

    return Task.CompletedTask;
};
```

<span data-ttu-id="24624-127">`WithAutomaticReconnect()` Konfigurieren Sie wird nicht die `HubConnection` zum Wiederholen von Fehlern der ersten Start, sodass Fehler beim Starten von manuell verarbeitet werden müssen:</span><span class="sxs-lookup"><span data-stu-id="24624-127">`WithAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

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

<span data-ttu-id="24624-128">Wenn der Client erfolgreich in die ersten vier Versuche, erneut eine Verbindung herstellen nicht die `HubConnection` wird der Übergang in die `Disconnected` Status und ausgelöst werden die <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> Ereignis.</span><span class="sxs-lookup"><span data-stu-id="24624-128">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> event.</span></span> <span data-ttu-id="24624-129">Dies bietet die Möglichkeit, die versuchen, starten Sie die Verbindung manuell neu, oder Benutzer zu informieren, dass die Verbindung dauerhaft verloren gegangen ist.</span><span class="sxs-lookup"><span data-stu-id="24624-129">This provides an opportunity to attempt to restart the connection manually or inform users the connection has been permanently lost.</span></span>

```csharp
connection.Closed += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Disconnected);

    // Notify users the connection has been closed or manually try to restart the connection.

    return Task.CompletedTask;
};
```

<span data-ttu-id="24624-130">Um eine benutzerdefinierte Anzahl von verbindungswiederherstellungsversuchen vor dem Trennen der Verbindung zu konfigurieren oder ändern die zeitplanung eine erneute Verbindung `WithAutomaticReconnect` akzeptiert ein Array von Zahlen, die, die die Verzögerung in Millisekunden darstellt, die Wartezeit vor dem Starten von jedem Versuch eine erneute Verbindung.</span><span class="sxs-lookup"><span data-stu-id="24624-130">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `WithAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.Zero, TimeSpan.FromSeconds(10) })
    .Build();

    // .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.FromSeconds(2), TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(30) }) yields the default behavior.
```

<span data-ttu-id="24624-131">Im vorhergehende Beispiel konfiguriert die `HubConnection` zu versuchen, Verbindungen, unmittelbar nachdem die Verbindung unterbrochen wird.</span><span class="sxs-lookup"><span data-stu-id="24624-131">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="24624-132">Dies gilt auch für die Standardkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="24624-132">This is also true for the default configuration.</span></span>

<span data-ttu-id="24624-133">Wenn eine erneute Verbindung beim erste Versuch fehlschlägt, wird die zweite Versuch für eine erneute Verbindung auch sofort starten, anstatt abzuwarten, 2 Sekunden, wie in der Standardkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="24624-133">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="24624-134">Wenn eine erneute Verbindung beim zweite Versuch ein Fehler auftritt, startet der dritte Versuch für eine erneute Verbindung innerhalb von 10 Sekunden, die wiederum wie die Standardkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="24624-134">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="24624-135">Das benutzerdefinierte Verhalten weicht klicken Sie dann erneut das Standardverhalten von stoppt, wenn Sie versuchen, das dritte Verbindung wiederherstellen Fehler.</span><span class="sxs-lookup"><span data-stu-id="24624-135">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure.</span></span> <span data-ttu-id="24624-136">In der Standardkonfiguration es würde werden eine mehr erneut eine Verbindung herzustellen versucht, die in einer anderen 30 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="24624-136">In the default configuration there would be one more reconnect attempt in another 30 seconds.</span></span>

<span data-ttu-id="24624-137">Wenn Sie möchten noch mehr Kontrolle über Zeitpunkt und Anzahl automatisch erneut eine Verbindung herzustellen versucht, `WithAutomaticReconnect` akzeptiert ein Objekt, das die `IRetryPolicy` -Schnittstelle, die eine einzige namens Methode `NextRetryDelay`.</span><span class="sxs-lookup"><span data-stu-id="24624-137">If you want even more control over the timing and number of automatic reconnect attempts, `WithAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `NextRetryDelay`.</span></span>

<span data-ttu-id="24624-138">`NextRetryDelay` akzeptiert ein einzelnes Argument mit dem Typ `RetryContext`.</span><span class="sxs-lookup"><span data-stu-id="24624-138">`NextRetryDelay` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="24624-139">Die `RetryContext` verfügt über drei Eigenschaften: `PreviousRetryCount`, `ElapsedTime` und `RetryReason` die sind eine `long`, `TimeSpan` und `Exception` bzw.</span><span class="sxs-lookup"><span data-stu-id="24624-139">The `RetryContext` has three properties: `PreviousRetryCount`, `ElapsedTime` and `RetryReason` which are a `long`, a `TimeSpan` and an `Exception` respectively.</span></span> <span data-ttu-id="24624-140">Vor dem ersten Versuch eine erneute Verbindung sowohl `PreviousRetryCount` und `ElapsedTime` werden 0 (null), und die `RetryReason` wird die Ausnahme, durch die Verbindung unterbrochen, werden.</span><span class="sxs-lookup"><span data-stu-id="24624-140">Before the first reconnect attempt, both `PreviousRetryCount` and `ElapsedTime` will be zero, and the `RetryReason` will be the Exception that caused the connection to be lost.</span></span> <span data-ttu-id="24624-141">Nach den fehlerhaften Wiederholungsversuchen beim Senden `PreviousRetryCount` wird werden um eins erhöht, `ElapsedTime` wird aktualisiert, um die Zeitspanne bis jetzt Wiederherstellen der Verbindung entsprechen und die `RetryReason` wird die Ausnahme, durch die letzte Verbindung abgebrochen, werden.</span><span class="sxs-lookup"><span data-stu-id="24624-141">After each failed retry attempt, `PreviousRetryCount` will be incremented by one, `ElapsedTime` will be updated to reflect the amount of time spent reconnecting so far, and the `RetryReason` will be the Exception that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="24624-142">`NextRetryDelay` muss entweder eine Zeitspanne, die den Zeitpunkt zu warten, bevor der nächste Versuch für eine erneute Verbindung zurückgeben oder `null` Wenn die `HubConnection` beendet werden sollte erneut eine Verbindung herzustellen.</span><span class="sxs-lookup"><span data-stu-id="24624-142">`NextRetryDelay` must return either a TimeSpan representing the time to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

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

<span data-ttu-id="24624-143">Alternativ können Sie schreiben Code, der den Client manuell, wie in wiederhergestellt [manuell erneut verbinden](#manually-reconnect).</span><span class="sxs-lookup"><span data-stu-id="24624-143">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="24624-144">Manuell erneut verbinden</span><span class="sxs-lookup"><span data-stu-id="24624-144">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="24624-145">Vor 3.0 wiederherzustellen nicht der .NET Client für SignalR automatisch.</span><span class="sxs-lookup"><span data-stu-id="24624-145">Prior to 3.0, the .NET client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="24624-146">Sie müssen Code schreiben, die den Client manuell neu verbunden wird.</span><span class="sxs-lookup"><span data-stu-id="24624-146">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="24624-147">Verwenden der <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> Ereignis reagieren, wenn die Verbindung wurde unterbrochen.</span><span class="sxs-lookup"><span data-stu-id="24624-147">Use the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> event to respond to a lost connection.</span></span> <span data-ttu-id="24624-148">Beispielsweise empfiehlt es sich zum erneuten Herstellen einer Verbindung zu automatisieren.</span><span class="sxs-lookup"><span data-stu-id="24624-148">For example, you might want to automate reconnection.</span></span>

<span data-ttu-id="24624-149">Die `Closed` Ereignis erfordert ein Delegat, der zurückgegeben eine `Task`, wodurch Async-Code für die Ausführung ohne Verwendung `async void`.</span><span class="sxs-lookup"><span data-stu-id="24624-149">The `Closed` event requires a delegate that returns a `Task`, which allows async code to run without using `async void`.</span></span> <span data-ttu-id="24624-150">Erfüllen Sie die Signatur des Delegaten in einen `Closed` -Ereignishandler, der ausgeführt wird synchron zurückgeben `Task.CompletedTask`:</span><span class="sxs-lookup"><span data-stu-id="24624-150">To satisfy the delegate signature in a `Closed` event handler that runs synchronously, return `Task.CompletedTask`:</span></span>

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

<span data-ttu-id="24624-151">Der Hauptgrund für die Async-Unterstützung ist, damit Sie die Verbindung neu starten können.</span><span class="sxs-lookup"><span data-stu-id="24624-151">The main reason for the async support is so you can restart the connection.</span></span> <span data-ttu-id="24624-152">Starten eine Verbindung ist eine asynchrone Aktion.</span><span class="sxs-lookup"><span data-stu-id="24624-152">Starting a connection is an async action.</span></span>

<span data-ttu-id="24624-153">In einem `Closed` Handler, der die Verbindung neu gestartet. warten Sie einige zufällige Verzögerung, um zu verhindern, Überlastung des Servers, berücksichtigen, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="24624-153">In a `Closed` handler that restarts the connection, consider waiting for some random delay to prevent overloading the server, as shown in the following example:</span></span>

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="24624-154">Aufrufen von Hub-Methoden von client</span><span class="sxs-lookup"><span data-stu-id="24624-154">Call hub methods from client</span></span>

<span data-ttu-id="24624-155">`InvokeAsync` Ruft Methoden für den Hub.</span><span class="sxs-lookup"><span data-stu-id="24624-155">`InvokeAsync` calls methods on the hub.</span></span> <span data-ttu-id="24624-156">Übergeben Sie den Namen des Hubs-Methode und alle Argumente, die in die hubmethode, die definiert `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="24624-156">Pass the hub method name and any arguments defined in the hub method to `InvokeAsync`.</span></span> <span data-ttu-id="24624-157">SignalR ist asynchron, verwenden Sie daher `async` und `await` bei den aufrufen.</span><span class="sxs-lookup"><span data-stu-id="24624-157">SignalR is asynchronous, so use `async` and `await` when making the calls.</span></span>

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

<span data-ttu-id="24624-158">Die `InvokeAsync` Methode gibt eine `Task` die abgeschlossen wird, wenn die Rückgabe der Servermethode.</span><span class="sxs-lookup"><span data-stu-id="24624-158">The `InvokeAsync` method returns a `Task` which completes when the server method returns.</span></span> <span data-ttu-id="24624-159">Der Rückgabewert, sofern vorhanden, dient als das Ergebnis der `Task`.</span><span class="sxs-lookup"><span data-stu-id="24624-159">The return value, if any, is provided as the result of the `Task`.</span></span> <span data-ttu-id="24624-160">Von der Methode auf dem Server ausgelösten Ausnahmen erzeugen eines fehlerhaften `Task`.</span><span class="sxs-lookup"><span data-stu-id="24624-160">Any exceptions thrown by the method on the server produce a faulted `Task`.</span></span> <span data-ttu-id="24624-161">Verwendung `await` Syntax für das warten, bis der Server-Methode abgeschlossen und `try...catch` Syntax, um Fehler zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="24624-161">Use `await` syntax to wait for the server method to complete and `try...catch` syntax to handle errors.</span></span>

<span data-ttu-id="24624-162">Die `SendAsync` Methode gibt eine `Task` die abgeschlossen wird, wenn die Nachricht an den Server gesendet wurde.</span><span class="sxs-lookup"><span data-stu-id="24624-162">The `SendAsync` method returns a `Task` which completes when the message has been sent to the server.</span></span> <span data-ttu-id="24624-163">Kein Rückgabewert wird bereitgestellt, da dies `Task` nicht gewartet, bis die Server-Methode abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="24624-163">No return value is provided since this `Task` doesn't wait until the server method completes.</span></span> <span data-ttu-id="24624-164">Alle Ausnahmen, die auf dem Client ausgelöst werden, beim Senden der Nachricht erzeugt einen Fehler `Task`.</span><span class="sxs-lookup"><span data-stu-id="24624-164">Any exceptions thrown on the client while sending the message produce a faulted `Task`.</span></span> <span data-ttu-id="24624-165">Verwendung `await` und `try...catch` senden Syntax zum Behandeln von Fehlern.</span><span class="sxs-lookup"><span data-stu-id="24624-165">Use `await` and `try...catch` syntax to handle send errors.</span></span>

> [!NOTE]
> <span data-ttu-id="24624-166">Bei Verwendung von Azure SignalR Service in *serverlose Modus*, Sie können nicht von einem Client hubmethoden aufrufen.</span><span class="sxs-lookup"><span data-stu-id="24624-166">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="24624-167">Weitere Informationen finden Sie unter den [SignalR Service-Dokumentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span><span class="sxs-lookup"><span data-stu-id="24624-167">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="24624-168">Rufen Sie Client-Methoden von Hub-Instanz</span><span class="sxs-lookup"><span data-stu-id="24624-168">Call client methods from hub</span></span>

<span data-ttu-id="24624-169">Definieren Sie Methoden, die der Hub Aufrufe mithilfe von `connection.On` nach der Erstellung, jedoch vor dem Starten der Verbindung.</span><span class="sxs-lookup"><span data-stu-id="24624-169">Define methods the hub calls using `connection.On` after building, but before starting the connection.</span></span>

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

<span data-ttu-id="24624-170">Der vorangehende Code in `connection.On` ausgeführt wird, wenn Sie serverseitigen Code aufruft, mit der `SendAsync` Methode.</span><span class="sxs-lookup"><span data-stu-id="24624-170">The preceding code in `connection.On` runs when server-side code calls it using the `SendAsync` method.</span></span>

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a><span data-ttu-id="24624-171">Fehlerbehandlung und Protokollierung</span><span class="sxs-lookup"><span data-stu-id="24624-171">Error handling and logging</span></span>

<span data-ttu-id="24624-172">Behandeln von Fehlern mit einem Try / Catch-Anweisung.</span><span class="sxs-lookup"><span data-stu-id="24624-172">Handle errors with a try-catch statement.</span></span> <span data-ttu-id="24624-173">Überprüfen Sie die `Exception` Objekt, um zu bestimmen, die entsprechende Aktion aus, um aufzuzeichnen, wenn ein Fehler auftritt.</span><span class="sxs-lookup"><span data-stu-id="24624-173">Inspect the `Exception` object to determine the proper action to take after an error occurs.</span></span>

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a><span data-ttu-id="24624-174">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="24624-174">Additional resources</span></span>

* [<span data-ttu-id="24624-175">Hubs</span><span class="sxs-lookup"><span data-stu-id="24624-175">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="24624-176">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="24624-176">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="24624-177">Veröffentlichen in Azure</span><span class="sxs-lookup"><span data-stu-id="24624-177">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="24624-178">Serverlose Azure SignalR Service-Dokumentation</span><span class="sxs-lookup"><span data-stu-id="24624-178">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)

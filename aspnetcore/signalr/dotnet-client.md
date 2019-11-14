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
# <a name="aspnet-core-opno-locsignalr-net-client"></a><span data-ttu-id="13567-103">ASP.net Core SignalR .NET-Client</span><span class="sxs-lookup"><span data-stu-id="13567-103">ASP.NET Core SignalR .NET Client</span></span>

<span data-ttu-id="13567-104">Mit der ASP.net Core SignalR .NET-Client Bibliothek können Sie über .net-apps mit SignalR Hubs kommunizieren.</span><span class="sxs-lookup"><span data-stu-id="13567-104">The ASP.NET Core SignalR .NET client library lets you communicate with SignalR hubs from .NET apps.</span></span>

<span data-ttu-id="13567-105">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="13567-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="13567-106">Das Codebeispiel in diesem Artikel ist eine WPF-App, die den ASP.net Core SignalR .NET-Client verwendet.</span><span class="sxs-lookup"><span data-stu-id="13567-106">The code sample in this article is a WPF app that uses the ASP.NET Core SignalR .NET client.</span></span>

## <a name="install-the-opno-locsignalr-net-client-package"></a><span data-ttu-id="13567-107">Installieren des SignalR .NET-Client Pakets</span><span class="sxs-lookup"><span data-stu-id="13567-107">Install the SignalR .NET client package</span></span>

<span data-ttu-id="13567-108">[Microsoft. aspnetcore.SignalR. Das Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) Paket ist erforderlich, damit .NET-Clients eine Verbindung mit SignalR Hubs herstellen können.</span><span class="sxs-lookup"><span data-stu-id="13567-108">The [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package is required for .NET clients to connect to SignalR hubs.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="13567-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="13567-109">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="13567-110">Um die Client Bibliothek zu installieren, führen Sie den folgenden Befehl im Fenster **Paket-Manager-Konsole** aus:</span><span class="sxs-lookup"><span data-stu-id="13567-110">To install the client library, run the following command in the **Package Manager Console** window:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="13567-111">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="13567-111">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="13567-112">Um die Client Bibliothek zu installieren, führen Sie den folgenden Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="13567-112">To install the client library, run the following command in a command shell:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="connect-to-a-hub"></a><span data-ttu-id="13567-113">Herstellen einer Verbindung mit einem Hub</span><span class="sxs-lookup"><span data-stu-id="13567-113">Connect to a hub</span></span>

<span data-ttu-id="13567-114">Erstellen Sie eine `HubConnectionBuilder`, und rufen Sie `Build`auf, um eine Verbindung herzustellen.</span><span class="sxs-lookup"><span data-stu-id="13567-114">To establish a connection, create a `HubConnectionBuilder` and call `Build`.</span></span> <span data-ttu-id="13567-115">Die Hub-URL, das Protokoll, der Transporttyp, die Protokollebene, Header und andere Optionen können beim Herstellen einer Verbindung konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="13567-115">The hub URL, protocol, transport type, log level, headers, and other options can be configured while building a connection.</span></span> <span data-ttu-id="13567-116">Konfigurieren Sie alle erforderlichen Optionen, indem Sie eine der `HubConnectionBuilder` Methoden in `Build`einfügen.</span><span class="sxs-lookup"><span data-stu-id="13567-116">Configure any required options by inserting any of the `HubConnectionBuilder` methods into `Build`.</span></span> <span data-ttu-id="13567-117">Starten Sie die Verbindung mit `StartAsync`.</span><span class="sxs-lookup"><span data-stu-id="13567-117">Start the connection with `StartAsync`.</span></span>

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a><span data-ttu-id="13567-118">Handle für verlorene Verbindung</span><span class="sxs-lookup"><span data-stu-id="13567-118">Handle lost connection</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="13567-119">Automatisch wiederherstellen der Verbindung</span><span class="sxs-lookup"><span data-stu-id="13567-119">Automatically reconnect</span></span>

<span data-ttu-id="13567-120">Der <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection> kann so konfiguriert werden, dass er mit der `WithAutomaticReconnect`-Methode auf dem <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>automatisch erneut eine Verbindung herstellt.</span><span class="sxs-lookup"><span data-stu-id="13567-120">The <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection> can be configured to automatically reconnect using the `WithAutomaticReconnect` method on the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="13567-121">Standardmäßig wird die Verbindung nicht automatisch wieder hergestellt.</span><span class="sxs-lookup"><span data-stu-id="13567-121">It won't automatically reconnect by default.</span></span>

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect()
    .Build();
```

<span data-ttu-id="13567-122">Ohne Parameter konfiguriert `WithAutomaticReconnect()` den Client so, dass 0, 2, 10 und 30 Sekunden gewartet wird, bevor versucht wird, jeden erneuten Verbindungsversuch durchzusetzen, wobei nach vier fehlgeschlagenen Versuchen angehalten wird.</span><span class="sxs-lookup"><span data-stu-id="13567-122">Without any parameters, `WithAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="13567-123">Vor dem Starten der erneuten Verbindungsversuche wechselt der `HubConnection` in den `HubConnectionState.Reconnecting`-Zustand und lösen das `Reconnecting`-Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="13567-123">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire the `Reconnecting` event.</span></span>  <span data-ttu-id="13567-124">Dies bietet die Möglichkeit, die Benutzer zu warnen, dass die Verbindung unterbrochen wurde, und Benutzeroberflächen Elemente zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="13567-124">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span> <span data-ttu-id="13567-125">Nicht interaktive Apps können Nachrichten in die Warteschlange einreihen oder löschen.</span><span class="sxs-lookup"><span data-stu-id="13567-125">Non-interactive apps can start queuing or dropping messages.</span></span>

```csharp
connection.Reconnecting += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Reconnecting);

    // Notify users the connection was lost and the client is reconnecting.
    // Start queuing or dropping messages.

    return Task.CompletedTask;
};
```

<span data-ttu-id="13567-126">Wenn der Client innerhalb der ersten vier Versuche erfolgreich erneut eine Verbindung herstellt, wechselt der `HubConnection` zurück in den `Connected` Zustand und löst das `Reconnected` Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="13567-126">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire the `Reconnected` event.</span></span> <span data-ttu-id="13567-127">Dies bietet die Möglichkeit, den Benutzern mitzuteilen, dass die Verbindung wieder hergestellt wurde, und alle Nachrichten in der Warteschlange aus der Warteschlange entfernt</span><span class="sxs-lookup"><span data-stu-id="13567-127">This provides an opportunity to inform users the connection has been reestablished and dequeue any queued messages.</span></span>

<span data-ttu-id="13567-128">Da die Verbindung mit dem Server vollständig neu ist, wird der `Reconnected` Ereignis Handlern eine neue `ConnectionId` bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="13567-128">Since the connection looks entirely new to the server, a new `ConnectionId` will be provided to the `Reconnected` event handlers.</span></span>

> [!WARNING]
> <span data-ttu-id="13567-129">Der `connectionId` Parameter des `Reconnected` Ereignis Handlers ist NULL, wenn die `HubConnection` zum über [springen der Aushandlung](xref:signalr/configuration#configure-client-options)konfiguriert wurde.</span><span class="sxs-lookup"><span data-stu-id="13567-129">The `Reconnected` event handler's `connectionId` parameter will be null if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```csharp
connection.Reconnected += connectionId =>
{
    Debug.Assert(connection.State == HubConnectionState.Connected);

    // Notify users the connection was reestablished.
    // Start dequeuing messages queued while reconnecting if any.

    return Task.CompletedTask;
};
```

<span data-ttu-id="13567-130">`WithAutomaticReconnect()` die `HubConnection` nicht so konfigurieren, dass anfängliche Start Fehler erneut auftreten, müssen Start Fehler manuell behandelt werden:</span><span class="sxs-lookup"><span data-stu-id="13567-130">`WithAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

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

<span data-ttu-id="13567-131">Wenn der Client nicht innerhalb der ersten vier Versuche erneut eine Verbindung herstellt, wechselt der `HubConnection` in den `Disconnected` Zustand und auslöst das <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> Ereignis.</span><span class="sxs-lookup"><span data-stu-id="13567-131">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> event.</span></span> <span data-ttu-id="13567-132">Auf diese Weise können Sie versuchen, die Verbindung manuell neu zu starten oder den Benutzern mitzuteilen, dass die Verbindung dauerhaft verloren gegangen ist.</span><span class="sxs-lookup"><span data-stu-id="13567-132">This provides an opportunity to attempt to restart the connection manually or inform users the connection has been permanently lost.</span></span>

```csharp
connection.Closed += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Disconnected);

    // Notify users the connection has been closed or manually try to restart the connection.

    return Task.CompletedTask;
};
```

<span data-ttu-id="13567-133">Um eine benutzerdefinierte Anzahl von Wiederholungs versuchen zu konfigurieren, bevor die Verbindung getrennt wird, oder um die Zeit für die Wiederherstellung der Verbindung zu ändern, nimmt `WithAutomaticReconnect` ein Array von Zahlen an, das die Wartezeit in Millisekunden angibt, die vor dem Start der einzelnen Wiederholungs Versuche</span><span class="sxs-lookup"><span data-stu-id="13567-133">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `WithAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.Zero, TimeSpan.FromSeconds(10) })
    .Build();

    // .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.FromSeconds(2), TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(30) }) yields the default behavior.
```

<span data-ttu-id="13567-134">Im vorherigen Beispiel wird der `HubConnection` so konfiguriert, dass nach dem Verlust der Verbindung erneut versucht wird, die Verbindung herzustellen.</span><span class="sxs-lookup"><span data-stu-id="13567-134">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="13567-135">Dies gilt auch für die Standardkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="13567-135">This is also true for the default configuration.</span></span>

<span data-ttu-id="13567-136">Wenn der erste Wiederholungsversuch fehlschlägt, wird der zweite Wiederholungsversuch auch sofort gestartet, anstatt zwei Sekunden zu warten, wie in der Standardkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="13567-136">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="13567-137">Wenn der zweite Wiederholungsversuch fehlschlägt, wird der dritte Versuch der erneuten Verbindungs Herstellung in 10 Sekunden gestartet. Dies entspricht der Standardkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="13567-137">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="13567-138">Das benutzerdefinierte Verhalten weicht dann erneut vom Standardverhalten ab, indem nach dem dritten Wiederholungsversuch der Verbindung angehalten wird.</span><span class="sxs-lookup"><span data-stu-id="13567-138">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure.</span></span> <span data-ttu-id="13567-139">In der Standardkonfiguration gibt es einen weiteren erneuten Verbindungsversuch in weiteren 30 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="13567-139">In the default configuration there would be one more reconnect attempt in another 30 seconds.</span></span>

<span data-ttu-id="13567-140">Wenn Sie noch mehr Kontrolle über die zeitliche Steuerung und die Anzahl der automatischen Wiederholungs Versuche benötigen, akzeptiert `WithAutomaticReconnect` ein Objekt, das die `IRetryPolicy`-Schnittstelle implementiert, die über eine einzige Methode mit dem Namen `NextRetryDelay`verfügt.</span><span class="sxs-lookup"><span data-stu-id="13567-140">If you want even more control over the timing and number of automatic reconnect attempts, `WithAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `NextRetryDelay`.</span></span>

<span data-ttu-id="13567-141">`NextRetryDelay` nimmt ein einzelnes Argument mit dem Typ `RetryContext`.</span><span class="sxs-lookup"><span data-stu-id="13567-141">`NextRetryDelay` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="13567-142">Der `RetryContext` verfügt über drei Eigenschaften: `PreviousRetryCount`, `ElapsedTime` und `RetryReason` die eine `long`, eine `TimeSpan` bzw. eine `Exception` sind.</span><span class="sxs-lookup"><span data-stu-id="13567-142">The `RetryContext` has three properties: `PreviousRetryCount`, `ElapsedTime` and `RetryReason` which are a `long`, a `TimeSpan` and an `Exception` respectively.</span></span> <span data-ttu-id="13567-143">Vor dem ersten Versuch der erneuten Verbindungs Herstellung sind sowohl `PreviousRetryCount` als auch `ElapsedTime` NULL, und die `RetryReason` ist die Ausnahme, die bewirkt hat, dass die Verbindung unterbrochen wurde.</span><span class="sxs-lookup"><span data-stu-id="13567-143">Before the first reconnect attempt, both `PreviousRetryCount` and `ElapsedTime` will be zero, and the `RetryReason` will be the Exception that caused the connection to be lost.</span></span> <span data-ttu-id="13567-144">Nach jedem fehlgeschlagenen Wiederholungsversuch werden `PreviousRetryCount` um eins inkrementiert. `ElapsedTime` wird aktualisiert, um die Zeitspanne für eine erneute Verbindungs Herstellung widerzuspiegeln, und die `RetryReason` ist die Ausnahme, die bewirkt hat, dass der letzte Wiederholungsversuch fehlgeschlagen ist.</span><span class="sxs-lookup"><span data-stu-id="13567-144">After each failed retry attempt, `PreviousRetryCount` will be incremented by one, `ElapsedTime` will be updated to reflect the amount of time spent reconnecting so far, and the `RetryReason` will be the Exception that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="13567-145">`NextRetryDelay` müssen entweder einen TimeSpan-Wert zurückgeben, der die Zeit angibt, die gewartet werden soll, bevor der nächste Verbindungs `null` Versuch wieder hergestellt `HubConnection` wird.</span><span class="sxs-lookup"><span data-stu-id="13567-145">`NextRetryDelay` must return either a TimeSpan representing the time to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

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

<span data-ttu-id="13567-146">Alternativ dazu können Sie auch Code schreiben, mit dem der Client manuell erneut eine Verbindung herstellt, wie in [Manuelles Wiederherstellen der Verbindung](#manually-reconnect)gezeigt.</span><span class="sxs-lookup"><span data-stu-id="13567-146">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="13567-147">Verbindung manuell wiederherstellen</span><span class="sxs-lookup"><span data-stu-id="13567-147">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="13567-148">Vor 3,0 wird die Verbindung des .NET-Clients für SignalR nicht automatisch wieder hergestellt.</span><span class="sxs-lookup"><span data-stu-id="13567-148">Prior to 3.0, the .NET client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="13567-149">Sie müssen Code schreiben, mit dem der Client manuell erneut eine Verbindung herstellt.</span><span class="sxs-lookup"><span data-stu-id="13567-149">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="13567-150">Verwenden Sie das <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed>-Ereignis, um auf eine verlorene Verbindung zu reagieren.</span><span class="sxs-lookup"><span data-stu-id="13567-150">Use the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> event to respond to a lost connection.</span></span> <span data-ttu-id="13567-151">Beispielsweise möchten Sie möglicherweise die erneute Verbindung automatisieren.</span><span class="sxs-lookup"><span data-stu-id="13567-151">For example, you might want to automate reconnection.</span></span>

<span data-ttu-id="13567-152">Das `Closed`-Ereignis erfordert einen Delegaten, der eine `Task`zurückgibt, die das Ausführen von asynchronem Code ohne Verwendung von `async void`ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="13567-152">The `Closed` event requires a delegate that returns a `Task`, which allows async code to run without using `async void`.</span></span> <span data-ttu-id="13567-153">Um die Delegatsignatur in einem `Closed` Ereignishandler zu erfüllen, der synchron ausgeführt wird, geben Sie `Task.CompletedTask`zurück:</span><span class="sxs-lookup"><span data-stu-id="13567-153">To satisfy the delegate signature in a `Closed` event handler that runs synchronously, return `Task.CompletedTask`:</span></span>

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

<span data-ttu-id="13567-154">Der Hauptgrund für die Async-Unterstützung ist, dass Sie die Verbindung neu starten können.</span><span class="sxs-lookup"><span data-stu-id="13567-154">The main reason for the async support is so you can restart the connection.</span></span> <span data-ttu-id="13567-155">Das Starten einer Verbindung ist eine asynchrone Aktion.</span><span class="sxs-lookup"><span data-stu-id="13567-155">Starting a connection is an async action.</span></span>

<span data-ttu-id="13567-156">In einem `Closed` Handler, der die Verbindung neu startet, sollten Sie auf eine zufällige Verzögerung warten, um zu verhindern, dass der Server überladen wird, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="13567-156">In a `Closed` handler that restarts the connection, consider waiting for some random delay to prevent overloading the server, as shown in the following example:</span></span>

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="13567-157">Hub-Methoden vom Client abrufen</span><span class="sxs-lookup"><span data-stu-id="13567-157">Call hub methods from client</span></span>

<span data-ttu-id="13567-158">`InvokeAsync` ruft Methoden auf dem Hub auf.</span><span class="sxs-lookup"><span data-stu-id="13567-158">`InvokeAsync` calls methods on the hub.</span></span> <span data-ttu-id="13567-159">Übergeben Sie den Namen der Hub-Methode und alle in der Hub-Methode definierten Argumente an `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="13567-159">Pass the hub method name and any arguments defined in the hub method to `InvokeAsync`.</span></span> SignalR<span data-ttu-id="13567-160"> asynchron ist, verwenden Sie beim Ausführen der Aufrufe `async` und `await`.</span><span class="sxs-lookup"><span data-stu-id="13567-160"> is asynchronous, so use `async` and `await` when making the calls.</span></span>

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

<span data-ttu-id="13567-161">Die `InvokeAsync`-Methode gibt eine `Task` zurück, die abgeschlossen wird, wenn die Server Methode zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="13567-161">The `InvokeAsync` method returns a `Task` which completes when the server method returns.</span></span> <span data-ttu-id="13567-162">Der Rückgabewert wird ggf. als Ergebnis des `Task`bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="13567-162">The return value, if any, is provided as the result of the `Task`.</span></span> <span data-ttu-id="13567-163">Alle Ausnahmen, die von der-Methode auf dem Server ausgelöst werden, führen zu einem Fehler `Task`.</span><span class="sxs-lookup"><span data-stu-id="13567-163">Any exceptions thrown by the method on the server produce a faulted `Task`.</span></span> <span data-ttu-id="13567-164">Verwenden Sie `await`-Syntax, um auf den Abschluss der Server Methode zu warten, und `try...catch` Syntax, um Fehler zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="13567-164">Use `await` syntax to wait for the server method to complete and `try...catch` syntax to handle errors.</span></span>

<span data-ttu-id="13567-165">Die `SendAsync`-Methode gibt eine `Task` zurück, die abgeschlossen wird, wenn die Nachricht an den Server gesendet wurde.</span><span class="sxs-lookup"><span data-stu-id="13567-165">The `SendAsync` method returns a `Task` which completes when the message has been sent to the server.</span></span> <span data-ttu-id="13567-166">Es wird kein Rückgabewert bereitgestellt, da dieser `Task` nicht wartet, bis die Server Methode abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="13567-166">No return value is provided since this `Task` doesn't wait until the server method completes.</span></span> <span data-ttu-id="13567-167">Alle Ausnahmen, die beim Senden der Nachricht auf dem Client ausgelöst werden, führen zu einem Fehler `Task`.</span><span class="sxs-lookup"><span data-stu-id="13567-167">Any exceptions thrown on the client while sending the message produce a faulted `Task`.</span></span> <span data-ttu-id="13567-168">Verwenden Sie `await`-und `try...catch`-Syntax, um Sende Fehler zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="13567-168">Use `await` and `try...catch` syntax to handle send errors.</span></span>

> [!NOTE]
> <span data-ttu-id="13567-169">Wenn Sie Azure SignalR Service im *Server losen Modus*verwenden, können Sie keine hubmethoden von einem Client aus abrufen.</span><span class="sxs-lookup"><span data-stu-id="13567-169">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="13567-170">Weitere Informationen finden Sie in der [Dokumentation zumSignalR-Dienst](/azure/azure-signalr/signalr-concept-serverless-development-config).</span><span class="sxs-lookup"><span data-stu-id="13567-170">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="13567-171">Client Methoden aus Hub abrufen</span><span class="sxs-lookup"><span data-stu-id="13567-171">Call client methods from hub</span></span>

<span data-ttu-id="13567-172">Definieren von Methoden, die der Hub mithilfe von `connection.On` nach dem Aufbau, aber vor dem Starten der Verbindung aufruft.</span><span class="sxs-lookup"><span data-stu-id="13567-172">Define methods the hub calls using `connection.On` after building, but before starting the connection.</span></span>

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

<span data-ttu-id="13567-173">Der vorangehende Code in `connection.On` wird ausgeführt, wenn der serverseitige Code ihn mit der `SendAsync`-Methode aufruft.</span><span class="sxs-lookup"><span data-stu-id="13567-173">The preceding code in `connection.On` runs when server-side code calls it using the `SendAsync` method.</span></span>

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a><span data-ttu-id="13567-174">Fehlerbehandlung und Protokollierung</span><span class="sxs-lookup"><span data-stu-id="13567-174">Error handling and logging</span></span>

<span data-ttu-id="13567-175">Behandeln Sie Fehler mit einer try-catch-Anweisung.</span><span class="sxs-lookup"><span data-stu-id="13567-175">Handle errors with a try-catch statement.</span></span> <span data-ttu-id="13567-176">Überprüfen Sie das `Exception` Objekt, um die ordnungsgemäße Aktion zu ermitteln, die nach einem Fehler ausgeführt werden soll.</span><span class="sxs-lookup"><span data-stu-id="13567-176">Inspect the `Exception` object to determine the proper action to take after an error occurs.</span></span>

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a><span data-ttu-id="13567-177">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="13567-177">Additional resources</span></span>

* [<span data-ttu-id="13567-178">Hubs</span><span class="sxs-lookup"><span data-stu-id="13567-178">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="13567-179">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="13567-179">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="13567-180">Veröffentlichen in Azure</span><span class="sxs-lookup"><span data-stu-id="13567-180">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* <span data-ttu-id="13567-181">[Server lose Dokumentation zu Azure SignalR Service](/azure/azure-signalr/signalr-concept-serverless-development-config)</span><span class="sxs-lookup"><span data-stu-id="13567-181">[Azure SignalR Service serverless documentation](/azure/azure-signalr/signalr-concept-serverless-development-config)</span></span>

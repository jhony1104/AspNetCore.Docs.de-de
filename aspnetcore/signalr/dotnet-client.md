---
title: ASP.NET Core SignalR .NET Client
author: bradygaster
description: Informationen zum ASP.NET Core SignalR .NET Client
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/17/2019
uid: signalr/dotnet-client
ms.openlocfilehash: b59af0f9c84a008f778709970dba2273abdfcd4f
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087713"
---
# <a name="aspnet-core-signalr-net-client"></a><span data-ttu-id="793ec-103">ASP.NET Core SignalR .NET Client</span><span class="sxs-lookup"><span data-stu-id="793ec-103">ASP.NET Core SignalR .NET Client</span></span>

<span data-ttu-id="793ec-104">Der ASP.NET Core SignalR .NET Client Library können Sie die Kommunikation mit SignalR-Hubs in .NET apps.</span><span class="sxs-lookup"><span data-stu-id="793ec-104">The ASP.NET Core SignalR .NET client library lets you communicate with SignalR hubs from .NET apps.</span></span>

<span data-ttu-id="793ec-105">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="793ec-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="793ec-106">Das Codebeispiel in diesem Artikel ist eine WPF-app, die ASP.NET Core SignalR .NET Client verwendet.</span><span class="sxs-lookup"><span data-stu-id="793ec-106">The code sample in this article is a WPF app that uses the ASP.NET Core SignalR .NET client.</span></span>

## <a name="install-the-signalr-net-client-package"></a><span data-ttu-id="793ec-107">Installieren Sie das SignalR .NET Client-Paket</span><span class="sxs-lookup"><span data-stu-id="793ec-107">Install the SignalR .NET client package</span></span>

<span data-ttu-id="793ec-108">Die `Microsoft.AspNetCore.SignalR.Client` für .NET-Clients zur Verbindung mit SignalR-Hubs ist ein Paket erforderlich.</span><span class="sxs-lookup"><span data-stu-id="793ec-108">The `Microsoft.AspNetCore.SignalR.Client` package is needed for .NET clients to connect to SignalR hubs.</span></span> <span data-ttu-id="793ec-109">Um die Clientbibliothek zu installieren, führen Sie den folgenden Befehl der **-Paket-Manager-Konsole** Fenster:</span><span class="sxs-lookup"><span data-stu-id="793ec-109">To install the client library, run the following command in the **Package Manager Console** window:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

## <a name="connect-to-a-hub"></a><span data-ttu-id="793ec-110">Verbinden mit einem hub</span><span class="sxs-lookup"><span data-stu-id="793ec-110">Connect to a hub</span></span>

<span data-ttu-id="793ec-111">Um eine Verbindung herzustellen, erstellen Sie eine `HubConnectionBuilder` , und rufen Sie `Build`.</span><span class="sxs-lookup"><span data-stu-id="793ec-111">To establish a connection, create a `HubConnectionBuilder` and call `Build`.</span></span> <span data-ttu-id="793ec-112">Die Hub-URL, Protokoll, Transporttyp, Protokollebene, Header und anderen Optionen können beim Erstellen einer Verbindungs konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="793ec-112">The hub URL, protocol, transport type, log level, headers, and other options can be configured while building a connection.</span></span> <span data-ttu-id="793ec-113">Konfigurieren Sie alle erforderlichen Optionen durch Einfügen eines der `HubConnectionBuilder` Methoden `Build`.</span><span class="sxs-lookup"><span data-stu-id="793ec-113">Configure any required options by inserting any of the `HubConnectionBuilder` methods into `Build`.</span></span> <span data-ttu-id="793ec-114">Starten Sie die Verbindung mit `StartAsync`.</span><span class="sxs-lookup"><span data-stu-id="793ec-114">Start the connection with `StartAsync`.</span></span>

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a><span data-ttu-id="793ec-115">Behandeln Sie die Verbindung wurde unterbrochen</span><span class="sxs-lookup"><span data-stu-id="793ec-115">Handle lost connection</span></span>

<span data-ttu-id="793ec-116">Verwenden der <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> Ereignis reagieren, wenn die Verbindung wurde unterbrochen.</span><span class="sxs-lookup"><span data-stu-id="793ec-116">Use the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> event to respond to a lost connection.</span></span> <span data-ttu-id="793ec-117">Beispielsweise empfiehlt es sich zum erneuten Herstellen einer Verbindung zu automatisieren.</span><span class="sxs-lookup"><span data-stu-id="793ec-117">For example, you might want to automate reconnection.</span></span>

<span data-ttu-id="793ec-118">Die `Closed` Ereignis erfordert ein Delegat, der zurückgegeben eine `Task`, wodurch Async-Code für die Ausführung ohne Verwendung `async void`.</span><span class="sxs-lookup"><span data-stu-id="793ec-118">The `Closed` event requires a delegate that returns a `Task`, which allows async code to run without using `async void`.</span></span> <span data-ttu-id="793ec-119">Erfüllen Sie die Signatur des Delegaten in einen `Closed` -Ereignishandler, der ausgeführt wird synchron zurückgeben `Task.CompletedTask`:</span><span class="sxs-lookup"><span data-stu-id="793ec-119">To satisfy the delegate signature in a `Closed` event handler that runs synchronously, return `Task.CompletedTask`:</span></span>

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

<span data-ttu-id="793ec-120">Der Hauptgrund für die Async-Unterstützung ist, damit Sie die Verbindung neu starten können.</span><span class="sxs-lookup"><span data-stu-id="793ec-120">The main reason for the async support is so you can restart the connection.</span></span> <span data-ttu-id="793ec-121">Starten eine Verbindung ist eine asynchrone Aktion.</span><span class="sxs-lookup"><span data-stu-id="793ec-121">Starting a connection is an async action.</span></span>

<span data-ttu-id="793ec-122">In einem `Closed` Handler, der die Verbindung neu gestartet. warten Sie einige zufällige Verzögerung, um zu verhindern, Überlastung des Servers, berücksichtigen, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="793ec-122">In a `Closed` handler that restarts the connection, consider waiting for some random delay to prevent overloading the server, as shown in the following example:</span></span>

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="793ec-123">Aufrufen von Hub-Methoden von client</span><span class="sxs-lookup"><span data-stu-id="793ec-123">Call hub methods from client</span></span>

<span data-ttu-id="793ec-124">`InvokeAsync` Ruft Methoden für den Hub.</span><span class="sxs-lookup"><span data-stu-id="793ec-124">`InvokeAsync` calls methods on the hub.</span></span> <span data-ttu-id="793ec-125">Übergeben Sie den Namen des Hubs-Methode und alle Argumente, die in die hubmethode, die definiert `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="793ec-125">Pass the hub method name and any arguments defined in the hub method to `InvokeAsync`.</span></span> <span data-ttu-id="793ec-126">SignalR ist asynchron, verwenden Sie daher `async` und `await` bei den aufrufen.</span><span class="sxs-lookup"><span data-stu-id="793ec-126">SignalR is asynchronous, so use `async` and `await` when making the calls.</span></span>

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

<span data-ttu-id="793ec-127">Die `InvokeAsync` Methode gibt eine `Task` die abgeschlossen wird, wenn die Rückgabe der Servermethode.</span><span class="sxs-lookup"><span data-stu-id="793ec-127">The `InvokeAsync` method returns a `Task` which completes when the server method returns.</span></span> <span data-ttu-id="793ec-128">Der Rückgabewert, sofern vorhanden, dient als das Ergebnis der `Task`.</span><span class="sxs-lookup"><span data-stu-id="793ec-128">The return value, if any, is provided as the result of the `Task`.</span></span> <span data-ttu-id="793ec-129">Von der Methode auf dem Server ausgelösten Ausnahmen erzeugen eines fehlerhaften `Task`.</span><span class="sxs-lookup"><span data-stu-id="793ec-129">Any exceptions thrown by the method on the server produce a faulted `Task`.</span></span> <span data-ttu-id="793ec-130">Verwendung `await` Syntax für das warten, bis der Server-Methode abgeschlossen und `try...catch` Syntax, um Fehler zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="793ec-130">Use `await` syntax to wait for the server method to complete and `try...catch` syntax to handle errors.</span></span>

<span data-ttu-id="793ec-131">Die `SendAsync` Methode gibt eine `Task` die abgeschlossen wird, wenn die Nachricht an den Server gesendet wurde.</span><span class="sxs-lookup"><span data-stu-id="793ec-131">The `SendAsync` method returns a `Task` which completes when the message has been sent to the server.</span></span> <span data-ttu-id="793ec-132">Kein Rückgabewert wird bereitgestellt, da dies `Task` nicht gewartet, bis die Server-Methode abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="793ec-132">No return value is provided since this `Task` doesn't wait until the server method completes.</span></span> <span data-ttu-id="793ec-133">Alle Ausnahmen, die auf dem Client ausgelöst werden, beim Senden der Nachricht erzeugt einen Fehler `Task`.</span><span class="sxs-lookup"><span data-stu-id="793ec-133">Any exceptions thrown on the client while sending the message produce a faulted `Task`.</span></span> <span data-ttu-id="793ec-134">Verwendung `await` und `try...catch` senden Syntax zum Behandeln von Fehlern.</span><span class="sxs-lookup"><span data-stu-id="793ec-134">Use `await` and `try...catch` syntax to handle send errors.</span></span>

> [!NOTE]
> <span data-ttu-id="793ec-135">Bei Verwendung von Azure SignalR Service in *serverlose Modus*, Sie können nicht von einem Client hubmethoden aufrufen.</span><span class="sxs-lookup"><span data-stu-id="793ec-135">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="793ec-136">Weitere Informationen finden Sie unter den [SignalR Service-Dokumentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span><span class="sxs-lookup"><span data-stu-id="793ec-136">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="793ec-137">Rufen Sie Client-Methoden von Hub-Instanz</span><span class="sxs-lookup"><span data-stu-id="793ec-137">Call client methods from hub</span></span>

<span data-ttu-id="793ec-138">Definieren Sie Methoden, die der Hub Aufrufe mithilfe von `connection.On` nach der Erstellung, jedoch vor dem Starten der Verbindung.</span><span class="sxs-lookup"><span data-stu-id="793ec-138">Define methods the hub calls using `connection.On` after building, but before starting the connection.</span></span>

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

<span data-ttu-id="793ec-139">Der vorangehende Code in `connection.On` ausgeführt wird, wenn Sie serverseitigen Code aufruft, mit der `SendAsync` Methode.</span><span class="sxs-lookup"><span data-stu-id="793ec-139">The preceding code in `connection.On` runs when server-side code calls it using the `SendAsync` method.</span></span>

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a><span data-ttu-id="793ec-140">Fehlerbehandlung und Protokollierung</span><span class="sxs-lookup"><span data-stu-id="793ec-140">Error handling and logging</span></span>

<span data-ttu-id="793ec-141">Behandeln von Fehlern mit einem Try / Catch-Anweisung.</span><span class="sxs-lookup"><span data-stu-id="793ec-141">Handle errors with a try-catch statement.</span></span> <span data-ttu-id="793ec-142">Überprüfen Sie die `Exception` Objekt, um zu bestimmen, die entsprechende Aktion aus, um aufzuzeichnen, wenn ein Fehler auftritt.</span><span class="sxs-lookup"><span data-stu-id="793ec-142">Inspect the `Exception` object to determine the proper action to take after an error occurs.</span></span>

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a><span data-ttu-id="793ec-143">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="793ec-143">Additional resources</span></span>

* [<span data-ttu-id="793ec-144">Hubs</span><span class="sxs-lookup"><span data-stu-id="793ec-144">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="793ec-145">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="793ec-145">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="793ec-146">Veröffentlichen in Azure</span><span class="sxs-lookup"><span data-stu-id="793ec-146">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="793ec-147">Serverlose Azure SignalR Service-Dokumentation</span><span class="sxs-lookup"><span data-stu-id="793ec-147">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)

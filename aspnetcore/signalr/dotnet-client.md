---
title: ASP.NET Core SignalR .NET Client
author: bradygaster
description: Informationen zum ASP.NET Core SignalR .NET Client
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/17/2019
uid: signalr/dotnet-client
ms.openlocfilehash: 640d75157e42ffa6d78235c5be03e4846e8dcde9
ms.sourcegitcommit: eb784a68219b4829d8e50c8a334c38d4b94e0cfa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2019
ms.locfileid: "59982945"
---
# <a name="aspnet-core-signalr-net-client"></a><span data-ttu-id="41b22-103">ASP.NET Core SignalR .NET Client</span><span class="sxs-lookup"><span data-stu-id="41b22-103">ASP.NET Core SignalR .NET Client</span></span>

<span data-ttu-id="41b22-104">Der ASP.NET Core SignalR .NET Client Library können Sie die Kommunikation mit SignalR-Hubs in .NET apps.</span><span class="sxs-lookup"><span data-stu-id="41b22-104">The ASP.NET Core SignalR .NET client library lets you communicate with SignalR hubs from .NET apps.</span></span>

> [!NOTE]
> <span data-ttu-id="41b22-105">Xamarin stellt besondere Anforderungen an die Visual Studio-Version.</span><span class="sxs-lookup"><span data-stu-id="41b22-105">Xamarin has special requirements for Visual Studio version.</span></span> <span data-ttu-id="41b22-106">Weitere Informationen finden Sie unter [SignalR-Client 2.1.1 in Xamarin](https://github.com/aspnet/Announcements/issues/305).</span><span class="sxs-lookup"><span data-stu-id="41b22-106">For more information, see [SignalR Client 2.1.1 in Xamarin](https://github.com/aspnet/Announcements/issues/305).</span></span>

<span data-ttu-id="41b22-107">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="41b22-107">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="41b22-108">Das Codebeispiel in diesem Artikel ist eine WPF-app, die ASP.NET Core SignalR .NET Client verwendet.</span><span class="sxs-lookup"><span data-stu-id="41b22-108">The code sample in this article is a WPF app that uses the ASP.NET Core SignalR .NET client.</span></span>

## <a name="install-the-signalr-net-client-package"></a><span data-ttu-id="41b22-109">Installieren Sie das SignalR .NET Client-Paket</span><span class="sxs-lookup"><span data-stu-id="41b22-109">Install the SignalR .NET client package</span></span>

<span data-ttu-id="41b22-110">Die `Microsoft.AspNetCore.SignalR.Client` für .NET-Clients zur Verbindung mit SignalR-Hubs ist ein Paket erforderlich.</span><span class="sxs-lookup"><span data-stu-id="41b22-110">The `Microsoft.AspNetCore.SignalR.Client` package is needed for .NET clients to connect to SignalR hubs.</span></span> <span data-ttu-id="41b22-111">Um die Clientbibliothek zu installieren, führen Sie den folgenden Befehl der **-Paket-Manager-Konsole** Fenster:</span><span class="sxs-lookup"><span data-stu-id="41b22-111">To install the client library, run the following command in the **Package Manager Console** window:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

## <a name="connect-to-a-hub"></a><span data-ttu-id="41b22-112">Verbinden mit einem hub</span><span class="sxs-lookup"><span data-stu-id="41b22-112">Connect to a hub</span></span>

<span data-ttu-id="41b22-113">Um eine Verbindung herzustellen, erstellen Sie eine `HubConnectionBuilder` , und rufen Sie `Build`.</span><span class="sxs-lookup"><span data-stu-id="41b22-113">To establish a connection, create a `HubConnectionBuilder` and call `Build`.</span></span> <span data-ttu-id="41b22-114">Die Hub-URL, Protokoll, Transporttyp, Protokollebene, Header und anderen Optionen können beim Erstellen einer Verbindungs konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="41b22-114">The hub URL, protocol, transport type, log level, headers, and other options can be configured while building a connection.</span></span> <span data-ttu-id="41b22-115">Konfigurieren Sie alle erforderlichen Optionen durch Einfügen eines der `HubConnectionBuilder` Methoden `Build`.</span><span class="sxs-lookup"><span data-stu-id="41b22-115">Configure any required options by inserting any of the `HubConnectionBuilder` methods into `Build`.</span></span> <span data-ttu-id="41b22-116">Starten Sie die Verbindung mit `StartAsync`.</span><span class="sxs-lookup"><span data-stu-id="41b22-116">Start the connection with `StartAsync`.</span></span>

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a><span data-ttu-id="41b22-117">Behandeln Sie die Verbindung wurde unterbrochen</span><span class="sxs-lookup"><span data-stu-id="41b22-117">Handle lost connection</span></span>

<span data-ttu-id="41b22-118">Verwenden der <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> Ereignis reagieren, wenn die Verbindung wurde unterbrochen.</span><span class="sxs-lookup"><span data-stu-id="41b22-118">Use the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> event to respond to a lost connection.</span></span> <span data-ttu-id="41b22-119">Beispielsweise empfiehlt es sich zum erneuten Herstellen einer Verbindung zu automatisieren.</span><span class="sxs-lookup"><span data-stu-id="41b22-119">For example, you might want to automate reconnection.</span></span>

<span data-ttu-id="41b22-120">Die `Closed` Ereignis erfordert ein Delegat, der zurückgegeben eine `Task`, wodurch Async-Code für die Ausführung ohne Verwendung `async void`.</span><span class="sxs-lookup"><span data-stu-id="41b22-120">The `Closed` event requires a delegate that returns a `Task`, which allows async code to run without using `async void`.</span></span> <span data-ttu-id="41b22-121">Erfüllen Sie die Signatur des Delegaten in einen `Closed` -Ereignishandler, der ausgeführt wird synchron zurückgeben `Task.CompletedTask`:</span><span class="sxs-lookup"><span data-stu-id="41b22-121">To satisfy the delegate signature in a `Closed` event handler that runs synchronously, return `Task.CompletedTask`:</span></span>

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

<span data-ttu-id="41b22-122">Der Hauptgrund für die Async-Unterstützung ist, damit Sie die Verbindung neu starten können.</span><span class="sxs-lookup"><span data-stu-id="41b22-122">The main reason for the async support is so you can restart the connection.</span></span> <span data-ttu-id="41b22-123">Starten eine Verbindung ist eine asynchrone Aktion.</span><span class="sxs-lookup"><span data-stu-id="41b22-123">Starting a connection is an async action.</span></span>

<span data-ttu-id="41b22-124">In einem `Closed` Handler, der die Verbindung neu gestartet. warten Sie einige zufällige Verzögerung, um zu verhindern, Überlastung des Servers, berücksichtigen, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="41b22-124">In a `Closed` handler that restarts the connection, consider waiting for some random delay to prevent overloading the server, as shown in the following example:</span></span>

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="41b22-125">Aufrufen von Hub-Methoden von client</span><span class="sxs-lookup"><span data-stu-id="41b22-125">Call hub methods from client</span></span>

<span data-ttu-id="41b22-126">`InvokeAsync` Ruft Methoden für den Hub.</span><span class="sxs-lookup"><span data-stu-id="41b22-126">`InvokeAsync` calls methods on the hub.</span></span> <span data-ttu-id="41b22-127">Übergeben Sie den Namen des Hubs-Methode und alle Argumente, die in die hubmethode, die definiert `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="41b22-127">Pass the hub method name and any arguments defined in the hub method to `InvokeAsync`.</span></span> <span data-ttu-id="41b22-128">SignalR ist asynchron, verwenden Sie daher `async` und `await` bei den aufrufen.</span><span class="sxs-lookup"><span data-stu-id="41b22-128">SignalR is asynchronous, so use `async` and `await` when making the calls.</span></span>

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

<span data-ttu-id="41b22-129">Die `InvokeAsync` Methode gibt eine `Task` die abgeschlossen wird, wenn die Rückgabe der Servermethode.</span><span class="sxs-lookup"><span data-stu-id="41b22-129">The `InvokeAsync` method returns a `Task` which completes when the server method returns.</span></span> <span data-ttu-id="41b22-130">Der Rückgabewert, sofern vorhanden, dient als das Ergebnis der `Task`.</span><span class="sxs-lookup"><span data-stu-id="41b22-130">The return value, if any, is provided as the result of the `Task`.</span></span> <span data-ttu-id="41b22-131">Von der Methode auf dem Server ausgelösten Ausnahmen erzeugen eines fehlerhaften `Task`.</span><span class="sxs-lookup"><span data-stu-id="41b22-131">Any exceptions thrown by the method on the server produce a faulted `Task`.</span></span> <span data-ttu-id="41b22-132">Verwendung `await` Syntax für das warten, bis der Server-Methode abgeschlossen und `try...catch` Syntax, um Fehler zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="41b22-132">Use `await` syntax to wait for the server method to complete and `try...catch` syntax to handle errors.</span></span>

<span data-ttu-id="41b22-133">Die `SendAsync` Methode gibt eine `Task` die abgeschlossen wird, wenn die Nachricht an den Server gesendet wurde.</span><span class="sxs-lookup"><span data-stu-id="41b22-133">The `SendAsync` method returns a `Task` which completes when the message has been sent to the server.</span></span> <span data-ttu-id="41b22-134">Kein Rückgabewert wird bereitgestellt, da dies `Task` nicht gewartet, bis die Server-Methode abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="41b22-134">No return value is provided since this `Task` doesn't wait until the server method completes.</span></span> <span data-ttu-id="41b22-135">Alle Ausnahmen, die auf dem Client ausgelöst werden, beim Senden der Nachricht erzeugt einen Fehler `Task`.</span><span class="sxs-lookup"><span data-stu-id="41b22-135">Any exceptions thrown on the client while sending the message produce a faulted `Task`.</span></span> <span data-ttu-id="41b22-136">Verwendung `await` und `try...catch` senden Syntax zum Behandeln von Fehlern.</span><span class="sxs-lookup"><span data-stu-id="41b22-136">Use `await` and `try...catch` syntax to handle send errors.</span></span>

> [!NOTE]
> <span data-ttu-id="41b22-137">Bei Verwendung von Azure SignalR Service in *serverlose Modus*, Sie können nicht von einem Client hubmethoden aufrufen.</span><span class="sxs-lookup"><span data-stu-id="41b22-137">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="41b22-138">Weitere Informationen finden Sie unter den [SignalR Service-Dokumentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span><span class="sxs-lookup"><span data-stu-id="41b22-138">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="41b22-139">Rufen Sie Client-Methoden von Hub-Instanz</span><span class="sxs-lookup"><span data-stu-id="41b22-139">Call client methods from hub</span></span>

<span data-ttu-id="41b22-140">Definieren Sie Methoden, die der Hub Aufrufe mithilfe von `connection.On` nach der Erstellung, jedoch vor dem Starten der Verbindung.</span><span class="sxs-lookup"><span data-stu-id="41b22-140">Define methods the hub calls using `connection.On` after building, but before starting the connection.</span></span>

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

<span data-ttu-id="41b22-141">Der vorangehende Code in `connection.On` ausgeführt wird, wenn Sie serverseitigen Code aufruft, mit der `SendAsync` Methode.</span><span class="sxs-lookup"><span data-stu-id="41b22-141">The preceding code in `connection.On` runs when server-side code calls it using the `SendAsync` method.</span></span>

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a><span data-ttu-id="41b22-142">Fehlerbehandlung und Protokollierung</span><span class="sxs-lookup"><span data-stu-id="41b22-142">Error handling and logging</span></span>

<span data-ttu-id="41b22-143">Behandeln von Fehlern mit einem Try / Catch-Anweisung.</span><span class="sxs-lookup"><span data-stu-id="41b22-143">Handle errors with a try-catch statement.</span></span> <span data-ttu-id="41b22-144">Überprüfen Sie die `Exception` Objekt, um zu bestimmen, die entsprechende Aktion aus, um aufzuzeichnen, wenn ein Fehler auftritt.</span><span class="sxs-lookup"><span data-stu-id="41b22-144">Inspect the `Exception` object to determine the proper action to take after an error occurs.</span></span>

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a><span data-ttu-id="41b22-145">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="41b22-145">Additional resources</span></span>

* [<span data-ttu-id="41b22-146">Hubs</span><span class="sxs-lookup"><span data-stu-id="41b22-146">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="41b22-147">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="41b22-147">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="41b22-148">Veröffentlichen in Azure</span><span class="sxs-lookup"><span data-stu-id="41b22-148">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="41b22-149">Serverlose Azure SignalR Service-Dokumentation</span><span class="sxs-lookup"><span data-stu-id="41b22-149">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)

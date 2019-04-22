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
# <a name="aspnet-core-signalr-net-client"></a>ASP.NET Core SignalR .NET Client

Der ASP.NET Core SignalR .NET Client Library können Sie die Kommunikation mit SignalR-Hubs in .NET apps.

> [!NOTE]
> Xamarin stellt besondere Anforderungen an die Visual Studio-Version. Weitere Informationen finden Sie unter [SignalR-Client 2.1.1 in Xamarin](https://github.com/aspnet/Announcements/issues/305).

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

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

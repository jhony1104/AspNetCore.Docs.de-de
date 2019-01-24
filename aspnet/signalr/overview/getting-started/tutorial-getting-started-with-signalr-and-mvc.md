---
uid: signalr/overview/getting-started/tutorial-getting-started-with-signalr-and-mvc
title: 'Tutorial: Echtzeit-Chat mit SignalR 2 und MVC 5 | Microsoft-Dokumentation'
author: bradygaster
description: Dieses Tutorial veranschaulicht, wie ASP.NET SignalR 2 zu verwenden, um eine chatanwendung mit Echtzeitfunktionalität erstellen. Eine MVC 5-Anwendung hinzugefügt SignalR.
ms.author: bradyg
ms.date: 01/22/2019
ms.assetid: 80bfe5fb-bdfc-41fe-ac43-2132e5d69fac
msc.legacyurl: /signalr/overview/getting-started/tutorial-getting-started-with-signalr-and-mvc
msc.type: authoredcontent
ms.topic: tutorial
ms.openlocfilehash: 1b02aecc68a93dbd6373ca5304530e76c9d0b6b5
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837000"
---
# <a name="tutorial-real-time-chat-with-signalr-2-and-mvc-5"></a>Tutorial: Echtzeit-Chat mit SignalR 2 und MVC 5

Dieses Tutorial veranschaulicht, wie ASP.NET SignalR 2 zu verwenden, um eine chatanwendung mit Echtzeitfunktionalität erstellen. Sie SignalR zu einer MVC 5-Anwendung hinzufügen und erstellen eine Chat-Ansicht zum Senden und Nachrichten anzuzeigen.

In diesem Tutorial:

> [!div class="checklist"]
> * Einrichten des Projekts
> * Ausführen des Beispiels
> * Überprüfen Sie den code

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

## <a name="prerequisites"></a>Vorraussetzungen

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) mit der **ASP.NET und Webentwicklung** arbeitsauslastung.

## <a name="set-up-the-project"></a>Einrichten des Projekts

In diesem Abschnitt wird gezeigt, wie Sie mit Visual Studio 2017 und SignalR 2 eine leere ASP.NET MVC 5-Anwendung erstellen, fügen Sie die SignalR-Bibliothek und die chatanwendung erstellen.

1. In Visual Studio Erstellen einer c# ASP.NET-Anwendung, die auf .NET Framework 4.5 abzielt, nennen Sie sie SignalRChat, und klicken Sie auf OK.

    ![Erstellen Sie web](tutorial-getting-started-with-signalr-and-mvc/_static/image1.png)

1. In **neue ASP.NET-Webanwendung – SignalRMvcChat**Option **MVC** und wählen Sie dann **Authentifizierung ändern**.

1. In **Authentifizierung ändern**Option **keine Authentifizierung** , und klicken Sie auf **OK**.

    ![Wählen Sie keine Authentifizierung](tutorial-getting-started-with-signalr-and-mvc/_static/image2.png)

1. In **neue ASP.NET-Webanwendung – SignalRMvcChat**Option **OK**.

1. In **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, und wählen Sie **hinzufügen** > **neues Element**.

1. In **neues Element hinzufügen - SignalRChat**Option **installiert** > **Visual C#**   >  **Web**  >  **SignalR** und wählen Sie dann **SignalR-Hubklasse (v2)**.

1. Nennen Sie die Klasse *ChatHub* und fügen sie dem Projekt hinzu.

    Dieser Schritt erstellt der *ChatHub.cs* Klassendatei, und fügt einen Satz von Skriptdateien und Assemblyverweise, die dem Projekt SignalR unterstützen.

1. Ersetzen Sie den Code in der neuen *ChatHub.cs* Klassendatei mit dem folgenden Code:

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample1.cs)]

1. In **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, und wählen Sie **hinzufügen** > **Klasse**.

1. Nennen Sie die neue Klasse *Start* und fügen sie dem Projekt hinzu.

1. Ersetzen Sie den Code in die *"Startup.cs"* Klassendatei mit dem folgenden Code:

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample2.cs)]

1. In **Projektmappen-Explorer**Option **Controller** > **"HomeController.cs"**.

1. Fügen Sie diese Methode, um die *"HomeController.cs"*.

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample3.cs)]

    Diese Methode gibt die **Chat** anzeigen, die Sie in einem späteren Schritt erstellen.

1. In **Projektmappen-Explorer**, mit der rechten Maustaste **Ansichten** > **Startseite**, und wählen Sie **hinzufügen**  >    **Ansicht**.

1. In **Ansicht hinzufügen**, benennen Sie die neue Ansicht **Chat** , und wählen Sie **hinzufügen**.

1. Ersetzen Sie den Inhalt der **Chat.cshtml** durch den folgenden Code:

    [!code-cshtml[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample4.cshtml)]

1. In **Projektmappen-Explorer**, erweitern Sie **Skripts**.

    Skriptbibliotheken für jQuery und SignalR werden in das Projekt angezeigt.

    > [!IMPORTANT]
    > Der Paket-Manager möglicherweise eine höhere Version von SignalR-Skripts installiert.

1. Überprüfen Sie, dass die Skriptverweise im Codeblock auf die Versionen der Skriptdateien im Projekt entsprechen.

    Die Skriptverweise aus der ursprünglichen Codeblock:

    ```cshtml
    <!--Script references. -->
    <!--The jQuery library is required and is referenced by default in _Layout.cshtml. -->
    <!--Reference the SignalR library. -->
    <script src="~/Scripts/jquery.signalR-2.1.0.min.js"></script>
    ```

1. Wenn sie nicht übereinstimmen, aktualisieren Sie die *.cshtml* Datei.

1. Wählen Sie in der Menüleiste **Datei** > **Alles speichern**.

## <a name="run-the-sample"></a>Ausführen des Beispiels

1. In der Symbolleiste aktivieren **Skriptdebugging** und wählen Sie dann auf die entsprechende Schaltfläche, um das Beispiel im Debugmodus auszuführen.

    ![Benutzername eingeben](tutorial-getting-started-with-signalr-and-mvc/_static/image3.png)

1. Wenn der Browser geöffnet wird, geben Sie einen Namen für Ihre Chat-Identität aus.

1. Kopieren Sie die URL aus dem Browser, öffnen Sie zwei andere Browser, und fügen Sie die URLs in die Adresse leisten.

1. Geben Sie in jedem Browser einen eindeutigen Namen ein.

1. Fügen Sie nun einen Kommentar, und wählen **senden**. Wiederholen Sie, die in den anderen Browsern. Die Kommentare werden in Echtzeit angezeigt.

    > [!NOTE]
    > Diese einfache chatanwendung werden den Kontext der Diskussion auf dem Server nicht verwaltet werden. Der Hub sendet, Kommentare, um alle aktuellen Benutzer. Benutzer, die im Chat später zusammenfügen sehen, dass Nachrichten ab dem Zeitpunkt hinzugefügt werden, dass sie aufgenommen werden.

    Sehen Sie, wie der Chat-Anwendung in drei verschiedenen Browsern ausgeführt wird. Beim Tom, Anand und Susan Senden von Nachrichten von allen Browsern, die in Echtzeit aktualisiert werden:

    ![Alle drei in Browsern angezeigt, den gleichen Chatverlauf](tutorial-getting-started-with-signalr-and-mvc/_static/image4.png)

1. In **Projektmappen-Explorer**, überprüfen Sie die **Skriptdokumente** Knoten für die ausgeführte Anwendung. Es gibt eine Skriptdatei namens *Hubs* , die den SignalR-Bibliothek zur Laufzeit generiert. Diese Datei, verwaltet die Kommunikation zwischen der jQuery-Skripts und serverseitigen Code.

    ![automatisch generierte-Hubs-Skript im Knoten "Skriptdokumente"](tutorial-getting-started-with-signalr-and-mvc/_static/image5.png)

## <a name="examine-the-code"></a>Überprüfen Sie den Code

Der SignalR Chat-Anwendung veranschaulicht zwei grundlegende Aufgaben für die SignalR-Entwicklung. Es veranschaulicht einen Hub erstellen. Der Server verwendet den Hub als Haupt-Coordination-Objekt. Der Hub verwendet die SignalR-jQuery-Bibliothek zum Senden und Empfangen von Nachrichten an.

### <a name="signalr-hubs-in-the-chathubcs"></a>SignalR-Hubs in der ChatHub.cs

Im Codebeispiel das `ChatHub` Klasse leitet sich von der `Microsoft.AspNet.SignalR.Hub` Klasse. Ableiten von der `Hub` Klasse ist eine gute Möglichkeit, eine SignalR-Anwendung zu erstellen. Sie können öffentliche Methoden auf Ihrem Hub-Klasse erstellen und dann Zugriff auf diese Methoden durch einen Aufruf über Skripts auf einer Webseite.

Im Code Chat Clients rufen die `ChatHub.Send` Methode, um eine neue Nachricht zu senden. Der Hub wiederum sendet die Nachricht an alle Clients durch den Aufruf `Clients.All.addNewMessageToPage`.

Die `Send` -Methode demonstriert, mehrere Hub-Konzepte:

* Deklarieren Sie öffentliche Methoden für einen Hub, damit Clients, die sie aufrufen können.

* Verwenden der `Microsoft.AspNet.SignalR.Hub.Clients` dynamische Eigenschaft für die Kommunikation mit allen Clients, die mit diesem Hub verbunden sind.

* Aufrufen einer Funktion auf dem Client (z. B. die `addNewMessageToPage` Funktion) zum Aktualisieren von Clients.

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample5.cs)]

### <a name="signalr-and-jquery-chatcshtml"></a>SignalR und jQuery Chat.cshtml

Die *Chat.cshtml* Ansicht im Codebeispiel zeigt, wie die SignalR-jQuery-Bibliothek, die zur Kommunikation mit einer SignalR-Hub verwenden.  Der Code führt viele wichtige Aufgaben. Es erstellt einen Verweis auf den automatisch generierten Proxy für den Hub, deklariert eine Funktion, dass der Server, das Sie aufrufen kann, um Inhalt an Clients zu verteilen, und sie eine Verbindung zum Senden von Nachrichten an den Hub beginnt.

[!code-javascript[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample6.js)]

> [!NOTE]
> In JavaScript wird der Verweis auf die Server-Klasse und deren Member camelCase. Das Beispiel verweist auf die C# `ChatHub` Klasse in JavaScript als `chatHub`.

In diesem Codeblock erstellen Sie eine Callback-Funktion im Skript.

[!code-html[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample7.html)]

Die hubklasse auf dem Server ruft diese Funktion, um Inhaltsupdates per Pushvorgang an jeden Client übertragen. Der Aufruf optional die `htmlEncode` Funktion zeigt die Möglichkeit, HTML codieren von Inhalt der Nachricht vor der Anzeige auf der Seite. Es ist eine Möglichkeit zum Skript-Injection verhindern.

Dieser Code öffnet eine Verbindung mit dem Hub.

[!code-javascript[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample8.js)]

> [!NOTE]
> Dieser Ansatz wird sichergestellt, dass Sie eine Verbindung herstellen, bevor der Ereignishandler ausgeführt wird.

Der Code wird die Verbindung gestartet und leitet diese dann in eine Funktion zum Behandeln der Click-Ereignis auf der **senden** auf der Seite Chat.

## <a name="get-the-code"></a>Abrufen des Codes

[Abgeschlossenes Projekt herunterladen](http://code.msdn.microsoft.com/Getting-Started-with-c366b2f3)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

Weitere Informationen zu SignalR finden Sie in den folgenden Ressourcen:

* [SignalR-Projekt](http://signalr.net)

* [SignalR GitHub und Beispiele](https://github.com/SignalR/SignalR)

* [SignalR Wiki](https://github.com/SignalR/SignalR/wiki)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial:

> [!div class="checklist"]
> * Einrichten des Projekts
> * Das Beispiel ausgeführt
> * Überprüft den code

Wechseln Sie zum nächsten Artikel erfahren, wie eine Webanwendung erstellen, die ASP.NET SignalR 2 verwendet wird, um häufige-messaging-Funktionalität bereitzustellen.
> [!div class="nextstepaction"]
> [Web-app mit hoher Frequenz messaging](tutorial-high-frequency-realtime-with-signalr.md)
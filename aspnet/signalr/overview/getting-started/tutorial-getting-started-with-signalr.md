---
uid: signalr/overview/getting-started/tutorial-getting-started-with-signalr
title: 'Tutorial: Echtzeit-Chat mit SignalR 2 | Microsoft-Dokumentation'
author: bradygaster
description: Dieses Tutorial zeigt, wie Sie mithilfe von SignalR eine Chatanwendung mit Echtzeitfunktionalität erstellen. Eine leere ASP.NET-Webanwendung hinzugefügt SignalR.
ms.author: bradyg
ms.date: 01/22/2019
ms.assetid: a8b3b778-f009-4369-85c7-e90f9878d8b4
msc.legacyurl: /signalr/overview/getting-started/tutorial-getting-started-with-signalr
msc.type: authoredcontent
ms.topic: tutorial
ms.openlocfilehash: 90f2c03fbda522e3a46200bc0132cc74100ce70f
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836791"
---
# <a name="tutorial-real-time-chat-with-signalr-2"></a>Tutorial: Echtzeit-Chat mit SignalR 2

In diesem Tutorial erfahren Sie, wie mit SignalR eine chatanwendung mit Echtzeitfunktionalität erstellen können. Sie SignalR eine leere ASP.NET-Webanwendung hinzufügen und erstellen Sie eine HTML-Seite zum Senden und Meldungen angezeigt werden.

In diesem Tutorial:

> [!div class="checklist"]
> * Einrichten des Projekts
> * Ausführen des Beispiels
> * Überprüfen Sie den code

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

## <a name="prerequisites"></a>Vorraussetzungen

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) mit der **ASP.NET und Webentwicklung** arbeitsauslastung.

## <a name="set-up-the-project"></a>Einrichten des Projekts

Fügen Sie in diesem Abschnitt zeigt, wie Sie Visual Studio 2017 und SignalR 2 verwenden, um eine leere ASP.NET-Webanwendung erstellen SignalR hinzu, und erstellen Sie die chatanwendung.

1. Erstellen Sie eine ASP.NET-Webanwendung in Visual Studio.

    ![Erstellen Sie web](tutorial-getting-started-with-signalr/_static/image2.png)

1. In der **neues ASP.NET-Projekt – SignalRChat** Fenster verlassen **leere** ausgewählt, und wählen Sie **OK**.

1. In **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, und wählen Sie **hinzufügen** > **neues Element**.

1. In **neues Element hinzufügen - SignalRChat**Option **installiert** > **Visual C#**   >  **Web**  >  **SignalR** und wählen Sie dann **SignalR-Hubklasse (v2)**.

1. Nennen Sie die Klasse *ChatHub* und fügen sie dem Projekt hinzu.

    Dieser Schritt erstellt der *ChatHub.cs* Klassendatei, und fügt einen Satz von Skriptdateien und Assemblyverweise, die dem Projekt SignalR unterstützen.

1. Ersetzen Sie den Code in der neuen *ChatHub.cs* Klassendatei mit dem folgenden Code:

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample1.cs)]

1. In **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, und wählen Sie **hinzufügen** > **neues Element**.

1. In **neues Element hinzufügen - SignalRChat** wählen **installiert** > **Visual C#**   >  **Web** und klicken Sie dann Wählen Sie **OWIN-Startklasse**.

1. Nennen Sie die Klasse *Start* und fügen sie dem Projekt hinzu.

1. In **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, und wählen Sie **hinzufügen** > **HTML-Seite**.

1. Nennen Sie die neue Seite *Index* , und wählen Sie **OK**.

1. In **Projektmappen-Explorer**mit der rechten Maustaste auf die HTML-Seite, die Sie erstellt haben, und wählen Sie **als Startseite festlegen**.

1. Ersetzen Sie den Standardcode in der HTML-Seite mit dem folgenden Code:

    [!code-html[Main](tutorial-getting-started-with-signalr/samples/sample3.html)]

1. In **Projektmappen-Explorer**, erweitern Sie **Skripts**.

    Skriptbibliotheken für jQuery und SignalR werden in das Projekt angezeigt.

    > [!IMPORTANT]
    > Der Paket-Manager möglicherweise eine höhere Version von SignalR-Skripts installiert.

1. Überprüfen Sie, dass die Skriptverweise im Codeblock auf die Versionen der Skriptdateien im Projekt entsprechen.

    Die Skriptverweise aus der ursprünglichen Codeblock:
    ```html
    <!--Script references. -->
    <!--Reference the jQuery library. -->
    <script src="Scripts/jquery-3.1.1.min.js" ></script>
    <!--Reference the SignalR library. -->
    <script src="Scripts/jquery.signalR-2.2.1.min.js"></script>
    ```

1. Wenn sie nicht übereinstimmen, aktualisieren Sie die *.html* Datei.

1. Wählen Sie in der Menüleiste **Datei** > **Alles speichern**.

## <a name="run-the-sample"></a>Ausführen des Beispiels

1. In der Symbolleiste aktivieren **Skriptdebugging** und wählen Sie dann auf die entsprechende Schaltfläche, um das Beispiel im Debugmodus auszuführen.

    ![Benutzername eingeben](tutorial-getting-started-with-signalr/_static/image3.png)

1. Wenn der Browser geöffnet wird, geben Sie einen Namen für Ihre Chat-Identität aus.

1. Kopieren Sie die URL aus dem Browser, öffnen Sie zwei andere Browser, und fügen Sie die URLs in die Adresse leisten.

1. Geben Sie in jedem Browser einen eindeutigen Namen ein.

1. Fügen Sie nun einen Kommentar, und wählen **senden**. Wiederholen Sie, die in den anderen Browsern. Die Kommentare werden in Echtzeit angezeigt.

    > [!NOTE]
    > Diese einfache chatanwendung werden den Kontext der Diskussion auf dem Server nicht verwaltet werden. Der Hub sendet, Kommentare, um alle aktuellen Benutzer. Benutzer, die im Chat später zusammenfügen sehen, dass Nachrichten ab dem Zeitpunkt hinzugefügt werden, dass sie aufgenommen werden.

    Sehen Sie, wie der Chat-Anwendung in drei verschiedenen Browsern ausgeführt wird. Beim Tom, Anand und Susan Senden von Nachrichten von allen Browsern, die in Echtzeit aktualisiert werden:

    ![Alle drei in Browsern angezeigt, den gleichen Chatverlauf](tutorial-getting-started-with-signalr/_static/image4.png)

1. In **Projektmappen-Explorer**, überprüfen Sie die **Skriptdokumente** Knoten für die ausgeführte Anwendung. Es gibt eine Skriptdatei namens *Hubs* , die den SignalR-Bibliothek zur Laufzeit generiert. Diese Datei, verwaltet die Kommunikation zwischen der jQuery-Skripts und serverseitigen Code.

    ![automatisch generierte-Hubs-Skript im Knoten "Skriptdokumente"](tutorial-getting-started-with-signalr/_static/image5.png)

## <a name="examine-the-code"></a>Überprüfen Sie den Code

Die Anwendung SignalRChat veranschaulicht zwei grundlegende Aufgaben für die SignalR-Entwicklung. Es veranschaulicht einen Hub erstellen. Der Server verwendet den Hub als Haupt-Coordination-Objekt. Der Hub verwendet die SignalR-jQuery-Bibliothek zum Senden und Empfangen von Nachrichten an.

### <a name="signalr-hubs-in-the-chathubcs"></a>SignalR-Hubs in der ChatHub.cs

Im obigen Codebeispiel die `ChatHub` Klasse leitet sich von der `Microsoft.AspNet.SignalR.Hub` Klasse. Ableiten von der `Hub` Klasse ist eine gute Möglichkeit, eine SignalR-Anwendung zu erstellen. Sie können öffentliche Methoden auf Ihrem Hub-Klasse erstellen und dann diese Methoden durch einen Aufruf über Skripts auf einer Webseite.

Im Code Chat Clients rufen die `ChatHub.Send` Methode, um eine neue Nachricht zu senden. Der Hub sendet dann die Nachricht an alle Clients durch den Aufruf `Clients.All.broadcastMessage`.

Die `Send` -Methode demonstriert, mehrere Hub-Konzepte:

* Deklarieren Sie öffentliche Methoden für einen Hub, damit Clients, die sie aufrufen können.

* Verwenden der `Microsoft.AspNet.SignalR.Hub.Clients` dynamische Eigenschaft für die Kommunikation mit allen Clients, die mit diesem Hub verbunden sind.

* Aufrufen einer Funktion auf dem Client (z. B. die `broadcastMessage` Funktion) zum Aktualisieren von Clients.

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample4.cs)]

### <a name="signalr-and-jquery-in-the-indexhtml"></a>SignalR und jQuery in der "Index.HTML"

Die *"Index.HTML"* Seite im Codebeispiel zeigt, wie die SignalR-jQuery-Bibliothek, die zur Kommunikation mit einer SignalR-Hub verwenden. Der Code führt viele wichtige Aufgaben. Er deklariert einen Proxy für den Hub zu verweisen, deklariert eine Funktion, dass der Server, das Sie, um die pushübertragung von Inhalten an Clients aufrufen kann, und sie eine Verbindung zum Senden von Nachrichten an den Hub beginnt.

[!code-javascript[Main](tutorial-getting-started-with-signalr/samples/sample5.js)]

> [!NOTE]
> In JavaScript muss der Verweis auf die Server-Klasse und ihre Member CamelCase sein. Das Beispiel verweist auf die C# *ChatHub* Klasse in JavaScript als `chatHub`.

In diesem Codeblock erstellen Sie eine Callback-Funktion im Skript.

[!code-html[Main](tutorial-getting-started-with-signalr/samples/sample6.html)]

Die hubklasse auf dem Server ruft diese Funktion, um Inhaltsupdates per Pushvorgang an jeden Client übertragen. Die beiden Linien, HTML-Codierung der Inhalt vor der Anzeige sind optional, und zeigen eine gute Möglichkeit, Script-Injection verhindern.

Dieser Code öffnet eine Verbindung mit dem Hub.

[!code-javascript[Main](tutorial-getting-started-with-signalr/samples/sample7.js)]

> [!NOTE]
> Dieser Ansatz wird sichergestellt, dass der Code eine Verbindung hergestellt wird, bevor der Ereignishandler ausgeführt wird.

Der Code wird die Verbindung gestartet und leitet diese dann in eine Funktion zum Behandeln der Click-Ereignis auf der **senden** auf der HTML-Seite.

## <a name="get-the-code"></a>Abrufen des Codes

[Abgeschlossenes Projekt herunterladen](http://code.msdn.microsoft.com/SignalR-Getting-Started-b9d18aa9)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

Weitere Informationen zu SignalR finden Sie in den folgenden Ressourcen:

* [SignalR-Projekt](http://signalr.net)

* [SignalR Github und Beispiele](https://github.com/SignalR/SignalR)

* [SignalR Wiki](https://github.com/SignalR/SignalR/wiki)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial Sie:

> [!div class="checklist"]
> * Einrichten des Projekts
> * Das Beispiel ausgeführt
> * Überprüft den code

Wechseln Sie zum nächsten Artikel erfahren, wie SignalR und MVC 5 verwendet.
> [!div class="nextstepaction"]
> [SignalR 2 und MVC 5](tutorial-getting-started-with-signalr-and-mvc.md)
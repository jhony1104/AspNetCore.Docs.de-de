---
uid: signalr/overview/getting-started/tutorial-high-frequency-realtime-with-signalr
title: 'Tutorial: Erstellen Sie in Echtzeit hoher Frequenz-app mit SignalR 2 | Microsoft-Dokumentation'
author: pfletcher
description: Dieses Tutorial veranschaulicht, wie eine Webanwendung erstellen, die ASP.NET SignalR verwendet wird, um häufige-messaging-Funktionalität bereitzustellen.
ms.author: riande
ms.date: 01/02/2019
ms.assetid: 9f969dda-78ea-4329-b1e3-e51c02210a2b
msc.legacyurl: /signalr/overview/getting-started/tutorial-high-frequency-realtime-with-signalr
msc.type: authoredcontent
ms.topic: tutorial
ms.openlocfilehash: 85503db0b41be6f87136627667d6dd71f0d4f609
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54098589"
---
# <a name="tutorial-create-high-frequency-real-time-app-with-signalr-2"></a>Tutorial: Erstellen Sie in Echtzeit hoher Frequenz-app mit SignalR 2

Dieses Tutorial veranschaulicht, wie eine Webanwendung erstellen, die ASP.NET SignalR 2 verwendet wird, um häufige-messaging-Funktionalität bereitzustellen. In diesem Fall also "hochfrequentes messaging" sendet der Server die Updates mit einer festen Rate. Sie senden bis zu 10 Nachrichten pro Sekunde.

Die Anwendung, die Sie erstellen, zeigt eine Form, die Benutzer ziehen können. Der Server wird die Position der Form in allen verbundenen Browsern entsprechend die Position der gezogene Form mithilfe von zeitlich festgelegte Updates aktualisiert.

In diesem Tutorial eingeführte Konzepte müssen Anwendungen in Echtzeit Spiele und andere Anwendungen für die Simulation.

In diesem Tutorial haben Sie:

> [!div class="checklist"]
> * Einrichten des Projekts
> * Erstellen Sie die grundlegende Anwendung
> * Mit dem Hub zugeordnet ist, beim Starten der app
> * Fügen Sie dem Client hinzu.
> * Ausführen der App
> * Die Client-Schleife hinzufügen
> * Der Server-Schleife hinzufügen
> * Fügen Sie flüssige Animation hinzu.

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

## <a name="prerequisites"></a>Vorraussetzungen

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) mit der **ASP.NET und Webentwicklung** arbeitsauslastung.

## <a name="set-up-the-project"></a>Einrichten des Projekts

In diesem Abschnitt erstellen Sie das Projekt in Visual Studio 2017.

In diesem Abschnitt veranschaulicht, wie Visual Studio 2017 verwenden, um eine leere ASP.NET Web-Anwendung erstellen, und fügen Sie die SignalR und jQuery.UI-Bibliotheken hinzu.

1. Erstellen Sie eine ASP.NET-Webanwendung in Visual Studio.

    ![Erstellen Sie web](tutorial-high-frequency-realtime-with-signalr/_static/image1.png)

1. In der **neue ASP.NET-Webanwendung – MoveShapeDemo** Fenster verlassen **leere** ausgewählt, und wählen Sie **OK**.

1. In **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, und wählen Sie **hinzufügen** > **neues Element**.

1. In **neues Element hinzufügen - MoveShapeDemo**Option **installiert** > **Visual C#**   >  **Web**  >  **SignalR** und wählen Sie dann **SignalR-Hubklasse (v2)**.

1. Nennen Sie die Klasse *MoveShapeHub* und fügen sie dem Projekt hinzu.

    Dieser Schritt erstellt der *MoveShapeHub.cs* Klassendatei. Gleichzeitig wird eine Reihe von Skriptdateien und Assemblyverweise, die dem Projekt SignalR unterstützen.

1. Wählen Sie **Tools** > **NuGet Paket-Manager** > **Paket-Manager Konsole**.

1. In **-Paket-Manager-Konsole**, führen Sie diesen Befehl:

    ```console
    Install-Package jQuery.UI.Combined
    ```

    Der Befehl installiert die jQuery-UI-Bibliothek. Damit können Sie um die Form zu animieren.

1. In **Projektmappen-Explorer**, erweitern Sie den Knoten des Skripts.

    ![Verweisen auf](tutorial-high-frequency-realtime-with-signalr/_static/image2.png)

    Skriptbibliotheken für jQuery, jQueryUI und SignalR werden in das Projekt angezeigt.

## <a name="create-the-base-application"></a>Erstellen Sie die grundlegende Anwendung

In diesem Abschnitt erstellen Sie eine Browser-Anwendung. Die app sendet die Position der Form an den Server während der einzelnen MouseMove-Ereignis. Der Server sendet diese Informationen an alle anderen verbundenen Clients in Echtzeit. Erfahren Sie mehr über diese Anwendung in späteren Abschnitten.

1. Öffnen der *MoveShapeHub.cs* Datei.

1. Ersetzen Sie den Code in die *MoveShapeHub.cs* Datei durch den folgenden Code:

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample1.cs)]

1. Speichern Sie die Datei.

Die `MoveShapeHub` -Klasse ist eine Implementierung eines SignalR-Hubs. Wie in der [erste Schritte mit SignalR](tutorial-getting-started-with-signalr.md) Tutorial der Hub verfügt über eine Methode, die den Clients direkt aufrufen. In diesem Fall ist der Client sendet ein Objekt mit der neuen X- und Y-Koordinaten der Form auf dem Server. Diese Koordinaten erhalten haben, alle anderen verbundenen Clients übertragen. SignalR wird automatisch dieses Objekts unter Verwendung von JSON serialisiert.

Die app sendet die `ShapeModel` Objekt an den Client. Verfügt er über Member, um die Position der Form zu speichern. Die Version des Objekts auf dem Server hat auch ein Element zum Nachverfolgen des Clients die Daten gespeichert werden. Dieses Objekt wird verhindert, dass der Server sendet die Daten eines Clients an sich selbst. Dieses Element verwendet die `JsonIgnore` Attribut, um die Anwendung verhindern, dass die Daten serialisieren und das Senden an den Client zurück.

## <a name="map-to-the-hub-when-app-starts"></a>Mit dem Hub zugeordnet ist, beim Starten der app

Als Nächstes richten Sie die Zuordnung mit dem Hub beim Starten der Anwendung. In SignalR 2 wird Hinzufügen einer OWIN-Startklasse die Zuordnung aus.

1. In **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, und wählen Sie **hinzufügen** > **neues Element**.

1. In **neues Element hinzufügen - MoveShapeDemo** wählen **installiert** > **Visual C#**   >  **Web** und klicken Sie dann Wählen Sie **OWIN-Startklasse**.

1. Nennen Sie die Klasse *Start* , und wählen Sie **OK**.

1. Ersetzen Sie den Standardcode in der *"Startup.cs"* Datei durch den folgenden Code:

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample2.cs)]

Die OWIN-Startup-Klasse Aufrufe `MapSignalR` Ausführung die app die `Configuration` Methode. Die app fügt die Klasse, um OWINs-Startup Verarbeitung mit der `OwinStartup` Assembly-Attribut.

## <a name="add-the-client"></a>Fügen Sie dem Client hinzu.

Fügen Sie der HTML-Seite für den Client hinzu.

1. In **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, und wählen Sie **hinzufügen** > **HTML-Seite**.

1. Nennen Sie die Seite **Standard** , und wählen Sie **OK**.

1. In **Projektmappen-Explorer**, mit der rechten Maustaste *"default.HTML"* , und wählen Sie **als Startseite festlegen**.

1. Ersetzen Sie den Standardcode in der *"default.HTML"* Datei durch den folgenden Code:

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample3.html?highlight=14-16)]

1. In **Projektmappen-Explorer**, erweitern Sie **Skripts**.

    Skriptbibliotheken für jQuery und SignalR werden in das Projekt angezeigt.

    > [!IMPORTANT]
    > Der Paket-Manager wird installiert, eine höhere Version von SignalR-Skripts.

1. Aktualisieren Sie die Skriptverweise im Codeblock auf die Versionen der Skriptdateien im Projekt entsprechen.

Dieser HTML und JavaScript-Code erstellt ein rotes `div` namens `shape`. Sie können das Verhalten des Shapes ziehen mit der jQuery-Bibliothek und verwendet die `drag` Ereignis, um die Position der Form an den Server gesendet.

## <a name="run-the-app"></a>Ausführen der App

Sie können die app ausführen, se'e es funktioniert. Wenn Sie die Form in einem Browserfenster ziehen, verschiebt die Form zu anderen Browser.

1. In der Symbolleiste aktivieren **Skriptdebugging** und wählen Sie dann auf die entsprechende Schaltfläche, um die Anwendung im Debugmodus auszuführen.

    ![Screenshot der Benutzer das Aktivieren von debugging-Modus und Play auswählen.](tutorial-high-frequency-realtime-with-signalr/_static/image3.png)

    Ein Browserfenster wird geöffnet, mit der Form "red", in der oberen rechten Ecke.

1. Kopieren Sie die URL der Seite.

1. Öffnen Sie einen anderen Browser, und fügen Sie die URL in die Adressleiste ein.

1. Ziehen Sie die Form in einem Fenster Ihres Browsers ein. Die Form im anderen Browserfenster folgt.

Während die Anwendung Funktionen, die mit dieser Methode, es ist kein empfohlene Programmiermodell. Es gibt keine obere Grenze für die Anzahl der gesendeten Daten abrufen. Daher erhalten die Clients und Server überlastet werden mit Nachrichten und die Leistung beeinträchtigt wird. Darüber hinaus zeigt die app eine separate Animation, auf dem Client. Diese Animationen ruckartig liegt daran, dass die Form von jeder Methode sofort verschoben wird. Es ist besser, wenn die Form reibungslos auf jeder neuen Speicherort verschoben wird. Als Nächstes erfahren Sie, wie Sie diese Probleme beheben.

## <a name="add-the-client-loop"></a>Die Client-Schleife hinzufügen

Senden den Speicherort der Form auf jede MouseMove-Ereignis erstellt eine unnötige Umfang des Netzwerkverkehrs. Die app muss die Nachrichten vom Client gedrosselt.

Verwenden Sie den JavaScript-Code `setInterval` Funktion, um eine Schleife einrichten, die Informationen zur neuen Position an den Server mit einer festen Rate sendet. Diese Schleife ist eine einfache Darstellung von "game Schleife". Es ist eine wiederholt aufgerufene Funktion, die alle die Funktionalität eines Spiels steuert.

1. Ersetzen Sie den Clientcode in die *"default.HTML"* Datei durch den folgenden Code:

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample4.html?highlight=14-16)]

    > [!IMPORTANT]
    > Sie müssen die Skriptverweise erneut zu ersetzen. Sie müssen die Versionen der Skripts im Projekt übereinstimmen.

    Dieser neue Code fügt die `updateServerModel` Funktion. Es wird auf einem festen Intervall aufgerufen. Die Funktion sendet die Positionsdaten, an den Server nach der `moved` Flag gibt an, dass neue die zu sendenden Positionsdaten.

1. Wählen Sie die entsprechende Schaltfläche, um die Anwendung starten

1. Kopieren Sie die URL der Seite.

1. Öffnen Sie einen anderen Browser, und fügen Sie die URL in die Adressleiste ein.

1. Ziehen Sie die Form in einem Fenster Ihres Browsers ein. Die Form im anderen Browserfenster folgt.

Haben zunächst, da die app die Anzahl der Nachrichten, die an den Server gesendet werden können drosselt, die Animation so reibungslos nicht angezeigt werden.

## <a name="add-the-server-loop"></a>Der Server-Schleife hinzufügen

Die aktuelle Anwendung wechseln Sie in Nachrichten, die vom Server an den Client gesendet so oft, sobald sie eingetroffen sind. Dieser Netzwerkverkehr stellt ein ähnliches Problem auf, wie Sie sehen auf dem Client.

Die app kann senden Nachrichten häufiger als sie benötigt werden. Die Verbindung kann daher überflutet werden. Dieser Abschnitt beschreibt die Vorgehensweise beim Aktualisieren des Servers, um einen Timer hinzuzufügen, der die Rate der ausgehenden Nachrichten drosselt.

1. Ersetzen Sie den Inhalt der `MoveShapeHub.cs` durch den folgenden Code:

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample5.cs)]

1. Wählen Sie die entsprechende Schaltfläche, um die Anwendung zu starten.

1. Kopieren Sie die URL der Seite.

1. Öffnen Sie einen anderen Browser, und fügen Sie die URL in die Adressleiste ein.

1. Ziehen Sie die Form in einem Fenster Ihres Browsers ein.

Dieser Code dehnt der Client hinzufügen die `Broadcaster` Klasse. Die neue Klasse drosselt die ausgehenden Nachrichten unter Verwendung der `Timer` Klasse von .NET Framework.

Es ist ratsam, erfahren, dass für der eigentlichen Hub vorübergehend ist. Er wird erstellt jedes Mal, wenn er benötigt wird. Damit die app erstellt die `Broadcaster` als Singleton. Verzögerten Initialisierung für das Zurückstellen von verwendet die `Broadcaster`Erstellung, bis diese benötigt wird. Dies garantiert, dass es sich bei die app die erste hubinstanz vollständig erstellt, bevor der Timer gestartet.

Der Aufruf des Clients `UpdateShape` Funktion wird dann aus des Hubs des verschoben `UpdateModel` Methode. Sie wird nicht mehr aufgerufen, sobald die app auf eingehende Nachrichten empfängt. Stattdessen sendet die app die Nachrichten an die Clients mit einer Geschwindigkeit von 25 Aufrufe pro Sekunde. Der Prozess wird von verwaltet die `_broadcastLoop` Zeitgeber innerhalb der `Broadcaster` Klasse.

Abschließend anstelle der Methode aus dem Hub direkt die `Broadcaster` Klasse muss einen Verweis auf die gerade arbeiten `_hubContext` Hub. Er ruft den Verweis auf die `GlobalHost`.

## <a name="add-smooth-animation"></a>Fügen Sie flüssige Animation hinzu.

Die Anwendung ist fast fertig, aber stellen wir eine weitere Verbesserung. Die app verschiebt die Form auf dem Client als Antwort auf die Server-Meldungen. Anstatt die Position der Form an den neuen Speicherort, der vom Server erhalten, verwenden Sie der JQuery-UI-Bibliotheks `animate` Funktion. Sie können die Form reibungslos zwischen der aktuellen und neuen Position verschieben.

1. Aktualisieren des Clients `updateShape` -Methode in der die *"default.HTML"* Datei der hervorgehobene Code aussehen:

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample6.html?highlight=33-40)]

1. Wählen Sie die entsprechende Schaltfläche, um die Anwendung zu starten.

1. Kopieren Sie die URL der Seite.

1. Öffnen Sie einen anderen Browser, und fügen Sie die URL in die Adressleiste ein.

1. Ziehen Sie die Form in einem Fenster Ihres Browsers ein.

Weniger ruckartige, wird die Verschiebung von der Form, in dem anderen Fenster angezeigt. Die app führt ihre Bewegung über anstatt einmal pro eingehender Nachricht festgelegt wird.

Dieser Code wird die Form am alten Speicherort in das neue Projekt verschoben. Der Server gibt die Position der Form im Verlauf des Intervalls Animation an. In diesem Fall ist, die 100 Millisekunden. Die app löscht alle vorherige Animation, auf die Form ausgeführt werden, bevor die neue Animation gestartet wird.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

Das Communication-Paradigma, die Sie gerade gelernt haben, zu eignet sich für die Entwicklung von Onlinespielen und andere Simulationen, wie z. B. [ShootR Spiel erstellt, die mit SignalR](https://shootr.azurewebsites.net/).

Weitere Informationen zu SignalR finden Sie in den folgenden Ressourcen:

* [SignalR-Projekt](http://signalr.net)

* [SignalR GitHub und Beispiele](https://github.com/SignalR/SignalR)

* [SignalR-Wiki](https://github.com/SignalR/SignalR/wiki)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie:

> [!div class="checklist"]
> * Einrichten des Projekts
> * Die Basis-Anwendung erstellt
> * An den Hub zugeordnet werden, wenn die app gestartet wird
> * Den Client hinzugefügt
> * Die app ausgeführt
> * Die Client-Schleife hinzugefügt
> * Die Schleife Server hinzugefügt
> * Hinzugefügte flüssige animation

Wechseln Sie zum nächsten Artikel erfahren, wie eine Webanwendung erstellen, die ASP.NET SignalR 2 verwendet, um Server-broadcast-Funktionalität bereitzustellen.
> [!div class="nextstepaction"]
> [SignalR 2 und Server broadcasting](tutorial-server-broadcast-with-signalr.md)
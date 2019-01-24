---
uid: signalr/overview/older-versions/tutorial-high-frequency-realtime-with-signalr
title: Echtzeitnachrichten mit SignalR 1.x | Microsoft-Dokumentation
author: bradygaster
description: Dieses Tutorial veranschaulicht, wie eine Webanwendung erstellen, die ASP.NET SignalR verwendet wird, um häufige-messaging-Funktionalität bereitzustellen. Häufige Messagings in...
ms.author: bradyg
ms.date: 04/16/2013
ms.assetid: ad2a5da5-2e79-40ea-bc84-028d327f5982
msc.legacyurl: /signalr/overview/older-versions/tutorial-high-frequency-realtime-with-signalr
msc.type: authoredcontent
ms.openlocfilehash: 6df35a420a0733003808a12d065b03f08ef56dd9
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837922"
---
<a name="high-frequency-realtime-with-signalr-1x"></a>Echtzeitnachrichten mit SignalR 1.x
====================
durch [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Dieses Tutorial veranschaulicht, wie eine Webanwendung erstellen, die ASP.NET SignalR verwendet wird, um häufige-messaging-Funktionalität bereitzustellen. Häufige messaging bedeutet in diesem Fall die Updates, die mit einer festen Rate gesendet werden; Bei dieser Anwendung bis zu 10 Nachrichten pro Sekunde.
> 
> Die Anwendung, die Sie in diesem Tutorial erstellen, zeigt eine Form, die Benutzer ziehen können. Die Position der Form in allen anderen verbundenen Browsern wird dann entsprechend die Position der gezogene Form mithilfe von zeitlich festgelegte Updates aktualisiert werden.
> 
> In diesem Tutorial eingeführte Konzepte müssen Anwendungen in Echtzeit Spiele und andere Anwendungen für die Simulation.
> 
> Kommentare, die auf dem Lernprogramm sind Willkommen. Wenn Sie Fragen, die nicht direkt mit dem Tutorial verknüpft sind haben, können Sie sie veröffentlichen das [ASP.NET SignalR-Forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) oder [StackOverflow.com](http://stackoverflow.com).


## <a name="overview"></a>Übersicht

In diesem Tutorial wird veranschaulicht, wie eine Anwendung erstellen, die den Zustand eines Objekts mit anderen Browsern in Echtzeit gemeinsam verwendet wird. Die Anwendung erstellten wird MoveShape aufgerufen. Die Seite "MoveShape" wird ein HTML-Div-Element anzeigen, die der Benutzer ziehen können; der Benutzer das DIV-Element zieht, wird seiner neue Position an den Server gesendet werden, die dann alle anderen verbundenen Clients, um die Position der Form entsprechend zu aktualisieren. darüber informiert.

![Das Anwendungsfenster](tutorial-high-frequency-realtime-with-signalr/_static/image1.png)

Die Anwendung, die in diesem Tutorial erstellten basiert auf einer Demo von Damian Edwards. Ein Video, enthält diese Demo sehen [hier](https://channel9.msdn.com/Series/Building-Web-Apps-with-ASP-NET-Jump-Start/Building-Web-Apps-with-ASPNET-Jump-Start-08-Real-time-Communication-with-SignalR).

Das Lernprogramm zunächst veranschaulicht, wie SignalR-Nachrichten von jedem Ereignis zu senden, die ausgelöst wird, wie die Form gezogen wird. Jede verbundene Clients klicken Sie dann aktualisiert die Position der lokalen Version von der Form jedes Mal, die eine Nachricht empfangen wird.

Während die Anwendung mit dieser Methode verwendet werden kann, ist dies keine empfohlene Programmiermodell, da gäbe es keine obere Grenze für die Anzahl der Nachrichten, Abrufen von gesendet werden, damit die Clients und Server mit Nachrichten überlastet zu erhalten konnte, und würde die Leistung beeinträchtigen . Die angezeigten Animation, auf dem Client wäre auch reibungslos auf jeder neuen Speicherort verschieben, statt separater, da die Form von jeder Methode, sofort verschoben werden sollen. Abschnitten des Lernprogramms zeige, wie zum Erstellen einer timerfunktion, die die maximale Rate einschränkt, mit der Nachrichten vom Client oder Server gesendet werden, und wie Sie die Form reibungslos zwischen Standorten verschieben. Die endgültige Version der in diesem Lernprogramm erstellten Anwendung kann heruntergeladen werden [Code Gallery](https://code.msdn.microsoft.com/SignalR-MoveShape-demo-3366dac6).

In diesem Tutorial enthält die folgenden Abschnitte:

- [Erforderliche Komponenten](#prerequisites)
- [Erstellen des Projekts](#createtheproject)
- [Fügen Sie die ASP.NET SignalR und JQuery.UI-NuGet-Pakete](#nugetpackages)
- [Erstellen Sie die grundlegende Anwendung](#baseapp)
- [Die Client-Schleife hinzufügen](#clientloop)
- [Der Server-Schleife hinzufügen](#serverloop)
- [Fügen Sie auf dem Client flüssige Animation hinzu](#animation)
- [Weitere Schritte](#furthersteps)

<a id="prerequisites"></a>

## <a name="prerequisites"></a>Vorraussetzungen

Dieses Lernprogramm erfordert Visual Studio 2012 oder Visual Studio 2010. Wenn Visual Studio 2010 verwendet wird, wird das Projekt .NET Framework 4 anstatt des .NET Framework 4.5 verwenden.

Wenn Sie Visual Studio 2012 verwenden, wird empfohlen, dass es sich bei der Installation der [Update für ASP.NET und Web Tools 2012.2](https://go.microsoft.com/fwlink/?LinkId=282650). Dieses Update enthält neue Funktionen wie veröffentlichen, neue Funktionen, Verbesserungen und neue Vorlagen.

Wenn Sie Visual Studio 2010 haben, stellen sicher, dass [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) installiert ist.

<a id="createtheproject"></a>

## <a name="create-the-project"></a>Erstellen eines Projekts

In diesem Abschnitt erstellen wir das Projekt in Visual Studio.

1. Von der **Datei** klicken Sie im Menü **neues Projekt**.
2. In der **neues Projekt** Dialogfeld erweitern Sie **c#** unter **Vorlagen** , und wählen Sie **Web**.
3. Wählen Sie die **leere ASP.NET-Webanwendung** Vorlage, nennen Sie das Projekt *MoveShapeDemo*, und klicken Sie auf **OK**.

    ![Erstellen des neuen Projekts](tutorial-high-frequency-realtime-with-signalr/_static/image2.png)

<a id="nugetpackages"></a>

## <a name="add-the-signalr-and-jqueryui-nuget-packages"></a>Fügen Sie den SignalR und JQuery.UI NuGet-Pakete

Sie können die SignalR-Funktionen zu einem Projekt hinzufügen, durch die Installation von NuGet-Paket. In diesem Tutorial wird das JQuery.UI-Paket auch verwenden, für das Zulassen der Form gezogen und animiert werden.

1. Klicken Sie auf **Extras | NuGet Paket-Manager | Paket-Manager Konsole**.
2. Geben Sie den folgenden Befehl aus, in der Paket-Manager.

    [!code-powershell[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample1.ps1)]

    Der SignalR-Paket installiert eine Reihe von anderen NuGet-Pakete als Abhängigkeiten. Wenn die Installation abgeschlossen ist, müssen Sie alle Server und Client-Komponenten erforderlich, um die SignalR-Komponente in einer ASP.NET-Anwendung einsetzen.
3. Geben Sie den folgenden Befehl aus, in der Paket-Manager-Konsole zum Installieren der Pakete JQuery und JQuery.UI.

    [!code-powershell[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample2.ps1)]

<a id="baseapp"></a>

## <a name="create-the-base-application"></a>Erstellen Sie die grundlegende Anwendung

In diesem Abschnitt erstellen wir eine Browser-Anwendung, die den Speicherort der Form an den Server, während jede MouseMove-Ereignis sendet. Der Server überträgt klicken Sie dann diese Informationen, um alle anderen verbundenen Clients, wie sie empfangen wird. Wir müssen für diese Anwendung in späteren Abschnitten erweitern.

1. In **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, und wählen Sie **hinzufügen**, **Klasse...** . Nennen Sie die Klasse **MoveShapeHub** , und klicken Sie auf **hinzufügen**.
2. Ersetzen Sie den Code in der neuen **MoveShapeHub** Klasse durch den folgenden Code.

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample3.cs)]

    Die `MoveShapeHub` oben gezeigte Klasse ist eine Implementierung eines SignalR-Hubs. Wie in der [erste Schritte mit SignalR](index.md) Tutorial der Hub verfügt über eine Methode, die den Clients direkt aufruft. In diesem Fall sendet der Client ein Objekt mit den neuen X und Y-Koordinaten der Form auf dem Server, wodurch die haben ruft dann alle anderen verbundenen Clients übertragen. SignalR wird automatisch dieses Objekts unter Verwendung von JSON zu serialisieren.

    Das Objekt, das an den Client gesendet wird (`ShapeModel`) enthält Elemente, um die Position der Form zu speichern. Die Version des Objekts auf dem Server enthält auch ein Element zum Nachverfolgen der Clientdaten gespeichert wird, damit ein bestimmtes Clients nicht ihre eigenen Daten gesendet wird. Dieses Element verwendet die `JsonIgnore` Attribut, um zu verhindern serialisiert und an den Client gesendet.
3. Als Nächstes müssen wir den Hub einrichten, beim Starten der Anwendung. In **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, und klicken Sie auf **hinzufügen | Globale Anwendungsklasse**. Akzeptieren Sie den Namen der *Global* , und klicken Sie auf **OK**.

    ![Hinzufügen von globalen Anwendungsklasse](tutorial-high-frequency-realtime-with-signalr/_static/image3.png)
4. Fügen Sie die folgenden `using` Anweisung nach der bereitgestellten **mit** Anweisungen in der Klasse "Global.asax.cs".

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample4.cs)]
5. Fügen Sie die folgende Zeile des Codes in der `Application_Start` -Methode der globalen Klasse die Standardroute für die SignalR zu registrieren.

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample5.cs)]

    Die Datei "Global.asax" sollte wie folgt aussehen:

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample6.cs)]
6. Als Nächstes fügen wir den Client. In **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, und klicken Sie auf **hinzufügen | Neues Element**. In der **neues Element hinzufügen** wählen Sie im Dialogfeld **Html-Seite**. Geben Sie die Seite einen passenden Namen (z. B. **"default.HTML"**), und klicken Sie auf **hinzufügen**.
7. In **Projektmappen-Explorer**mit der rechten Maustaste auf die Seite, die Sie gerade erstellt haben, und klicken Sie auf **als Startseite festlegen**.
8. Ersetzen Sie den Standardcode in der HTML-Seite mit den folgenden Codeausschnitt ein.

    > [!NOTE]
    > Stellen Sie sicher, dass das Skript unten Übereinstimmung, die Pakete dem Projekt im Ordner "Scripts" hinzugefügt verweist. In Visual Studio 2010 kann die Version von JQuery und SignalR, die dem Projekt hinzugefügt, nicht die folgenden Versionsnummern überein.

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample7.html)]

    Der obige HTML und JavaScript-Code erstellt ein Rot DIV-Element wird aufgerufen, Form, können Sie das Verhalten des Shapes ziehen mit der jQuery-Bibliothek und verwendet die Form vom Typ `drag` Ereignis, um die Position der Form an den Server gesendet.
9. Starten Sie die Anwendung durch Drücken von F5. Kopieren Sie die URL der Seite, und fügen Sie ihn in ein zweites Browserfenster. Ziehen Sie die Form eines Browserfenster; die Form im anderen Browserfenster sollten verschieben.

    ![Das Anwendungsfenster](tutorial-high-frequency-realtime-with-signalr/_static/image4.png)

<a id="clientloop"></a>

## <a name="add-the-client-loop"></a>Die Client-Schleife hinzufügen

Da ein ist ggf. unnötig Umfang des Netzwerkverkehrs, senden den Speicherort der Form auf jede MouseMove-Ereignis erstellt werden wird, müssen die Nachrichten vom Client gedrosselt werden. Wir verwenden den JavaScript-Code `setInterval` Funktion, um eine Schleife einrichten, die Informationen zur neuen Position an den Server mit einer festen Rate sendet. Diese Schleife ist eine einfache Darstellung einer "spielschleife" eine wiederholt aufgerufene Funktion, die die Funktionalität eines Spiels oder einer anderen Simulation steuert.

1. Aktualisieren Sie den Clientcode in der HTML-Seite entsprechend den folgenden Codeausschnitt ein.

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample8.html)]

    Die oben genannten Update fügt die `updateServerModel` -Funktion, die einer festen Häufigkeit aufgerufen wird. Diese Funktion sendet die Positionsdaten, an den Server nach der `moved` Flag gibt an, dass neue die zu sendenden Positionsdaten.
2. Starten Sie die Anwendung durch Drücken von F5. Kopieren Sie die URL der Seite, und fügen Sie ihn in ein zweites Browserfenster. Ziehen Sie die Form eines Browserfenster; die Form im anderen Browserfenster sollten verschieben. Da die Anzahl der Nachrichten, die an den Server gesendet werden können gedrosselt werden, wird die Animation nicht so reibungslos wie im vorherigen Abschnitt angezeigt.

    ![Das Anwendungsfenster](tutorial-high-frequency-realtime-with-signalr/_static/image5.png)

<a id="serverloop"></a>

## <a name="add-the-server-loop"></a>Der Server-Schleife hinzufügen

Die aktuelle Anwendung wechseln Sie in Nachrichten, die vom Server an den Client gesendet so oft, wie sie empfangen werden. Dies stellt ein ähnliches Problem wie auf dem Client aufgetreten ist; Nachrichten können als werden bei Bedarf, und die Verbindung kann daher überflutet werden häufig gesendet werden. Dieser Abschnitt beschreibt das Aktualisieren des Servers zum Implementieren eines Zeitgebers, das die Rate der ausgehenden Nachrichten drosselt.

1. Ersetzen Sie den Inhalt der `MoveShapeHub.cs` mit dem folgenden Codeausschnitt.

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample9.cs)]

    Der obige Code dehnt der Client hinzufügen die `Broadcaster` -Klasse, die schränkt den ausgehenden Nachrichten anhand der `Timer` Klasse von .NET Framework.

    Da der Hub selbst vorübergehende ist (es wird erstellt jedes Mal, wenn er benötigt wird), die `Broadcaster` als Singleton erstellt werden. Verzögerter Initialisierung (eingeführt in .NET 4) wird zum seiner Erstellung zu verzögern, bis diese benötigt wird, um sicherzustellen, dass die erste hubinstanz vollständig erstellt wurde, bevor der Timer gestartet wird, verwendet.

    Der Aufruf des Clients `UpdateShape` Funktion wird dann aus des Hubs des verschoben `UpdateModel` Methode, sodass die It nicht mehr aufgerufen wird, sobald eingehende Nachrichten empfangen werden. Stattdessen werden die Nachrichten an die Clients gesendet werden, mit einer Geschwindigkeit von 25 Aufrufe pro Sekunde, von verwaltet die `_broadcastLoop` Zeitgeber innerhalb der `Broadcaster` Klasse.

    Abschließend anstelle der Methode aus dem Hub direkt die `Broadcaster` Klasse muss einen Verweis auf den aktuell ausgeführten Hub abrufen (`_hubContext`) mithilfe der `GlobalHost`.
2. Starten Sie die Anwendung durch Drücken von F5. Kopieren Sie die URL der Seite, und fügen Sie ihn in ein zweites Browserfenster. Ziehen Sie die Form eines Browserfenster; die Form im anderen Browserfenster sollten verschieben. Es werden nicht sichtbarer Unterschied im Browser aus dem vorherigen Abschnitt, aber die Anzahl der Nachrichten, die an den Client gesendet werden können, werden gedrosselt werden.

    ![Das Anwendungsfenster](tutorial-high-frequency-realtime-with-signalr/_static/image6.png)

<a id="animation"></a>

## <a name="add-smooth-animation-on-the-client"></a>Fügen Sie auf dem Client flüssige Animation hinzu

Die Anwendung ist fast abgeschlossen, aber stellen wir eine weitere Verbesserung, die während der Übertragung der Form auf dem Client während der Übertragung als Antwort auf die Server-Meldungen. Anstatt die Position der Form an den neuen Speicherort, der vom Server erhalten, verwenden wir der JQuery-UI-Bibliotheks `animate` Funktion, um die Form reibungslos zwischen der aktuellen und neuen Position zu verschieben.

1. Aktualisieren des Clients `updateShape` Methode, um zu sehen, wie den folgenden hervorgehobenen Code:

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample10.html?highlight=35-42)]

    Der obige Code verschiebt die Form am alten Speicherort in das neue Projekt, das vom Server im Verlauf der Animation-Intervall (in diesem Fall 100 Millisekunden) angegeben. Alle vorherige Animation, die unter der Form "wird gelöscht, bevor die neue Animation gestartet wird.
2. Starten Sie die Anwendung durch Drücken von F5. Kopieren Sie die URL der Seite, und fügen Sie ihn in ein zweites Browserfenster. Ziehen Sie die Form eines Browserfenster; die Form im anderen Browserfenster sollten verschieben. Die Verschiebung von der Form, in dem anderen Fenster sollte weniger ruckartige, wie ihre Bewegung auf einmal pro eingehender Nachricht festgelegt wird, statt Zeit interpoliert wird angezeigt.

    ![Das Anwendungsfenster](tutorial-high-frequency-realtime-with-signalr/_static/image7.png)

<a id="furthersteps"></a>

## <a name="further-steps"></a>Weitere Schritte

In diesem Tutorial haben Sie gelernt, wie Sie eine SignalR-Anwendung programmieren, die häufige Nachrichten zwischen Clients und Servern sendet. Dieses Paradigma für die Kommunikation ist nützlich für die Entwicklung von Onlinespielen und andere Simulationen, z. B. [ShootR Spiel erstellt, die mit SignalR](http://shootr.signalr.net).

Die vollständige Anwendung, die in diesem Tutorial erstellten kann heruntergeladen werden [Code Gallery](https://code.msdn.microsoft.com/SignalR-MoveShape-demo-3366dac6).

Weitere Informationen zu Konzepten der SignalR-Entwicklung finden Sie unter den folgenden Websites für SignalR-Quellcode und Ressourcen:

- [SignalR-Projekt](http://signalr.net)
- [SignalR Github und Beispiele](https://github.com/SignalR/SignalR)
- [SignalR Wiki](https://github.com/SignalR/SignalR/wiki)

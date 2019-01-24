---
uid: signalr/overview/older-versions/tutorial-getting-started-with-signalr-and-mvc-4
title: 'Tutorial: Erste Schritte mit SignalR 1.x und MVC 4 | Microsoft-Dokumentation'
author: bradygaster
description: Verwenden Sie SignalR für ASP.NET und ASP.NET MVC 4, um eine chatanwendung mit Echtzeitfunktionalität erstellen.
ms.author: bradyg
ms.date: 03/29/2013
ms.assetid: eeef9f73-6de3-49f9-b50b-9af22108f2ce
msc.legacyurl: /signalr/overview/older-versions/tutorial-getting-started-with-signalr-and-mvc-4
msc.type: authoredcontent
ms.openlocfilehash: dd55ca22004b7e3899f6a8789494c842b984787f
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837792"
---
<a name="tutorial-getting-started-with-signalr-1x-and-mvc-4"></a>Tutorial: Erste Schritte mit SignalR 1.x und MVC 4
====================
durch [Patrick Fletcher](https://github.com/pfletcher), [Tim Teebken](https://github.com/timlt)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> In diesem Tutorial wird gezeigt, wie mit ASP.NET SignalR eine chatanwendung mit Echtzeitfunktionalität erstellen. Sie SignalR eine MVC 4-Anwendung hinzufügen und erstellen Sie eine Chat-Ansicht zum Senden und Meldungen angezeigt werden.


## <a name="overview"></a>Übersicht

Dieses Tutorial führt Sie in Echtzeit-Webanwendungsentwicklung mit ASP.NET SignalR und ASP.NET MVC 4. Das Lernprogramm verwendet den gleichen Code der Chat-Anwendung wie den [SignalR, erste Schritte-Lernprogramm](tutorial-getting-started-with-signalr.md), aber zeigt, wie sie eine MVC 4-Anwendung, die basierend auf der Internet-Vorlage hinzugefügt.

In diesem Thema lernen Sie die folgenden Entwicklungsaufgaben von SignalR:

- Eine MVC 4-Anwendung wird die SignalR-Bibliothek hinzugefügt.
- Erstellen einer hubklasse, um Inhalt an Clients zu verteilen.
- Verwenden die SignalR-jQuery-Bibliothek auf einer Webseite zum Senden von Nachrichten und Updates vom Hub angezeigt.

Der folgende Screenshot zeigt die abgeschlossene Chat-Anwendung, die in einem Browser ausgeführt.

![Chatinstanzen](tutorial-getting-started-with-signalr-and-mvc-4/_static/image2.png)

Abschnitte:

- [Einrichten des Projekts](#setup)
- [Ausführen des Beispiels](#run)
- [Überprüfen Sie den Code](#code)
- [Nächste Schritte](#next)

<a id="setup"></a>

## <a name="set-up-the-project"></a>Einrichten des Projekts

Erforderliche Komponenten:

- Visual Studio 2010 SP1, Visual Studio 2012 oder Visual Studio 2012 Express. Wenn Sie nicht über Visual Studio verfügen, finden Sie unter [ASP.NET-Downloads](https://www.asp.net/downloads) um die kostenlose Visual Studio 2012 Express Entwicklungstool zu erhalten.
- Für Visual Studio 2010 installieren [ASP.NET MVC 4](https://www.microsoft.com/download/details.aspx?id=30683).

In diesem Abschnitt veranschaulicht das Erstellen einer ASP.NET MVC 4-Anwendung, die SignalR-Bibliothek hinzufügen und erstellen Sie die chatanwendung.

1. 1. Erstellen Sie in Visual Studio eine ASP.NET MVC 4-Anwendung, nennen Sie sie SignalRChat, und klicken Sie auf OK.

        > [!NOTE]
        > Wählen Sie in Visual Studio 2010 **.NET Framework 4** im Framework-Version Dropdown-Steuerelement. SignalR-Code, die auf .NET Framework-Versionen 4 und 4.5 ausgeführt wird.

        ![Erstellen von Mvc-Webanwendung](tutorial-getting-started-with-signalr-and-mvc-4/_static/image3.png)
      2. Wählen Sie die Internet Application-Vorlage, deaktivieren Sie die Option zum **erstellen ein Komponententestprojekts**, und klicken Sie auf OK.

         ![Mvc-IIS-Site erstellen](tutorial-getting-started-with-signalr-and-mvc-4/_static/image4.png)
      3. Öffnen der **Tools > NuGet Paket-Manager > Paket-Manager Console** und führen folgenden Befehl aus. Dieser Schritt fügt dem Projekt hinzu einen Satz von Skriptdateien und Assemblyverweise, die SignalR-Funktionalität zu aktivieren.

         `install-package Microsoft.AspNet.SignalR -Version 1.1.3`
      4. In **Projektmappen-Explorer** erweitern Sie den Ordner "Scripts". Beachten Sie, dass die Skriptbibliotheken für SignalR zum Projekt hinzugefügt wurden.

         ![Verweise auf die Typbibliothek](tutorial-getting-started-with-signalr-and-mvc-4/_static/image6.png)
      5. In **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, wählen Sie **hinzufügen | Neuer Ordner**, und fügen Sie einen neuen Ordner namens **Hubs**.
      6. Mit der rechten Maustaste die **Hubs** Ordner, klicken Sie auf **hinzufügen | Klasse**, und erstellen Sie eine neue c#-Klasse, mit dem Namen **ChatHub.cs**. Sie verwenden diese Klasse als einen SignalR-Hub, die Nachrichten an alle Clients sendet.

> [!NOTE]
> Wenn Sie Visual Studio 2012 und installiert die [Update für ASP.NET und Web Tools 2012.2](../../../visual-studio/overview/2012/aspnet-and-web-tools-20122-release-notes-rtw.md#_Installation), können Sie die neue SignalR-Elementvorlage, um die hubklasse zu erstellen. Zu diesem Zweck Maustaste der **Hubs** Ordner, klicken Sie auf **hinzufügen | Neues Element**Option **SignalR-Hubklasse (v1)**, und nennen Sie die Klasse **ChatHub.cs**.


1. Ersetzen Sie den Code in die **ChatHub** Klasse durch den folgenden Code.

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample1.cs)]
2. Öffnen der **"Global.asax"** -Datei für das Projekt, und fügen Sie einen Aufruf der Methode `RouteTable.Routes.MapHubs();` als erste Zeile des Codes in der `Application_Start` Methode. Dieser Code registriert die Standardroute für SignalR-Hubs und muss aufgerufen werden, bevor Sie anderen Routen registrieren. Die abgeschlossene `Application_Start` Methode sieht wie im folgenden Beispiel aus.

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample2.cs)]
3. Bearbeiten der `HomeController` Klasse finden Sie im **Controllers/HomeController.cs** und fügen Sie die folgende Methode der Klasse. Diese Methode gibt die **Chat** anzeigen, die Sie in einem späteren Schritt erstellen.

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample3.cs)]
4. Mit der rechten Maustaste innerhalb der `Chat` Methode, die Sie gerade erstellt, und klicken Sie auf **Ansicht hinzufügen** um eine neue Datei zu erstellen.
5. In der **Ansicht hinzufügen** Dialogfeld stellen Sie sicher, dass Sie dieses Kontrollkästchen aktiviert ist **mithilfe eine Seite Layout- oder Masterseite** (deaktivieren Sie die anderen Kontrollkästchen), und klicken Sie dann auf **hinzufügen**.

    ![Hinzufügen einer Ansicht](tutorial-getting-started-with-signalr-and-mvc-4/_static/image8.png)
6. Bearbeiten Sie die neue Ansichtsdatei mit dem Namen **Chat.cshtml**. Nach der &lt;h2&gt; markieren, fügen Sie die folgende &lt;Div&gt; Abschnitt und `@section scripts` Codeblock in die Seite. Dieses Skript kann die Seite Chatnachrichten zu senden und Nachrichten vom Server anzuzeigen. Der vollständige Code für die Chat-Sicht wird in den folgenden Codeblock angezeigt.

    > [!IMPORTANT]
    > Wenn Sie SignalR und anderen JavaScript-Bibliotheken zu Ihrem Visual Studio-Projekt hinzufügen, kann der Paket-Manager-Versionen der Skripts installiert, jünger als die in diesem Thema aufgeführten Versionen sind. Stellen Sie sicher, dass die Skriptverweise im Code die Skriptbibliotheken, die in Ihrem Projekt installiert die Versionen übereinstimmen.

    [!code-cshtml[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample4.cshtml)]
7. **Speichern Sie alle** für das Projekt.

<a id="run"></a>

## <a name="run-the-sample"></a>Ausführen des Beispiels

1. Drücken Sie F5, um das Projekt im Debugmodus auszuführen.
2. Fügen Sie in der Adresszeile des Browsers **/Home/Chat** an die URL der Standardseite für das Projekt. Die Chat-Seite lädt in eine Instanz eines Webbrowsers und aufforderungen für einen Benutzernamen ein.

    ![Benutzername eingeben](tutorial-getting-started-with-signalr-and-mvc-4/_static/image9.png)
3. Geben Sie einen Benutzernamen ein.
4. Kopieren Sie die URL aus der Adresszeile des Browsers, und öffnen Sie zwei Browserinstanzen für weitere. Geben Sie in den einzelnen Instanzen Browser einen eindeutigen Benutzernamen ein.
5. Klicken Sie in den einzelnen Instanzen Browser Fügt einen Kommentar hinzu, und klicken Sie auf **senden**. Die Kommentare sollte in alle Browserinstanzen angezeigt werden.

    > [!NOTE]
    > Diese einfache chatanwendung werden den Kontext der Diskussion auf dem Server nicht verwaltet werden. Der Hub sendet, Kommentare, um alle aktuellen Benutzer. Benutzer, die im Chat später zusammenfügen sehen, dass Nachrichten ab dem Zeitpunkt hinzugefügt werden, dass sie aufgenommen werden.
6. Der folgende Screenshot zeigt die chatanwendung, die in einem Browser ausgeführt.

    ![Chat-Browser](tutorial-getting-started-with-signalr-and-mvc-4/_static/image11.png)
7. In **Projektmappen-Explorer**, überprüfen Sie die **Skriptdokumente** Knoten für die ausgeführte Anwendung. Dieser Knoten ist sichtbar im Debugmodus, wenn Sie Internet Explorer als Browser verwenden. Es gibt eine Skriptdatei namens **Hubs** , die den SignalR-Bibliothek wird zur Laufzeit dynamisch generiert. Diese Datei, verwaltet die Kommunikation zwischen der jQuery-Skripts und serverseitigen Code. Wenn Sie einen Browser als Internet Explorer verwenden, können Sie auch zugreifen, auf den dynamischen **Hubs** Datei navigieren darin direkt, beispielsweise http://mywebsite/signalr/hubs.

    ![Generierte Hub-Skript](tutorial-getting-started-with-signalr-and-mvc-4/_static/image13.png)

<a id="code"></a>

## <a name="examine-the-code"></a>Überprüfen Sie den Code

Der SignalR Chat-Anwendung zeigt zwei grundlegende Aufgaben für die SignalR-Entwicklung: Erstellen eines Hubs wie das wichtigste Coordination-Objekt, auf dem Server, und mithilfe der SignalR-jQuery-Bibliothek zum Senden und Empfangen von Nachrichten.

### <a name="signalr-hubs"></a>SignalR Hubs

Im Codebeispiel enthält die **ChatHub** Klasse leitet sich von der **Microsoft.AspNet.SignalR.Hub** Klasse. Ableiten von der **Hub** Klasse ist eine gute Möglichkeit, eine SignalR-Anwendung zu erstellen. Sie können öffentliche Methoden auf Ihrem Hub-Klasse erstellen und dann Zugriff auf diese Methoden durch einen Aufruf über die jQuery-Skripts auf einer Webseite.

Im Code Chat Clients rufen die **ChatHub.Send** Methode, um eine neue Nachricht zu senden. Der Hub wiederum sendet die Nachricht an alle Clients durch den Aufruf **Clients.All.addNewMessageToPage**.

Die **senden** -Methode demonstriert, mehrere Hub-Konzepte:

- Deklarieren Sie öffentliche Methoden für einen Hub, damit Clients, die sie aufrufen können.
- Verwenden der **Microsoft.AspNet.SignalR.Hub.Clients** Eigenschaft, um alle Clients zugreifen, die mit diesem Hub verbunden sind.
- Aufrufen einer jQuery-Funktion auf dem Client (z. B. die `addNewMessageToPage` Funktion) zum Aktualisieren von Clients.

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample5.cs)]

### <a name="signalr-and-jquery"></a>SignalR und jQuery

Die **Chat.cshtml** Ansicht im Codebeispiel zeigt, wie die SignalR-jQuery-Bibliothek, die zur Kommunikation mit einer SignalR-Hub verwenden. Die wesentlichen Aufgaben in der Code erstellen einen Verweis auf den automatisch generierten Proxy für den Hub, Deklarieren einer Funktion, die Inhalt per Pushvorgang an Clients der Server aufrufen können, und starten eine Verbindung zum Senden von Nachrichten an den Hub.

Der folgende Code deklariert einen Proxy für einen Hub.

[!code-javascript[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample6.js)]

> [!NOTE]
> In jQuery wird der Verweis auf die Server-Klasse und ihre Member in Camel-Case. Das Codebeispiel verweist auf die C#- **ChatHub** Klasse in jQuery als **ChatHub**. Wenn Sie verweisen möchten die `ChatHub` Klasse in jQuery mit herkömmlichen Pascal Groß-/Kleinschreibung wie in c#-Datei der ChatHub.cs bearbeiten. Hinzufügen einer `using` Anweisung auf die `Microsoft.AspNet.SignalR.Hubs` Namespace. Fügen Sie dann die `HubName` -Attribut auf die `ChatHub` Klasse, zum Beispiel `[HubName("ChatHub")]`. Zum Schluss aktualisieren Sie den jQuery-Verweis auf die `ChatHub` Klasse.


Der folgende Code zeigt, wie Sie eine Callback-Funktion im Skript zu erstellen. Die hubklasse auf dem Server ruft diese Funktion, um Inhaltsupdates per Pushvorgang an jeden Client übertragen. Der Aufruf optional die `htmlEncode` Funktion zeigt die Möglichkeit, HTML codieren von Inhalt der Nachricht vor der Anzeige auf der Seite als eine Möglichkeit zum Skript-Injection verhindern.

[!code-html[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample7.html)]

Der folgende Code zeigt, wie Sie eine Verbindung mit dem Hub zu öffnen. Der Code wird die Verbindung gestartet und leitet diese dann in eine Funktion zum Behandeln der Click-Ereignis auf der **senden** auf der Seite Chat.

> [!NOTE]
> Dieser Ansatz wird sichergestellt, dass die Verbindung hergestellt wurde, bevor der Ereignishandler ausgeführt wird.


[!code-javascript[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample8.js)]

<a id="next"></a>

## <a name="next-steps"></a>Nächste Schritte

Sie haben gelernt, dass es sich bei SignalR handelt es sich ein Framework zum Erstellen von Echtzeit-Webanwendungen. Sie erfahren auch mehrere Aufgaben bei der Entwicklung von SignalR: wie eine ASP.NET-Anwendung SignalR hinzugefügt, wie zum Erstellen einer hubklasse und das Senden und Empfangen von Nachrichten aus dem Hub.

Erweiterte Konzepte der SignalR-Entwicklungen finden Sie unter den folgenden Websites für SignalR-Quellcode und Ressourcen:

- [SignalR-Projekt](http://signalr.net)
- [SignalR Github und Beispiele](https://github.com/SignalR/SignalR)
- [SignalR Wiki](https://github.com/SignalR/SignalR/wiki)

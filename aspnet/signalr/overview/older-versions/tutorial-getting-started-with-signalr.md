---
uid: signalr/overview/older-versions/tutorial-getting-started-with-signalr
title: 'Tutorial: Erste Schritte mit SignalR 1.x | Microsoft-Dokumentation'
author: bradygaster
description: Verwenden Sie ASP.NET SignalR, um eine Echtzeit-Chat-Anwendung in eine HTML-Seite zu erstellen.
ms.author: bradyg
ms.date: 02/18/2013
ms.assetid: fdc3599a-5217-44c1-951f-0eec9812dce7
msc.legacyurl: /signalr/overview/older-versions/tutorial-getting-started-with-signalr
msc.type: authoredcontent
ms.openlocfilehash: b4b632a84e40aa0b73dfc7a30da0cf28249cc5b4
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836631"
---
<a name="tutorial-getting-started-with-signalr-1x"></a>Tutorial: Erste Schritte mit SignalR 1.x
====================
durch [Patrick Fletcher](https://github.com/pfletcher), [Tim Teebken](https://github.com/timlt)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Dieses Tutorial zeigt, wie Sie mithilfe von SignalR eine Chatanwendung mit Echtzeitfunktionalität erstellen. Sie werden eine leere ASP.NET-Webanwendung SignalR hinzu, und erstellen Sie eine HTML-Seite zum Senden und Meldungen angezeigt werden.


## <a name="overview"></a>Übersicht

In diesem Tutorial wird SignalR-Entwicklung eingeführt, wie eine einfache browserbasierte Chat-Anwendung zu erstellen. Sie werden die SignalR-Bibliothek mit einer leeren ASP.NET-Webanwendung hinzufügen, erstellen Sie eine hubklasse für das Senden von Nachrichten für Clients und erstellen Sie eine HTML-Seite, mit dem Benutzer, senden und Empfangen von Chatnachrichten. Ein ähnliches Tutorial, das Erstellen eine chatanwendung in MVC 4 zeigt die Verwendung einer MVC-Ansicht, finden Sie unter [erste Schritte mit SignalR und MVC 4](index.md).

> [!NOTE]
> Dieses Tutorial verwendet die Version (1.x) Version von SignalR. Weitere Informationen zu Änderungen zwischen SignalR 1.x und 2.0, finden Sie unter [aktualisieren SignalR 1.x-Projekte](../releases/upgrading-signalr-1x-projects-to-20.md).

SignalR ist eine Open Source-.NET Bibliothek zum Erstellen von Webanwendungen, die live Benutzerinteraktionen oder datenaktualisierungen in Echtzeit erfordern. Beispiele hierfür sind soziale Anwendungen, mehrere Benutzer Spiele, Business-Zusammenarbeit und Neuigkeiten, Wetter oder finanzielle Anwendungsupdates an. Diese werden häufig in Echtzeit Anwendungen bezeichnet.

SignalR vereinfacht das Erstellen von Echtzeit-Anwendungen. Es enthält eine Server-Bibliothek von ASP.NET und eine JavaScript-Clientbibliothek zum Verwalten von Client / Server-Verbindungen ein, und Übertragen von Inhaltsupdates an Clients zu erleichtern. Sie können eine vorhandene ASP.NET-Anwendung um Echtzeitfunktionen zu erhalten. die SignalR-Bibliothek hinzufügen.

Das Tutorial veranschaulicht die folgenden Entwicklungsaufgaben von SignalR:

- Eine ASP.NET-Webanwendung wird die SignalR-Bibliothek hinzugefügt.
- Erstellen einer hubklasse, um Inhalt an Clients zu verteilen.
- Verwenden die SignalR-jQuery-Bibliothek auf einer Webseite zum Senden von Nachrichten und Updates vom Hub angezeigt.

Der folgende Screenshot zeigt die chatanwendung, die in einem Browser ausgeführt. Jeden neuer Benutzer kann Kommentare Posten und Kommentare hinzugefügt, nachdem der Benutzer im Chat verknüpft.

![Chatinstanzen](tutorial-getting-started-with-signalr/_static/image1.png)

Abschnitte:

- [Einrichten des Projekts](#setup)
- [Ausführen des Beispiels](#run)
- [Überprüfen Sie den Code](#code)
- [Nächste Schritte](#next)

<a id="setup"></a>

## <a name="set-up-the-project"></a>Einrichten des Projekts

Fügen Sie in diesem Abschnitt zeigt, wie Sie eine leere ASP.NET-Webanwendung erstellen SignalR hinzu, und erstellen Sie die chatanwendung.

Erforderliche Komponenten:

- Visual Studio 2010 SP1 oder 2012. Wenn Sie nicht über Visual Studio verfügen, finden Sie unter [ASP.NET-Downloads](https://www.asp.net/downloads) um die kostenlose Visual Studio 2012 Express Entwicklungstool zu erhalten.
- [Microsoft ASP.NET und Web Tools 2012.2](https://go.microsoft.com/fwlink/?LinkId=279941). Für Visual Studio 2012 fügt dieses Installationsprogramm neue ASP.NET-Funktionen, einschließlich der SignalR-Vorlagen für Visual Studio. Für Visual Studio 2010 SP1 ein Installationsprogramm ist nicht verfügbar, aber Sie können das Tutorial abschließen, indem Sie den SignalR-NuGet-Paket installiert wird, wie in den setupschritten beschrieben.

Die folgenden Schritte verwenden Visual Studio 2012, erstellen eine leere ASP.NET-Webanwendung und Hinzufügen von SignalR-Bibliothek:

1. Erstellen Sie eine leere ASP.NET-Webanwendung in Visual Studio.

    ![Leeres Web erstellen](tutorial-getting-started-with-signalr/_static/image2.png)
2. Öffnen der **-Paket-Manager-Konsole** dazu **Tools | NuGet Paket-Manager | Paket-Manager-Konsole**. Geben Sie den folgenden Befehl in das Konsolenfenster ein:

    `Install-Package Microsoft.AspNet.SignalR -Version 1.1.3`

    Dieser Befehl installiert die neueste Version von SignalR 1.x.
3. In **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, wählen Sie **hinzufügen | Klasse**. Nennen Sie die neue Klasse **ChatHub**.
4. In **Projektmappen-Explorer** erweitern Sie den Knoten des Skripts. Skriptbibliotheken für jQuery und SignalR werden in das Projekt angezeigt.

    ![Verweise auf die Typbibliothek](tutorial-getting-started-with-signalr/_static/image3.png)
5. Ersetzen Sie den Code in die **ChatHub** Klasse durch den folgenden Code.

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample1.cs)]
6. In **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, und klicken Sie auf **hinzufügen | Neues Element**. In der **neues Element hinzufügen** wählen Sie im Dialogfeld **Globale Anwendungsklasse** , und klicken Sie auf **hinzufügen**.

    ![Hinzufügen einer globalen](tutorial-getting-started-with-signalr/_static/image4.png)
7. Fügen Sie die folgenden `using` Anweisungen nach der bereitgestellten `using` Anweisungen in der Klasse "Global.asax.cs".

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample2.cs)]
8. Fügen Sie die folgende Zeile des Codes in der `Application_Start` -Methode der globalen Klasse um die Standardroute für SignalR-Hubs zu registrieren.

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample3.cs)]
9. In **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, und klicken Sie auf **hinzufügen | Neues Element**. In der **neues Element hinzufügen** Dialogfeld, auf Html-Seite, und klicken Sie auf **hinzufügen**.
10. In **Projektmappen-Explorer**mit der rechten Maustaste auf die HTML-Seite, die Sie gerade erstellt haben, und klicken Sie auf **als Startseite festlegen**.
11. Ersetzen Sie den Standardcode in der HTML-Seite mit den folgenden Code ein.

    [!code-html[Main](tutorial-getting-started-with-signalr/samples/sample4.html)]
12. **Speichern Sie alle** für das Projekt.

<a id="run"></a>

## <a name="run-the-sample"></a>Ausführen des Beispiels

1. Drücken Sie F5, um das Projekt im Debugmodus auszuführen. Die HTML-Seite lädt in eine Instanz eines Webbrowsers und aufforderungen für einen Benutzernamen ein.

    ![Benutzername eingeben](tutorial-getting-started-with-signalr/_static/image5.png)
2. Geben Sie einen Benutzernamen ein.
3. Kopieren Sie die URL aus der Adresszeile des Browsers, und öffnen Sie zwei Browserinstanzen für weitere. Geben Sie in den einzelnen Instanzen Browser einen eindeutigen Benutzernamen ein.
4. Klicken Sie in den einzelnen Instanzen Browser Fügt einen Kommentar hinzu, und klicken Sie auf **senden**. Die Kommentare sollte in alle Browserinstanzen angezeigt werden.

    > [!NOTE]
    > Diese einfache chatanwendung werden den Kontext der Diskussion auf dem Server nicht verwaltet werden. Der Hub sendet, Kommentare, um alle aktuellen Benutzer. Benutzer, die im Chat später zusammenfügen sehen, dass Nachrichten ab dem Zeitpunkt hinzugefügt werden, dass sie aufgenommen werden.

    Der folgende Screenshot zeigt die Chat-Anwendung, die auf drei Browserinstanzen, die aktualisiert werden, wenn eine Instanz eine Nachricht sendet:

    ![Chat-Browser](tutorial-getting-started-with-signalr/_static/image6.png)
5. In **Projektmappen-Explorer**, überprüfen Sie die **Skriptdokumente** Knoten für die ausgeführte Anwendung. Es gibt eine Skriptdatei namens **Hubs** , die den SignalR-Bibliothek wird zur Laufzeit dynamisch generiert. Diese Datei, verwaltet die Kommunikation zwischen der jQuery-Skripts und serverseitigen Code.

    ![Generierte Hub-Skript](tutorial-getting-started-with-signalr/_static/image7.png)

<a id="code"></a>

## <a name="examine-the-code"></a>Überprüfen Sie den Code

Der SignalR Chat-Anwendung zeigt zwei grundlegende Aufgaben für die SignalR-Entwicklung: Erstellen eines Hubs wie das wichtigste Coordination-Objekt, auf dem Server, und mithilfe der SignalR-jQuery-Bibliothek zum Senden und Empfangen von Nachrichten.

### <a name="signalr-hubs"></a>SignalR Hubs

Im Codebeispiel enthält die **ChatHub** Klasse leitet sich von der **Microsoft.AspNet.SignalR.Hub** Klasse. Ableiten von der **Hub** Klasse ist eine gute Möglichkeit, eine SignalR-Anwendung zu erstellen. Sie können öffentliche Methoden auf Ihrem Hub-Klasse erstellen und dann Zugriff auf diese Methoden durch einen Aufruf über die jQuery-Skripts auf einer Webseite.

Im Code Chat Clients rufen die **ChatHub.Send** Methode, um eine neue Nachricht zu senden. Der Hub wiederum sendet die Nachricht an alle Clients durch den Aufruf **Clients.All.broadcastMessage**.

Die **senden** -Methode demonstriert, mehrere Hub-Konzepte:

- Deklarieren Sie öffentliche Methoden für einen Hub, damit Clients, die sie aufrufen können.
- Verwenden der **Microsoft.AspNet.SignalR.Hub.Clients** dynamische Eigenschaft alle Clients den Zugriff auf, die mit diesem Hub verbunden sind.
- Aufrufen einer jQuery-Funktion auf dem Client (z. B. die `broadcastMessage` Funktion) zum Aktualisieren von Clients.

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample5.cs)]

### <a name="signalr-and-jquery"></a>SignalR und jQuery

Die HTML-Seite im Codebeispiel veranschaulicht, wie die SignalR-jQuery-Bibliothek für die Kommunikation mit einer SignalR-Hub. Die wesentlichen Aufgaben in den Code deklarieren einen Proxy für den Hub, Deklarieren einer Funktion, die Inhalt per Pushvorgang an Clients der Server aufrufen können, und starten eine Verbindung zum Senden von Nachrichten an den Hub zu verweisen.

Der folgende Code deklariert einen Proxy für einen Hub.

[!code-javascript[Main](tutorial-getting-started-with-signalr/samples/sample6.js)]

> [!NOTE]
> In jQuery wird der Verweis auf die Server-Klasse und ihre Member in Camel-Case. Das Codebeispiel verweist auf die C#- **ChatHub** Klasse in jQuery als **ChatHub**.


Der folgende Code ist, wie Sie eine Callback-Funktion im Skript erstellen. Die hubklasse auf dem Server ruft diese Funktion, um Inhaltsupdates per Pushvorgang an jeden Client übertragen. Die beiden Zeilen an, dass die HTML-Codierung vor der Anzeige des Inhalts sind optional, und zeigen eine einfache Möglichkeit zum Skript-Injection verhindern.

[!code-html[Main](tutorial-getting-started-with-signalr/samples/sample7.html)]

Der folgende Code zeigt, wie Sie eine Verbindung mit dem Hub zu öffnen. Der Code wird die Verbindung gestartet und leitet diese dann in eine Funktion zum Behandeln der Click-Ereignis auf der **senden** auf der HTML-Seite.

> [!NOTE]
> Dieser Ansatz wird sichergestellt, dass die Verbindung hergestellt wurde, bevor der Ereignishandler ausgeführt wird.


[!code-javascript[Main](tutorial-getting-started-with-signalr/samples/sample8.js)]

<a id="next"></a>

## <a name="next-steps"></a>Nächste Schritte

Sie haben gelernt, dass es sich bei SignalR handelt es sich ein Framework zum Erstellen von Echtzeit-Webanwendungen. Sie erfahren auch mehrere Aufgaben bei der Entwicklung von SignalR: wie eine ASP.NET-Anwendung SignalR hinzugefügt, wie zum Erstellen einer hubklasse und das Senden und Empfangen von Nachrichten aus dem Hub.

Sie können die beispielanwendung in diesem Tutorial oder andere SignalR-Anwendungen verfügbar über das Internet, indem sie bei einem Hostinganbieter bereitstellen. Microsoft bietet kostenloses Webhosting für bis zu 10 Websites in einem kostenlosen [Windows Azure-Testkonto](https://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A443DD604). Eine exemplarische Vorgehensweise zum Bereitstellen der beispielanwendung für die SignalR finden Sie unter [der SignalR Beispiel "Erste Schritte" als ein Windows Azure-Website veröffentlichen](https://blogs.msdn.com/b/timlee/archive/2013/02/27/deploy-the-signalr-getting-started-sample-as-a-windows-azure-web-site.aspx). Ausführliche Informationen dazu, wie Sie ein Visual Studio-Webprojekt auf einer Windows Azure-Website bereitstellen, finden Sie unter [Bereitstellung einer ASP.NET-Anwendung auf einem Windows Azure-Website](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet). (Beachten Sie: Der WebSocket-Transport wird derzeit nicht für Windows Azure Web Sites unterstützt. Bei der WebSocket-Transport ist nicht verfügbar, die SignalR verwendet die andere verfügbare Transporte wie beschrieben im Abschnitt Transporte, der die [Einführung zu SignalR Thema](index.md).)

Erweiterte Konzepte der SignalR-Entwicklungen finden Sie unter den folgenden Websites für SignalR-Quellcode und Ressourcen:

- [SignalR-Projekt](http://signalr.net)
- [SignalR Github und Beispiele](https://github.com/SignalR/SignalR)
- [SignalR Wiki](https://github.com/SignalR/SignalR/wiki)

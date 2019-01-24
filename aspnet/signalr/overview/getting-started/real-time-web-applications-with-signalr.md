---
uid: signalr/overview/getting-started/real-time-web-applications-with-signalr
title: 'Hands On Lab: Echtzeit-Webanwendungen mit SignalR | Microsoft-Dokumentation'
author: bradygaster
description: Echtzeit-Webanwendungen bieten die Möglichkeit, serverseitige an verbundene Clients Inhalte, während es geschieht, können Sie in Echtzeit mithilfe von push. Für ASP.NET-Entwickler ASP...
ms.author: bradyg
ms.date: 07/16/2014
ms.assetid: ba07958c-42e1-4da0-81db-ba6925ed6db0
msc.legacyurl: /signalr/overview/getting-started/real-time-web-applications-with-signalr
msc.type: authoredcontent
ms.openlocfilehash: d4998c8b739b4b1a06699a17464a7399a87a8595
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837506"
---
<a name="hands-on-lab-real-time-web-applications-with-signalr"></a>Hands On Lab: Echtzeit-Webanwendungen mit SignalR
====================

durch [Web Camps Team](https://twitter.com/webcamps)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

[Herunterladen Sie Web Camps Training Kit](http://aka.ms/webcamps-training-kit)

> Echtzeit-Webanwendungen bieten die Möglichkeit, serverseitige an verbundene Clients Inhalte, während es geschieht, können Sie in Echtzeit mithilfe von push. Für ASP.NET-Entwickler **ASP.NET SignalR** ist eine Bibliothek in ihren Anwendungen echtzeitwebfunktionalität hinzu. Es nutzt verschiedene Transporte, das automatische auswählen von den am besten verfügbaren Transport erhält der Client und am besten verfügbaren Transport-Servers. Es nutzt **WebSocket**, eine HTML5-API, die bidirektionale Kommunikation zwischen Browser und Server ermöglicht.
> 
> **SignalR** bietet auch eine einfache, allgemeine API für die Server, Client-RPC (Aufrufen von JavaScript-Funktionen in Ihrer Clients Browsern aus .NET-Code serverseitige) in Ihrer ASP.NET-Anwendung sowie nützliche Hooks für die verbindungsverwaltung, hinzufügen z. B. eine Verbindung herstellen/trennen-Ereignisse, Gruppierung Verbindungen und -Autorisierung.
> 
> **SignalR** ist eine Abstraktion über einige der Transporte, die in Echtzeit Aufgaben zwischen Client und Server erforderlich sind. Ein **SignalR** Verbindung HTTP startet, und klicken Sie dann auf heraufgestuft wird eine **WebSocket** Verbindung, sofern verfügbar. **WebSocket** ist der ideale Transport für **SignalR**, da die Nutzung des Serverarbeitsspeichers vereinfacht, die geringste Latenz und verfügt über die zugrunde liegenden Funktionen (z. B. Vollduplexkommunikation zwischen Client und Server), aber auch über den die strengen Anforderungen an: **WebSocket** erfordert, dass der Server verwenden **Windows Server 2012** oder **Windows 8**, zusammen mit **.NET Framework 4.5**. Wenn diese Anforderungen nicht erfüllt sind, **SignalR** wird versucht, andere Transporte verwenden, um die Verbindungen herstellen (z. B. *Ajax langer Abruf*).
> 
> Die **SignalR** -API enthält zwei Modelle für die Kommunikation zwischen Clients und Servern: **Permanente Verbindungen** und **Hubs**. Ein **Verbindung** stellt einen einfachen Endpunkt dar, für die einzelnen-Empfänger senden gruppiert, oder Senden von Nachrichten. Ein **Hub** ist eine weitere allgemeine Pipeline, die auf den Verbindungs-API, die Ihre Client- und Methoden direkt aufeinander aufrufen können.
> 
> ![SignalR-Architektur](real-time-web-applications-with-signalr/_static/image1.png)
> 
> Alle Beispielcode und Ausschnitte sind im Web Camps Training Kit unter enthalten [ http://aka.ms/webcamps-training-kit ](http://aka.ms/webcamps-training-kit).


<a id="Overview"></a>
## <a name="overview"></a>Übersicht

<a id="Objectives"></a>
### <a name="objectives"></a>Ziele

In dieser praktischen Übungseinheit erfahren Sie, wie Sie:

- Senden von Benachrichtigungen vom Server an den Client, Verwendung von SignalR.
- Skalieren Sie Ihre Anwendung mit SignalR **SQL Server**.

<a id="Prerequisites"></a>
### <a name="prerequisites"></a>Vorraussetzungen

Folgendes ist erforderlich, um diese praktische Übungseinheit auszuführen:

- [Visual Studio Express 2013 für Web](https://www.microsoft.com/visualstudio/) oder höher

<a id="Setup"></a>
### <a name="setup"></a>Setup

Um die Übungen in dieser praktischen Übungseinheit auszuführen, müssen Sie zunächst die Umgebung einrichten.

1. Öffnen Sie ein Windows-Explorer-Fenster, und navigieren Sie zu des Labs die **Quelle** Ordner.
2. Mit der rechten Maustaste **"Setup.cmd"** , und wählen Sie **als Administrator ausführen** zum Starten des Setup-Prozesses, die die Umgebung konfigurieren, und installieren die Visual Studio-Codeausschnitte für diese Übung.
3. Wenn das Dialogfeld "Benutzerkontensteuerung" angezeigt wird, bestätigen Sie die Aktion aus, um den Vorgang fortzusetzen.

> [!NOTE]
> Stellen Sie sicher, dass Sie alle Abhängigkeiten für diese laborumgebung aktiviert haben, bevor Sie das Setup ausführen.


<a id="CodeSnippets"></a>
### <a name="using-the-code-snippets"></a>Verwenden von Codeausschnitten

In diesem Dokument Lab werden Sie aufgefordert, zum Einfügen von Codeblöcken. Der Einfachheit halber die meisten dieser Code dient als Visual Studio-Codeausschnitten, die Sie in Visual Studio 2013, um zu vermeiden, müssen sie manuell hinzufügen aus zugreifen können.

> [!NOTE]
> Jede Übung umfasst eine ab Lösung befindet sich in der **beginnen** Ordner der Übung, mit dem Sie jede Übung unabhängig von den anderen verfolgen kann. Bedenken Sie bitte, dass die Codeausschnitte, die während der Übung hinzugefügt werden fehlen aus diesen Lösungen ab und funktioniert möglicherweise nicht, bis Sie in dieser Übung abgeschlossen haben. In den Quellcode für eine Übung, finden Sie auch eine **End** Ordner, der Visual Studio-Projektmappe mit dem Code, die aus der Schritte in der entsprechenden Übung enthält. Sie können diese Lösungen als Leitfaden verwenden, wenn Sie zusätzliche Hilfe benötigen, wie Sie mithilfe dieser praktischen Übungseinheit arbeiten.


* * *

<a id="Exercises"></a>
## <a name="exercises"></a>Übungen

Dieser praktischen Übungseinheit enthält die folgenden Übungen:

1. [Arbeiten mit Daten in Echtzeit mit SignalR](#Exercise1)
2. [Horizontales Skalieren mithilfe von SQLServer](#Exercise2)

Geschätzte Zeit für diese testumgebung abzuschließen: **60 Minuten**

> [!NOTE]
> Wenn Sie Visual Studio zum ersten Mal starten, müssen Sie eine der vordefinierten Einstellungen Sammlungen auswählen. Jede vordefinierte Sammlung dient einem bestimmten Entwicklungsstil und bestimmt, Fensterlayouts, Editor-Verhalten, IntelliSense-Codeausschnitte und Dialogfeld "Optionen". In dieser Übung wird beschrieben, die erforderlichen Aktionen zum Ausführen der jeweiligen Aufgabe in Visual Studio bei Verwendung der **allgemeine Entwicklungseinstellungen** Auflistung. Wenn Sie eine Sammlung mit anderen Einstellungen für Ihre Entwicklungsumgebung auswählen, unter Umständen gibt es bestehen Unterschiede in den Schritten, die Sie berücksichtigen sollten.


<a id="Exercise1"></a>
### <a name="exercise-1-working-with-real-time-data-using-signalr"></a>Übung 1: Arbeiten mit Daten in Echtzeit mit SignalR

Beim Chat häufig als Beispiel verwendet wird, erreichen Sie insgesamt noch viel mehr mit Echtzeit-Webfunktionen. Jedes Mal ein Benutzer eine Webseite, um neue Daten oder die Seite implementiert Ajax langer Abruf, um neue Daten abzurufen, finden Sie unter aktualisiert wird, können Sie SignalR verwenden.

SignalR unterstützt **Serverpush** oder **Broadcasting** Funktionen verbindungsverwaltung wird automatisch durchgeführt. Klicken Sie in klassischen HTTP-Verbindungen für die Kommunikation zwischen Client und Server-Verbindung wird wiederhergestellt, für jede Anforderung, aber SignalR stellt dauerhafte Verbindung zwischen Client und Server. In SignalR, die der Code in einen Clientcode im Browser aufrufen Remoteprozeduraufruf (RPC) aufruft, anstatt die Anforderungs-/ antwortmodells wissen wir noch heute.

In dieser Übung konfigurieren Sie die **Meister Quiz** Anwendung SignalR zu verwenden, um das Dashboard Statistiken mit den aktualisierten Metriken ohne Aktualisierung der gesamten Seite anzuzeigen.

<a id="Ex1Task1"></a>
#### <a name="task-1--exploring-the-geek-quiz-statistics-page"></a>Aufgabe 1: untersuchen die Seite für die Streber Quiz Statistik

In dieser Aufgabe wird Sie der Anwendung durchlaufen und überprüfen Sie, wie die Seite Statistiken angezeigt wird, und wie Sie die Art der Informationen verbessern können werden aktualisiert.

1. Öffnen Sie **Visual Studio Express 2013 für Web** , und öffnen Sie die **GeekQuiz.sln** Lösung befindet sich in der **Source\Ex1-WorkingWithRealTimeData\Begin** Ordner.
2. Drücken Sie **F5** um die Projektmappe auszuführen. Die **melden Sie sich bei** Seite sollte im Browser angezeigt werden.

    ![Ausführen der Projektmappe](real-time-web-applications-with-signalr/_static/image2.png "Ausführen der Lösung")

    *Ausführen der Lösung*
3. Klicken Sie auf **registrieren** in oben rechts auf der Seite, um einen neuen Benutzer in der Anwendung zu erstellen.

    ![Registrieren von Link](real-time-web-applications-with-signalr/_static/image3.png "Link \"Register\"")

    *Link "Register"*
4. In der **registrieren** geben eine **Benutzernamen** und **Kennwort**, und klicken Sie dann auf **registrieren**.

    ![Registrieren eines Benutzers](real-time-web-applications-with-signalr/_static/image4.png "Registrieren eines Benutzers")

    *Registrieren eines Benutzers*
5. Die Anwendung registriert, das neue Konto und der Benutzer authentifiziert und wieder an die Startseite zeigt die erste Frage des Quiz umgeleitet wird.
6. Öffnen der **Statistiken** Seite in einem neuen Fenster, und fügen Sie der **Startseite** Seite und **Statistiken** Seite von Seite-an-Seite.

    ![Seite-an-Seite Windows](real-time-web-applications-with-signalr/_static/image5.png "Seite von Seite Windows")

    *Seite-an-Seite windows*
7. In der **Startseite** Seite, beantworten Sie die Frage, indem Sie eine der Optionen auf.

    ![Die Beantwortung einer Frage](real-time-web-applications-with-signalr/_static/image6.png "die Beantwortung einer Frage")

    *Die Beantwortung einer Frage*
8. Nach dem Klicken auf eine der Schaltflächen, sollte die Antwort angezeigt werden.

    ![Frage richtig beantwortet](real-time-web-applications-with-signalr/_static/image7.png "Frage richtig beantwortet")

    *Frage richtig beantwortet*
9. Beachten Sie, dass die Informationen in die Seite Statistiken veraltet sind. Aktualisieren Sie die Seite, um die aktualisierten Ergebnisse anzuzeigen.

    ![Seite "Statistik"](real-time-web-applications-with-signalr/_static/image8.png "Seite Statistiken")

    *Seite "Statistik"*
10. Wechseln Sie zurück zu Visual Studio, und beenden Sie des Debuggens.

<a id="Ex1Task2"></a>
#### <a name="task-2--adding-signalr-to-geek-quiz-to-show-online-charts"></a>Aufgabe 2: Hinzufügen von SignalR Meister Quiz zu Online-Diagrammen

In dieser Aufgabe Sie SignalR zu Projektmappe hinzufügen und Updates für die Clients automatisch senden, wenn eine neue Antwort an den Server gesendet wird.

1. Von der **Tools** in Visual Studio, wählen Sie im Menü **NuGet Package Manager**, und klicken Sie dann auf **-Paket-Manager-Konsole**.
2. In der **-Paket-Manager-Konsole** Fenster den folgenden Befehl ausführen:

    [!code-powershell[Main](real-time-web-applications-with-signalr/samples/sample1.ps1)]

    ![SignalR-Paketinstallation](real-time-web-applications-with-signalr/_static/image9.png "SignalR-Paketinstallation")

    *SignalR-Paketinstallation*

   > [!NOTE]
   > Bei der Installation von **SignalR** Version der NuGet-Pakete 2.0.2 aus einer neuen MVC 5-Anwendung, Sie müssen manuell aktualisieren, **OWIN** -Paketen zur Version 2.0.1 (oder höher) vor der Installation von SignalR. Sie können zu diesem Zweck das folgende Skript im Ausführen der **-Paket-Manager-Konsole**:
   > 
   > [!code-powershell[Main](real-time-web-applications-with-signalr/samples/sample2.ps1)]
   > 
   > In einer zukünftigen Version von SignalR werden die OWIN-Abhängigkeiten automatisch aktualisiert werden.
3. In **Projektmappen-Explorer**, erweitern Sie die **Skripts** Ordner, die SignalR *Js* wurden die Dateien der Projektmappe hinzugefügt.

    ![SignalR JavaScript verweist auf](real-time-web-applications-with-signalr/_static/image10.png "SignalR JavaScript verweist.")

    *SignalR-JavaScript-Referenzen*
4. In **Projektmappen-Explorer**, mit der rechten Maustaste die **GeekQuiz** -Projekt, wählen **hinzufügen** | **neuer Ordner**, und nennen Sie sie  **Hubs**.
5. Mit der rechten Maustaste die **Hubs** Ordner, und wählen **hinzufügen | Neues Element**.

    ![Neues Element hinzufügen](real-time-web-applications-with-signalr/_static/image11.png "neues Element hinzufügen")

    *Neues Element hinzufügen*
6. In der **neues Element hinzufügen** wählen Sie im Dialogfeld die **Visual c# | Web | SignalR** Knoten in den linken Bereich die Option **SignalR-Hubklasse (v2)** im mittleren Bereich aus, nennen Sie die Datei **StatisticsHub.cs** , und klicken Sie auf **hinzufügen**.

    ![Im Dialogfeld für neues Element hinzufügen](real-time-web-applications-with-signalr/_static/image12.png "im Dialogfeld für neues Element hinzufügen")

    *Fügen Sie im Dialogfeld Neues Element hinzu.*
7. Ersetzen Sie den Code in die **StatisticsHub** Klasse durch den folgenden Code.

    (Codeausschnitt - *RealTimeSignalR - Ex1 - StatisticsHubClass*)

    [!code-csharp[Main](real-time-web-applications-with-signalr/samples/sample3.cs)]
8. Open **"Startup.cs"** und fügen Sie die folgende Zeile am Ende der **Konfiguration** Methode.

    (Codeausschnitt - *RealTimeSignalR - Ex1 - MapSignalR*)

    [!code-csharp[Main](real-time-web-applications-with-signalr/samples/sample4.cs)]
9. Öffnen der **StatisticsService.cs** Seite innerhalb der **Services** Ordner, und fügen Sie die folgenden using-Direktiven.

    (Codeausschnitt - *RealTimeSignalR - Ex1 - UsingDirectives*)

    [!code-csharp[Main](real-time-web-applications-with-signalr/samples/sample5.cs)]
10. Um verbundene Clients Updates zu benachrichtigen, Sie rufen Sie zuerst eine **Kontext** Objekt für die aktuelle Verbindung. Die **Hub** Objekt enthält Methoden zum Senden von Nachrichten an einen einzelnen Client oder broadcast an alle verbundenen Clients. Fügen Sie die folgende Methode der **StatisticsService** Klasse, um die Statistikdaten zu übertragen.

    (Codeausschnitt - *RealTimeSignalR - Ex1 - NotifyUpdatesMethod*)

    [!code-csharp[Main](real-time-web-applications-with-signalr/samples/sample6.cs)]

    > [!NOTE]
    > Im obigen Code, verwenden Sie den Methodennamen eine beliebige zum Aufrufen einer Funktion auf dem Client (d. h.: *UpdateStatistics*). Der Name der Methode, die Sie angeben wird als ein dynamisches Objekt interpretiert, was bedeutet, dass es gibt keine IntelliSense oder der Kompilierzeit-Überprüfung für sie. Der Ausdruck wird zur Laufzeit ausgewertet. Wenn der Methodenaufruf ausgeführt wird, sendet SignalR den Methodennamen und die Parameterwerte an den Client. Verfügt der Client eine Methode, die mit dem Namen übereinstimmt, wird diese Methode wird aufgerufen, und die Parameterwerte werden an sie übergeben. Wenn keine übereinstimmende Methode auf dem Client gefunden wird, wird kein Fehler ausgelöst. Weitere Informationen finden Sie unter [für die ASP.NET SignalR-Hubs-API](../guide-to-the-api/hubs-api-guide-server.md).
11. Öffnen der **TriviaController.cs** Seite innerhalb der **Controller** Ordner, und fügen Sie die folgenden using-Direktiven.

    [!code-csharp[Main](real-time-web-applications-with-signalr/samples/sample7.cs)]
12. Fügen Sie den folgenden hervorgehobenen Code in die **Post** Aktionsmethode.

    (Codeausschnitt - *RealTimeSignalR - Ex1 - NotifyUpdatesCall*)

    [!code-csharp[Main](real-time-web-applications-with-signalr/samples/sample8.cs)]
13. Öffnen der **Statistics.cshtml** Seite innerhalb der **Ansichten | Startseite** Ordner. Suchen Sie die **Skripts** Abschnitt, und fügen Sie die folgenden Skriptverweise am Anfang des Abschnitts.

    (Codeausschnitt - *RealTimeSignalR - Ex1 - SignalRScriptReferences*)

    [!code-cshtml[Main](real-time-web-applications-with-signalr/samples/sample9.cshtml)]

    > [!NOTE]
    > Wenn Sie SignalR und anderen JavaScript-Bibliotheken zu Ihrem Visual Studio-Projekt hinzufügen, kann der Paket-Manager eine Version der SignalR-Skriptdatei installieren, als die Version, die in diesem Thema gezeigten ist. Stellen Sie sicher, dass der Verweis auf das Skript in Ihrem Code mit der Version der Skriptbibliothek in Ihrem Projekt installiert übereinstimmt.
14. Fügen Sie folgenden hervorgehobenen Code zum Herstellen einer Verbindung den SignalR-Hub mit dem Client und die Statistikdaten zu aktualisieren, wenn eine neue Nachricht, aus dem Hub empfangen wird hinzu.

    (Codeausschnitt - *RealTimeSignalR - Ex1 - SignalRClientCode*)

    [!code-cshtml[Main](real-time-web-applications-with-signalr/samples/sample10.cshtml)]

    In diesem Code wird eine Hubproxy-Klasse erstellen und registrieren einen Ereignishandler zum Lauschen auf Nachrichten, die vom Server gesendet wird. In diesem Fall durch gesendete Nachrichten Abhören der *UpdateStatistics* Methode.

<a id="Ex1Task3"></a>
#### <a name="task-3--running-the-solution"></a>Aufgabe 3: Ausführen der Lösung

In dieser Aufgabe führen Sie die Projektmappe, um sicherzustellen, dass die Statistiken Ansicht automatisch mit SignalR aktualisiert wird nach eine neue Frage zu beantworten.

1. Drücken Sie **F5** um die Projektmappe auszuführen.

    > [!NOTE]
    > Wenn nicht bereits bei der Anwendung angemeldet, melden Sie sich mit dem Benutzer, die, den Sie in Aufgabe 1 erstellt haben.
2. Öffnen der **Statistiken** Seite in einem neuen Fenster, und fügen Sie der **Startseite** Seite und **Statistiken** Seite von Seite-an-Seite wie in Aufgabe 1.
3. In der **Startseite** Seite, beantworten Sie die Frage, indem Sie eine der Optionen auf.

    ![Eine weitere Frage beantworten](real-time-web-applications-with-signalr/_static/image13.png "eine weitere Frage beantworten")

    *Eine weitere Frage beantworten*
4. Nach dem Klicken auf eine der Schaltflächen, sollte die Antwort angezeigt werden. Beachten Sie, dass die statistischen Informationen auf der Seite automatisch aktualisiert wird, nach der Beantwortung der jeweiligen Fragen mit den aktualisierten Informationen ohne die gesamte Seite aktualisieren.

    ![Seite Statistiken aktualisiert werden, nach der Antwort](real-time-web-applications-with-signalr/_static/image14.png "Seite Statistiken aktualisiert werden, nach der Antwort")

    *Seite Statistiken, die nach der Antwort aktualisiert.*

<a id="Exercise2"></a>
### <a name="exercise-2-scaling-out-using-sql-server"></a>Übung 2: Horizontales Skalieren mithilfe von SQLServer

Beim Skalieren einer Web-Anwendung in der Regel können Sie zwischen *hochskalieren* und *horizontales hochskalieren* Optionen. *Zentrales hochskalieren* bedeutet einen größeren Server, mit mehr Ressourcen (CPU, Arbeitsspeicher usw.) während *horizontal hochskalieren* bedeutet, dass weitere Server zur Bewältigung der Arbeitslast hinzufügen. Das Problem mit dem zweiten ist, dass die Clients auf verschiedenen Servern weitergeleitet werden können. Ein Client, der mit einem Server verbunden ist, erhalten keine Nachrichten von einem anderen Server gesendet.

Sie können diese Probleme beheben, indem Sie mithilfe einer Komponente namens *Rückwandplatine*zum Weiterleiten von Nachrichten zwischen Servern. Mit einer Rückwandplatine aktiviert ist jede Anwendungsinstanz sendet Nachrichten an die Backplane und der Rückwand an die anderen Anwendungsinstanzen weitergeleitet werden.

Es gibt derzeit drei Typen von Backplanes für SignalR:

- **Windows Azure-Servicebus**. Service Bus ist eine messaging-Infrastruktur, die Komponenten zum Senden von lose verbundenen Nachrichten ermöglicht.
- **SQL Server**. Die SQL Server-Rückwandplatine Nachrichten, die in SQL-Tabellen geschrieben wird. Der Rückwand verwendet Service Broker für effiziente messaging. Es funktioniert jedoch auch, wenn Service Broker nicht aktiviert ist.
- **Redis**. Redis ist ein Schlüssel-Wert-Speicher im Arbeitsspeicher. Redis unterstützt ein Muster zum Veröffentlichen/Abonnieren ("Pub/Sub") zum Senden von Nachrichten an.

Jede Nachricht wird über einen Nachrichtenbus gesendet. Implementiert ein Nachrichtenbus der ["imessagebus"](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.messaging.imessagebus(v=vs.100).aspx) -Schnittstelle, die eine Abstraktion zum Veröffentlichen/Abonnieren bereitstellt. Die Backplanes zu arbeiten, indem Sie den standardmäßigen ersetzen **"imessagebus"** mit einem Bus, die für diese Rückwandplatine konzipiert.

Jede Serverinstanz eine Verbindung mit der Rückwand über den Bus. Wenn eine Nachricht gesendet wird, an die Backplane darin, und der Rückwand sendet sie an jedem Server. Wenn ein Server eine Nachricht von der Backplane empfängt, speichert er die Nachricht in ihrem lokalen Cache. Der Server sendet dann Nachrichten für Clients aus dem lokalen Cache.

Weitere Informationen zur Funktionsweise der SignalR-Backplane finden Sie in diesem [Artikel](../performance/scaleout-in-signalr.md).

> [!NOTE]
> Es gibt einige Szenarien, in denen eine Rückwandplatine Engpässe auftreten können. Hier sind einige typischen Szenarien für SignalR:
> 
> - [Serverübertragung](tutorial-server-broadcast-with-signalr.md) (beispielsweise Börsenticker): Backplanes eignen sich gut für dieses Szenario, da der Server die Rate kontrolliert, an der Nachrichten gesendet werden.
> - [Client-zu-Client](tutorial-getting-started-with-signalr.md) (z. B. chat): In diesem Szenario wird möglicherweise der Rückwand einen Engpass, wenn die Anzahl der Nachrichten mit der Anzahl der Clients skaliert werden kann; d. h., wenn die Anzahl der Nachrichten wird verknüpfen proportional mehr Clients.
> - [Echtzeitnachrichten](tutorial-high-frequency-realtime-with-signalr.md) (z. B. in Echtzeit Spiele): Eine Rückwandplatine wird für dieses Szenario nicht empfohlen.


In dieser Übung verwenden Sie **SQL Server** zum Verteilen von Nachrichten über die **Meister Quiz** Anwendung. Führen Sie diese Aufgaben auf einem einzelnen Testcomputer zu erfahren, wie die Konfiguration einzurichten, sondern um den vollen Effekt zu erzielen, benötigen Sie zum Bereitstellen der SignalR-Anwendung mit zwei oder mehr Servern. Sie müssen auch SQL Server auf einem der Server oder auf einem separaten dedizierten Server installieren.

![Scale Out mit SQL Server-Diagramm](real-time-web-applications-with-signalr/_static/image15.png)

<a id="Ex2Task1"></a>
#### <a name="task-1---understanding-the-scenario"></a>Aufgabe 1 – Grundlegendes zur das Szenario

In dieser Aufgabe führen Sie 2 Instanzen **Meister Quiz** simulieren mehrere IIS-Instanzen auf dem lokalen Computer. In diesem Szenario, bei der Beantwortung von Fragen Trivia, eine Anwendung wird nicht auf die Seite Statistiken der zweiten Instanz Update benachrichtigt werden. Dieser Simulation ähnelt eine Umgebung, in denen Ihre Anwendung auf mehreren Instanzen bereitgestellt ist, und mit einem Load Balancer mit ihnen kommunizieren.

1. Öffnen der **Begin.sln** Lösung befindet sich in der **Quelle/Ex2-ScalingOutWithSQLServer/Anfang** Ordner. Nach dem Laden, werden Sie feststellen, auf die **Server-Explorer** , dass die Lösung besteht aus zwei Projekte mit identischer Strukturen aber unterschiedliche Namen. Dies wird simulieren, zwei Instanzen der gleichen Anwendung auf Ihrem lokalen Computer ausgeführt.

    ![Beginnen Sie Lösung 2 Instanzen Meister Quiz simulieren](real-time-web-applications-with-signalr/_static/image16.png "Lösung simulieren von 2 Instanzen Meister Quiz beginnen")

    *Beginnen Sie Lösung 2 Instanzen Meister Quiz simulieren*
2. Öffnen Sie die Seite "Eigenschaften" der Projektmappe, indem Sie mit der rechten Maustaste den Knoten "Projektmappe", und wählen **Eigenschaften**. Unter **Startprojekt**Option **mehrere Startprojekte** , und ändern Sie die **Aktion** Wert für beide Projekte in *starten*.

    ![Mehrere Projekte starten](real-time-web-applications-with-signalr/_static/image17.png "mehrere Projekte starten")

    *Mehrere Projekte starten*
3. Drücken Sie **F5** um die Projektmappe auszuführen. Starten der Anwendung werden zwei Instanzen von **Meister Quiz** in verschiedene Ports, simulieren Sie mehrere Instanzen der gleichen Anwendung. Heften Sie einen Browser auf der linken Seite und die andere auf der rechten Seite des Bildschirms. Melden Sie sich mit Ihren Anmeldeinformationen, oder registrieren Sie einen neuen Benutzer. Nach der Anmeldung auf der linken Seite die Trivia beizubehalten, und fahren Sie mit der **Statistiken** Seite im Browser auf der rechten Seite.

    ![Meister Quiz nebeneinander](real-time-web-applications-with-signalr/_static/image18.png)

    *Meister Quiz nebeneinander*

    ![Meister Quiz in verschiedene Ports](real-time-web-applications-with-signalr/_static/image19.png)

    *Meister Quiz in verschiedene Ports*
4. Starten der Beantwortung von Fragen im linken Browser, und Sie werden feststellen, dass die **Statistiken** Seite in der richtigen Browser wird nicht aktualisiert. Grund hierfür ist, **SignalR** verwendet eine lokale Zwischenspeichern, um Nachrichten auf ihren Clients verteilen und mehrere Instanzen dieses Szenario simuliert, daher der Cache wird nicht gemeinsam genutzt werden. Sie können sicherstellen, dass **SignalR** testen die gleichen Schritte jedoch mithilfe einer einzigen Apps funktioniert. In den folgenden Aufgaben konfigurieren Sie eine Rückwandplatine, um die Nachrichten über Instanzen hinweg zu replizieren.
5. Wechseln Sie zurück zu Visual Studio, und beenden Sie des Debuggens.

<a id="Ex2Task2"></a>
#### <a name="task-2--creating-the-sql-server-backplane"></a>Aufgabe 2: Erstellen der SQL Server-Rückwandplatine

In dieser Aufgabe erstellen Sie eine Datenbank, die als eine Rückwandplatine für dient der **Meister Quiz** Anwendung. Verwenden Sie **Objekt-Explorer von SQL Server** auf Ihrem Server suchen und Initialisieren der Datenbank. Aktivieren Sie darüber hinaus die **Service Broker**.

1. In **Visual Studio**, offene Menü **Ansicht** , und wählen Sie **Objekt-Explorer von SQL Server**.
2. Verbinden mit der LocalDB-Instanz, indem Sie mit der rechten Maustaste die **SQL Server** und dann **SQL Server hinzufügen...**  Option.

    ![Hinzufügen einer SQL Server-Instanz](real-time-web-applications-with-signalr/_static/image20.png "Hinzufügen einer SQL Server-Instanz")

    *Hinzufügen von SQL Server-Instanz zu SQL Server-Objekt-Explorer*
3. Legen Sie die **Servernamen** zu *(Localdb) \v11.0* und lassen Sie **Windows-Authentifizierung** als Authentifizierungsmodus. Klicken Sie auf **Connect** um den Vorgang fortzusetzen.

    ![Herstellen einer Verbindung mit LocalDB](real-time-web-applications-with-signalr/_static/image21.png "Herstellen einer Verbindung mit LocalDB")

    *Herstellen einer Verbindung mit LocalDB*
4. Nun, da Sie mit der LocalDB-Instanz verbunden sind, müssen Sie eine Datenbank zu erstellen, die die SQL Server-Rückwandplatine für SignalR darstellt. Dazu, mit der Maustaste der **Datenbanken** Knoten, und wählen **neue Datenbank hinzufügen**.

    ![Hinzufügen einer neuen Datenbank](real-time-web-applications-with-signalr/_static/image22.png "Hinzufügen einer neuen Datenbank")

    *Hinzufügen einer neuen Datenbank*
5. Legen Sie den Datenbanknamen *SignalR* , und klicken Sie auf **OK** , ihn zu erstellen.

    ![Erstellen der Datenbank SignalR](real-time-web-applications-with-signalr/_static/image23.png "die SignalR-Datenbank erstellen")

    *Erstellen der SignalR-Datenbank*

    > [!NOTE]
    > Sie können einen beliebigen Namen für die Datenbank auswählen.
6. Um Updates von der Backplane effizienter zu erhalten, wird empfohlen, die Service Broker für die Datenbank zu aktivieren. Service Broker bietet systemeigene Unterstützung für Messaging- und Warteschlangen in SQL Server. Der Rückwand funktioniert auch ohne Service Broker. Öffnen Sie eine neue Abfrage, indem Sie mit der rechten Maustaste die Datenbank, und wählen **neue Abfrage**.

    ![Öffnen eine neue Abfrage](real-time-web-applications-with-signalr/_static/image24.png "eine neue Abfrage öffnen")

    *Öffnen eine neue Abfrage*
7. Um zu überprüfen, ob Service Broker aktiviert ist, Abfragen der **ist\_Broker\_aktiviert** -Spalte in der **sys.databases** -Katalogsicht angezeigt. Führen Sie das folgende Skript in der zuletzt geöffneten Abfragefenster ein.

    [!code-sql[Main](real-time-web-applications-with-signalr/samples/sample11.sql)]

    ![Abfragen des Status der Service Broker](real-time-web-applications-with-signalr/_static/image25.png "Abfragen des Service Broker-Status")

    *Abfragen des Service Broker-Status*
8. Wenn der Wert des der **ist\_Broker\_aktiviert** Spalte in der Datenbank ist &quot;0&quot;, verwenden Sie den folgenden Befehl, um ihn zu aktivieren. Ersetzen Sie dies **&lt;Ihre Datenbank&gt;** mit dem Namen, die Sie beim Erstellen der Datenbank festlegen (z. B.: SignalR).

    [!code-sql[Main](real-time-web-applications-with-signalr/samples/sample12.sql)]

    ![Aktivieren von Service Broker](real-time-web-applications-with-signalr/_static/image26.png "Aktivieren von Service Broker")

    *Aktivieren von Service Broker*

    > [!NOTE]
    > Wenn diese Abfrage angezeigt wird, ein Deadlock auftreten, stellen Sie sicher, dass keine Programme, die mit der Datenbank verbunden sind.

<a id="Ex2Task3"></a>
#### <a name="task-3--configuring-the-signalr-application"></a>Aufgabe 3: Konfigurieren der SignalR-Anwendung

In dieser Aufgabe Konfigurieren Sie **Meister Quiz** zur Verbindung mit der SQL Server-Rückwandplatine. Fügen Sie zuerst die **SignalR.SqlServer** Zeichenfolge von NuGet-Paket und die Verbindung mit Ihrer Datenbank Backplane.

1. Öffnen der **-Paket-Manager-Konsole** aus **Tools** > **NuGet-Paket-Manager**. Stellen Sie sicher, dass **GeekQuiz** Projekt ausgewählt ist, der **Standardprojekt** Dropdown-Liste. Geben Sie den folgenden Befehl zum Installieren der **Microsoft.AspNet.SignalR.SqlServer** NuGet-Paket.

    [!code-powershell[Main](real-time-web-applications-with-signalr/samples/sample13.ps1)]
2. Wiederholen Sie dieses Mal jedoch den vorherigen Schritt für Projekt **GeekQuiz2**.
3. Öffnen Sie zum Konfigurieren der SQL Server-Rückwandplatine der **"Startup.cs"** Datei mit den **GeekQuiz** Projekt, und fügen Sie den folgenden Code der **konfigurieren** Methode. Ersetzen Sie dies **&lt;Ihre Datenbank&gt;** durch Ihren Datenbanknamen, die Sie beim Erstellen der SQL Server-Rückwandplatine verwendet. Wiederholen Sie diesen Schritt für die **GeekQuiz2** Projekt.

    (Codeausschnitt - *RealTimeSignalR - Ex2 - StartupConfiguration*)

    [!code-csharp[Main](real-time-web-applications-with-signalr/samples/sample14.cs)]
4. Nun, dass beide Projekte für die Verwendung der SQL Server-Rückwandplatine konfiguriert sind, drücken Sie die **F5** sie gleichzeitig ausführen.
5. In diesem Fall **Visual Studio** startet nun zwei Instanzen von **Meister Quiz** in unterschiedlichen Ports. Heften Sie einer der Browser auf der linken Seite und der andere auf der rechten Seite des Bildschirms an, und melden Sie sich mit Ihren Anmeldeinformationen. Halten Sie die Trivia-Seite auf der linken Seite, und wechseln Sie zu **Statistiken** Pagein der richtige Browser.
6. Starten Sie die Beantwortung von Fragen im linken Browser. Dieses Mal die **Statistiken** Seite wird aktualisiert, Dank der Backplane. Wechseln zwischen Anwendungen (**Statistiken** befindet sich jetzt auf der linken Seite und **Trivia** auf der rechten Seite), und wiederholen Sie den Test aus, um sicherzustellen, dass sie für beide Instanzen funktioniert. Der Rückwand dient als ein *freigegebenen Cache* von Nachrichten für jeden verbundenen Server, und jeder Server speichert die Nachrichten in ihren eigenen lokalen Cache verbundenen Clients zu verteilen.
7. Wechseln Sie zurück zu Visual Studio, und beenden Sie des Debuggens.
8. Die SQL Server-Rückwandplatine-Komponente generiert automatisch die erforderlichen Tabellen für die angegebene Datenbank. In der **Objekt-Explorer von SQL Server** Bereich, öffnen Sie die Datenbank, die Sie für die Rückwandplatine erstellt haben (z. B.: SignalR), und erweitern Sie die Tabellen. In den folgenden Tabellen sollte angezeigt werden:

    ![Rückwand generiert Tabellen](real-time-web-applications-with-signalr/_static/image27.png)

    *Rückwand generiert Tabellen*
9. Mit der rechten Maustaste die **SignalR.Messages\_0** Tabelle, und wählen Sie **Ansichtsdaten**.

    ![Tabelle für die SignalR-Backplane Nachrichten](real-time-web-applications-with-signalr/_static/image28.png)

    *Tabelle für die SignalR-Backplane Nachrichten*
10. Sehen Sie die verschiedenen Nachrichten, die an die **Hub** , wenn die Trivia Fragen zu beantworten. Der Rückwand verteilt diese Nachrichten in einer beliebigen Instanz verbunden.

    ![Rückwand Nachrichten-Tabelle](real-time-web-applications-with-signalr/_static/image29.png)

    *Rückwand Nachrichten-Tabelle*

* * *

<a id="Summary"></a>
## <a name="summary"></a>Zusammenfassung

In dieser praktischen Übungseinheit haben Sie gelernt, wie hinzuzufügende **SignalR** können Sie Ihre Anwendung "und" Send-Benachrichtigungen vom Server zu Ihren verbundenen Clients, die mithilfe **Hubs**. Darüber hinaus haben Sie gelernt, skalieren Sie Ihre Anwendung mithilfe einer *Rückwandplatine* Komponente auf, wenn Ihre Anwendung in mehreren IIS-Instanzen bereitgestellt wird.

---
uid: signalr/overview/deployment/tutorial-signalr-self-host
title: 'Tutorial: Selfhosten von SignalR | Microsoft-Dokumentation'
author: bradygaster
description: In diesem Tutorial wird gezeigt, wie Sie einen selbst gehosteten SignalR-2-Server zu erstellen und wie Sie eine Verbindung mit einem JavaScript-Client herstellen. Softwareversionen, die in diesem Tutorial V verwendet...
ms.author: bradyg
ms.date: 06/10/2014
ms.assetid: 400db427-27af-4f2f-abf0-5486d5e024b5
msc.legacyurl: /signalr/overview/deployment/tutorial-signalr-self-host
msc.type: authoredcontent
ms.openlocfilehash: 6a6359d59a4b715e13fe2bbcef57da6d6d6294b5
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54835751"
---
<a name="tutorial-signalr-self-host"></a>Tutorial: Selfhosten von SignalR
====================
durch [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

[Abgeschlossenes Projekt herunterladen](http://code.msdn.microsoft.com/SignalR-Self-Host-Sample-6da0f383)

> In diesem Tutorial wird gezeigt, wie Sie einen selbst gehosteten SignalR-2-Server zu erstellen und wie Sie eine Verbindung mit einem JavaScript-Client herstellen.
>
> ## <a name="software-versions-used-in-the-tutorial"></a>Softwareversionen, die in diesem Tutorial verwendet werden.
>
>
> - [Visual Studio 2013](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - .NET 4.5
> - SignalR-Version 2
>
>
>
> ## <a name="using-visual-studio-2012-with-this-tutorial"></a>In diesem Tutorial mithilfe von Visual Studio 2012
>
>
> Um Visual Studio 2012 mit diesem Tutorial verwenden möchten, führen Sie folgende Schritte aus:
>
> - Aktualisieren Ihrer [-Paket-Manager](http://docs.nuget.org/docs/start-here/installing-nuget) auf die neueste Version.
> - Installieren Sie die [Webplattform-Installer](https://www.microsoft.com/web/downloads/platform.aspx).
> - Suchen Sie in den Webplattform-Installer, und installieren Sie **ASP.NET und Web Tools 2013.1 für Visual Studio 2012**. Dadurch wird Visual Studio-Vorlagen für SignalR-Klassen wie z. B. installiert **Hub**.
> - Einige Vorlagen (z. B. **OWIN-Startklasse**) nicht zur Verfügung; verwenden Sie für diese stattdessen eine Klassendatei.
>
>
> ## <a name="questions-and-comments"></a>Fragen und Kommentare
>
> Lassen Sie Feedback, auf wie Ihnen in diesem Tutorial gefallen hat und was wir in den Kommentaren am unteren Rand der Seite verbessern können. Wenn Sie Fragen, die nicht direkt mit dem Tutorial verknüpft sind haben, können Sie sie veröffentlichen das [ASP.NET SignalR-Forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) oder [StackOverflow.com](http://stackoverflow.com/).


## <a name="overview"></a>Übersicht

Ein SignalR-Server in der Regel in einer ASP.NET-Anwendung in IIS gehostet wird, aber sie können auch selbst gehostet sein (z. B. in einer Konsolenanwendung oder eine Windows-Dienst) mithilfe der Self-Hosting-Bibliothek. Diese Bibliothek, wie alle SignalR 2 baut auf OWIN ([Open Web Interface for .NET](http://owin.org)). OWIN definiert eine Abstraktion zwischen Webservern für .NET und Webanwendungen. OWIN entkoppelt die Webanwendung aus dem Server, wodurch OWIN ideal für das Selbsthosting einer Webanwendung in Ihrem eigenen Prozess, außerhalb von IIS.

Gründe für die nicht in IIS hosten umfassen:

- Umgebungen, in denen IIS nicht wünschenswert ist, z. B. einer vorhandenen Serverfarm ohne IIS oder verfügbar ist.
- Der Mehraufwand an Leistung von IIS muss vermieden werden.
- SignalR-Funktion ist eine vorhandene Anwendung hinzugefügt werden, die in einem Windows-Dienst, Azure-workerrolle oder anderer Prozess ausgeführt wird.

Wenn eine Projektmappe als Self-Hosting zur Verbesserung der Leistung entwickelt wird, wird auch Test die Anwendung in IIS gehostet wird, um zu bestimmen, die Leistungsvorteile bei empfohlen.

In diesem Tutorial enthält die folgenden Abschnitte:

- [Erstellen des Servers](#server)
- [Zugriff auf den Server mit einem JavaScript-client](#js)

<a id="server"></a>

## <a name="creating-the-server"></a>Erstellen des Servers

In diesem Tutorial erstellen Sie einen Server, der gehostet wird in einer Konsolenanwendung, aber der Server kann in jede Art von Prozess, z. B. eine Windows-Dienst oder die Azure-workerrolle gehostet werden. Beispielcode zum Hosten eines SignalR-Servers in einem Windows-Dienst finden Sie unter [Self-Hosting SignalR in einem Windows-Dienst](https://code.msdn.microsoft.com/SignalR-self-hosted-in-6ff7e6c3).

1. Öffnen Sie Visual Studio 2013 mit Administratorrechten aus. Wählen Sie **Datei**, **neues Projekt**. Wählen Sie **Windows** unter der **Visual C#-** Knoten in der **Vorlagen** Bereich, und wählen Sie die **Konsolenanwendung** Vorlage. Nennen Sie das neue Projekt "SignalRSelfHost", und klicken Sie auf **OK**.

    ![](tutorial-signalr-self-host/_static/image1.png)
2. Öffnen Sie die NuGet-Paket-Manager-Konsole dazu **Tools** > **NuGet Package Manager** > **-Paket-Manager-Konsole**.
3. Geben Sie in der Paket-Manager-Konsole den folgenden Befehl aus:

    [!code-powershell[Main](tutorial-signalr-self-host/samples/sample1.ps1)]

    Mit diesem Befehl werden dem Projekt die Bibliotheken selfhosten von SignalR 2 hinzugefügt.
4. Geben Sie in der Paket-Manager-Konsole den folgenden Befehl aus:

    [!code-powershell[Main](tutorial-signalr-self-host/samples/sample2.ps1)]

    Dieser Befehl fügt der Microsoft.Owin.Cors-Bibliothek zum Projekt hinzu. Diese Bibliothek wird für die Unterstützung für domänenübergreifende, verwendet die für Anwendungen erforderlich ist, die SignalR und eine Webseite-Client in unterschiedlichen Domänen hosten. Da Sie den SignalR-Server und der Webclient an unterschiedlichen Ports hosten, bedeutet dies, dass es sich bei diesem domänenübergreifende für die Kommunikation zwischen diesen Komponenten aktiviert werden muss.
5. Ersetzen Sie den Inhalt von Program.cs durch den folgenden Code.

    [!code-csharp[Main](tutorial-signalr-self-host/samples/sample3.cs)]

    Der obige Code enthält drei Klassen:

    - **Programm**, einschließlich der **Main** Methode, die den primären Pfad, der Ausführung zu definieren. Bei dieser Methode eine Webanwendung vom Typ **Start** wird gestartet, an der angegebenen URL (`http://localhost:8080`). Wenn Sicherheit auf dem Endpunkt erforderlich ist, kann SSL implementiert werden. Weitere Informationen finden Sie unter [How to: Konfigurieren eines Anschlusses mit einem SSL-Zertifikat](https://msdn.microsoft.com/library/ms733791.aspx) für Weitere Informationen.
    - **Beim Start**, Klasse, die die Konfiguration für den SignalR-Server enthält (die einzige Konfiguration, die in diesem Tutorial wird verwendet, ist der Aufruf von `UseCors`), und der Aufruf von `MapSignalR`, die Routen für alle Objekte der Hub im Projekt erstellt.
    - **MyHub**, die SignalR-Hub-Klasse, die die Anwendung für Clients bereitstellt. Diese Klasse verfügt über eine einzelne Methode, **senden**, dass Clients aufrufen werden, um eine Nachricht an alle anderen verbundenen Clients zu übertragen.
6. Kompilieren Sie die Anwendung, und führen Sie sie aus. Die Adresse, die der Server ausgeführt wird, sollte in einem Konsolenfenster angezeigt.

    ![](tutorial-signalr-self-host/_static/image2.png)
7. Wenn die Ausführung mit der Ausnahme fehlschlägt `System.Reflection.TargetInvocationException was unhandled`, Sie müssen Visual Studio mit Administratorrechten neu zu starten.
8. Beenden Sie die Anwendung vor dem Fortfahren mit dem nächsten Abschnitt.

<a id="js"></a>

## <a name="accessing-the-server-with-a-javascript-client"></a>Zugriff auf den Server mit einem JavaScript-client

In diesem Abschnitt verwenden Sie den gleichen JavaScript-Client aus der [Tutorials: Erste Schritte](../getting-started/tutorial-getting-started-with-signalr.md). Wir stellen nur eine Änderung an den Client, um die Hub-URL explizit zu definieren. Mit einer selbst gehosteten Anwendung die Server unbedingt auf die gleiche Adresse wie der Verbindungs-URL (aufgrund von Reverseproxys und Load balancer), also die URL muss explizit definiert werden möglicherweise nicht.

1. In **Projektmappen-Explorer**mit der rechten Maustaste auf die Projektmappe, und wählen Sie **hinzufügen**, **neues Projekt**. Wählen Sie die **Web** Knoten, und wählen die **ASP.NET-Webanwendung** Vorlage. Nennen Sie das Projekt "JavascriptClient", und klicken Sie auf **OK**.

    ![](tutorial-signalr-self-host/_static/image3.png)
2. Wählen Sie die **leere** -Vorlage und die übrigen Optionen deaktiviert lassen. Wählen Sie **erstellen Projekt**.

    ![](tutorial-signalr-self-host/_static/image4.png)
3. In der Konsole des Paket-Manager, wählen Sie das Projekt "JavascriptClient" in der **Standardprojekt** Dropdown-Liste, und führen Sie den folgenden Befehl aus:

    [!code-powershell[Main](tutorial-signalr-self-host/samples/sample4.ps1)]

    Dieser Befehl installiert die SignalR und JQuery-Bibliotheken, die Sie benötigen, auf dem Client.
4. Mit der rechten Maustaste auf Ihr Projekt, und wählen Sie **hinzufügen**, **neues Element**. Wählen Sie die **Web** Knoten, und wählen Sie HTML-Seite. Nennen Sie die Seite **"default.HTML"**.

    ![](tutorial-signalr-self-host/_static/image5.png)
5. Ersetzen Sie den Inhalt der neuen HTML-Seite mit den folgenden Code ein. Stellen Sie sicher, dass die Skriptverweise hier die Skripts im Ordner "Scripts" des Projekts übereinstimmen.

    [!code-html[Main](tutorial-signalr-self-host/samples/sample5.html?highlight=31-32)]

    Der folgende Code (im obigen Codebeispiel hervorgehoben), ist das Hinzufügen, die Sie an den Client in diesem Erste-Schritte-Tutorial (zusätzlich zum Aktualisieren des Codes auf SignalR-Betaversion 2) verwendet. Diese Codezeile wird explizit die Basis-URL für SignalR, auf dem Server.

    [!code-javascript[Main](tutorial-signalr-self-host/samples/sample6.js)]
6. Mit der rechten Maustaste auf die Projektmappe, und wählen **Startprojekte festlegen...** . Wählen Sie die **mehrere Startprojekte** Optionsfeld aus, und legen Sie beide Projekte **Aktion** zu **starten**.

    ![](tutorial-signalr-self-host/_static/image6.png)
7. Mit der rechten Maustaste auf "Default.html", und wählen Sie **als Startseite festlegen**.
8. Führen Sie die Anwendung aus. Die Seite "und" Server werden gestartet. Möglicherweise müssen Sie die Webseite neu zu laden (oder wählen Sie **Weiter** im Debugger) Wenn die Seite geladen wird, bevor der Server gestartet wurde.
9. Geben Sie einen Benutzernamen, wenn Sie aufgefordert werden, im Browser. Kopieren Sie die URL der Seite in einer anderen Browserregisterkarte oder Fenster, und geben Sie einen anderen Benutzernamen. Sie werden zum Senden von Nachrichten aus einem Browser zum anderen, wie in den Tutorials: Erste Schritte können.

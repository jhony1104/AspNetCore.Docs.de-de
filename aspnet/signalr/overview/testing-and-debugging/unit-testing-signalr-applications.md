---
uid: signalr/overview/testing-and-debugging/unit-testing-signalr-applications
title: Komponententests für SignalR-Anwendungen | Microsoft-Dokumentation
author: bradygaster
description: Dieser Artikel beschreibt, wie Sie die Unit Testing-Funktionen von SignalR 2.0 zu verwenden.
ms.author: bradyg
ms.date: 06/10/2014
ms.assetid: d1983524-e0d5-4ee6-9d87-1f552f7cb964
msc.legacyurl: /signalr/overview/testing-and-debugging/unit-testing-signalr-applications
msc.type: authoredcontent
ms.openlocfilehash: cb4eb25aeedfe31ac2606de9fe7d280eb95ce2e6
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836037"
---
<a name="unit-testing-signalr-applications"></a>Komponententests für SignalR-Anwendungen
====================
durch [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> In diesem Artikel wird beschrieben, mit den Komponententest-Features von SignalR 2.
>
> ## <a name="software-versions-used-in-this-topic"></a>In diesem Thema verwendeten Softwareversionen
>
>
> - [Visual Studio 2013](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - .NET 4.5
> - SignalR-Version 2
>
>
>
> ## <a name="questions-and-comments"></a>Fragen und Kommentare
>
> Lassen Sie Feedback, auf wie Ihnen in diesem Tutorial gefallen hat und was wir in den Kommentaren am unteren Rand der Seite verbessern können. Wenn Sie Fragen, die nicht direkt mit dem Tutorial verknüpft sind haben, können Sie sie veröffentlichen das [ASP.NET SignalR-Forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) oder [StackOverflow.com](http://stackoverflow.com/).


<a id="unit"></a>
## <a name="unit-testing-signalr-applications"></a>Komponententests für SignalR-Anwendungen

Sie können die Komponententestfunktionen in SignalR 2 verwenden, um Komponententests für Ihre SignalR-Anwendung zu erstellen. SignalR 2 enthält die [IHubCallerConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.ihubcallerconnectioncontext(v=vs.118).aspx) -Schnittstelle, die verwendet werden kann, um ein mock-Objekt zum Simulieren von Ihrem hubmethoden für das Testen zu erstellen.

In diesem Abschnitt fügen Sie Komponententests für die Anwendung erstellt, der [Tutorials: Erste Schritte](../getting-started/tutorial-getting-started-with-signalr.md) mit [XUnit.net](https://github.com/xunit/xunit) und [Moq](https://github.com/Moq/moq4).

XUnit.net dienen zum Steuern des Tests; Moq wird zum Erstellen einer [simulieren](http://en.wikipedia.org/wiki/Mock_object) Objekt zum Testen. Anderen Mockframeworks können verwendet werden, falls gewünscht. [NSubstitute](http://nsubstitute.github.io/) ist auch eine gute Wahl. In diesem Tutorial wird veranschaulicht, wie Sie das mock-Objekt auf zwei Arten einrichten: Zunächst mithilfe einer `dynamic` Objekt (eingeführt in .NET Framework 4) und dann mithilfe einer Schnittstelle.

### <a name="contents"></a>Inhalt

In diesem Tutorial enthält die folgenden Abschnitte.

- [Komponententests mit dynamischen](#dynamic)
- [Komponententests, die nach Typ](#type)

<a id="dynamic"></a>
### <a name="unit-testing-with-dynamic"></a>Komponententests mit dynamischen

In diesem Abschnitt fügen Sie einen Komponententest für die Anwendung erstellt, der [Tutorials: Erste Schritte](../getting-started/tutorial-getting-started-with-signalr.md) ein dynamisches Objekt verwenden.

1. Installieren Sie die [XUnit Runner Erweiterung](https://visualstudiogallery.msdn.microsoft.com/463c5987-f82b-46c8-a97e-b1cde42b9099) für Visual Studio 2013.
2. Entweder ist zum Abschließen der [Tutorials: Erste Schritte](../getting-started/tutorial-getting-started-with-signalr.md), oder die fertige Anwendung herunterladen [MSDN Code Gallery](https://code.msdn.microsoft.com/SignalR-Getting-Started-b9d18aa9).
3. Wenn Sie die Download-Version erste Schritte-Anwendung verwenden, öffnen Sie **-Paket-Manager-Konsole** , und klicken Sie auf **wiederherstellen** das SignalR-Paket zum Projekt hinzufügen.

    ![Wiederherstellen von Paketen](unit-testing-signalr-applications/_static/image1.png)
4. Fügen Sie ein Projekt der Projektmappe für den Komponententest aus. Mit der rechten Maustaste in der Projektmappe in **Projektmappen-Explorer** , und wählen Sie **hinzufügen**, **neues Projekt...** . Unter den **c#** Knoten die **Windows** Knoten. Wählen Sie **Klassenbibliothek**. Nennen Sie das neue Projekt **Testbibliothek** , und klicken Sie auf **OK**.

    ![Erstellen Sie die Bibliothek für das Testen](unit-testing-signalr-applications/_static/image2.png)
5. Fügen Sie einen Verweis auf das Projekt SignalRChat im Testprojekt-Bibliothek. Mit der rechten Maustaste die **Testbibliothek** Projekt, und wählen **hinzufügen**, **Verweis...** . Wählen Sie die **Projekte** unter den Knoten der **Lösung** Knoten, und überprüfen Sie **SignalRChat**. Klicken Sie auf **OK**.

    ![Projektverweis hinzufügen](unit-testing-signalr-applications/_static/image3.png)
6. Fügen Sie die SignalR, Moq und XUnit-Pakete, die **Testbibliothek** Projekt. In der **-Paket-Manager-Konsole**legen die **Standardprojekt** Dropdownliste aus, um **Testbibliothek**. Führen Sie die folgenden Befehle im Konsolenfenster angezeigt:

   - `Install-Package Microsoft.AspNet.SignalR`
   - `Install-Package Moq`
   - `Install-Package XUnit`

     ![Installieren von Paketen](unit-testing-signalr-applications/_static/image4.png)
7. Erstellen Sie die Testdatei. Mit der rechten Maustaste die **Testbibliothek** Projekt, und klicken Sie auf **hinzufügen...** , **Klasse**. Nennen Sie die neue Klasse **Tests.cs**.
8. Ersetzen Sie den Inhalt der Tests.cs durch den folgenden Code ein.

    [!code-csharp[Main](unit-testing-signalr-applications/samples/sample1.cs)]

    Im obigen Code wird ein Testclient erstellt, mit der `Mock` -Objekt aus der [Moq](https://github.com/Moq/moq4) Bibliothek, die vom Typ [IHubCallerConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.ihubcallerconnectioncontext(v=vs.118).aspx) (weisen Sie in SignalR 2.1 `dynamic` für den Typ die Parameter.) Die `IHubCallerConnectionContext` Schnittstelle ist das Proxyobjekt, mit denen Sie die Methoden auf dem Client aufrufen. Die `broadcastMessage` Funktion ist dann für den simulierten Client definiert, damit sie aufgerufen werden kann, indem die `ChatHub` Klasse. Die Test-Engine ruft dann die `Send` Methode der `ChatHub` -Klasse, die wiederum die simulierte aufruft, `broadcastMessage` Funktion.
9. Erstellen Sie die Projektmappe durch Drücken von **F6**.
10. Führen Sie den Komponententest aus. Wählen Sie in Visual Studio **Test**, **Windows**, **Test-Explorer**. Im Fenster Test-Explorers mit der Maustaste **HubsAreMockableViaDynamic** , und wählen Sie **ausgewählte Tests ausführen**.

    ![Test-Explorer](unit-testing-signalr-applications/_static/image5.png)
11. Stellen Sie sicher, dass der Test erfolgreich war, anhand der im unteren Bereich im Test-Explorer-Fenster. Das Fenster wird angezeigt, das erfolgreiche Ergebnis.

    ![Bestandene Tests](unit-testing-signalr-applications/_static/image6.png)

<a id="type"></a>
### <a name="unit-testing-by-type"></a>Komponententests, die nach Typ

In diesem Abschnitt fügen Sie einen Test für die Anwendung erstellt, der [Tutorials: Erste Schritte](../getting-started/tutorial-getting-started-with-signalr.md) mithilfe einer Schnittstelle, die die Methode, die getestet werden, enthält.

1. Führen Sie die Schritte 1 bis 7 in der [Unit testing mit dynamischen](#dynamic) oben genannten Tutorial.
2. Ersetzen Sie den Inhalt der Tests.cs durch den folgenden Code ein.

    [!code-csharp[Main](unit-testing-signalr-applications/samples/sample2.cs)]

    Im obigen Code wird eine Schnittstelle erstellt, die Signatur der Definition der `broadcastMessage` Methode, die für die die Test-Engine einen mock-Client erstellen. Ein mock Client erstellt dann mithilfe der `Mock` Objekt des Typs [IHubCallerConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.ihubcallerconnectioncontext(v=vs.118).aspx) (weisen Sie in SignalR 2.1 `dynamic` für den Parameter.) Die `IHubCallerConnectionContext` Schnittstelle ist das Proxyobjekt, mit denen Sie die Methoden auf dem Client aufrufen.

    Der Test erstellt dann eine Instanz des `ChatHub`, und erstellt dann eine Pseudoversion des der `broadcastMessage` -Methode, die wiederum, durch den Aufruf aufgerufen wird der `Send` Methode auf dem Hub.
3. Erstellen Sie die Projektmappe durch Drücken von **F6**.
4. Führen Sie den Komponententest aus. Wählen Sie in Visual Studio **Test**, **Windows**, **Test-Explorer**. Im Fenster Test-Explorers mit der Maustaste **HubsAreMockableViaDynamic** , und wählen Sie **ausgewählte Tests ausführen**.

    ![Test-Explorer](unit-testing-signalr-applications/_static/image7.png)
5. Stellen Sie sicher, dass der Test erfolgreich war, anhand der im unteren Bereich im Test-Explorer-Fenster. Das Fenster wird angezeigt, das erfolgreiche Ergebnis.

    ![Bestandene Tests](unit-testing-signalr-applications/_static/image8.png)

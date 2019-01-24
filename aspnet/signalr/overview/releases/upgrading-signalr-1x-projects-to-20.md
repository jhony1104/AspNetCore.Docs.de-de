---
uid: signalr/overview/releases/upgrading-signalr-1x-projects-to-20
title: Aktualisieren von SignalR 1.x-Projekte auf Version 2 | Microsoft-Dokumentation
author: bradygaster
description: In diesem Thema wird beschrieben, wie Sie ein vorhandenes SignalR 1.x-Projekt auf SignalR aktualisieren 2.x, und Beheben von Problemen, die während des Upgrades auftreten können...
ms.author: bradyg
ms.date: 06/10/2014
ms.assetid: adcfef99-9bc5-489d-a91b-9b7c2bc35e04
msc.legacyurl: /signalr/overview/releases/upgrading-signalr-1x-projects-to-20
msc.type: authoredcontent
ms.openlocfilehash: a1cb4903f3cdeef70ffd0f624a3a2170f641a395
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837337"
---
<a name="upgrading-signalr-1x-projects-to-version-2"></a>Aktualisieren von SignalR 1.x-Projekte auf Version 2
====================
durch [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> In diesem Thema wird beschrieben, wie Sie ein vorhandenes SignalR 1.x-Projekt auf SignalR aktualisieren 2.x, und Beheben von Problemen, die während des Upgrades auftreten können.
>
> ## <a name="software-versions-used-in-the-tutorial"></a>Softwareversionen, die in diesem Tutorial verwendet werden.
>
>
> - [Visual Studio 2013](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - .NET 4.5
> - SignalR-Versionen 1 und 2
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


SignalR 2 bietet eine einheitliche Entwicklungsumgebung bietet über Serverplattformen hinweg [OWIN](http://owin.org). Dieser Artikel beschreibt die Schritte, die erforderlich sind, um einer SignalR 1.x-Anwendung auf Version 2 zu aktualisieren.

Obwohl empfohlen wird, Anwendungen mit SignalR 2 zu aktualisieren, werden SignalR 1.x weiterhin unterstützt.

Dieses Tutorial beschreibt, wie Sie eine im Internet gehostete Anwendung mit SignalR 2 zu aktualisieren. Selbst gehostete Anwendungen (diejenigen, die ein Server in einer Konsolenanwendung, Windows-Dienst oder andere geplante Prozesses hosten) werden jetzt als SignalR-2 unterstützt. Informationen zum Erstellen einer selbst gehosteten Anwendung mit SignalR 2 beginnen, finden Sie unter [Lernprogramm: Selfhosten von SignalR](../deployment/tutorial-signalr-self-host.md).

## <a name="contents"></a>Inhalt

Die folgenden Abschnitte beschreiben die Aufgaben zum beim Upgrade von SignalR-Projekte, und Beheben von Problemen, die auftreten können.

- [Anpassen von mit VSTU Aktualisieren die Tutorials: Erste Schritte mit SignalR 2](#example)
- [Problembehandlung für Fehler, die während des Upgrades](#troubleshooting)

<a id="example"></a>

## <a name="example-upgrading-the-getting-started-tutorial-application-to-signalr-2"></a>Beispiel: Aktualisieren die erste Schritte-Tutorial Anwendung mit SignalR 2

In diesem Abschnitt Aktualisieren Sie die Anwendung, die erstellt werden, der [SignalR 1.x-Version der Getting Started Tutorial](../older-versions/index.md) mit SignalR 2.

1. Nachdem Sie das erste Schritte-Tutorial abgeschlossen haben, mit der rechten Maustaste auf das Projekt, und wählen **Eigenschaften**. Überprüfen Sie, ob die **Zielframework** nastaven NA hodnotu **.NET Framework 4.5.**
2. Öffnen Sie die Paket-Manager-Konsole. Entfernen von SignalR 1.x aus dem Projekt mit dem folgenden Befehl:

    [!code-powershell[Main](upgrading-signalr-1x-projects-to-20/samples/sample1.ps1)]
3. Installieren Sie SignalR 2. mit dem folgenden Befehl ein:

    [!code-powershell[Main](upgrading-signalr-1x-projects-to-20/samples/sample2.ps1)]
4. Aktualisieren Sie in der HTML-Seite den Skriptverweis für SignalR entsprechend der Version des Skripts nun im Projekt enthalten ist.

    [!code-html[Main](upgrading-signalr-1x-projects-to-20/samples/sample3.html)]
5. Entfernen Sie den Aufruf MapHubs, in der globalen Anwendung-Klasse.

    [!code-csharp[Main](upgrading-signalr-1x-projects-to-20/samples/sample4.cs)]
6. Mit der rechten Maustaste in der Projektmappe, und wählen **hinzufügen**, **neues Element...** . Wählen Sie im Dialogfeld **Owin-Startklasse**. Nennen Sie die neue Klasse **"Startup.cs"**.

    ![](upgrading-signalr-1x-projects-to-20/_static/image1.png)
7. Ersetzen Sie den Inhalt der "Startup.cs" durch den folgenden Code:

    [!code-csharp[Main](upgrading-signalr-1x-projects-to-20/samples/sample5.cs)]

    Das Assemblyattribut Owins-Startvorgangs, führt die Klasse hinzugefügt der `Configuration` Methode, wenn die Owin gestartet wird. Dies wiederum ruft die `MapSignalR` -Methode, die Routen für alle SignalR-Hubs in der Anwendung erstellt.
8. Führen Sie das Projekt, und kopieren Sie die URL der Hauptseite in einen anderen Browser oder Browserbereich wie vorher. Jede Seite wird gefragt, einen Benutzernamen ein, und von jeder Seite gesendeten Daten beider Bereiche Browser angezeigt werden.

<a id="troubleshooting"></a>

## <a name="troubleshooting-errors-encountered-during-upgrading"></a>Problembehandlung für Fehler, die während des Upgrades

Dieser Abschnitt beschreibt Probleme, die während des Upgrades auftreten können. Eine umfangreichere Liste von Fehlern und Problemen, die mit einer SignalR-Anwendung auftreten können, finden Sie unter [SignalR Problembehandlung](../testing-and-debugging/troubleshooting.md).

### <a name="the-call-is-ambiguous-between-the-following-methods-or-properties"></a>'Der Aufruf ist nicht eindeutig zwischen folgenden Methoden oder Eigenschaften'

Dieser Fehler tritt auf, wenn ein Verweis auf `Microsoft.AspNet.SignalR.Owin` wird nicht entfernt. Dieses Paket ist veraltet. der Verweis muss entfernt werden, und die 1.x-Version des Pakets SelfHost muss deinstalliert werden.

### <a name="hub-methods-fail-silently"></a>Hub-Methoden fehl automatisch.

Überprüfen, ob die Skriptverweise in Ihrem Client bis zum Datums- und, sind die `OwinStartup` Attribut für Ihr Startup-Klasse, die richtige Klasse und den Assemblynamen für das Projekt hat. Versuchen Sie außerdem, die die Hubs-Adresse (/ Signalr/Hubs) in Ihrem Browser zu öffnen; alle Fehler, die angezeigt wird bietet weitere Informationen zu welcher Fehler vorliegt.

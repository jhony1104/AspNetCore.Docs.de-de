---
uid: signalr/overview/performance/using-signalr-performance-counters-in-an-azure-web-role
title: Verwenden von SignalR-Leistungsindikatoren in einer Azure-Webrolle | Microsoft-Dokumentation
author: guardrex
description: Informationen zum Installieren und Verwenden von SignalR-Leistungsindikatoren in einer Azure-Webrolle.
keywords: ASP.NET,SignalR,Performance Leistungsindikator und Azure-Webrolle
ms.author: bradyg
ms.date: 10/03/2018
ms.assetid: 2a127d3b-21ed-4cc9-bec0-cdab4e742a25
msc.legacyurl: /signalr/overview/performance/using-signalr-performance-counters-in-an-azure-web-role
msc.type: authoredcontent
ms.openlocfilehash: 8e17e945bc144731dd149bd7ddfc9e29160eaf0b
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836482"
---
# <a name="using-signalr-performance-counters-in-an-azure-web-role"></a>Verwenden von SignalR-Leistungsindikatoren in einer Azure-Webrolle

Von [Luke Latham](https://github.com/guardrex)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

SignalR-Leistungsindikatoren werden verwendet, um die Leistung Ihrer app in einer Azure-Webrolle zu überwachen. Die Leistungsindikatoren werden von Microsoft Azure-Diagnose erfasst. SignalR-Leistungsindikatoren in Azure mit der Installation *signalr.exe*, das gleiche Tool zum eigenständigen oder lokalen apps. Da Azure-Rollen nur vorübergehend auftreten, konfigurieren Sie eine app zum Installieren und Registrieren von SignalR-Leistungsindikatoren nach dem Start.

## <a name="prerequisites"></a>Vorraussetzungen

* Visual Studio 2015 oder [2017](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017)
* [Microsoft Azure SDK für Visual Studio](https://azure.microsoft.com/downloads/) **beachten: Starten Sie Ihren Computer nach der Installation des SDK neu.**
* Microsoft Azure-Abonnement: Für ein kostenloses Azure-Testkonto registrieren zu können, finden Sie unter [Azure – kostenlose Testversion](https://azure.microsoft.com/free/).

## <a name="creating-an-azure-web-role-application-that-exposes-signalr-performance-counters"></a>Erstellen einer Anwendung für Azure-Webrolle, stellt SignalR-Leistungsindikatoren

1. Öffnen Sie Visual Studio.

2. Klicken Sie in Visual Studio auf **Datei** > **Neu** > **Projekt**.

3. In der **neues Projekt** wählen Sie im Dialogfeld die **Visual C#-** > **Cloud** Kategorie auf der linken Seite, und wählen Sie dann die **fürAzure-Clouddienst** Vorlage. Benennen Sie die app **SignalRPerfCounters** , und wählen Sie **OK**.

   ![Neue Cloud-Anwendung](using-signalr-performance-counters-in-an-azure-web-role/_static/image1.png)

   > [!NOTE]
   > Wenn Sie nicht angezeigt wird der **Cloud** Vorlagenkategorie oder **Azure-Clouddienst** , müssen Sie zum Installieren der **Azure-Entwicklung** Workload für Visual Studio 2017. Wählen Sie die **Visual Studio-Installer öffnen** Link auf der linken unteren Seite des der **neues Projekt** Dialogfeld, um Visual Studio-Installer zu öffnen. Wählen Sie die **Azure-Entwicklung** Workload, und wählen Sie dann **ändern** Installieren der arbeitsauslastungs.
   >
   > ![Azure-entwicklungsworkload in Visual Studio-Installer](using-signalr-performance-counters-in-an-azure-web-role/_static/azure-development-workload.png)

4. In der **neuen Microsoft Azure-Cloud-Dienst** wählen Sie im Dialogfeld **ASP.NET-Webrolle** , und wählen Sie die >, um die Rolle zum Projekt hinzuzufügen. Klicken Sie auf **OK**.

   ![Hinzufügen von ASP.NET Web-Rolle](using-signalr-performance-counters-in-an-azure-web-role/_static/image2.png)

5. In der **neue ASP.NET-Webanwendung – WebRole1** wählen Sie im Dialogfeld die **MVC** Vorlage, und wählen Sie dann **OK**.

   ![Hinzufügen von MVC und Web-API](using-signalr-performance-counters-in-an-azure-web-role/_static/image3.png)

6. In **Projektmappen-Explorer**öffnen die *"Diagnostics.wadcfgx"* Datei **WebRole1**.

   ![Projektmappen-Explorer "Diagnostics.wadcfgx"](using-signalr-performance-counters-in-an-azure-web-role/_static/image4.png)

7. Speichern Sie die Datei, und Ersetzen Sie den Inhalt der Datei mit der folgenden Konfiguration:

   [!code-xml[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample1.xml)]

8. Öffnen der **-Paket-Manager-Konsole** aus **Tools** > **NuGet-Paket-Manager**. Geben Sie die folgenden Befehle aus, um die neueste Version von SignalR und des SignalR-Dienstprogramme-Pakets zu installieren:

   [!code-powershell[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample2.ps1)]

9. Konfigurieren Sie die app, um die SignalR-Leistungsindikatoren in der Rolleninstanz installieren, wenn er gestartet oder neu gestartet wird. In **Projektmappen-Explorer**, mit der rechten Maustaste auf die **WebRole1** Projekt, und wählen **hinzufügen** > **neuer Ordner**. Nennen Sie diesen Ordner *Start*.

   ![Startordner hinzufügen](using-signalr-performance-counters-in-an-azure-web-role/_static/image5.png)

10. Kopieren der *signalr.exe* Datei (hinzugefügt, mit der **Microsoft.AspNet.SignalR.Utils** Paket) von \<Projektordner > / SignalRPerfCounters/packages/Microsoft.AspNet.SignalR.Utils.\< Version > / tools unter der *Start* Ordner, die Sie im vorherigen Schritt erstellt haben.

11. In **Projektmappen-Explorer**, mit der rechten Maustaste die *Start* Ordner, und wählen **hinzufügen** > **vorhandenes Element**. Wählen Sie im daraufhin angezeigten Dialogfeld, *signalr.exe* , und wählen Sie **hinzufügen**.

    ![Signalr.exe zu Projekt hinzufügen](using-signalr-performance-counters-in-an-azure-web-role/_static/image6.png)

12. Mit der rechten Maustaste auf die *Start* Ordner, die Sie erstellt haben. Klicken Sie auf **Hinzufügen** > **Neues Element**. Wählen Sie die **allgemeine** Knoten **Textdatei**, und nennen Sie das neue Element *SignalRPerfCounterInstall.cmd*. Diese Befehlsdatei ist die SignalR-Leistungsindikatoren in der Web-Rolle installieren.

    ![Erstellen von SignalR Performance Counter-Batch-Installationsdatei](using-signalr-performance-counters-in-an-azure-web-role/_static/image7.png)

13. Wenn Visual Studio erstellt die *SignalRPerfCounterInstall.cmd* -Datei wird automatisch geöffnet im Hauptfenster. Ersetzen Sie den Inhalt der Datei mit dem folgenden Skript, und klicken Sie dann speichern Sie und schließen Sie die Datei. Dieses Skript führt *signalr.exe*, der die Rolleninstanz die SignalR-Leistungsindikatoren hinzugefügt.

    [!code-console[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample3.cmd)]

14. Wählen Sie die *signalr.exe* Datei **Projektmappen-Explorer**. In der Datei **Eigenschaften**legen **in Ausgabeverzeichnis kopieren** zu **immer kopieren**.

    ![Legen Sie in Ausgabeverzeichnis kopieren immer kopieren](using-signalr-performance-counters-in-an-azure-web-role/_static/image8.png)

15. Wiederholen Sie den vorherigen Schritt für die *SignalRPerfCounterInstall.cmd* Datei.


16. Mit der rechten Maustaste auf die *SignalRPerfCounterInstall.cmd* und wählen Sie **Öffnen mit**. Wählen Sie im daraufhin angezeigten Dialogfeld, **Binär-Editor** , und wählen Sie **OK**.

    ![Mit dem Binär-Editor öffnen](using-signalr-performance-counters-in-an-azure-web-role/_static/image9.png)

17. Klicken Sie im Binär-Editor wählen Sie alle führenden Bytes in der Datei, und löschen. Speichern und schließen Sie die Datei.

    ![Löschen Sie die führenden bytes](using-signalr-performance-counters-in-an-azure-web-role/_static/image10.png)

18. Open *"Servicedefinition.csdef"* und fügen Sie eine Startaufgabe, die ausgeführt wird die *SignalrPerfCounterInstall.cmd* -Datei, wenn der Dienst wird gestartet:

    [!code-xml[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample4.xml?highlight=4-7)]

19. Open `Views/Shared/_Layout.cshtml` und entfernen Sie das jQuery-Pakets-Skript am Ende der Datei.

    [!code-cshtml[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample5.cshtml)]

20. Hinzufügen ein JavaScript-Clients, die ständig aufruft, die `increment` Methode auf dem Server. Open `Views/Home/Index.cshtml` und Ersetzen Sie den Inhalt durch den folgenden Code:

    [!code-cshtml[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample6.cshtml)]

21. Erstellen eines neuen Ordners in der **WebRole1** Projekt mit dem Namen *Hubs*. Mit der rechten Maustaste die *Hubs* Ordner **Projektmappen-Explorer** , und wählen Sie **hinzufügen** > **neues Element**. In der **neues Element hinzufügen** wählen Sie im Dialogfeld die **Web** > **SignalR** Kategorie, und wählen Sie dann die **SignalR-Hubklasse (v2)** Elementvorlage. Benennen Sie den neuen Hub *MyHub.cs* , und wählen Sie **hinzufügen**.

    ![Hinzufügen von SignalR-Hubklasse zu dem Ordner "Hubs" in das Dialogfeld "Neues Element hinzufügen"](using-signalr-performance-counters-in-an-azure-web-role/_static/image13.png)

22. *MyHub.cs* wird automatisch im Hauptfenster geöffnet. Ersetzen Sie den Inhalt durch den folgenden Code, und klicken Sie dann speichern Sie und schließen Sie die Datei:

    [!code-csharp[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample7.cs)]

23. *[Crank.exe](signalr-connection-density-testing-with-crank.md)*  eine Verbindung Dichte Testtool, mit der Codebasis von SignalR bereitgestellt wird. Da Crank eine permanente Verbindung erforderlich ist, fügen Sie eine auf Ihrer Website für die Verwendung beim Testen. Hinzufügen eines neuen Ordners, der **WebRole1** -Projekt namens *PersistentConnections*. Mit der rechten Maustaste in diesem Ordner, und wählen Sie **hinzufügen** > **Klasse**. Benennen Sie die neue Klassendatei *MyPersistentConnections.cs* , und wählen Sie **hinzufügen**.

24. Visual Studio öffnet die *MyPersistentConnections.cs* -Datei in das Hauptfenster. Ersetzen Sie den Inhalt durch den folgenden Code, und klicken Sie dann speichern Sie und schließen Sie die Datei:

    [!code-csharp[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample8.cs)]

25. Mithilfe der `Startup` -Klasse, die SignalR-Objekte zu starten, wenn OWIN wird gestartet. Öffnen oder erstellen Sie *"Startup.cs"* und Ersetzen Sie den Inhalt durch den folgenden Code:

    [!code-csharp[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample9.cs)]

    Im obigen Code die `OwinStartup` -Attribut markiert diese Klasse, um OWIN zu starten. Die `Configuration` Methode mit dem SignalR beginnt.

26. Testen Sie Ihre Anwendung in Microsoft Azure-Emulator durch Drücken von **F5**.

    > [!NOTE]
    > Wenn auftreten eine **FileLoadException** an **MapSignalR**, ändern Sie die bindungsumleitungen in *"Web.config"* folgt:

    [!code-xml[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample12.xml?highlight=3,7)]

27. Warten Sie etwa eine Minute. Öffnen Sie das Cloud-Explorer-Toolfenster in Visual Studio (**Ansicht** > **Cloud-Explorer**), und erweitern Sie den Pfad `(Local)/Storage Accounts/(Development)/Tables`. Doppelklicken Sie auf **WADPerformanceCountersTable**. SignalR-Leistungsindikatoren in den Tabellendaten sollte angezeigt werden. Wenn Sie in der Tabelle nicht angezeigt wird, müssen Sie möglicherweise auf Ihre Azure Storage-Anmeldeinformationen erneut eingeben. Sie müssen möglicherweise die **aktualisieren** Schaltfläche, um die Tabelle in finden Sie unter **Cloud-Explorer** , oder wählen die **aktualisieren** Schaltfläche im Fenster Tabelle öffnen, um die Daten in der Tabelle finden Sie unter.

    ![Auswählen der WAD-Leistungsindikatortabelle im Cloud-Explorer von Visual Studio](using-signalr-performance-counters-in-an-azure-web-role/_static/image11.png)

    ![In der WAD-Leistungsindikatortabelle erfassten Leistungsindikatoren angezeigt](using-signalr-performance-counters-in-an-azure-web-role/_static/image12.png)

28. Aktualisieren Sie zum Testen Ihrer Anwendung in der Cloud Die **ServiceConfiguration.Cloud.cscfg** Datei und legen Sie die `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` auf eine gültige Verbindungszeichenfolge für den Azure Storage-Konto.

    [!code-xml[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample10.xml)]

29. Bereitstellen Sie die Anwendung in Ihrem Azure-Abonnement. Weitere Informationen darüber, wie Sie eine Anwendung in Azure bereitstellen, finden Sie unter [erstellen und Bereitstellen eines Clouddiensts](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-create-deploy).

30. Warten Sie einige Minuten. In **Cloud-Explorer**, suchen Sie nach der konfigurierten Speicherkonto, und suchen die `WADPerformanceCountersTable` Tabelle darin. SignalR-Leistungsindikatoren in den Tabellendaten sollte angezeigt werden. Wenn Sie in der Tabelle nicht angezeigt wird, müssen Sie möglicherweise auf Ihre Azure Storage-Anmeldeinformationen erneut eingeben. Sie müssen möglicherweise die **aktualisieren** Schaltfläche, um die Tabelle in finden Sie unter **Cloud-Explorer** , oder wählen die **aktualisieren** Schaltfläche im Fenster Tabelle öffnen, um die Daten in der Tabelle finden Sie unter.

Besonderen Dank an [Martin Richard](https://social.msdn.microsoft.com/profile/Martin+Richard) für den ursprünglichen Inhalt in diesem Tutorial verwendet.

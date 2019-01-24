---
uid: signalr/overview/older-versions/scaleout-with-windows-azure-service-bus
title: Horizontale Skalierung in SignalR mit Azure Servicebus (SignalR 1.x) | Microsoft-Dokumentation
author: bradygaster
description: ''
ms.author: bradyg
ms.date: 05/01/2013
ms.assetid: 501db899-e68c-49ff-81b2-1dc561bfe908
msc.legacyurl: /signalr/overview/older-versions/scaleout-with-windows-azure-service-bus
msc.type: authoredcontent
ms.openlocfilehash: 77186f43b38a8423a1cbd4cf42723c5b9ccdd953
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837909"
---
<a name="signalr-scaleout-with-azure-service-bus-signalr-1x"></a>Horizontale Skalierung in SignalR mit Azure Servicebus (SignalR 1.x)
====================
durch [Mike Wasson](https://github.com/MikeWasson), [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

In diesem Tutorial stellen Sie eine SignalR-Anwendung für eine Windows Azure-Webrolle, mit der Service Bus-Rückwandplatine zum Verteilen von Nachrichten an alle Rolleninstanzen bereit.

![](scaleout-with-windows-azure-service-bus/_static/image1.png)

Erforderliche Komponenten:

- Ein Windows Azure-Konto.
- Die [Windows Azure SDK](https://go.microsoft.com/fwlink/?linkid=254364&amp;clcid=0x409).
- Visual Studio 2012.

Die Service Bus-Rückwandplatine ist auch mit kompatibel [Service Bus für Windows Server](https://msdn.microsoft.com/library/windowsazure/dn282144.aspx), Version 1.1. Es ist jedoch nicht kompatibel mit Version 1.0 von Service Bus für Windows Server.

## <a name="pricing"></a>Preise

Die Service Bus-Rückwandplatine werden Themen zum Senden von Nachrichten verwendet. Aktuelle Preisinformationen, finden Sie unter [Service Bus](https://azure.microsoft.com/pricing/details/service-bus/). Zum Zeitpunkt der Erstellung dieses Dokuments können Sie für weniger als $1 1.000.000 Nachrichten pro Monat senden. Der Rückwand sendet eine Service Bus-Nachricht für jeden Aufruf einer SignalR-Hub-Methode. Es gibt auch einige Steuerungsnachrichten für Verbindungen, Trennvorgänge, verknüpfen oder eine verlassen Gruppen und So weiter. In den meisten Fällen werden die meisten dem Volumen des Nachrichtenverkehrs hubmethodenaufrufe.

## <a name="overview"></a>Übersicht

Bevor wir mit dem ausführlichen Tutorial eingehen, sieht ein schnellen Überblick darüber, welche Aktionen Sie ausgeführt werden.

1. Verwenden Sie Windows Azure-Portal, um einen neuen Service Bus-Namespace zu erstellen.
2. Fügen Sie diese NuGet-Pakete für Ihre Anwendung hinzu: 

    - [Microsoft.AspNet.SignalR](http://nuget.org/packages/Microsoft.AspNet.SignalR)
    - [Microsoft.AspNet.SignalR.ServiceBus](http://www.nuget.org/packages/SignalR.WindowsAzureServiceBus)
3. Erstellen einer SignalR-Anwendung.
4. Fügen Sie den folgenden Code, der "Global.asax" So konfigurieren Sie die Rückwandplatine: 

    [!code-csharp[Main](scaleout-with-windows-azure-service-bus/samples/sample1.cs)]

Wählen Sie für jede Anwendung einen anderen Wert für "Nameihrerapp" ein. Verwenden Sie nicht den gleichen Wert für mehrere Anwendungen.

## <a name="create-the-azure-services"></a>Erstellen Sie die Azure-Dienste

Erstellen Sie einen Cloud-Dienst aus, wie im [erstellen und Bereitstellen eines Clouddiensts](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-create-deploy). Führen Sie die Schritte im Abschnitt "Vorgehensweise: Erstellen Sie einen Cloud-Dienst mithilfe der Schnellerfassung". Für dieses Tutorial müssen Sie nicht, ein Zertifikat hochzuladen.

![](scaleout-with-windows-azure-service-bus/_static/image2.png)

Erstellen Sie einen neuen Service Bus-Namespace, wie im [wie, verwendet Service Bus Themen/-Abonnements](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions). Führen Sie die Schritte im Abschnitt "Erstellen einer Dienst-Namespace" aus.

![](scaleout-with-windows-azure-service-bus/_static/image3.png)

> [!NOTE]
> Stellen Sie sicher, dass die gleiche Region für den Clouddienst und Service Bus-Namespace.


## <a name="create-the-visual-studio-project"></a>Visual Studio-Projekt erstellen

Starten Sie Visual Studio. Von der **Datei** Menü klicken Sie auf **neues Projekt**.

In der **neues Projekt** Dialogfeld erweitern Sie **Visual C#-**. Klicken Sie unter **installierte Vorlagen**Option **Cloud** und wählen Sie dann **Windows Azure Cloud Service**. Behalten Sie die standardmäßigen .NET Framework 4.5. Nennen Sie die Anwendung ChatService, und klicken Sie auf **OK**.

![](scaleout-with-windows-azure-service-bus/_static/image4.png)

In der **neuen Windows Azure-Cloud-Dienst** im Dialogfeld auf ASP.NET MVC 4-Webrolle. Klicken Sie auf den Pfeil nach rechts-Schaltfläche (**&gt;**) um die Rolle der Projektmappe hinzuzufügen.

Zeigen Sie auf den die neue Rolle, also das Stiftsymbol angezeigt. Klicken Sie auf dieses Symbol, um die Rolle umbenennen. Benennen Sie die Rolle "SignalRChat", und klicken Sie auf **OK**.

![](scaleout-with-windows-azure-service-bus/_static/image5.png)

In der **neues ASP.NET MVC 4-Projekt** Assistenten **Internetanwendung**. Klicken Sie auf **OK**. Der Assistent erstellt zwei Projekte:

- ChatService: Dieses Projekt ist das Windows Azure-Anwendung. Es definiert die Azure-Rollen und andere Konfigurationsoptionen.
- SignalRChat: Dieses Projekt ist das ASP.NET MVC 4-Projekt.

## <a name="create-the-signalr-chat-application"></a>Erstellen Sie die SignalR Chat-Anwendung

Um die chatanwendung zu erstellen, führen Sie die Schritte in diesem Tutorial [erste Schritte mit SignalR und MVC 4](tutorial-getting-started-with-signalr-and-mvc-4.md).

Verwenden Sie NuGet, um die erforderlichen Bibliotheken zu installieren. Von der **Tools** , wählen Sie im Menü **NuGet Package Manager**, und wählen Sie dann **-Paket-Manager-Konsole**. In der **-Paket-Manager-Konsole** Fenster, geben Sie die folgenden Befehle aus:

[!code-powershell[Main](scaleout-with-windows-azure-service-bus/samples/sample2.ps1)]

Verwenden der `-ProjectName` Option aus, um die Pakete auf das ASP.NET MVC-Projekt, und nicht auf Windows Azure-Projekt zu installieren.

## <a name="configure-the-backplane"></a>Konfigurieren der Rückwandplatine

Fügen Sie in der Datei Global.asax der Anwendung den folgenden Code hinzu:

[!code-csharp[Main](scaleout-with-windows-azure-service-bus/samples/sample3.cs)]

Nun müssen Sie Ihre Service Bus-Verbindungszeichenfolge zu erhalten. Wählen Sie im Azure-Portal die Service Bus-Namespace, den Sie erstellt haben, und klicken Sie auf das Symbol "Zugriffsschlüssel".

![](scaleout-with-windows-azure-service-bus/_static/image6.png)

Kopieren Sie die Verbindungszeichenfolge in die Zwischenablage, und fügen Sie ihn in das *"ConnectionString"* Variable.

![](scaleout-with-windows-azure-service-bus/_static/image7.png)

[!code-csharp[Main](scaleout-with-windows-azure-service-bus/samples/sample4.cs)]

## <a name="deploy-to-azure"></a>Bereitstellung in Azure

Erweitern Sie im Projektmappen-Explorer die **Rollen** Ordner innerhalb des Projekts ChatService.

![](scaleout-with-windows-azure-service-bus/_static/image8.png)

Mit der rechten Maustaste in der Rolle "SignalRChat", und wählen Sie **Eigenschaften**. Wählen Sie die **Konfiguration** Registerkarte. Klicken Sie unter **Instanzen** 2 auswählen. Sie können auch die Größe des virtuellen Computers festlegen, um **sehr klein**.

![](scaleout-with-windows-azure-service-bus/_static/image9.png)

Speichern Sie die Änderungen.

Klicken Sie im Projektmappen-Explorer das Projekt ChatService. Wählen Sie **Veröffentlichen**.

![](scaleout-with-windows-azure-service-bus/_static/image10.png)

Wenn dies Ihre erste Zeit mit der Veröffentlichung in Windows Azure ist, müssen Sie Ihre Anmeldeinformationen herunterladen. In der **veröffentlichen** -Assistenten klicken Sie auf "Sign in to download Credentials". Dadurch werden Sie aufgefordert, sich bei Windows Azure-Portal anzumelden, und eine Datei mit veröffentlichungseinstellungen herunterladen.

![](scaleout-with-windows-azure-service-bus/_static/image11.png)

Klicken Sie auf **Import** , und wählen Sie die von Ihnen heruntergeladene Datei mit den veröffentlichungseinstellungen.

Klicken Sie auf **Weiter**. In der **Veröffentlichungseinstellungen** Dialogfeld unter **Clouddienst**, wählen Sie den Clouddienst, die Sie zuvor erstellt haben.

![](scaleout-with-windows-azure-service-bus/_static/image12.png)

Klicken Sie auf **Veröffentlichen**. Es dauert einige Minuten, bis die Anwendung bereitstellen und starten die virtuellen Computer.

Wenn Sie die chatanwendung ausführen, kommunizieren die Rolleninstanzen jetzt über Azure Service Bus, ein Service Bus-Thema mit. Ein Thema ist eine Warteschlange, die mehrere Abonnenten ermöglicht.

Der Rückwand wird automatisch erstellt, das Thema und die Abonnements. Um die Abonnements und die Nachrichtenaktivität anzuzeigen, öffnen Sie das Azure-Portal, wählen Sie den Service Bus-Namespace, und klicken Sie auf "Themen".

![](scaleout-with-windows-azure-service-bus/_static/image13.png)

Sie nehmen einige Minuten, bis die Aktivität auf dem Dashboard angezeigt werden.

![](scaleout-with-windows-azure-service-bus/_static/image14.png)

SignalR verwaltet die Lebensdauer des Themas. Solange Ihre Anwendung bereitgestellt wird, versuchen Sie nicht manuell löschen von Themen oder Ändern der Einstellungen für das Thema.

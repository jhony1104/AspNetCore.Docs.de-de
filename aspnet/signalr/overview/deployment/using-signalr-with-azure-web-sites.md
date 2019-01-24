---
uid: signalr/overview/deployment/using-signalr-with-azure-web-sites
title: Verwenden von SignalR mit Web-Apps in Azure App Service | Microsoft-Dokumentation
author: bradygaster
description: Dieses Dokument beschreibt, wie Sie eine SignalR-Anwendung zu konfigurieren, die in Microsoft Azure ausgeführt wird. Softwareversionen, die im Tutorial verwendet werden, Visual Studio 2013 oder VIS...
ms.author: bradyg
ms.date: 07/01/2015
ms.assetid: 2a7517a0-b88c-4162-ade3-9bf6ca7062fd
msc.legacyurl: /signalr/overview/deployment/using-signalr-with-azure-web-sites
msc.type: authoredcontent
ms.openlocfilehash: 13eb5d29a2c40f52aed4b569ec8695f014a05f03
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837701"
---
<a name="using-signalr-with-web-apps-in-azure-app-service"></a>Verwenden von SignalR mit Web-Apps in Azure App Service
====================
durch [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Dieses Dokument beschreibt, wie Sie eine SignalR-Anwendung zu konfigurieren, die in Microsoft Azure ausgeführt wird.
>
> ## <a name="software-versions-used-in-the-tutorial"></a>Softwareversionen, die in diesem Tutorial verwendet werden.
>
>
> - [Visual Studio 2013](https://my.visualstudio.com/Downloads?q=visual%20studio%202013) oder Visual Studio 2012
> - .NET 4.5
> - SignalR-Version 2
> - Azure SDK 2.3 für Visual Studio 2013 oder 2012
>
>
>
> ## <a name="questions-and-comments"></a>Fragen und Kommentare
>
> Lassen Sie Feedback, auf wie Ihnen in diesem Tutorial gefallen hat und was wir in den Kommentaren am unteren Rand der Seite verbessern können. Wenn Sie Fragen, die nicht direkt mit dem Tutorial verknüpft sind haben, können Sie sie veröffentlichen das [ASP.NET SignalR-Forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR), [StackOverflow.com](http://stackoverflow.com/), oder die [Microsoft Azure-Foren](https://social.msdn.microsoft.com/Forums/windowsazure/home?category=windowsazureplatform).


## <a name="table-of-contents"></a>Inhaltsverzeichnis

- [Introduction (Einführung)](#introduction)
- [Bereitstellen einer SignalR-Web-App in Azure App Service](#deploying)
- [Aktivieren WebSockets in Azure App Service](#websocket)
- [Mithilfe der Azure Redis Cache-Rückwandplatine](#backplane)
- [Nächste Schritte](#nextsteps)

<a id="introduction"></a>
## <a name="introduction"></a>Einführung

ASP.NET SignalR kann verwendet werden, um eine neue Ebene der Interaktivität zwischen Server und Web oder -Clients für .NET zu bringen. Wenn in Azure gehostet wird, SignalR-Anwendungen profitieren von der hoch verfügbare, skalierbare und leistungsstarke Umgebung, die in der Cloud bereitstellt.

<a id="deploying"></a>
## <a name="deploying-a-signalr-web-app-to-azure-app-service"></a>Bereitstellen einer SignalR-Web-App in Azure App Service

SignalR hinzufügen keine bestimmten schwierigkeiten für die Bereitstellung von einer Anwendung in Azure und der Bereitstellung mit einem lokalen Server. Eine Anwendung, die SignalR verwendet in Azure gehostet werden kann, ohne Änderungen an der Konfiguration oder sonstige Einstellungen (über WebSockets-Unterstützung finden Sie unter [Aktivieren von WebSockets in Azure App Service](#websocket) unten.) In diesem Tutorial stellen Sie die Anwendung erstellt, der [Getting Started Tutorial](../getting-started/tutorial-getting-started-with-signalr.md) in Azure.

**Erforderliche Komponenten**

- Visual Studio 2013. Wenn Sie Visual Studio nicht haben, ist Visual Studio-2013 Express für Web in der Installation des Azure SDK enthalten.
- [Azure SDK 2.3 für Visual Studio 2013](https://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409) oder [Azure SDK 2.3 für Visual Studio 2012](https://go.microsoft.com/fwlink/p/?linkid=323511).
- Um dieses Tutorial abzuschließen, benötigen Sie ein Azure-Abonnement. Sie können [Ihre MSDN-abonnentenvorteile aktivieren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), oder [registrieren Sie sich für ein Testabonnement](https://azure.microsoft.com/pricing/free-trial/).

### <a name="deploying-a-signalr-web-app-to-azure"></a>Eine SignalR-Web-app Bereitstellen in Azure

1. Abschließen der [Getting Started Tutorial](../getting-started/tutorial-getting-started-with-signalr.md), oder Laden Sie das fertige Projekt [Code Gallery](https://code.msdn.microsoft.com/SignalR-Getting-Started-b9d18aa9).
2. Wählen Sie in Visual Studio **erstellen**, **veröffentlichen SignalR Chat**.
3. Wählen Sie im Dialogfeld "Web veröffentlichen" "Windows Azure-Websites".

    ![Wählen Sie die Azure-Websites](using-signalr-with-azure-web-sites/_static/image1.png)
4. Wenn Sie sich mit Ihrem Microsoft-Konto angemeldet sind, klicken Sie auf **anmelden...**  in das Dialogfeld "vorhandene Website auswählen", und melden Sie sich.

    ![Vorhandene Website auswählen](using-signalr-with-azure-web-sites/_static/image2.png)    ![Melden Sie sich bei Azure an.](using-signalr-with-azure-web-sites/_static/image3.png)
5. Klicken Sie im Dialogfeld "vorhandene Website auswählen" auf **neu**.

    ![Neue Website](using-signalr-with-azure-web-sites/_static/image4.png)
6. Geben Sie im Dialogfeld "Website auf Windows Azure erstellen" einen eindeutigen Anwendungsnamen ein. Wählen Sie die Ihnen am nächsten gelegene Region, in der Region-Dropdownliste. Klicken Sie auf **Erstellen**.

    ![Erstellen Sie die Website in Azure](using-signalr-with-azure-web-sites/_static/image5.png)
7. Klicken Sie im Dialogfeld "Web veröffentlichen" auf **veröffentlichen**.

    ![Website veröffentlichen](using-signalr-with-azure-web-sites/_static/image6.png)
8. Wenn die app die Veröffentlichung abgeschlossen ist, wird die SignalR Chat-Anwendung in Azure App Service-Web-Apps gehosteten in einem Browser geöffnet.

    ![Website, die in einem Browser öffnen](using-signalr-with-azure-web-sites/_static/image7.png)

<a id="websocket"></a>
### <a name="enabling-websockets-on-azure-app-service-web-apps"></a>Aktivieren WebSockets in Azure App Service-Web-Apps

WebSockets muss explizit in Ihrer Web-app aktiviert werden, in einer SignalR-Anwendung verwendet werden; andernfalls, die andere Protokolle verwendet werden (finden Sie unter [Transporte und Fallbacks](../getting-started/introduction-to-signalr.md#transports) Einzelheiten).

Um WebSockets auf Azure App Service-Web-Apps zu verwenden, aktivieren sie im Abschnitt "Konfiguration" der Web-app. Zu diesem Zweck öffnen Sie Ihre Web-app die [Azure-Verwaltungsportal](https://manage.windowsazure.com/), und wählen Sie konfigurieren.

![Registerkarte „Konfigurieren“](using-signalr-with-azure-web-sites/_static/image8.png)

Am oberen Rand der Konfigurationsseite stellen Sie sicher, dass .NET 4.5 für Ihre Web-app verwendet wird.

![.NET Framework Version 4.5-Einstellung](using-signalr-with-azure-web-sites/_static/image9.png)

Auf der Seite-Konfiguration in der **WebSockets** wählen Sie die Einstellung **auf**.

![WebSockets-Einstellung: Ein](using-signalr-with-azure-web-sites/_static/image10.png)

Wählen Sie am unteren Rand der Konfigurationsseite, **speichern** zum Speichern der Änderungen.

![Speichern Sie die Einstellungen](using-signalr-with-azure-web-sites/_static/image11.png)

<a id="backplane"></a>
## <a name="using-the-azure-redis-cache-backplane"></a>Mithilfe der Azure Redis Cache-Rückwandplatine

Wenn Sie mehrere Instanzen für Ihre Web-app, und die Benutzer dieser Instanzen müssen miteinander interagieren (sodass z. B. Chat-Nachrichten erstellt, die in einer Instanz mit anderen Instanzen verbundenen Benutzer erreichen können), wird die [Azure Redis Cache Rückwand](../performance/scaleout-with-redis.md) in Ihrer Anwendung implementiert werden müssen.

<a id="nextsteps"></a>
## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Web-Apps in Azure App Service, finden Sie unter [-Web-Apps – Übersicht](https://azure.microsoft.com/documentation/articles/app-service-web-overview/).

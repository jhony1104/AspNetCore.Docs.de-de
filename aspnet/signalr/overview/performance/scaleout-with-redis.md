---
uid: signalr/overview/performance/scaleout-with-redis
title: Horizontale Skalierung in SignalR mit Redis | Microsoft-Dokumentation
author: bradygaster
description: Version der Software verwendete in diesem Thema Visual Studio 2013 .NET 4.5 SignalR Version 2-Vorgängerversionen des in diesem Thema Informationen zu früheren Versionen von...
ms.author: bradyg
ms.date: 06/10/2014
ms.assetid: 6ecd08c1-e364-4cd7-ad4c-806521911585
msc.legacyurl: /signalr/overview/performance/scaleout-with-redis
msc.type: authoredcontent
ms.openlocfilehash: 22b8379fd2d97aeb85137e1cc128fe5d053f44ed
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837025"
---
<a name="signalr-scaleout-with-redis"></a>Horizontale Skalierung in SignalR mit Redis
====================
durch [Mike Wasson](https://github.com/MikeWasson)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> ## <a name="software-versions-used-in-this-topic"></a>In diesem Thema verwendeten Softwareversionen
>
>
> - [Visual Studio 2013](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - .NET 4.5
> - SignalR-Version 2.4
>
>
>
> ## <a name="previous-versions-of-this-topic"></a>Vorherige Versionen dieses Themas
>
> Weitere Informationen zu früheren Versionen von SignalR, finden Sie unter [ältere Versionen von SignalR](../older-versions/index.md).
>
> ## <a name="questions-and-comments"></a>Fragen und Kommentare
>
> Lassen Sie Feedback, auf wie Ihnen in diesem Tutorial gefallen hat und was wir in den Kommentaren am unteren Rand der Seite verbessern können. Wenn Sie Fragen, die nicht direkt mit dem Tutorial verknüpft sind haben, können Sie sie veröffentlichen das [ASP.NET SignalR-Forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) oder [StackOverflow.com](http://stackoverflow.com/).


In diesem Tutorial verwenden Sie [Redis](http://redis.io/) zur Verteilung von Nachrichten in einer SignalR-Anwendung, die auf zwei separaten IIS-Instanzen bereitgestellt wird.

Redis ist ein Schlüssel-Wert-Speicher im Arbeitsspeicher. Darüber hinaus unterstützt er ein messaging-System mit einem Veröffentlichen/Abonnieren-Modell. Die Redis-SignalR-Backplane verwendet das Pub/Sub-Feature, um Nachrichten an andere Server weiterzuleiten.

![](scaleout-with-redis/_static/image1.png)

In diesem Tutorial verwenden Sie drei Server:

- Zwei Server unter Windows, das Sie zum Bereitstellen einer SignalR-Anwendung verwenden werden.
- Ein Server unter Linux, das Sie zum Ausführen von Redis verwenden. Die Screenshots in diesem Tutorial verwendet Ubuntu 12.04 TLS.

Wenn Sie drei physische Servern für die Verwendung nicht haben, können Sie virtuelle Computer auf Hyper-V erstellen. Eine weitere Option ist zum Erstellen von VMs in Azure.

Obwohl in diesem Tutorial die offizielle Redis-Implementierung verwendet wird, es gibt auch eine [Windows Port von Redis](https://github.com/MSOpenTech/redis) aus MSOpenTech. Setup und Konfiguration sind unterschiedlich, aber die Schritte sind identisch.

> [!NOTE]
>
> Horizontale Skalierung in SignalR mit Redis unterstützt Redis-Cluster nicht.


## <a name="overview"></a>Übersicht

Bevor wir mit dem ausführlichen Tutorial eingehen, sieht ein schnellen Überblick darüber, welche Aktionen Sie ausgeführt werden.

1. Installieren Sie Redis, und starten Sie den Redis-Server.
2. Fügen Sie diese NuGet-Pakete für Ihre Anwendung hinzu:

    - [Microsoft.AspNet.SignalR](http://nuget.org/packages/Microsoft.AspNet.SignalR)
    - [Microsoft.AspNet.SignalR.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.StackExchangeRedis)
    
3. Erstellen einer SignalR-Anwendung.
4. Fügen Sie den folgenden Code, "Startup.cs" So konfigurieren Sie die Rückwandplatine:

    [!code-csharp[Main](scaleout-with-redis/samples/sample1.cs)]

## <a name="ubuntu-on-hyper-v"></a>Ubuntu in Hyper-V

Verwenden Windows Hyper-V, können Sie problemlos eine Ubuntu-VM unter Windows Server erstellen.

Laden Sie die Ubuntu-ISO-Datei von [ http://www.ubuntu.com ](http://www.ubuntu.com/).

Fügen Sie einen neuen virtuellen Computer in Hyper-V hinzu. In der **virtuelle Festplatte verbinden** Schritt select **erstellen Sie eine virtuelle Festplatte**.

![](scaleout-with-redis/_static/image2.png)

In der **Installationsoptionen** Schritt select **Imagedatei (.iso)**, klicken Sie auf **Durchsuchen**, und navigieren Sie zu die ISO für Ubuntu-Installation.

![](scaleout-with-redis/_static/image3.png)

## <a name="install-redis"></a>Installieren Sie Redis

Führen Sie die Schritte unter [ http://redis.io/download ](http://redis.io/download) zum Herunterladen und Erstellen von Redis.

[!code-console[Main](scaleout-with-redis/samples/sample2.cmd)]

Dies erstellt die Redis-Binärdateien die `src` Verzeichnis.

Standardmäßig wird Redis kein Kennwort erforderlich. Bearbeiten Sie zum Festlegen eines Kennworts die `redis.conf` -Datei, die im Stammverzeichnis des Quellcodes befindet. (Stellen Sie eine Sicherungskopie der Datei aus, bevor Sie sie bearbeiten.) Fügen Sie die folgende Anweisung zum `redis.conf`:

[!code-powershell[Main](scaleout-with-redis/samples/sample3.ps1)]

Starten Sie jetzt die Redis-Server:

[!code-css[Main](scaleout-with-redis/samples/sample4.css)]

![](scaleout-with-redis/_static/image4.png)

Geöffneten Port 6379 aufheben, wird der Standardport, der Redis wird lauscht. (Sie können die Portnummer in der Konfigurationsdatei ändern.)

## <a name="create-the-signalr-application"></a>Erstellen Sie die SignalR-Anwendung

Erstellen einer SignalR-Anwendung entweder mit diesen Lernprogrammen:

- [Erste Schritte mit SignalR 2.0](../getting-started/tutorial-getting-started-with-signalr.md)
- [Erste Schritte mit SignalR 2.0 und MVC 5](../getting-started/tutorial-getting-started-with-signalr-and-mvc.md)

Als Nächstes ändern wir die Chat-Anwendung zur Unterstützung von horizontale Skalierung mit Redis. Fügen Sie zunächst die `Microsoft.AspNet.SignalR.StackExchangeRedis` NuGet-Paket Ihrem Projekt. In Visual Studio aus der **Tools** die Option **NuGet Paket-Manager**, wählen Sie **Paket-Manager Konsole**. Geben Sie im Fenster Paket-Manager-Konsole den folgenden Befehl aus:

[!code-powershell[Main](scaleout-with-redis/samples/sample5.ps1)]

Öffnen Sie als Nächstes die Datei "Startup.cs". Fügen Sie den folgenden Code der **Konfiguration** Methode:

[!code-csharp[Main](scaleout-with-redis/samples/sample6.cs)]

- "Server" ist der Name des Servers, der Redis ausgeführt wird.
- *Port* ist die Nummer des Ports
- "Kennwort" ist das Kennwort, das Sie in der Datei redis.conf definiert.
- "AppName" ist eine beliebige Zeichenfolge. SignalR erstellt einen Redis-Pub/Sub-Kanal mit diesem Namen.

Zum Beispiel:

[!code-csharp[Main](scaleout-with-redis/samples/sample7.cs)]

## <a name="deploy-and-run-the-application"></a>Bereitstellen und Ausführen der Anwendung

Bereiten Sie Ihre Windows Server-Instanzen, um die SignalR-Anwendung bereitzustellen.

Fügen Sie die IIS-Rolle hinzu. Umfassen Sie "Anwendungsentwicklung"-Features, einschließlich der WebSocket-Protokoll.

![](scaleout-with-redis/_static/image5.png)

Außerdem enthalten Sie den Management-Dienst (unter "Management Tools" aufgeführt).

![](scaleout-with-redis/_static/image6.png)

**Installieren Sie Web Deploy 3.0.** Wenn Sie die IIS-Manager ausführen, werden Sie zum Installieren von Microsoft-Webplattform aufgefordert, oder Sie können [Herunterladen der Intstaller](https://go.microsoft.com/fwlink/?LinkId=255386). Klicken Sie in den Webplattform-Installer Web Deploy suchen Sie und installieren Sie Web Deploy 3.0

![](scaleout-with-redis/_static/image7.png)

Überprüfen Sie, dass die Web-Management-Dienst ausgeführt wird. Wenn dies nicht der Fall ist, starten Sie den Dienst. (Wenn Sie Web-Management-Dienst in der Liste der Windows-Dienste nicht angezeigt wird, stellen Sie sicher, dass Sie den Management-Dienst installiert, wenn Sie die IIS-Rolle hinzugefügt haben.)

Standardmäßig wird TCP-Port 8172 den Web-Management-Dienst lauscht. Erstellen Sie eine neue eingehende Regel zum Zulassen von TCP-Datenverkehr an Port 8172, in der Windows-Firewall. Weitere Informationen finden Sie unter [Firewallregeln konfigurieren](https://technet.microsoft.com/library/dd448559(WS.10).aspx). (Wenn Sie die VMs in Azure hosten, dies direkt im Azure-Portal möglich. Finden Sie unter [Einrichten von Endpunkten für einen virtuellen Computer](https://azure.microsoft.com/documentation/articles/virtual-machines-set-up-endpoints/).)

Jetzt sind Sie bereit für die Bereitstellung von Visual Studio-Projekt vom Entwicklungscomputer auf dem Server. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste in der Projektmappe, und klicken Sie auf **veröffentlichen**.

Weitere Dokumentation zur Bereitstellung finden Sie unter [Einstieg in die Webbereitstellung für Visual Studio und ASP.NET](../../../whitepapers/aspnet-web-deployment-content-map.md).

Wenn Sie die Anwendung auf zwei Servern bereitstellen, können Sie jede Instanz in einem separaten Browserfenster zu öffnen und sehen, dass jeder SignalR-Nachrichten von einem anderen erhält. (Natürlich in einer produktionsumgebung, die beiden Server hinter einem Load Balancer saß.)

![](scaleout-with-redis/_static/image8.png)

Wenn Sie wissen möchten, finden Sie unter der Nachrichten, die mit Redis, gesendet werden, können Sie die **Redis-Cli** Client, der mit Redis installiert wird.

[!code-xml[Main](scaleout-with-redis/samples/sample8.xml)]

![](scaleout-with-redis/_static/image9.png)

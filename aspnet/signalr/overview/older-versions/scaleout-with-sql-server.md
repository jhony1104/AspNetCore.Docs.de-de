---
uid: signalr/overview/older-versions/scaleout-with-sql-server
title: Horizontale Skalierung in SignalR mit SQLServer (SignalR 1.x) | Microsoft-Dokumentation
author: bradygaster
description: ''
ms.author: bradyg
ms.date: 05/01/2013
ms.assetid: 1dca7967-8296-444a-9533-837eb284e78c
msc.legacyurl: /signalr/overview/older-versions/scaleout-with-sql-server
msc.type: authoredcontent
ms.openlocfilehash: 7fd05a4487be4cc96fa945cf08226841e3f01806
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54835934"
---
<a name="signalr-scaleout-with-sql-server-signalr-1x"></a>Horizontale Skalierung in SignalR mit SQLServer (SignalR 1.x)
====================
durch [Mike Wasson](https://github.com/MikeWasson), [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

In diesem Tutorial verwenden Sie SQL Server zur Verteilung von Nachrichten in einer SignalR-Anwendung, die in zwei verschiedenen IIS-Instanzen bereitgestellt wird. Sie können auch in diesem Tutorial ausführen, auf einem einzelnen Testcomputer, aber um den vollen Effekt zu erhalten, müssen Sie die SignalR-Anwendung in zwei oder mehr Servern bereitzustellen. Sie müssen auch SQL Server auf einem der Server oder auf einem separaten dedizierten Server installieren. Eine weitere Möglichkeit ist das Tutorial mit virtuellen Computern in Azure ausführen.

![](scaleout-with-sql-server/_static/image1.png)

## <a name="prerequisites"></a>Vorraussetzungen

Microsoft SQLServer 2005 oder höher. Der Rückwand unterstützt Desktop- und Server-Editionen von SQL Server. Es unterstützt SQL Server Compact Edition oder Azure SQL-Datenbank nicht. (Wenn Ihre Anwendung in Azure gehostet wird, erwägen Sie die Service Bus-Rückwandplatine stattdessen.)

## <a name="overview"></a>Übersicht

Bevor wir mit dem ausführlichen Tutorial eingehen, sieht ein schnellen Überblick darüber, welche Aktionen Sie ausgeführt werden.

1. Erstellen Sie eine neue leere Datenbank ein. Der Rückwand erstellt die erforderlichen Tabellen in dieser Datenbank.
2. Fügen Sie diese NuGet-Pakete für Ihre Anwendung hinzu: 

    - [Microsoft.AspNet.SignalR](http://nuget.org/packages/Microsoft.AspNet.SignalR)
    - [Microsoft.AspNet.SignalR.SqlServer](http://nuget.org/packages/Microsoft.AspNet.SignalR.SqlServer)
3. Erstellen einer SignalR-Anwendung.
4. Fügen Sie den folgenden Code, der "Global.asax" So konfigurieren Sie die Rückwandplatine: 

    [!code-csharp[Main](scaleout-with-sql-server/samples/sample1.cs)]

## <a name="configure-the-database"></a>Konfigurieren Sie die Datenbank

Entscheiden Sie, ob es sich bei der Anwendung Windows-Authentifizierung oder SQL Server-Authentifizierung verwendet werden, auf die Datenbank zugreifen. Unabhängig davon, stellen Sie sicher, dass der Datenbankbenutzer berechtigt ist, melden Sie sich, Erstellen von Schemas und Tabellen erstellen.

Erstellen Sie eine neue Datenbank für die Rückwandplatine verwenden. Sie können die Datenbank einen beliebigen Namen geben. Sie müssen keine Tabellen in der Datenbank zu erstellen. der Rückwand erstellt die erforderlichen Tabellen.

![](scaleout-with-sql-server/_static/image2.png)

## <a name="enable-service-broker"></a>Aktivieren Sie Service Broker

Es wird empfohlen, um Service Broker für die Rückwandplatine-Datenbank zu aktivieren. Service Broker bietet systemeigene Unterstützung für Messaging- und Warteschlangen in SQL Server, der in der Rückwand Updates effizienter erhalten können. (Allerdings funktioniert der Rückwand auch ohne Service Broker.)

Um zu überprüfen, ob Service Broker aktiviert ist, Abfragen der **ist\_Broker\_aktiviert** -Spalte in der **sys.databases** -Katalogsicht angezeigt.

[!code-sql[Main](scaleout-with-sql-server/samples/sample2.sql)]

![](scaleout-with-sql-server/_static/image3.png)

Verwenden Sie die folgende SQL-Abfrage, um Service Broker zu aktivieren:

[!code-sql[Main](scaleout-with-sql-server/samples/sample3.sql)]

> [!NOTE]
> Wenn diese Abfrage angezeigt wird, ein Deadlock auftreten, stellen Sie sicher, dass keine Programme, die mit der Datenbank verbunden sind.

Wenn Sie die Ablaufverfolgung aktiviert haben, zeigt die ablaufverfolgungen auch, ob Service Broker aktiviert ist.

## <a name="create-a-signalr-application"></a>Erstellen einer SignalR-Anwendung

Erstellen einer SignalR-Anwendung entweder mit diesen Lernprogrammen:

- [Erste Schritte mit SignalR](../getting-started/tutorial-getting-started-with-signalr.md)
- [Erste Schritte mit SignalR und MVC 4](tutorial-getting-started-with-signalr-and-mvc-4.md)

Als Nächstes ändern wir die Chat-Anwendung mit horizontaler Skalierung mit SQL Server zu unterstützen. Fügen Sie zunächst das SignalR.SqlServer NuGet-Paket Ihrem Projekt ein. In Visual Studio aus der **Tools** die Option **NuGet Paket-Manager**, wählen Sie **Paket-Manager Konsole**. Geben Sie im Fenster Paket-Manager-Konsole den folgenden Befehl aus:

[!code-powershell[Main](scaleout-with-sql-server/samples/sample4.ps1)]

Öffnen Sie als Nächstes die Datei "Global.asax". Fügen Sie den folgenden Code der **Anwendung\_starten** Methode:

[!code-csharp[Main](scaleout-with-sql-server/samples/sample5.cs)]

## <a name="deploy-and-run-the-application"></a>Bereitstellen und Ausführen der Anwendung

Bereiten Sie Ihre Windows Server-Instanzen, um die SignalR-Anwendung bereitzustellen.

Fügen Sie die IIS-Rolle hinzu. Umfassen Sie "Anwendungsentwicklung"-Features, einschließlich der WebSocket-Protokoll.

![](scaleout-with-sql-server/_static/image4.png)

Außerdem enthalten Sie den Management-Dienst (unter "Management Tools" aufgeführt).

![](scaleout-with-sql-server/_static/image5.png)

**Installieren Sie Web Deploy 3.0.** Wenn Sie die IIS-Manager ausführen, werden Sie zum Installieren von Microsoft-Webplattform aufgefordert, oder Sie können [Herunterladen der Intstaller](https://go.microsoft.com/fwlink/?LinkId=255386). Klicken Sie in den Webplattform-Installer Web Deploy suchen Sie und installieren Sie Web Deploy 3.0

![](scaleout-with-sql-server/_static/image6.png)

Überprüfen Sie, dass die Web-Management-Dienst ausgeführt wird. Wenn dies nicht der Fall ist, starten Sie den Dienst. (Wenn Sie Web-Management-Dienst in der Liste der Windows-Dienste nicht angezeigt wird, stellen Sie sicher, dass Sie den Management-Dienst installiert, wenn Sie die IIS-Rolle hinzugefügt haben.)

Öffnen Sie abschließend Port 8172 für TCP. Dies ist der Port, den das Web Deploy-Tool verwendet.

Jetzt sind Sie bereit für die Bereitstellung von Visual Studio-Projekt vom Entwicklungscomputer auf dem Server. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste in der Projektmappe, und klicken Sie auf **veröffentlichen**.

Weitere Dokumentation zur Bereitstellung finden Sie unter [Einstieg in die Webbereitstellung für Visual Studio und ASP.NET](../../../whitepapers/aspnet-web-deployment-content-map.md).

Wenn Sie die Anwendung auf zwei Servern bereitstellen, können Sie jede Instanz in einem separaten Browserfenster zu öffnen und sehen, dass jeder SignalR-Nachrichten von einem anderen erhält. (Natürlich in einer produktionsumgebung, die beiden Server hinter einem Load Balancer saß.)

![](scaleout-with-sql-server/_static/image7.png)

Nachdem Sie die Anwendung ausführen, sehen Sie sich, dass SignalR automatisch Tabellen in der Datenbank erstellt wurde:

![](scaleout-with-sql-server/_static/image8.png)

SignalR verwaltet die Tabellen. Solange Ihre Anwendung bereitgestellt wird, keine Zeilen löschen, ändern Sie die Tabelle und so weiter.

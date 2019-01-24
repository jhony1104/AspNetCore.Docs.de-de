---
uid: signalr/overview/guide-to-the-api/mapping-users-to-connections
title: Zuordnen von SignalR-Benutzern zu Verbindungen | Microsoft-Dokumentation
author: bradygaster
description: In diesem Thema zeigt, wie Informationen über Benutzer und ihre Verbindungen beibehalten wird. Patrick Fletcher dabei geholfen, dieses Thema zu schreiben. Softwareversionen, die in diesem Thema verwendeten...
ms.author: bradyg
ms.date: 12/30/2014
ms.assetid: f80c08b1-3f1f-432c-980c-c7b6edeb31b1
msc.legacyurl: /signalr/overview/guide-to-the-api/mapping-users-to-connections
msc.type: authoredcontent
ms.openlocfilehash: 786fc6bbc0b8d430770cf19d1647dbdba26347aa
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837194"
---
<a name="mapping-signalr-users-to-connections"></a>Zuordnen von SignalR-Benutzern zu Verbindungen
====================
durch [Tom FitzMacken](https://github.com/tfitzmac)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> In diesem Thema zeigt, wie Informationen über Benutzer und ihre Verbindungen beibehalten wird.
>
> Patrick Fletcher dabei geholfen, dieses Thema zu schreiben.
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
> ## <a name="previous-versions-of-this-topic"></a>Vorherige Versionen dieses Themas
>
> Weitere Informationen zu früheren Versionen von SignalR, finden Sie unter [ältere Versionen von SignalR](../older-versions/index.md).
>
> ## <a name="questions-and-comments"></a>Fragen und Kommentare
>
> Lassen Sie Feedback, auf wie Ihnen in diesem Tutorial gefallen hat und was wir in den Kommentaren am unteren Rand der Seite verbessern können. Wenn Sie Fragen, die nicht direkt mit dem Tutorial verknüpft sind haben, können Sie sie veröffentlichen das [ASP.NET SignalR-Forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) oder [StackOverflow.com](http://stackoverflow.com/).

## <a name="introduction"></a>Einführung

Jeder Client, Herstellen einer Verbindung mit einem Hub übergibt eine eindeutige Verbindungs-Id an. Sie können diesen Wert im Abrufen der `Context.ConnectionId` Eigenschaft der Hub-Kontexts. Wenn Ihre Anwendung muss einen Benutzer die Verbindungs-Id zuordnen, und die Zuordnung beizubehalten, können Sie eine der folgenden:

- [Die Benutzer-ID-Anbieter (SignalR 2)](#IUserIdProvider)
- [In-Memory-Speicher](#inmemory), z. B. ein Wörterbuch
- [SignalR-Gruppe für jeden Benutzer](#groups)
- [Dauerhafte, externe Speicher](#database), z. B. eine Datenbanktabelle oder eine Azure-Tabellenspeicher

Jede dieser Implementierungen ist in diesem Thema dargestellt. Sie verwenden die `OnConnected`, `OnDisconnected`, und `OnReconnected` Methoden der `Hub` Klasse, um den Verbindungsstatus für den Benutzer zu verfolgen.

Der beste Ansatz für Ihre Anwendung hängt von:

- Die Anzahl von Webservern, die Ihre Anwendung hosten.
- Gibt an, ob müssen Sie eine Liste der aktuell verbundenen Benutzer zu erhalten.
- Gibt an, ob müssen Sie die Gruppe und die Benutzerinformationen dauerhaft zu speichern, wenn die Anwendung oder den Server neu gestartet wird.
- Gibt an, ob die Latenz des Aufrufs von eines externen Servers ein Problem besteht.

Die folgende Tabelle zeigt, welcher Ansatz für diese Überlegungen funktioniert.

|  | Mehr als einem server | Rufen Sie die Liste der derzeit verbundenen Benutzer | Speichern Sie die Informationen nach dem Neustart | Eine optimale Leistung |
| --- | --- | --- | --- | --- |
| Benutzer-ID-Anbieter | ![](mapping-users-to-connections/_static/image1.png) |  |  | ![](mapping-users-to-connections/_static/image2.png) |
| Im Arbeitsspeicher |  | ![](mapping-users-to-connections/_static/image3.png) |  | ![](mapping-users-to-connections/_static/image4.png) |
| Einzelbenutzer-Gruppen | ![](mapping-users-to-connections/_static/image5.png) |  |  | ![](mapping-users-to-connections/_static/image6.png) |
| Externe permanente | ![](mapping-users-to-connections/_static/image7.png) | ![](mapping-users-to-connections/_static/image8.png) | ![](mapping-users-to-connections/_static/image9.png) |  |

<a id="IUserIdProvider"></a>

## <a name="iuserid-provider"></a>IUserID-Anbieter

Dieses Feature ermöglicht Benutzern, um anzugeben, was die Benutzer-ID basierend auf einer IRequest über eine neue Schnittstelle IUserIdProvider.

**Die IUserIdProvider**

[!code-csharp[Main](mapping-users-to-connections/samples/sample1.cs)]

Standardmäßig wird eine Implementierung, des Benutzers verwendet `IPrincipal.Identity.Name` als Benutzernamen ein. Um dies zu ändern, registrieren Sie Ihre Implementierung von `IUserIdProvider` mit dem globalen Host beim Start der Anwendung:

[!code-csharp[Main](mapping-users-to-connections/samples/sample2.cs)]

Von werden innerhalb eines Hubs, Senden von Nachrichten an diese Benutzer über die folgende API werden:

**Senden einer Nachricht an einen bestimmten Benutzer**

[!code-csharp[Main](mapping-users-to-connections/samples/sample3.cs?highlight=5)]

<a id="inmemory"></a>

## <a name="in-memory-storage"></a>In-Memory-Speicher

Die folgenden Beispiele zeigen, wie Sie die Verbindung und des Benutzers Informationen in einem Wörterbuch beizubehalten, die im Arbeitsspeicher gespeichert wird. Das Wörterbuch verwendet eine `HashSet` die Verbindungs-Id zu speichern. Zu einem beliebigen Zeitpunkt kann ein Benutzer mehr als eine Verbindung mit dem SignalR-Anwendung verfügen. Beispielsweise würde ein Benutzer, die über mehrere Geräte oder mehr als eine Registerkarte "Browser" verbunden ist mehr als ein Verbindungs-Id haben.

Wenn die Anwendung heruntergefahren wird, alle Informationen geht verloren, aber erneut aufgefüllt wird, wie die Benutzer die Verbindung erneut herzustellen. In-Memory-Speicher funktionieren nicht, wenn die Umgebung mehrere Webserver umfasst, da jeder Server eine separate Auflistung von Verbindungen verfügen würde.

Das erste Beispiel zeigt eine Klasse, die Zuordnung von Benutzern zu Verbindungen verwaltet, werden. Der Schlüssel für das HashSet werden der Name des Benutzers.

[!code-csharp[Main](mapping-users-to-connections/samples/sample4.cs)]

Das nächste Beispiel zeigt, wie Sie mit der Zuordnung Verbindungsklasse über einen Hub. Die Instanz der Klasse befindet sich in einen Variablennamen `_connections`.

[!code-csharp[Main](mapping-users-to-connections/samples/sample5.cs)]

<a id="groups"></a>

## <a name="single-user-groups"></a>Einzelbenutzer-Gruppen

Sie können eine Gruppe für jeden Benutzer erstellen und zu dieser Gruppe klicken Sie dann eine Nachricht senden, wenn Sie nur dieser Benutzer erreichen möchten. Der Name jeder Gruppe ist der Name des Benutzers. Wenn ein Benutzer mehrere Verbindungen verfügt, wird die Gruppe des Benutzers jeder Verbindungs-Id hinzugefügt.

Sie sollten nicht manuell entfernen des Benutzers aus der Gruppe, wenn der Benutzer die Verbindung trennt. Dadurch wird automatisch vom SignalR-Framework ausgeführt werden.

Das folgende Beispiel zeigt, wie Sie Gruppen für einzelne Benutzer zu implementieren.

[!code-csharp[Main](mapping-users-to-connections/samples/sample6.cs)]

<a id="database"></a>

## <a name="permanent-external-storage"></a>Dauerhafte, externe Speicher

In diesem Thema wird gezeigt, wie eine Datenbank oder Azure-Tabellenspeicher verwenden Sie zum Speichern von Verbindungsinformationen. Dieser Ansatz funktioniert, wenn Sie mehrere Webserver haben, da jedem Webserver mit der gleichen Data-Repository interagieren kann. Wenn Ihre Webserver arbeiten oder die Anwendung neu gestartet wird, beenden die `OnDisconnected` Methode wird nicht aufgerufen. Aus diesem Grund ist es möglich, dass Ihr Datenrepository Datensätze für Verbindungs-Ids gibt, die nicht mehr gültig sind. Um diese verwaiste Einträge zu bereinigen, möchten Sie möglicherweise eine Verbindung für ungültig zu erklären, die außerhalb eines Zeitrahmens erstellt wurde, die für Ihre Anwendung relevant ist. In die Beispielen in diesem Abschnitt enthalten einen Wert zum Nachverfolgen, wann die Verbindung erstellt wurde, aber nicht um alte Datensätze zu bereinigen, da möglicherweise Sie dies als Hintergrundprozess möchten anzeigen.

### <a name="database"></a>Datenbank

Die folgenden Beispiele zeigen, wie Sie die Verbindung und des Benutzers Informationen in einer Datenbank beibehalten wird. Sie können neue datenzugriffstechnologie verwenden. Im folgenden Beispiel zeigt jedoch wie Sie Modelle, die mithilfe von Entity Framework zu definieren. Diese Entitätsmodelle, Tabellen und Felder entsprechen. Die Datenstruktur kann je nach den Anforderungen Ihrer Anwendung erheblich variieren.

Das erste Beispiel zeigt, wie Sie eine Benutzerentität definieren, die viele Entitäten der Verbindung zugeordnet werden können.

[!code-csharp[Main](mapping-users-to-connections/samples/sample7.cs)]

Anschließend können Sie im Hub den Status der einzelnen Verbindung mit den folgenden Code verfolgen.

[!code-csharp[Main](mapping-users-to-connections/samples/sample8.cs)]

<a id="azure"></a>
### <a name="azure-table-storage"></a>Azure-Tabellenspeicher

Im folgenden Beispiel von Azure Table Storage ist ähnlich wie im Beispiel für die Datenbank. Es enthält alle Informationen, die keine, die Sie benötigen würden, erste Schritte mit Azure Table Storage-Dienst. Weitere Informationen finden Sie unter [Gewusst wie: Verwenden von Tabellenspeicher aus .NET](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-tables/).

Das folgende Beispiel zeigt eine Tabellenentität zum Speichern von Verbindungsinformationen. Sie Partitionen, die Daten über den Benutzernamen, und jede Entität von der Verbindungs­ID identifiziert werden, damit ein Benutzer mehrere Verbindungen zu einem beliebigen Zeitpunkt verfügen kann.

[!code-csharp[Main](mapping-users-to-connections/samples/sample9.cs)]

In den Hub verfolgen Sie den Status der Verbindung des Benutzers.

[!code-csharp[Main](mapping-users-to-connections/samples/sample10.cs)]

---
uid: signalr/overview/older-versions/mapping-users-to-connections
title: Zuordnen von SignalR-Benutzern zu Verbindungen in SignalR 1.x | Microsoft-Dokumentation
author: bradygaster
description: In diesem Thema zeigt, wie Informationen über Benutzer und ihre Verbindungen beibehalten wird.
ms.author: bradyg
ms.date: 10/17/2013
ms.assetid: ebbc93a8-e6c4-4122-8e0d-3aa42293c747
msc.legacyurl: /signalr/overview/older-versions/mapping-users-to-connections
msc.type: authoredcontent
ms.openlocfilehash: 1e65a11e08a5b060cf8b096b5fe5b90eb8dc5b51
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836713"
---
<a name="mapping-signalr-users-to-connections-in-signalr-1x"></a>Zuordnen von SignalR-Benutzern zu Verbindungen in SignalR 1.x
====================
durch [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> In diesem Thema zeigt, wie Informationen über Benutzer und ihre Verbindungen beibehalten wird.


## <a name="introduction"></a>Einführung

Jeder Client, Herstellen einer Verbindung mit einem Hub übergibt eine eindeutige Verbindungs-Id an. Sie können diesen Wert im Abrufen der `Context.ConnectionId` Eigenschaft der Hub-Kontexts. Wenn Ihre Anwendung muss einen Benutzer die Verbindungs-Id zuordnen, und die Zuordnung beizubehalten, können Sie eine der folgenden:

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
| Im Arbeitsspeicher |  | ![](mapping-users-to-connections/_static/image1.png) |  | ![](mapping-users-to-connections/_static/image2.png) |
| Einzelbenutzer-Gruppen | ![](mapping-users-to-connections/_static/image3.png) |  |  | ![](mapping-users-to-connections/_static/image4.png) |
| Externe permanente | ![](mapping-users-to-connections/_static/image5.png) | ![](mapping-users-to-connections/_static/image6.png) | ![](mapping-users-to-connections/_static/image7.png) |  |

<a id="inmemory"></a>

## <a name="in-memory-storage"></a>In-Memory-Speicher

Die folgenden Beispiele zeigen, wie Sie die Verbindung und des Benutzers Informationen in einem Wörterbuch beizubehalten, die im Arbeitsspeicher gespeichert wird. Das Wörterbuch verwendet eine `HashSet` die Verbindungs-Id zu speichern. Zu einem beliebigen Zeitpunkt kann ein Benutzer mehr als eine Verbindung mit dem SignalR-Anwendung verfügen. Beispielsweise würde ein Benutzer, die über mehrere Geräte oder mehr als eine Registerkarte "Browser" verbunden ist mehr als ein Verbindungs-Id haben.

Wenn die Anwendung heruntergefahren wird, alle Informationen geht verloren, aber erneut aufgefüllt wird, wie die Benutzer die Verbindung erneut herzustellen. In-Memory-Speicher funktionieren nicht, wenn die Umgebung mehrere Webserver umfasst, da jeder Server eine separate Auflistung von Verbindungen verfügen würde.

Das erste Beispiel zeigt eine Klasse, die Zuordnung von Benutzern zu Verbindungen verwaltet, werden. Der Schlüssel für das HashSet werden der Name des Benutzers.

[!code-csharp[Main](mapping-users-to-connections/samples/sample1.cs)]

Das nächste Beispiel zeigt, wie Sie mit der Zuordnung Verbindungsklasse über einen Hub. Die Instanz der Klasse befindet sich in einen Variablennamen `_connections`.

[!code-csharp[Main](mapping-users-to-connections/samples/sample2.cs)]

<a id="groups"></a>

## <a name="single-user-groups"></a>Einzelbenutzer-Gruppen

Sie können eine Gruppe für jeden Benutzer erstellen und zu dieser Gruppe klicken Sie dann eine Nachricht senden, wenn Sie nur dieser Benutzer erreichen möchten. Der Name jeder Gruppe ist der Name des Benutzers. Wenn ein Benutzer mehrere Verbindungen verfügt, wird die Gruppe des Benutzers jeder Verbindungs-Id hinzugefügt.

Sie sollten nicht manuell entfernen des Benutzers aus der Gruppe, wenn der Benutzer die Verbindung trennt. Dadurch wird automatisch vom SignalR-Framework ausgeführt werden.

Das folgende Beispiel zeigt, wie Sie Gruppen für einzelne Benutzer zu implementieren.

[!code-csharp[Main](mapping-users-to-connections/samples/sample3.cs)]

<a id="database"></a>

## <a name="permanent-external-storage"></a>Dauerhafte, externe Speicher

In diesem Thema wird gezeigt, wie eine Datenbank oder Azure-Tabellenspeicher verwenden Sie zum Speichern von Verbindungsinformationen. Dieser Ansatz funktioniert, wenn Sie mehrere Webserver haben, da jedem Webserver mit der gleichen Data-Repository interagieren kann. Wenn Ihre Webserver arbeiten oder die Anwendung neu gestartet wird, beenden die `OnDisconnected` Methode wird nicht aufgerufen. Aus diesem Grund ist es möglich, dass Ihr Datenrepository Datensätze für Verbindungs-Ids gibt, die nicht mehr gültig sind. Um diese verwaiste Einträge zu bereinigen, möchten Sie möglicherweise eine Verbindung für ungültig zu erklären, die außerhalb eines Zeitrahmens erstellt wurde, die für Ihre Anwendung relevant ist. In die Beispielen in diesem Abschnitt enthalten einen Wert zum Nachverfolgen, wann die Verbindung erstellt wurde, aber nicht um alte Datensätze zu bereinigen, da möglicherweise Sie dies als Hintergrundprozess möchten anzeigen.

### <a name="database"></a>Datenbank

Die folgenden Beispiele zeigen, wie Sie die Verbindung und des Benutzers Informationen in einer Datenbank beibehalten wird. Sie können neue datenzugriffstechnologie verwenden. Im folgenden Beispiel zeigt jedoch wie Sie Modelle, die mithilfe von Entity Framework zu definieren. Diese Entitätsmodelle, Tabellen und Felder entsprechen. Die Datenstruktur kann je nach den Anforderungen Ihrer Anwendung erheblich variieren.

Das erste Beispiel zeigt, wie Sie eine Benutzerentität definieren, die viele Entitäten der Verbindung zugeordnet werden können.

[!code-csharp[Main](mapping-users-to-connections/samples/sample4.cs)]

Anschließend können Sie im Hub den Status der einzelnen Verbindung mit den folgenden Code verfolgen.

[!code-csharp[Main](mapping-users-to-connections/samples/sample5.cs)]

### <a name="azure-table-storage"></a>Azure-Tabellenspeicher

Im folgenden Beispiel von Azure Table Storage ist ähnlich wie im Beispiel für die Datenbank. Es enthält alle Informationen, die keine, die Sie benötigen würden, erste Schritte mit Azure Table Storage-Dienst. Weitere Informationen finden Sie unter [Gewusst wie: Verwenden von Tabellenspeicher aus .NET](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-tables/).

Das folgende Beispiel zeigt eine Tabellenentität zum Speichern von Verbindungsinformationen. Sie Partitionen, die Daten über den Benutzernamen, und jede Entität von der Verbindungs­ID identifiziert werden, damit ein Benutzer mehrere Verbindungen zu einem beliebigen Zeitpunkt verfügen kann.

[!code-csharp[Main](mapping-users-to-connections/samples/sample6.cs)]

In den Hub verfolgen Sie den Status der Verbindung des Benutzers.

[!code-csharp[Main](mapping-users-to-connections/samples/sample7.cs)]

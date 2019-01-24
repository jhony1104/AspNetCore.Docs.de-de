---
uid: signalr/overview/guide-to-the-api/working-with-groups
title: Arbeiten mit Gruppen in SignalR | Microsoft-Dokumentation
author: bradygaster
description: In diesem Thema wird beschrieben, wie Informationen über die Gruppenmitgliedschaft mit der Hub-API beibehalten wird.
ms.author: bradyg
ms.date: 06/10/2014
ms.assetid: cd378ecd-3e9e-4236-b902-65916d85a048
msc.legacyurl: /signalr/overview/guide-to-the-api/working-with-groups
msc.type: authoredcontent
ms.openlocfilehash: 384b7e5f07fa46ea3cc32e5c18c3c2327b7aedd3
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836804"
---
<a name="working-with-groups-in-signalr"></a>Arbeiten mit Gruppen in SignalR
====================
durch [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> In diesem Thema wird beschrieben, wie zum Hinzufügen von Benutzern zu Gruppen und Beibehalten von Informationen über die Gruppenmitgliedschaft wird.
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

## <a name="overview"></a>Übersicht

Gruppen in SignalR bieten eine Methode zum Übertragen von Nachrichten an die angegebene Teilmengen von verbundenen Clients. Eine Gruppe kann eine beliebige Anzahl Clients umfassen, und ein Client kann Mitglied einer beliebigen Anzahl von Gruppen sein. Sie haben keine Gruppen explizit zu erstellen. Aktiviert ist, eine Gruppe wird automatisch beim ersten Sie seinen Namen in einem Aufruf von Groups.Add angeben erstellt, und es wird gelöscht, wenn Sie aus der Mitgliedschaft in der sie die letzte Verbindung entfernen. Eine Einführung zum Verwenden von Gruppen finden Sie unter [zum Verwalten von Gruppenmitgliedschaften aus der hubklasse](hubs-api-guide-server.md#groupsfromhub) in die Hubs-API – Server-Handbuch.

Es ist keine API zum Abrufen der Mitgliederliste einer Gruppe oder eine Liste der Gruppen ein. SignalR sendet Nachrichten an Clients und Gruppen, die auf Grundlage eines Pub/Sub-Modells, und der Server behält keine Listen mit Gruppen oder Gruppenmitgliedschaften. Dadurch wird das Maximieren der Skalierbarkeit, da Wenn Sie einen Knoten zu einer Webfarm hinzufügen, verfügt über einem beliebigen Zustand, in der SignalR verwaltet, die auf den neuen Knoten weitergegeben werden.

Wenn Sie einen Benutzer hinzufügen, um eine Gruppe mit der `Groups.Add` -Methode, die der Benutzer erhält Nachrichten, die zu dieser Gruppe für die Dauer der aktuellen Verbindung geleitet, aber die Mitgliedschaft des Benutzers in dieser Gruppe wird nicht beibehalten, über die aktuelle Verbindung. Wenn Sie Informationen zu Gruppen und Gruppenmitgliedschaften dauerhaft beibehalten möchten, müssen Sie die Daten in einem Repository wie z. B. eine Datenbank oder Azure-Tabellenspeicher speichern. Klicken Sie dann jedes Mal, wenn ein Benutzer eine Verbindung Ihrer Anwendung mit Sie aus dem Repository abrufen, welchen Gruppen der Benutzer angehört, und fügen Sie diesen Benutzer für diese Gruppen manuell.

Beim Wiederherstellen der Verbindung nach einer vorübergehenden Störung verknüpft neu der Benutzer automatisch die zuvor zugewiesene Gruppen. Tritt automatisch eine Gruppe gilt nur beim Wiederherstellen der Verbindung nicht verwendet werden, wenn eine neue Verbindung herstellen. Ein digital signiertes Token wird vom Client übergeben, die die Liste der zuvor zugewiesenen Gruppen enthält. Wenn Sie möchten überprüfen, ob der Benutzer den angeforderten Gruppen angehört, können Sie das Standardverhalten überschreiben.

Dieses Thema enthält die folgenden Abschnitte:

- [Hinzufügen und Entfernen von Benutzern](#add)
- [Aufrufen von Membern einer Gruppe](#call)
- [Mitgliedschaft in einer Datenbank gespeichert.](#storedatabase)
- [Das Speichern von Gruppenmitgliedschaften in Azure-Tabellenspeicher](#storeazuretable)
- [Überprüfen der Gruppenmitgliedschaft, beim Wiederherstellen der Verbindung](#verify)

<a id="add"></a>

## <a name="adding-and-removing-users"></a>Hinzufügen und Entfernen von Benutzern

Rufen Sie zum Hinzufügen oder Entfernen von Benutzern aus einer Gruppe, die [hinzufügen](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.igroupmanager.add(v=vs.111).aspx) oder [entfernen](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.igroupmanager.remove(v=vs.111).aspx) Methoden und Verbindungs-Id des Benutzers und des Namens der Gruppe als Parameter übergeben. Sie müssen sich nicht um einen Benutzer manuell aus einer Gruppe zu entfernen, wenn die Verbindung beendet wird.

Das folgende Beispiel zeigt die `Groups.Add` und `Groups.Remove` Methoden, die in hubmethoden verwendet.

[!code-csharp[Main](working-with-groups/samples/sample1.cs?highlight=5,10)]

Die `Groups.Add` und `Groups.Remove` Methoden asynchron ausgeführt.

Wenn Sie einen Client zu einer Gruppe hinzufügen und eine Nachricht sofort an den Client senden, mit dem Group möchten, müssen Sie sicherstellen, dass die Methode Groups.Add zuerst beendet wird. Die folgenden Codebeispiele zeigen, wie das geht.

[!code-csharp[Main](working-with-groups/samples/sample2.cs?highlight=1,3)]

Im Allgemeinen sollten Sie nicht einschließen `await` beim Aufrufen der `Groups.Remove` Methode, da die Verbindungs-Id, die Sie entfernen möchten, sind möglicherweise nicht mehr verfügbar. In diesem Fall `TaskCanceledException` wird ausgelöst, nachdem die Anforderung ein auftritt Timeout. Wenn Ihre Anwendung sicherstellen muss, dass der Benutzer vor dem Senden einer Nachricht an die Gruppe aus der Gruppe entfernt wurde, können Sie hinzufügen `await` vor `Groups.Remove`, und klicken Sie dann Abfangen der `TaskCanceledException` Ausnahme, die ausgelöst werden.

<a id="call"></a>

## <a name="calling-members-of-a-group"></a>Aufrufen von Membern einer Gruppe

Sie können Nachrichten an alle Mitglieder einer Gruppe oder nur angegebene Mitglieder der Gruppe senden, wie in den folgenden Beispielen gezeigt.

- **Alle** verbundene Clients in einer angegebenen Gruppe.

    [!code-css[Main](working-with-groups/samples/sample3.css)]
- Alle verbundenen Clients in einer angegebenen Gruppe **mit Ausnahme der angegebenen Clients**identifizierte Verbindungs-ID.

    [!code-csharp[Main](working-with-groups/samples/sample4.cs)]
- Alle verbundenen Clients in einer angegebenen Gruppe **mit Ausnahme des aufrufenden Clients**.

    [!code-css[Main](working-with-groups/samples/sample5.css)]

<a id="storedatabase"></a>

## <a name="storing-group-membership-in-a-database"></a>Mitgliedschaft in einer Datenbank gespeichert.

Die folgenden Beispiele zeigen, wie Gruppen- und Informationen in einer Datenbank beibehalten werden sollen. Sie können neue datenzugriffstechnologie verwenden. Im folgenden Beispiel zeigt jedoch wie Sie Modelle, die mithilfe von Entity Framework zu definieren. Diese Entitätsmodelle, Tabellen und Felder entsprechen. Die Datenstruktur kann je nach den Anforderungen Ihrer Anwendung erheblich variieren. Dieses Beispiel enthält eine Klasse namens `ConversationRoom` Was wäre nur für eine Anwendung, die Benutzern ermöglicht, Konversationen zu verschiedenen Themen, wie z. B. Sport "oder" Gartenarbeit zu verknüpfen. Dieses Beispiel enthält auch eine Klasse für die Verbindungen. Connection-Klasse ist nicht unbedingt erforderlich ist, für die nachverfolgung der Gruppenmitgliedschaft, aber es ist häufig Teil stabile Lösung zum Nachverfolgen von Benutzern.

[!code-csharp[Main](working-with-groups/samples/sample6.cs)]

Anschließend können Sie im Hub, rufen die Gruppe und Informationen aus der Datenbank und der manuell zu den entsprechenden Gruppen hinzugefügt. Das Beispiel enthält Code zum Nachverfolgen der benutzerverbindungen keine. In diesem Beispiel die `await` Schlüsselwort wird nicht angewendet, bevor Sie `Groups.Add` , da eine Nachricht nicht sofort auf Mitglieder der Gruppe gesendet wird. Wenn eine Nachricht an alle Mitglieder der Gruppe zu senden, unmittelbar nachdem das neue Element hinzugefügt werden sollen, Sie möchten gelten die `await` Schlüsselwort, um sicherzustellen, dass der asynchrone Vorgang abgeschlossen wurde.

[!code-csharp[Main](working-with-groups/samples/sample7.cs)]

<a id="storeazuretable"></a>

## <a name="storing-group-membership-in-azure-table-storage"></a>Das Speichern von Gruppenmitgliedschaften in Azure-Tabellenspeicher

Mit Azure Table Storage zum Speichern von Informationen von Gruppen- und ähnelt der Verwendung einer Datenbank. Das folgende Beispiel zeigt eine Tabelle-Entität, die den Namen und Gruppennamen speichert.

[!code-csharp[Main](working-with-groups/samples/sample8.cs)]

Rufen Sie im Hub die zugewiesenen Gruppen aus, wenn der Benutzer eine Verbindung herstellt.

[!code-csharp[Main](working-with-groups/samples/sample9.cs)]

<a id="verify"></a>

## <a name="verifying-group-membership-when-reconnecting"></a>Überprüfen der Gruppenmitgliedschaft, beim Wiederherstellen der Verbindung

In der Standardeinstellung weist erneut SignalR automatisch einen Benutzer den entsprechenden Gruppen beim Wiederherstellen der Verbindung von einer vorübergehenden Störung, z. B. wenn eine Verbindung gelöscht und erneut hergestellt wird, bevor die Verbindung ein eintritt Timeout. Gruppeninformationen des Benutzers wird in einem Token übergeben, beim Wiederherstellen der Verbindung, und dieses Token wird auf dem Server überprüft. Weitere Informationen zu der Überprüfungsprozess für tritt der Benutzer, Gruppen, finden Sie unter [tritt von Gruppen beim Wiederherstellen der Verbindung](../security/introduction-to-security.md#rejoingroup).

Im Allgemeinen sollten Sie das Standardverhalten des automatisch tritt, dass die Gruppen auf Verbindung verwenden. SignalR-Gruppen sind nicht als Sicherheitsmechanismus zum Einschränken des Zugriffs auf sensible Daten vorgesehen. Wenn Ihre Anwendung beim Wiederherstellen der Verbindung Gruppenmitgliedschaft des Benutzers überprüfen muss, können Sie das Standardverhalten jedoch überschreiben. Ändern des Standardverhaltens kann einer Last mit der Datenbank hinzugefügt werden, da Gruppenmitgliedschaft des Benutzers abgerufen werden muss, für jeden erneuten Herstellen einer Verbindung und nicht nur dann, wenn der Benutzer eine Verbindung herstellt.

Wenn Sie auf der Gruppenmitgliedschaft überprüfen müssen erneut eine Verbindung herzustellen Sie, erstellen Sie ein neues Hub-Pipeline-Modul, das eine Liste mit zugewiesenen Gruppen zurückgibt, wie unten dargestellt.

[!code-csharp[Main](working-with-groups/samples/sample10.cs)]

Anschließend fügen Sie das Modul an die Hubpipeline wie unten markiert.

[!code-csharp[Main](working-with-groups/samples/sample11.cs?highlight=4)]

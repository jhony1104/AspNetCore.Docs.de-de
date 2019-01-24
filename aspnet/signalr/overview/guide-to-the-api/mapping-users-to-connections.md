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
<a name="mapping-signalr-users-to-connections"></a><span data-ttu-id="9f48e-105">Zuordnen von SignalR-Benutzern zu Verbindungen</span><span class="sxs-lookup"><span data-stu-id="9f48e-105">Mapping SignalR Users to Connections</span></span>
====================
<span data-ttu-id="9f48e-106">durch [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="9f48e-106">by [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="9f48e-107">In diesem Thema zeigt, wie Informationen über Benutzer und ihre Verbindungen beibehalten wird.</span><span class="sxs-lookup"><span data-stu-id="9f48e-107">This topic shows how to retain information about users and their connections.</span></span>
>
> <span data-ttu-id="9f48e-108">Patrick Fletcher dabei geholfen, dieses Thema zu schreiben.</span><span class="sxs-lookup"><span data-stu-id="9f48e-108">Patrick Fletcher helped write this topic.</span></span>
>
> ## <a name="software-versions-used-in-this-topic"></a><span data-ttu-id="9f48e-109">In diesem Thema verwendeten Softwareversionen</span><span class="sxs-lookup"><span data-stu-id="9f48e-109">Software versions used in this topic</span></span>
>
>
> - [<span data-ttu-id="9f48e-110">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="9f48e-110">Visual Studio 2013</span></span>](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - <span data-ttu-id="9f48e-111">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="9f48e-111">.NET 4.5</span></span>
> - <span data-ttu-id="9f48e-112">SignalR-Version 2</span><span class="sxs-lookup"><span data-stu-id="9f48e-112">SignalR version 2</span></span>
>
>
>
> ## <a name="previous-versions-of-this-topic"></a><span data-ttu-id="9f48e-113">Vorherige Versionen dieses Themas</span><span class="sxs-lookup"><span data-stu-id="9f48e-113">Previous versions of this topic</span></span>
>
> <span data-ttu-id="9f48e-114">Weitere Informationen zu früheren Versionen von SignalR, finden Sie unter [ältere Versionen von SignalR](../older-versions/index.md).</span><span class="sxs-lookup"><span data-stu-id="9f48e-114">For information about earlier versions of SignalR, see [SignalR Older Versions](../older-versions/index.md).</span></span>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="9f48e-115">Fragen und Kommentare</span><span class="sxs-lookup"><span data-stu-id="9f48e-115">Questions and comments</span></span>
>
> <span data-ttu-id="9f48e-116">Lassen Sie Feedback, auf wie Ihnen in diesem Tutorial gefallen hat und was wir in den Kommentaren am unteren Rand der Seite verbessern können.</span><span class="sxs-lookup"><span data-stu-id="9f48e-116">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="9f48e-117">Wenn Sie Fragen, die nicht direkt mit dem Tutorial verknüpft sind haben, können Sie sie veröffentlichen das [ASP.NET SignalR-Forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) oder [StackOverflow.com](http://stackoverflow.com/).</span><span class="sxs-lookup"><span data-stu-id="9f48e-117">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>

## <a name="introduction"></a><span data-ttu-id="9f48e-118">Einführung</span><span class="sxs-lookup"><span data-stu-id="9f48e-118">Introduction</span></span>

<span data-ttu-id="9f48e-119">Jeder Client, Herstellen einer Verbindung mit einem Hub übergibt eine eindeutige Verbindungs-Id an. Sie können diesen Wert im Abrufen der `Context.ConnectionId` Eigenschaft der Hub-Kontexts.</span><span class="sxs-lookup"><span data-stu-id="9f48e-119">Each client connecting to a hub passes a unique connection id. You can retrieve this value in the `Context.ConnectionId` property of the hub context.</span></span> <span data-ttu-id="9f48e-120">Wenn Ihre Anwendung muss einen Benutzer die Verbindungs-Id zuordnen, und die Zuordnung beizubehalten, können Sie eine der folgenden:</span><span class="sxs-lookup"><span data-stu-id="9f48e-120">If your application needs to map a user to the connection id and persist that mapping, you can use one of the following:</span></span>

- [<span data-ttu-id="9f48e-121">Die Benutzer-ID-Anbieter (SignalR 2)</span><span class="sxs-lookup"><span data-stu-id="9f48e-121">The User ID Provider (SignalR 2)</span></span>](#IUserIdProvider)
- <span data-ttu-id="9f48e-122">[In-Memory-Speicher](#inmemory), z. B. ein Wörterbuch</span><span class="sxs-lookup"><span data-stu-id="9f48e-122">[In-memory storage](#inmemory), such as a dictionary</span></span>
- [<span data-ttu-id="9f48e-123">SignalR-Gruppe für jeden Benutzer</span><span class="sxs-lookup"><span data-stu-id="9f48e-123">SignalR group for each user</span></span>](#groups)
- <span data-ttu-id="9f48e-124">[Dauerhafte, externe Speicher](#database), z. B. eine Datenbanktabelle oder eine Azure-Tabellenspeicher</span><span class="sxs-lookup"><span data-stu-id="9f48e-124">[Permanent, external storage](#database), such as a database table or Azure table storage</span></span>

<span data-ttu-id="9f48e-125">Jede dieser Implementierungen ist in diesem Thema dargestellt.</span><span class="sxs-lookup"><span data-stu-id="9f48e-125">Each of these implementations is shown in this topic.</span></span> <span data-ttu-id="9f48e-126">Sie verwenden die `OnConnected`, `OnDisconnected`, und `OnReconnected` Methoden der `Hub` Klasse, um den Verbindungsstatus für den Benutzer zu verfolgen.</span><span class="sxs-lookup"><span data-stu-id="9f48e-126">You use the `OnConnected`, `OnDisconnected`, and `OnReconnected` methods of the `Hub` class to track the user connection status.</span></span>

<span data-ttu-id="9f48e-127">Der beste Ansatz für Ihre Anwendung hängt von:</span><span class="sxs-lookup"><span data-stu-id="9f48e-127">The best approach for your application depends on:</span></span>

- <span data-ttu-id="9f48e-128">Die Anzahl von Webservern, die Ihre Anwendung hosten.</span><span class="sxs-lookup"><span data-stu-id="9f48e-128">The number of web servers hosting your application.</span></span>
- <span data-ttu-id="9f48e-129">Gibt an, ob müssen Sie eine Liste der aktuell verbundenen Benutzer zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="9f48e-129">Whether you need to get a list of the currently connected users.</span></span>
- <span data-ttu-id="9f48e-130">Gibt an, ob müssen Sie die Gruppe und die Benutzerinformationen dauerhaft zu speichern, wenn die Anwendung oder den Server neu gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="9f48e-130">Whether you need to persist group and user information when the application or server restarts.</span></span>
- <span data-ttu-id="9f48e-131">Gibt an, ob die Latenz des Aufrufs von eines externen Servers ein Problem besteht.</span><span class="sxs-lookup"><span data-stu-id="9f48e-131">Whether the latency of calling an external server is an issue.</span></span>

<span data-ttu-id="9f48e-132">Die folgende Tabelle zeigt, welcher Ansatz für diese Überlegungen funktioniert.</span><span class="sxs-lookup"><span data-stu-id="9f48e-132">The following table shows which approach works for these considerations.</span></span>

|  | <span data-ttu-id="9f48e-133">Mehr als einem server</span><span class="sxs-lookup"><span data-stu-id="9f48e-133">More than one server</span></span> | <span data-ttu-id="9f48e-134">Rufen Sie die Liste der derzeit verbundenen Benutzer</span><span class="sxs-lookup"><span data-stu-id="9f48e-134">Get list of currently connected users</span></span> | <span data-ttu-id="9f48e-135">Speichern Sie die Informationen nach dem Neustart</span><span class="sxs-lookup"><span data-stu-id="9f48e-135">Persist information after restarts</span></span> | <span data-ttu-id="9f48e-136">Eine optimale Leistung</span><span class="sxs-lookup"><span data-stu-id="9f48e-136">Optimal performance</span></span> |
| --- | --- | --- | --- | --- |
| <span data-ttu-id="9f48e-137">Benutzer-ID-Anbieter</span><span class="sxs-lookup"><span data-stu-id="9f48e-137">UserID Provider</span></span> | ![](mapping-users-to-connections/_static/image1.png) |  |  | ![](mapping-users-to-connections/_static/image2.png) |
| <span data-ttu-id="9f48e-138">Im Arbeitsspeicher</span><span class="sxs-lookup"><span data-stu-id="9f48e-138">In-memory</span></span> |  | ![](mapping-users-to-connections/_static/image3.png) |  | ![](mapping-users-to-connections/_static/image4.png) |
| <span data-ttu-id="9f48e-139">Einzelbenutzer-Gruppen</span><span class="sxs-lookup"><span data-stu-id="9f48e-139">Single-user groups</span></span> | ![](mapping-users-to-connections/_static/image5.png) |  |  | ![](mapping-users-to-connections/_static/image6.png) |
| <span data-ttu-id="9f48e-140">Externe permanente</span><span class="sxs-lookup"><span data-stu-id="9f48e-140">Permanent, external</span></span> | ![](mapping-users-to-connections/_static/image7.png) | ![](mapping-users-to-connections/_static/image8.png) | ![](mapping-users-to-connections/_static/image9.png) |  |

<a id="IUserIdProvider"></a>

## <a name="iuserid-provider"></a><span data-ttu-id="9f48e-141">IUserID-Anbieter</span><span class="sxs-lookup"><span data-stu-id="9f48e-141">IUserID provider</span></span>

<span data-ttu-id="9f48e-142">Dieses Feature ermöglicht Benutzern, um anzugeben, was die Benutzer-ID basierend auf einer IRequest über eine neue Schnittstelle IUserIdProvider.</span><span class="sxs-lookup"><span data-stu-id="9f48e-142">This feature allows users to specify what the userId is based on an IRequest via a new interface IUserIdProvider.</span></span>

<span data-ttu-id="9f48e-143">**Die IUserIdProvider**</span><span class="sxs-lookup"><span data-stu-id="9f48e-143">**The IUserIdProvider**</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample1.cs)]

<span data-ttu-id="9f48e-144">Standardmäßig wird eine Implementierung, des Benutzers verwendet `IPrincipal.Identity.Name` als Benutzernamen ein.</span><span class="sxs-lookup"><span data-stu-id="9f48e-144">By default, there will be an implementation that uses the user's `IPrincipal.Identity.Name` as the user name.</span></span> <span data-ttu-id="9f48e-145">Um dies zu ändern, registrieren Sie Ihre Implementierung von `IUserIdProvider` mit dem globalen Host beim Start der Anwendung:</span><span class="sxs-lookup"><span data-stu-id="9f48e-145">To change this, register your implementation of `IUserIdProvider` with the global host when your application starts:</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample2.cs)]

<span data-ttu-id="9f48e-146">Von werden innerhalb eines Hubs, Senden von Nachrichten an diese Benutzer über die folgende API werden:</span><span class="sxs-lookup"><span data-stu-id="9f48e-146">From within a hub, you'll be able to send messages to these users via the following API:</span></span>

<span data-ttu-id="9f48e-147">**Senden einer Nachricht an einen bestimmten Benutzer**</span><span class="sxs-lookup"><span data-stu-id="9f48e-147">**Sending a message to a specific user**</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample3.cs?highlight=5)]

<a id="inmemory"></a>

## <a name="in-memory-storage"></a><span data-ttu-id="9f48e-148">In-Memory-Speicher</span><span class="sxs-lookup"><span data-stu-id="9f48e-148">In-memory storage</span></span>

<span data-ttu-id="9f48e-149">Die folgenden Beispiele zeigen, wie Sie die Verbindung und des Benutzers Informationen in einem Wörterbuch beizubehalten, die im Arbeitsspeicher gespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="9f48e-149">The following examples show how to retain connection and user information in a dictionary that is stored in memory.</span></span> <span data-ttu-id="9f48e-150">Das Wörterbuch verwendet eine `HashSet` die Verbindungs-Id zu speichern. Zu einem beliebigen Zeitpunkt kann ein Benutzer mehr als eine Verbindung mit dem SignalR-Anwendung verfügen.</span><span class="sxs-lookup"><span data-stu-id="9f48e-150">The dictionary uses a `HashSet` to store the connection id. At any time a user could have more than one connection to the SignalR application.</span></span> <span data-ttu-id="9f48e-151">Beispielsweise würde ein Benutzer, die über mehrere Geräte oder mehr als eine Registerkarte "Browser" verbunden ist mehr als ein Verbindungs-Id haben.</span><span class="sxs-lookup"><span data-stu-id="9f48e-151">For example, a user who is connected through multiple devices or more than one browser tab would have more than one connection id.</span></span>

<span data-ttu-id="9f48e-152">Wenn die Anwendung heruntergefahren wird, alle Informationen geht verloren, aber erneut aufgefüllt wird, wie die Benutzer die Verbindung erneut herzustellen.</span><span class="sxs-lookup"><span data-stu-id="9f48e-152">If the application shuts down, all of the information is lost, but it will be re-populated as the users re-establish their connections.</span></span> <span data-ttu-id="9f48e-153">In-Memory-Speicher funktionieren nicht, wenn die Umgebung mehrere Webserver umfasst, da jeder Server eine separate Auflistung von Verbindungen verfügen würde.</span><span class="sxs-lookup"><span data-stu-id="9f48e-153">In-memory storage does not work if your environment includes more than one web server because each server would have a separate collection of connections.</span></span>

<span data-ttu-id="9f48e-154">Das erste Beispiel zeigt eine Klasse, die Zuordnung von Benutzern zu Verbindungen verwaltet, werden.</span><span class="sxs-lookup"><span data-stu-id="9f48e-154">The first example shows a class that manages the mapping of users to connections.</span></span> <span data-ttu-id="9f48e-155">Der Schlüssel für das HashSet werden der Name des Benutzers.</span><span class="sxs-lookup"><span data-stu-id="9f48e-155">The key for the HashSet will be the user's name.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample4.cs)]

<span data-ttu-id="9f48e-156">Das nächste Beispiel zeigt, wie Sie mit der Zuordnung Verbindungsklasse über einen Hub.</span><span class="sxs-lookup"><span data-stu-id="9f48e-156">The next example shows how to use the connection mapping class from a hub.</span></span> <span data-ttu-id="9f48e-157">Die Instanz der Klasse befindet sich in einen Variablennamen `_connections`.</span><span class="sxs-lookup"><span data-stu-id="9f48e-157">The instance of the class is stored in a variable name `_connections`.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample5.cs)]

<a id="groups"></a>

## <a name="single-user-groups"></a><span data-ttu-id="9f48e-158">Einzelbenutzer-Gruppen</span><span class="sxs-lookup"><span data-stu-id="9f48e-158">Single-user groups</span></span>

<span data-ttu-id="9f48e-159">Sie können eine Gruppe für jeden Benutzer erstellen und zu dieser Gruppe klicken Sie dann eine Nachricht senden, wenn Sie nur dieser Benutzer erreichen möchten.</span><span class="sxs-lookup"><span data-stu-id="9f48e-159">You can create a group for each user, and then send a message to that group when you want to reach only that user.</span></span> <span data-ttu-id="9f48e-160">Der Name jeder Gruppe ist der Name des Benutzers.</span><span class="sxs-lookup"><span data-stu-id="9f48e-160">The name of each group is the name of the user.</span></span> <span data-ttu-id="9f48e-161">Wenn ein Benutzer mehrere Verbindungen verfügt, wird die Gruppe des Benutzers jeder Verbindungs-Id hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="9f48e-161">If a user has more than one connection, each connection id is added to the user's group.</span></span>

<span data-ttu-id="9f48e-162">Sie sollten nicht manuell entfernen des Benutzers aus der Gruppe, wenn der Benutzer die Verbindung trennt.</span><span class="sxs-lookup"><span data-stu-id="9f48e-162">You should not manually remove the user from the group when the user disconnects.</span></span> <span data-ttu-id="9f48e-163">Dadurch wird automatisch vom SignalR-Framework ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="9f48e-163">This action is automatically performed by the SignalR framework.</span></span>

<span data-ttu-id="9f48e-164">Das folgende Beispiel zeigt, wie Sie Gruppen für einzelne Benutzer zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="9f48e-164">The following example shows how to implement single-user groups.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample6.cs)]

<a id="database"></a>

## <a name="permanent-external-storage"></a><span data-ttu-id="9f48e-165">Dauerhafte, externe Speicher</span><span class="sxs-lookup"><span data-stu-id="9f48e-165">Permanent, external storage</span></span>

<span data-ttu-id="9f48e-166">In diesem Thema wird gezeigt, wie eine Datenbank oder Azure-Tabellenspeicher verwenden Sie zum Speichern von Verbindungsinformationen.</span><span class="sxs-lookup"><span data-stu-id="9f48e-166">This topic shows how to use either a database or Azure table storage for storing connection information.</span></span> <span data-ttu-id="9f48e-167">Dieser Ansatz funktioniert, wenn Sie mehrere Webserver haben, da jedem Webserver mit der gleichen Data-Repository interagieren kann.</span><span class="sxs-lookup"><span data-stu-id="9f48e-167">This approach works when you have multiple web servers because each web server can interact with the same data repository.</span></span> <span data-ttu-id="9f48e-168">Wenn Ihre Webserver arbeiten oder die Anwendung neu gestartet wird, beenden die `OnDisconnected` Methode wird nicht aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="9f48e-168">If your web servers stop working or the application restarts, the `OnDisconnected` method is not called.</span></span> <span data-ttu-id="9f48e-169">Aus diesem Grund ist es möglich, dass Ihr Datenrepository Datensätze für Verbindungs-Ids gibt, die nicht mehr gültig sind.</span><span class="sxs-lookup"><span data-stu-id="9f48e-169">Therefore, it is possible that your data repository will have records for connection ids that are no longer valid.</span></span> <span data-ttu-id="9f48e-170">Um diese verwaiste Einträge zu bereinigen, möchten Sie möglicherweise eine Verbindung für ungültig zu erklären, die außerhalb eines Zeitrahmens erstellt wurde, die für Ihre Anwendung relevant ist.</span><span class="sxs-lookup"><span data-stu-id="9f48e-170">To clean up these orphaned records, you may wish to invalidate any connection that was created outside of a timeframe that is relevant to your application.</span></span> <span data-ttu-id="9f48e-171">In die Beispielen in diesem Abschnitt enthalten einen Wert zum Nachverfolgen, wann die Verbindung erstellt wurde, aber nicht um alte Datensätze zu bereinigen, da möglicherweise Sie dies als Hintergrundprozess möchten anzeigen.</span><span class="sxs-lookup"><span data-stu-id="9f48e-171">The examples in this section include a value for tracking when the connection was created, but do not show how to clean up old records because you may want to do that as background process.</span></span>

### <a name="database"></a><span data-ttu-id="9f48e-172">Datenbank</span><span class="sxs-lookup"><span data-stu-id="9f48e-172">Database</span></span>

<span data-ttu-id="9f48e-173">Die folgenden Beispiele zeigen, wie Sie die Verbindung und des Benutzers Informationen in einer Datenbank beibehalten wird.</span><span class="sxs-lookup"><span data-stu-id="9f48e-173">The following examples show how to retain connection and user information in a database.</span></span> <span data-ttu-id="9f48e-174">Sie können neue datenzugriffstechnologie verwenden. Im folgenden Beispiel zeigt jedoch wie Sie Modelle, die mithilfe von Entity Framework zu definieren.</span><span class="sxs-lookup"><span data-stu-id="9f48e-174">You can use any data access technology; however, the example below shows how to define models using Entity Framework.</span></span> <span data-ttu-id="9f48e-175">Diese Entitätsmodelle, Tabellen und Felder entsprechen.</span><span class="sxs-lookup"><span data-stu-id="9f48e-175">These entity models correspond to database tables and fields.</span></span> <span data-ttu-id="9f48e-176">Die Datenstruktur kann je nach den Anforderungen Ihrer Anwendung erheblich variieren.</span><span class="sxs-lookup"><span data-stu-id="9f48e-176">Your data structure could vary considerably depending on the requirements of your application.</span></span>

<span data-ttu-id="9f48e-177">Das erste Beispiel zeigt, wie Sie eine Benutzerentität definieren, die viele Entitäten der Verbindung zugeordnet werden können.</span><span class="sxs-lookup"><span data-stu-id="9f48e-177">The first example shows how to define a user entity that can be associated with many connection entities.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample7.cs)]

<span data-ttu-id="9f48e-178">Anschließend können Sie im Hub den Status der einzelnen Verbindung mit den folgenden Code verfolgen.</span><span class="sxs-lookup"><span data-stu-id="9f48e-178">Then, from the hub, you can track the state of each connection with the code shown below.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample8.cs)]

<a id="azure"></a>
### <a name="azure-table-storage"></a><span data-ttu-id="9f48e-179">Azure-Tabellenspeicher</span><span class="sxs-lookup"><span data-stu-id="9f48e-179">Azure table storage</span></span>

<span data-ttu-id="9f48e-180">Im folgenden Beispiel von Azure Table Storage ist ähnlich wie im Beispiel für die Datenbank.</span><span class="sxs-lookup"><span data-stu-id="9f48e-180">The following Azure table storage example is similar to the database example.</span></span> <span data-ttu-id="9f48e-181">Es enthält alle Informationen, die keine, die Sie benötigen würden, erste Schritte mit Azure Table Storage-Dienst.</span><span class="sxs-lookup"><span data-stu-id="9f48e-181">It does not include all of the information that you would need to get started with Azure Table Storage Service.</span></span> <span data-ttu-id="9f48e-182">Weitere Informationen finden Sie unter [Gewusst wie: Verwenden von Tabellenspeicher aus .NET](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-tables/).</span><span class="sxs-lookup"><span data-stu-id="9f48e-182">For information, see [How to use Table storage from .NET](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-tables/).</span></span>

<span data-ttu-id="9f48e-183">Das folgende Beispiel zeigt eine Tabellenentität zum Speichern von Verbindungsinformationen.</span><span class="sxs-lookup"><span data-stu-id="9f48e-183">The following example shows a table entity for storing connection information.</span></span> <span data-ttu-id="9f48e-184">Sie Partitionen, die Daten über den Benutzernamen, und jede Entität von der Verbindungs­ID identifiziert werden, damit ein Benutzer mehrere Verbindungen zu einem beliebigen Zeitpunkt verfügen kann.</span><span class="sxs-lookup"><span data-stu-id="9f48e-184">It partitions the data by user name, and identifies each entity by the connection id, so a user can have multiple connections at any time.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample9.cs)]

<span data-ttu-id="9f48e-185">In den Hub verfolgen Sie den Status der Verbindung des Benutzers.</span><span class="sxs-lookup"><span data-stu-id="9f48e-185">In the hub, you track the status of each user's connection.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample10.cs)]

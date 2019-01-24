---
uid: signalr/overview/performance/scaleout-with-sql-server
title: Horizontale Skalierung in SignalR mit SQLServer | Microsoft-Dokumentation
author: bradygaster
description: Version der Software verwendete in diesem Thema Visual Studio 2013 .NET 4.5 SignalR Version 2-Vorgängerversionen des in diesem Thema Informationen zu früheren Versionen von...
ms.author: bradyg
ms.date: 06/10/2014
ms.assetid: 98358b6e-9139-4239-ba3a-2d7dd74dd664
msc.legacyurl: /signalr/overview/performance/scaleout-with-sql-server
msc.type: authoredcontent
ms.openlocfilehash: 5984a5e6c3215e7dde8c09ef702bf6453730a3ee
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837896"
---
<a name="signalr-scaleout-with-sql-server"></a><span data-ttu-id="a96f7-103">Horizontale Skalierung in SignalR mit SQLServer</span><span class="sxs-lookup"><span data-stu-id="a96f7-103">SignalR Scaleout with SQL Server</span></span>
====================
<span data-ttu-id="a96f7-104">durch [Mike Wasson](https://github.com/MikeWasson), [Patrick Fletcher](https://github.com/pfletcher)</span><span class="sxs-lookup"><span data-stu-id="a96f7-104">by [Mike Wasson](https://github.com/MikeWasson), [Patrick Fletcher](https://github.com/pfletcher)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> ## <a name="software-versions-used-in-this-topic"></a><span data-ttu-id="a96f7-105">In diesem Thema verwendeten Softwareversionen</span><span class="sxs-lookup"><span data-stu-id="a96f7-105">Software versions used in this topic</span></span>
>
>
> - [<span data-ttu-id="a96f7-106">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="a96f7-106">Visual Studio 2013</span></span>](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - <span data-ttu-id="a96f7-107">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="a96f7-107">.NET 4.5</span></span>
> - <span data-ttu-id="a96f7-108">SignalR-Version 2</span><span class="sxs-lookup"><span data-stu-id="a96f7-108">SignalR version 2</span></span>
>
>
>
> ## <a name="previous-versions-of-this-topic"></a><span data-ttu-id="a96f7-109">Vorherige Versionen dieses Themas</span><span class="sxs-lookup"><span data-stu-id="a96f7-109">Previous versions of this topic</span></span>
>
> <span data-ttu-id="a96f7-110">Weitere Informationen zu früheren Versionen von SignalR, finden Sie unter [ältere Versionen von SignalR](../older-versions/index.md).</span><span class="sxs-lookup"><span data-stu-id="a96f7-110">For information about earlier versions of SignalR, see [SignalR Older Versions](../older-versions/index.md).</span></span>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="a96f7-111">Fragen und Kommentare</span><span class="sxs-lookup"><span data-stu-id="a96f7-111">Questions and comments</span></span>
>
> <span data-ttu-id="a96f7-112">Lassen Sie Feedback, auf wie Ihnen in diesem Tutorial gefallen hat und was wir in den Kommentaren am unteren Rand der Seite verbessern können.</span><span class="sxs-lookup"><span data-stu-id="a96f7-112">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="a96f7-113">Wenn Sie Fragen, die nicht direkt mit dem Tutorial verknüpft sind haben, können Sie sie veröffentlichen das [ASP.NET SignalR-Forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) oder [StackOverflow.com](http://stackoverflow.com/).</span><span class="sxs-lookup"><span data-stu-id="a96f7-113">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>


<span data-ttu-id="a96f7-114">In diesem Tutorial verwenden Sie SQL Server zur Verteilung von Nachrichten in einer SignalR-Anwendung, die in zwei verschiedenen IIS-Instanzen bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="a96f7-114">In this tutorial, you will use SQL Server to distribute messages across a SignalR application that is deployed in two separate IIS instances.</span></span> <span data-ttu-id="a96f7-115">Sie können auch in diesem Tutorial ausführen, auf einem einzelnen Testcomputer, aber um den vollen Effekt zu erhalten, müssen Sie die SignalR-Anwendung in zwei oder mehr Servern bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="a96f7-115">You can also run this tutorial on a single test machine, but to get the full effect, you need to deploy the SignalR application to two or more servers.</span></span> <span data-ttu-id="a96f7-116">Sie müssen auch SQL Server auf einem der Server oder auf einem separaten dedizierten Server installieren.</span><span class="sxs-lookup"><span data-stu-id="a96f7-116">You must also install SQL Server on one of the servers, or on a separate dedicated server.</span></span> <span data-ttu-id="a96f7-117">Eine weitere Möglichkeit ist das Tutorial mit virtuellen Computern in Azure ausführen.</span><span class="sxs-lookup"><span data-stu-id="a96f7-117">Another option is to run the tutorial using VMs on Azure.</span></span>

![](scaleout-with-sql-server/_static/image1.png)

## <a name="prerequisites"></a><span data-ttu-id="a96f7-118">Vorraussetzungen</span><span class="sxs-lookup"><span data-stu-id="a96f7-118">Prerequisites</span></span>

<span data-ttu-id="a96f7-119">Microsoft SQLServer 2005 oder höher.</span><span class="sxs-lookup"><span data-stu-id="a96f7-119">Microsoft SQL Server 2005 or later.</span></span> <span data-ttu-id="a96f7-120">Der Rückwand unterstützt Desktop- und Server-Editionen von SQL Server.</span><span class="sxs-lookup"><span data-stu-id="a96f7-120">The backplane supports both desktop and server editions of SQL Server.</span></span> <span data-ttu-id="a96f7-121">Es unterstützt SQL Server Compact Edition oder Azure SQL-Datenbank nicht.</span><span class="sxs-lookup"><span data-stu-id="a96f7-121">It does not support SQL Server Compact Edition or Azure SQL Database.</span></span> <span data-ttu-id="a96f7-122">(Wenn Ihre Anwendung in Azure gehostet wird, erwägen Sie die Service Bus-Rückwandplatine stattdessen.)</span><span class="sxs-lookup"><span data-stu-id="a96f7-122">(If your application is hosted on Azure, consider the Service Bus backplane instead.)</span></span>

## <a name="overview"></a><span data-ttu-id="a96f7-123">Übersicht</span><span class="sxs-lookup"><span data-stu-id="a96f7-123">Overview</span></span>

<span data-ttu-id="a96f7-124">Bevor wir mit dem ausführlichen Tutorial eingehen, sieht ein schnellen Überblick darüber, welche Aktionen Sie ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="a96f7-124">Before we get to the detailed tutorial, here is a quick overview of what you will do.</span></span>

1. <span data-ttu-id="a96f7-125">Erstellen Sie eine neue leere Datenbank ein.</span><span class="sxs-lookup"><span data-stu-id="a96f7-125">Create a new empty database.</span></span> <span data-ttu-id="a96f7-126">Der Rückwand erstellt die erforderlichen Tabellen in dieser Datenbank.</span><span class="sxs-lookup"><span data-stu-id="a96f7-126">The backplane will create the necessary tables in this database.</span></span>
2. <span data-ttu-id="a96f7-127">Fügen Sie diese NuGet-Pakete für Ihre Anwendung hinzu:</span><span class="sxs-lookup"><span data-stu-id="a96f7-127">Add these NuGet packages to your application:</span></span>

    - [<span data-ttu-id="a96f7-128">Microsoft.AspNet.SignalR</span><span class="sxs-lookup"><span data-stu-id="a96f7-128">Microsoft.AspNet.SignalR</span></span>](http://nuget.org/packages/Microsoft.AspNet.SignalR)
    - [<span data-ttu-id="a96f7-129">Microsoft.AspNet.SignalR.SqlServer</span><span class="sxs-lookup"><span data-stu-id="a96f7-129">Microsoft.AspNet.SignalR.SqlServer</span></span>](http://nuget.org/packages/Microsoft.AspNet.SignalR.SqlServer)
3. <span data-ttu-id="a96f7-130">Erstellen einer SignalR-Anwendung.</span><span class="sxs-lookup"><span data-stu-id="a96f7-130">Create a SignalR application.</span></span>
4. <span data-ttu-id="a96f7-131">Fügen Sie den folgenden Code, "Startup.cs" So konfigurieren Sie die Rückwandplatine:</span><span class="sxs-lookup"><span data-stu-id="a96f7-131">Add the following code to Startup.cs to configure the backplane:</span></span>

    [!code-csharp[Main](scaleout-with-sql-server/samples/sample1.cs)]

   <span data-ttu-id="a96f7-132">Dieser Code konfiguriert die Rückwandplatine mit den Standardwerten für [TableCount](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.sqlscaleoutconfiguration.tablecount(v=vs.118).aspx) und [MaxQueueLength](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.messaging.scaleoutconfiguration.maxqueuelength(v=vs.118).aspx).</span><span class="sxs-lookup"><span data-stu-id="a96f7-132">This code configures the backplane with the default values for [TableCount](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.sqlscaleoutconfiguration.tablecount(v=vs.118).aspx) and [MaxQueueLength](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.messaging.scaleoutconfiguration.maxqueuelength(v=vs.118).aspx).</span></span> <span data-ttu-id="a96f7-133">Informationen zum Ändern dieser Werte finden Sie unter [SignalR-Leistung: Horizontale Skalierung Metriken](signalr-performance.md#scaleout_metrics).</span><span class="sxs-lookup"><span data-stu-id="a96f7-133">For information on changing these values, see [SignalR Performance: Scaleout Metrics](signalr-performance.md#scaleout_metrics).</span></span>

## <a name="configure-the-database"></a><span data-ttu-id="a96f7-134">Konfigurieren Sie die Datenbank</span><span class="sxs-lookup"><span data-stu-id="a96f7-134">Configure the Database</span></span>

<span data-ttu-id="a96f7-135">Entscheiden Sie, ob es sich bei der Anwendung Windows-Authentifizierung oder SQL Server-Authentifizierung verwendet werden, auf die Datenbank zugreifen.</span><span class="sxs-lookup"><span data-stu-id="a96f7-135">Decide whether the application will use Windows authentication or SQL Server authentication to access the database.</span></span> <span data-ttu-id="a96f7-136">Unabhängig davon, stellen Sie sicher, dass der Datenbankbenutzer berechtigt ist, melden Sie sich, Erstellen von Schemas und Tabellen erstellen.</span><span class="sxs-lookup"><span data-stu-id="a96f7-136">Regardless, make sure the database user has permissions to log in, create schemas, and create tables.</span></span>

<span data-ttu-id="a96f7-137">Erstellen Sie eine neue Datenbank für die Rückwandplatine verwenden.</span><span class="sxs-lookup"><span data-stu-id="a96f7-137">Create a new database for the backplane to use.</span></span> <span data-ttu-id="a96f7-138">Sie können die Datenbank einen beliebigen Namen geben.</span><span class="sxs-lookup"><span data-stu-id="a96f7-138">You can give the database any name.</span></span> <span data-ttu-id="a96f7-139">Sie müssen keine Tabellen in der Datenbank zu erstellen. der Rückwand erstellt die erforderlichen Tabellen.</span><span class="sxs-lookup"><span data-stu-id="a96f7-139">You don't need to create any tables in the database; the backplane will create the necessary tables.</span></span>

![](scaleout-with-sql-server/_static/image2.png)

## <a name="enable-service-broker"></a><span data-ttu-id="a96f7-140">Aktivieren Sie Service Broker</span><span class="sxs-lookup"><span data-stu-id="a96f7-140">Enable Service Broker</span></span>

<span data-ttu-id="a96f7-141">Es wird empfohlen, um Service Broker für die Rückwandplatine-Datenbank zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="a96f7-141">It is recommended to enable Service Broker for the backplane database.</span></span> <span data-ttu-id="a96f7-142">Service Broker bietet systemeigene Unterstützung für Messaging- und Warteschlangen in SQL Server, der in der Rückwand Updates effizienter erhalten können.</span><span class="sxs-lookup"><span data-stu-id="a96f7-142">Service Broker provides native support for messaging and queuing in SQL Server, which lets the backplane receive updates more efficiently.</span></span> <span data-ttu-id="a96f7-143">(Allerdings funktioniert der Rückwand auch ohne Service Broker.)</span><span class="sxs-lookup"><span data-stu-id="a96f7-143">(However, the backplane also works without Service Broker.)</span></span>

<span data-ttu-id="a96f7-144">Um zu überprüfen, ob Service Broker aktiviert ist, Abfragen der **ist\_Broker\_aktiviert** -Spalte in der **sys.databases** -Katalogsicht angezeigt.</span><span class="sxs-lookup"><span data-stu-id="a96f7-144">To check whether Service Broker is enabled, query the **is\_broker\_enabled** column in the **sys.databases** catalog view.</span></span>

[!code-sql[Main](scaleout-with-sql-server/samples/sample2.sql)]

![](scaleout-with-sql-server/_static/image3.png)

<span data-ttu-id="a96f7-145">Verwenden Sie die folgende SQL-Abfrage, um Service Broker zu aktivieren:</span><span class="sxs-lookup"><span data-stu-id="a96f7-145">To enable Service Broker, use the following SQL query:</span></span>

[!code-sql[Main](scaleout-with-sql-server/samples/sample3.sql)]

> [!NOTE]
> <span data-ttu-id="a96f7-146">Wenn diese Abfrage angezeigt wird, ein Deadlock auftreten, stellen Sie sicher, dass keine Programme, die mit der Datenbank verbunden sind.</span><span class="sxs-lookup"><span data-stu-id="a96f7-146">If this query appears to deadlock, make sure there are no applications connected to the DB.</span></span>


<span data-ttu-id="a96f7-147">Wenn Sie die Ablaufverfolgung aktiviert haben, zeigt die ablaufverfolgungen auch, ob Service Broker aktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="a96f7-147">If you have enabled tracing, the traces will also show whether Service Broker is enabled.</span></span>

## <a name="create-a-signalr-application"></a><span data-ttu-id="a96f7-148">Erstellen einer SignalR-Anwendung</span><span class="sxs-lookup"><span data-stu-id="a96f7-148">Create a SignalR Application</span></span>

<span data-ttu-id="a96f7-149">Erstellen einer SignalR-Anwendung entweder mit diesen Lernprogrammen:</span><span class="sxs-lookup"><span data-stu-id="a96f7-149">Create a SignalR application by following either of these tutorials:</span></span>

- [<span data-ttu-id="a96f7-150">Erste Schritte mit SignalR 2.0</span><span class="sxs-lookup"><span data-stu-id="a96f7-150">Getting Started with SignalR 2.0</span></span>](../getting-started/tutorial-getting-started-with-signalr.md)
- [<span data-ttu-id="a96f7-151">Erste Schritte mit SignalR 2.0 und MVC 5</span><span class="sxs-lookup"><span data-stu-id="a96f7-151">Getting Started with SignalR 2.0 and MVC 5</span></span>](../getting-started/tutorial-getting-started-with-signalr-and-mvc.md)

<span data-ttu-id="a96f7-152">Als Nächstes ändern wir die Chat-Anwendung mit horizontaler Skalierung mit SQL Server zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="a96f7-152">Next, we'll modify the chat application to support scaleout with SQL Server.</span></span> <span data-ttu-id="a96f7-153">Fügen Sie zunächst das SignalR.SqlServer NuGet-Paket Ihrem Projekt ein.</span><span class="sxs-lookup"><span data-stu-id="a96f7-153">First, add the SignalR.SqlServer NuGet package to your project.</span></span> <span data-ttu-id="a96f7-154">In Visual Studio aus der **Tools** die Option **NuGet Paket-Manager**, wählen Sie **Paket-Manager Konsole**.</span><span class="sxs-lookup"><span data-stu-id="a96f7-154">In Visual Studio, from the **Tools** menu, select **NuGet Package Manager**, then select **Package Manager Console**.</span></span> <span data-ttu-id="a96f7-155">Geben Sie im Fenster Paket-Manager-Konsole den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="a96f7-155">In the Package Manager Console window, enter the following command:</span></span>

[!code-powershell[Main](scaleout-with-sql-server/samples/sample4.ps1)]

<span data-ttu-id="a96f7-156">Öffnen Sie als Nächstes die Datei "Startup.cs".</span><span class="sxs-lookup"><span data-stu-id="a96f7-156">Next, open the Startup.cs file.</span></span> <span data-ttu-id="a96f7-157">Fügen Sie den folgenden Code der **konfigurieren** Methode:</span><span class="sxs-lookup"><span data-stu-id="a96f7-157">Add the following code to the **Configure** method:</span></span>

[!code-csharp[Main](scaleout-with-sql-server/samples/sample5.cs)]

## <a name="deploy-and-run-the-application"></a><span data-ttu-id="a96f7-158">Bereitstellen und Ausführen der Anwendung</span><span class="sxs-lookup"><span data-stu-id="a96f7-158">Deploy and Run the Application</span></span>

<span data-ttu-id="a96f7-159">Bereiten Sie Ihre Windows Server-Instanzen, um die SignalR-Anwendung bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="a96f7-159">Prepare your Windows Server instances to deploy the SignalR application.</span></span>

<span data-ttu-id="a96f7-160">Fügen Sie die IIS-Rolle hinzu.</span><span class="sxs-lookup"><span data-stu-id="a96f7-160">Add the IIS role.</span></span> <span data-ttu-id="a96f7-161">Umfassen Sie "Anwendungsentwicklung"-Features, einschließlich der WebSocket-Protokoll.</span><span class="sxs-lookup"><span data-stu-id="a96f7-161">Include "Application Development" features, including the WebSocket Protocol.</span></span>

![](scaleout-with-sql-server/_static/image4.png)

<span data-ttu-id="a96f7-162">Außerdem enthalten Sie den Management-Dienst (unter "Management Tools" aufgeführt).</span><span class="sxs-lookup"><span data-stu-id="a96f7-162">Also include the Management Service (listed under "Management Tools").</span></span>

![](scaleout-with-sql-server/_static/image5.png)

<span data-ttu-id="a96f7-163">**Installieren Sie Web Deploy 3.0.**</span><span class="sxs-lookup"><span data-stu-id="a96f7-163">**Install Web Deploy 3.0.**</span></span> <span data-ttu-id="a96f7-164">Wenn Sie die IIS-Manager ausführen, werden Sie zum Installieren von Microsoft-Webplattform aufgefordert, oder Sie können [Herunterladen der Intstaller](https://go.microsoft.com/fwlink/?LinkId=255386).</span><span class="sxs-lookup"><span data-stu-id="a96f7-164">When you run IIS Manager, it will prompt you to install Microsoft Web Platform, or you can [download the intstaller](https://go.microsoft.com/fwlink/?LinkId=255386).</span></span> <span data-ttu-id="a96f7-165">Klicken Sie in den Webplattform-Installer Web Deploy suchen Sie und installieren Sie Web Deploy 3.0</span><span class="sxs-lookup"><span data-stu-id="a96f7-165">In the Platform Installer, search for Web Deploy and install Web Deploy 3.0</span></span>

![](scaleout-with-sql-server/_static/image6.png)

<span data-ttu-id="a96f7-166">Überprüfen Sie, dass die Web-Management-Dienst ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="a96f7-166">Check that the Web Management Service is running.</span></span> <span data-ttu-id="a96f7-167">Wenn dies nicht der Fall ist, starten Sie den Dienst.</span><span class="sxs-lookup"><span data-stu-id="a96f7-167">If not, start the service.</span></span> <span data-ttu-id="a96f7-168">(Wenn Sie Web-Management-Dienst in der Liste der Windows-Dienste nicht angezeigt wird, stellen Sie sicher, dass Sie den Management-Dienst installiert, wenn Sie die IIS-Rolle hinzugefügt haben.)</span><span class="sxs-lookup"><span data-stu-id="a96f7-168">(If you don't see Web Management Service in the list of Windows services, make sure that you installed the Management Service when you added the IIS role.)</span></span>

<span data-ttu-id="a96f7-169">Öffnen Sie abschließend Port 8172 für TCP.</span><span class="sxs-lookup"><span data-stu-id="a96f7-169">Finally, open port 8172 for TCP.</span></span> <span data-ttu-id="a96f7-170">Dies ist der Port, den das Web Deploy-Tool verwendet.</span><span class="sxs-lookup"><span data-stu-id="a96f7-170">This is the port that the Web Deploy tool uses.</span></span>

<span data-ttu-id="a96f7-171">Jetzt sind Sie bereit für die Bereitstellung von Visual Studio-Projekt vom Entwicklungscomputer auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="a96f7-171">Now you are ready to deploy the Visual Studio project from your development machine to the server.</span></span> <span data-ttu-id="a96f7-172">Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste in der Projektmappe, und klicken Sie auf **veröffentlichen**.</span><span class="sxs-lookup"><span data-stu-id="a96f7-172">In Solution Explorer, right-click the solution and click **Publish**.</span></span>

<span data-ttu-id="a96f7-173">Weitere Dokumentation zur Bereitstellung finden Sie unter [Einstieg in die Webbereitstellung für Visual Studio und ASP.NET](../../../whitepapers/aspnet-web-deployment-content-map.md).</span><span class="sxs-lookup"><span data-stu-id="a96f7-173">For more detailed documentation about web deployment, see [Web Deployment Content Map for Visual Studio and ASP.NET](../../../whitepapers/aspnet-web-deployment-content-map.md).</span></span>

<span data-ttu-id="a96f7-174">Wenn Sie die Anwendung auf zwei Servern bereitstellen, können Sie jede Instanz in einem separaten Browserfenster zu öffnen und sehen, dass jeder SignalR-Nachrichten von einem anderen erhält.</span><span class="sxs-lookup"><span data-stu-id="a96f7-174">If you deploy the application to two servers, you can open each instance in a separate browser window and see that they each receive SignalR messages from the other.</span></span> <span data-ttu-id="a96f7-175">(Natürlich in einer produktionsumgebung, die beiden Server hinter einem Load Balancer saß.)</span><span class="sxs-lookup"><span data-stu-id="a96f7-175">(Of course, in a production environment, the two servers would sit behind a load balancer.)</span></span>

![](scaleout-with-sql-server/_static/image7.png)

<span data-ttu-id="a96f7-176">Nachdem Sie die Anwendung ausführen, sehen Sie sich, dass SignalR automatisch Tabellen in der Datenbank erstellt wurde:</span><span class="sxs-lookup"><span data-stu-id="a96f7-176">After you run the application, you can see that SignalR has automatically created tables in the database:</span></span>

![](scaleout-with-sql-server/_static/image8.png)

<span data-ttu-id="a96f7-177">SignalR verwaltet die Tabellen.</span><span class="sxs-lookup"><span data-stu-id="a96f7-177">SignalR manages the tables.</span></span> <span data-ttu-id="a96f7-178">Solange Ihre Anwendung bereitgestellt wird, keine Zeilen löschen, ändern Sie die Tabelle und so weiter.</span><span class="sxs-lookup"><span data-stu-id="a96f7-178">As long as your application is deployed, don't delete rows, modify the table, and so forth.</span></span>

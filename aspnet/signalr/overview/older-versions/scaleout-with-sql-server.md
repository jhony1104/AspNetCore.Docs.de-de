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
<a name="signalr-scaleout-with-sql-server-signalr-1x"></a><span data-ttu-id="10509-102">Horizontale Skalierung in SignalR mit SQLServer (SignalR 1.x)</span><span class="sxs-lookup"><span data-stu-id="10509-102">SignalR Scaleout with SQL Server (SignalR 1.x)</span></span>
====================
<span data-ttu-id="10509-103">durch [Mike Wasson](https://github.com/MikeWasson), [Patrick Fletcher](https://github.com/pfletcher)</span><span class="sxs-lookup"><span data-stu-id="10509-103">by [Mike Wasson](https://github.com/MikeWasson), [Patrick Fletcher](https://github.com/pfletcher)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

<span data-ttu-id="10509-104">In diesem Tutorial verwenden Sie SQL Server zur Verteilung von Nachrichten in einer SignalR-Anwendung, die in zwei verschiedenen IIS-Instanzen bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="10509-104">In this tutorial, you will use SQL Server to distribute messages across a SignalR application that is deployed in two separate IIS instances.</span></span> <span data-ttu-id="10509-105">Sie können auch in diesem Tutorial ausführen, auf einem einzelnen Testcomputer, aber um den vollen Effekt zu erhalten, müssen Sie die SignalR-Anwendung in zwei oder mehr Servern bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="10509-105">You can also run this tutorial on a single test machine, but to get the full effect, you need to deploy the SignalR application to two or more servers.</span></span> <span data-ttu-id="10509-106">Sie müssen auch SQL Server auf einem der Server oder auf einem separaten dedizierten Server installieren.</span><span class="sxs-lookup"><span data-stu-id="10509-106">You must also install SQL Server on one of the servers, or on a separate dedicated server.</span></span> <span data-ttu-id="10509-107">Eine weitere Möglichkeit ist das Tutorial mit virtuellen Computern in Azure ausführen.</span><span class="sxs-lookup"><span data-stu-id="10509-107">Another option is to run the tutorial using VMs on Azure.</span></span>

![](scaleout-with-sql-server/_static/image1.png)

## <a name="prerequisites"></a><span data-ttu-id="10509-108">Vorraussetzungen</span><span class="sxs-lookup"><span data-stu-id="10509-108">Prerequisites</span></span>

<span data-ttu-id="10509-109">Microsoft SQLServer 2005 oder höher.</span><span class="sxs-lookup"><span data-stu-id="10509-109">Microsoft SQL Server 2005 or later.</span></span> <span data-ttu-id="10509-110">Der Rückwand unterstützt Desktop- und Server-Editionen von SQL Server.</span><span class="sxs-lookup"><span data-stu-id="10509-110">The backplane supports both desktop and server editions of SQL Server.</span></span> <span data-ttu-id="10509-111">Es unterstützt SQL Server Compact Edition oder Azure SQL-Datenbank nicht.</span><span class="sxs-lookup"><span data-stu-id="10509-111">It does not support SQL Server Compact Edition or Azure SQL Database.</span></span> <span data-ttu-id="10509-112">(Wenn Ihre Anwendung in Azure gehostet wird, erwägen Sie die Service Bus-Rückwandplatine stattdessen.)</span><span class="sxs-lookup"><span data-stu-id="10509-112">(If your application is hosted on Azure, consider the Service Bus backplane instead.)</span></span>

## <a name="overview"></a><span data-ttu-id="10509-113">Übersicht</span><span class="sxs-lookup"><span data-stu-id="10509-113">Overview</span></span>

<span data-ttu-id="10509-114">Bevor wir mit dem ausführlichen Tutorial eingehen, sieht ein schnellen Überblick darüber, welche Aktionen Sie ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="10509-114">Before we get to the detailed tutorial, here is a quick overview of what you will do.</span></span>

1. <span data-ttu-id="10509-115">Erstellen Sie eine neue leere Datenbank ein.</span><span class="sxs-lookup"><span data-stu-id="10509-115">Create a new empty database.</span></span> <span data-ttu-id="10509-116">Der Rückwand erstellt die erforderlichen Tabellen in dieser Datenbank.</span><span class="sxs-lookup"><span data-stu-id="10509-116">The backplane will create the necessary tables in this database.</span></span>
2. <span data-ttu-id="10509-117">Fügen Sie diese NuGet-Pakete für Ihre Anwendung hinzu:</span><span class="sxs-lookup"><span data-stu-id="10509-117">Add these NuGet packages to your application:</span></span> 

    - [<span data-ttu-id="10509-118">Microsoft.AspNet.SignalR</span><span class="sxs-lookup"><span data-stu-id="10509-118">Microsoft.AspNet.SignalR</span></span>](http://nuget.org/packages/Microsoft.AspNet.SignalR)
    - [<span data-ttu-id="10509-119">Microsoft.AspNet.SignalR.SqlServer</span><span class="sxs-lookup"><span data-stu-id="10509-119">Microsoft.AspNet.SignalR.SqlServer</span></span>](http://nuget.org/packages/Microsoft.AspNet.SignalR.SqlServer)
3. <span data-ttu-id="10509-120">Erstellen einer SignalR-Anwendung.</span><span class="sxs-lookup"><span data-stu-id="10509-120">Create a SignalR application.</span></span>
4. <span data-ttu-id="10509-121">Fügen Sie den folgenden Code, der "Global.asax" So konfigurieren Sie die Rückwandplatine:</span><span class="sxs-lookup"><span data-stu-id="10509-121">Add the following code to Global.asax to configure the backplane:</span></span> 

    [!code-csharp[Main](scaleout-with-sql-server/samples/sample1.cs)]

## <a name="configure-the-database"></a><span data-ttu-id="10509-122">Konfigurieren Sie die Datenbank</span><span class="sxs-lookup"><span data-stu-id="10509-122">Configure the Database</span></span>

<span data-ttu-id="10509-123">Entscheiden Sie, ob es sich bei der Anwendung Windows-Authentifizierung oder SQL Server-Authentifizierung verwendet werden, auf die Datenbank zugreifen.</span><span class="sxs-lookup"><span data-stu-id="10509-123">Decide whether the application will use Windows authentication or SQL Server authentication to access the database.</span></span> <span data-ttu-id="10509-124">Unabhängig davon, stellen Sie sicher, dass der Datenbankbenutzer berechtigt ist, melden Sie sich, Erstellen von Schemas und Tabellen erstellen.</span><span class="sxs-lookup"><span data-stu-id="10509-124">Regardless, make sure the database user has permissions to log in, create schemas, and create tables.</span></span>

<span data-ttu-id="10509-125">Erstellen Sie eine neue Datenbank für die Rückwandplatine verwenden.</span><span class="sxs-lookup"><span data-stu-id="10509-125">Create a new database for the backplane to use.</span></span> <span data-ttu-id="10509-126">Sie können die Datenbank einen beliebigen Namen geben.</span><span class="sxs-lookup"><span data-stu-id="10509-126">You can give the database any name.</span></span> <span data-ttu-id="10509-127">Sie müssen keine Tabellen in der Datenbank zu erstellen. der Rückwand erstellt die erforderlichen Tabellen.</span><span class="sxs-lookup"><span data-stu-id="10509-127">You don't need to create any tables in the database; the backplane will create the necessary tables.</span></span>

![](scaleout-with-sql-server/_static/image2.png)

## <a name="enable-service-broker"></a><span data-ttu-id="10509-128">Aktivieren Sie Service Broker</span><span class="sxs-lookup"><span data-stu-id="10509-128">Enable Service Broker</span></span>

<span data-ttu-id="10509-129">Es wird empfohlen, um Service Broker für die Rückwandplatine-Datenbank zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="10509-129">It is recommended to enable Service Broker for the backplane database.</span></span> <span data-ttu-id="10509-130">Service Broker bietet systemeigene Unterstützung für Messaging- und Warteschlangen in SQL Server, der in der Rückwand Updates effizienter erhalten können.</span><span class="sxs-lookup"><span data-stu-id="10509-130">Service Broker provides native support for messaging and queuing in SQL Server, which lets the backplane receive updates more efficiently.</span></span> <span data-ttu-id="10509-131">(Allerdings funktioniert der Rückwand auch ohne Service Broker.)</span><span class="sxs-lookup"><span data-stu-id="10509-131">(However, the backplane also works without Service Broker.)</span></span>

<span data-ttu-id="10509-132">Um zu überprüfen, ob Service Broker aktiviert ist, Abfragen der **ist\_Broker\_aktiviert** -Spalte in der **sys.databases** -Katalogsicht angezeigt.</span><span class="sxs-lookup"><span data-stu-id="10509-132">To check whether Service Broker is enabled, query the **is\_broker\_enabled** column in the **sys.databases** catalog view.</span></span>

[!code-sql[Main](scaleout-with-sql-server/samples/sample2.sql)]

![](scaleout-with-sql-server/_static/image3.png)

<span data-ttu-id="10509-133">Verwenden Sie die folgende SQL-Abfrage, um Service Broker zu aktivieren:</span><span class="sxs-lookup"><span data-stu-id="10509-133">To enable Service Broker, use the following SQL query:</span></span>

[!code-sql[Main](scaleout-with-sql-server/samples/sample3.sql)]

> [!NOTE]
> <span data-ttu-id="10509-134">Wenn diese Abfrage angezeigt wird, ein Deadlock auftreten, stellen Sie sicher, dass keine Programme, die mit der Datenbank verbunden sind.</span><span class="sxs-lookup"><span data-stu-id="10509-134">If this query appears to deadlock, make sure there are no applications connected to the DB.</span></span>

<span data-ttu-id="10509-135">Wenn Sie die Ablaufverfolgung aktiviert haben, zeigt die ablaufverfolgungen auch, ob Service Broker aktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="10509-135">If you have enabled tracing, the traces will also show whether Service Broker is enabled.</span></span>

## <a name="create-a-signalr-application"></a><span data-ttu-id="10509-136">Erstellen einer SignalR-Anwendung</span><span class="sxs-lookup"><span data-stu-id="10509-136">Create a SignalR Application</span></span>

<span data-ttu-id="10509-137">Erstellen einer SignalR-Anwendung entweder mit diesen Lernprogrammen:</span><span class="sxs-lookup"><span data-stu-id="10509-137">Create a SignalR application by following either of these tutorials:</span></span>

- [<span data-ttu-id="10509-138">Erste Schritte mit SignalR</span><span class="sxs-lookup"><span data-stu-id="10509-138">Getting Started with SignalR</span></span>](../getting-started/tutorial-getting-started-with-signalr.md)
- [<span data-ttu-id="10509-139">Erste Schritte mit SignalR und MVC 4</span><span class="sxs-lookup"><span data-stu-id="10509-139">Getting Started with SignalR and MVC 4</span></span>](tutorial-getting-started-with-signalr-and-mvc-4.md)

<span data-ttu-id="10509-140">Als Nächstes ändern wir die Chat-Anwendung mit horizontaler Skalierung mit SQL Server zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="10509-140">Next, we'll modify the chat application to support scaleout with SQL Server.</span></span> <span data-ttu-id="10509-141">Fügen Sie zunächst das SignalR.SqlServer NuGet-Paket Ihrem Projekt ein.</span><span class="sxs-lookup"><span data-stu-id="10509-141">First, add the SignalR.SqlServer NuGet package to your project.</span></span> <span data-ttu-id="10509-142">In Visual Studio aus der **Tools** die Option **NuGet Paket-Manager**, wählen Sie **Paket-Manager Konsole**.</span><span class="sxs-lookup"><span data-stu-id="10509-142">In Visual Studio, from the **Tools** menu, select **NuGet Package Manager**, then select **Package Manager Console**.</span></span> <span data-ttu-id="10509-143">Geben Sie im Fenster Paket-Manager-Konsole den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="10509-143">In the Package Manager Console window, enter the following command:</span></span>

[!code-powershell[Main](scaleout-with-sql-server/samples/sample4.ps1)]

<span data-ttu-id="10509-144">Öffnen Sie als Nächstes die Datei "Global.asax".</span><span class="sxs-lookup"><span data-stu-id="10509-144">Next, open the Global.asax file.</span></span> <span data-ttu-id="10509-145">Fügen Sie den folgenden Code der **Anwendung\_starten** Methode:</span><span class="sxs-lookup"><span data-stu-id="10509-145">Add the following code to the **Application\_Start** method:</span></span>

[!code-csharp[Main](scaleout-with-sql-server/samples/sample5.cs)]

## <a name="deploy-and-run-the-application"></a><span data-ttu-id="10509-146">Bereitstellen und Ausführen der Anwendung</span><span class="sxs-lookup"><span data-stu-id="10509-146">Deploy and Run the Application</span></span>

<span data-ttu-id="10509-147">Bereiten Sie Ihre Windows Server-Instanzen, um die SignalR-Anwendung bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="10509-147">Prepare your Windows Server instances to deploy the SignalR application.</span></span>

<span data-ttu-id="10509-148">Fügen Sie die IIS-Rolle hinzu.</span><span class="sxs-lookup"><span data-stu-id="10509-148">Add the IIS role.</span></span> <span data-ttu-id="10509-149">Umfassen Sie "Anwendungsentwicklung"-Features, einschließlich der WebSocket-Protokoll.</span><span class="sxs-lookup"><span data-stu-id="10509-149">Include "Application Development" features, including the WebSocket Protocol.</span></span>

![](scaleout-with-sql-server/_static/image4.png)

<span data-ttu-id="10509-150">Außerdem enthalten Sie den Management-Dienst (unter "Management Tools" aufgeführt).</span><span class="sxs-lookup"><span data-stu-id="10509-150">Also include the Management Service (listed under "Management Tools").</span></span>

![](scaleout-with-sql-server/_static/image5.png)

<span data-ttu-id="10509-151">**Installieren Sie Web Deploy 3.0.**</span><span class="sxs-lookup"><span data-stu-id="10509-151">**Install Web Deploy 3.0.**</span></span> <span data-ttu-id="10509-152">Wenn Sie die IIS-Manager ausführen, werden Sie zum Installieren von Microsoft-Webplattform aufgefordert, oder Sie können [Herunterladen der Intstaller](https://go.microsoft.com/fwlink/?LinkId=255386).</span><span class="sxs-lookup"><span data-stu-id="10509-152">When you run IIS Manager, it will prompt you to install Microsoft Web Platform, or you can [download the intstaller](https://go.microsoft.com/fwlink/?LinkId=255386).</span></span> <span data-ttu-id="10509-153">Klicken Sie in den Webplattform-Installer Web Deploy suchen Sie und installieren Sie Web Deploy 3.0</span><span class="sxs-lookup"><span data-stu-id="10509-153">In the Platform Installer, search for Web Deploy and install Web Deploy 3.0</span></span>

![](scaleout-with-sql-server/_static/image6.png)

<span data-ttu-id="10509-154">Überprüfen Sie, dass die Web-Management-Dienst ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="10509-154">Check that the Web Management Service is running.</span></span> <span data-ttu-id="10509-155">Wenn dies nicht der Fall ist, starten Sie den Dienst.</span><span class="sxs-lookup"><span data-stu-id="10509-155">If not, start the service.</span></span> <span data-ttu-id="10509-156">(Wenn Sie Web-Management-Dienst in der Liste der Windows-Dienste nicht angezeigt wird, stellen Sie sicher, dass Sie den Management-Dienst installiert, wenn Sie die IIS-Rolle hinzugefügt haben.)</span><span class="sxs-lookup"><span data-stu-id="10509-156">(If you don't see Web Management Service in the list of Windows services, make sure that you installed the Management Service when you added the IIS role.)</span></span>

<span data-ttu-id="10509-157">Öffnen Sie abschließend Port 8172 für TCP.</span><span class="sxs-lookup"><span data-stu-id="10509-157">Finally, open port 8172 for TCP.</span></span> <span data-ttu-id="10509-158">Dies ist der Port, den das Web Deploy-Tool verwendet.</span><span class="sxs-lookup"><span data-stu-id="10509-158">This is the port that the Web Deploy tool uses.</span></span>

<span data-ttu-id="10509-159">Jetzt sind Sie bereit für die Bereitstellung von Visual Studio-Projekt vom Entwicklungscomputer auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="10509-159">Now you are ready to deploy the Visual Studio project from your development machine to the server.</span></span> <span data-ttu-id="10509-160">Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste in der Projektmappe, und klicken Sie auf **veröffentlichen**.</span><span class="sxs-lookup"><span data-stu-id="10509-160">In Solution Explorer, right-click the solution and click **Publish**.</span></span>

<span data-ttu-id="10509-161">Weitere Dokumentation zur Bereitstellung finden Sie unter [Einstieg in die Webbereitstellung für Visual Studio und ASP.NET](../../../whitepapers/aspnet-web-deployment-content-map.md).</span><span class="sxs-lookup"><span data-stu-id="10509-161">For more detailed documentation about web deployment, see [Web Deployment Content Map for Visual Studio and ASP.NET](../../../whitepapers/aspnet-web-deployment-content-map.md).</span></span>

<span data-ttu-id="10509-162">Wenn Sie die Anwendung auf zwei Servern bereitstellen, können Sie jede Instanz in einem separaten Browserfenster zu öffnen und sehen, dass jeder SignalR-Nachrichten von einem anderen erhält.</span><span class="sxs-lookup"><span data-stu-id="10509-162">If you deploy the application to two servers, you can open each instance in a separate browser window and see that they each receive SignalR messages from the other.</span></span> <span data-ttu-id="10509-163">(Natürlich in einer produktionsumgebung, die beiden Server hinter einem Load Balancer saß.)</span><span class="sxs-lookup"><span data-stu-id="10509-163">(Of course, in a production environment, the two servers would sit behind a load balancer.)</span></span>

![](scaleout-with-sql-server/_static/image7.png)

<span data-ttu-id="10509-164">Nachdem Sie die Anwendung ausführen, sehen Sie sich, dass SignalR automatisch Tabellen in der Datenbank erstellt wurde:</span><span class="sxs-lookup"><span data-stu-id="10509-164">After you run the application, you can see that SignalR has automatically created tables in the database:</span></span>

![](scaleout-with-sql-server/_static/image8.png)

<span data-ttu-id="10509-165">SignalR verwaltet die Tabellen.</span><span class="sxs-lookup"><span data-stu-id="10509-165">SignalR manages the tables.</span></span> <span data-ttu-id="10509-166">Solange Ihre Anwendung bereitgestellt wird, keine Zeilen löschen, ändern Sie die Tabelle und so weiter.</span><span class="sxs-lookup"><span data-stu-id="10509-166">As long as your application is deployed, don't delete rows, modify the table, and so forth.</span></span>

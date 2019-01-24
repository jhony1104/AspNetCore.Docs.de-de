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
<a name="signalr-scaleout-with-redis"></a><span data-ttu-id="30926-103">Horizontale Skalierung in SignalR mit Redis</span><span class="sxs-lookup"><span data-stu-id="30926-103">SignalR Scaleout with Redis</span></span>
====================
<span data-ttu-id="30926-104">durch [Mike Wasson](https://github.com/MikeWasson)</span><span class="sxs-lookup"><span data-stu-id="30926-104">by [Mike Wasson](https://github.com/MikeWasson)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> ## <a name="software-versions-used-in-this-topic"></a><span data-ttu-id="30926-105">In diesem Thema verwendeten Softwareversionen</span><span class="sxs-lookup"><span data-stu-id="30926-105">Software versions used in this topic</span></span>
>
>
> - [<span data-ttu-id="30926-106">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="30926-106">Visual Studio 2013</span></span>](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - <span data-ttu-id="30926-107">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="30926-107">.NET 4.5</span></span>
> - <span data-ttu-id="30926-108">SignalR-Version 2.4</span><span class="sxs-lookup"><span data-stu-id="30926-108">SignalR version 2.4</span></span>
>
>
>
> ## <a name="previous-versions-of-this-topic"></a><span data-ttu-id="30926-109">Vorherige Versionen dieses Themas</span><span class="sxs-lookup"><span data-stu-id="30926-109">Previous versions of this topic</span></span>
>
> <span data-ttu-id="30926-110">Weitere Informationen zu früheren Versionen von SignalR, finden Sie unter [ältere Versionen von SignalR](../older-versions/index.md).</span><span class="sxs-lookup"><span data-stu-id="30926-110">For information about earlier versions of SignalR, see [SignalR Older Versions](../older-versions/index.md).</span></span>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="30926-111">Fragen und Kommentare</span><span class="sxs-lookup"><span data-stu-id="30926-111">Questions and comments</span></span>
>
> <span data-ttu-id="30926-112">Lassen Sie Feedback, auf wie Ihnen in diesem Tutorial gefallen hat und was wir in den Kommentaren am unteren Rand der Seite verbessern können.</span><span class="sxs-lookup"><span data-stu-id="30926-112">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="30926-113">Wenn Sie Fragen, die nicht direkt mit dem Tutorial verknüpft sind haben, können Sie sie veröffentlichen das [ASP.NET SignalR-Forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) oder [StackOverflow.com](http://stackoverflow.com/).</span><span class="sxs-lookup"><span data-stu-id="30926-113">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>


<span data-ttu-id="30926-114">In diesem Tutorial verwenden Sie [Redis](http://redis.io/) zur Verteilung von Nachrichten in einer SignalR-Anwendung, die auf zwei separaten IIS-Instanzen bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="30926-114">In this tutorial, you will use [Redis](http://redis.io/) to distribute messages across a SignalR application that is deployed on two separate IIS instances.</span></span>

<span data-ttu-id="30926-115">Redis ist ein Schlüssel-Wert-Speicher im Arbeitsspeicher.</span><span class="sxs-lookup"><span data-stu-id="30926-115">Redis is an in-memory key-value store.</span></span> <span data-ttu-id="30926-116">Darüber hinaus unterstützt er ein messaging-System mit einem Veröffentlichen/Abonnieren-Modell.</span><span class="sxs-lookup"><span data-stu-id="30926-116">It also supports a messaging system with a publish/subscribe model.</span></span> <span data-ttu-id="30926-117">Die Redis-SignalR-Backplane verwendet das Pub/Sub-Feature, um Nachrichten an andere Server weiterzuleiten.</span><span class="sxs-lookup"><span data-stu-id="30926-117">The SignalR Redis backplane uses the pub/sub feature to forward messages to other servers.</span></span>

![](scaleout-with-redis/_static/image1.png)

<span data-ttu-id="30926-118">In diesem Tutorial verwenden Sie drei Server:</span><span class="sxs-lookup"><span data-stu-id="30926-118">For this tutorial, you will use three servers:</span></span>

- <span data-ttu-id="30926-119">Zwei Server unter Windows, das Sie zum Bereitstellen einer SignalR-Anwendung verwenden werden.</span><span class="sxs-lookup"><span data-stu-id="30926-119">Two servers running Windows, which you will use to deploy a SignalR application.</span></span>
- <span data-ttu-id="30926-120">Ein Server unter Linux, das Sie zum Ausführen von Redis verwenden.</span><span class="sxs-lookup"><span data-stu-id="30926-120">One server running Linux, which you will use to run Redis.</span></span> <span data-ttu-id="30926-121">Die Screenshots in diesem Tutorial verwendet Ubuntu 12.04 TLS.</span><span class="sxs-lookup"><span data-stu-id="30926-121">For the screenshots in this tutorial, I used Ubuntu 12.04 TLS.</span></span>

<span data-ttu-id="30926-122">Wenn Sie drei physische Servern für die Verwendung nicht haben, können Sie virtuelle Computer auf Hyper-V erstellen.</span><span class="sxs-lookup"><span data-stu-id="30926-122">If you don't have three physical servers to use, you can create VMs on Hyper-V.</span></span> <span data-ttu-id="30926-123">Eine weitere Option ist zum Erstellen von VMs in Azure.</span><span class="sxs-lookup"><span data-stu-id="30926-123">Another option is to create VMs on Azure.</span></span>

<span data-ttu-id="30926-124">Obwohl in diesem Tutorial die offizielle Redis-Implementierung verwendet wird, es gibt auch eine [Windows Port von Redis](https://github.com/MSOpenTech/redis) aus MSOpenTech.</span><span class="sxs-lookup"><span data-stu-id="30926-124">Although this tutorial uses the official Redis implementation, there is also a [Windows port of Redis](https://github.com/MSOpenTech/redis) from MSOpenTech.</span></span> <span data-ttu-id="30926-125">Setup und Konfiguration sind unterschiedlich, aber die Schritte sind identisch.</span><span class="sxs-lookup"><span data-stu-id="30926-125">Setup and configuration are different, but otherwise the steps are the same.</span></span>

> [!NOTE]
>
> <span data-ttu-id="30926-126">Horizontale Skalierung in SignalR mit Redis unterstützt Redis-Cluster nicht.</span><span class="sxs-lookup"><span data-stu-id="30926-126">SignalR scaleout with Redis does not support Redis clusters.</span></span>


## <a name="overview"></a><span data-ttu-id="30926-127">Übersicht</span><span class="sxs-lookup"><span data-stu-id="30926-127">Overview</span></span>

<span data-ttu-id="30926-128">Bevor wir mit dem ausführlichen Tutorial eingehen, sieht ein schnellen Überblick darüber, welche Aktionen Sie ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="30926-128">Before we get to the detailed tutorial, here is a quick overview of what you will do.</span></span>

1. <span data-ttu-id="30926-129">Installieren Sie Redis, und starten Sie den Redis-Server.</span><span class="sxs-lookup"><span data-stu-id="30926-129">Install Redis and start the Redis server.</span></span>
2. <span data-ttu-id="30926-130">Fügen Sie diese NuGet-Pakete für Ihre Anwendung hinzu:</span><span class="sxs-lookup"><span data-stu-id="30926-130">Add these NuGet packages to your application:</span></span>

    - [<span data-ttu-id="30926-131">Microsoft.AspNet.SignalR</span><span class="sxs-lookup"><span data-stu-id="30926-131">Microsoft.AspNet.SignalR</span></span>](http://nuget.org/packages/Microsoft.AspNet.SignalR)
    - [<span data-ttu-id="30926-132">Microsoft.AspNet.SignalR.StackExchangeRedis</span><span class="sxs-lookup"><span data-stu-id="30926-132">Microsoft.AspNet.SignalR.StackExchangeRedis</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.StackExchangeRedis)
    
3. <span data-ttu-id="30926-133">Erstellen einer SignalR-Anwendung.</span><span class="sxs-lookup"><span data-stu-id="30926-133">Create a SignalR application.</span></span>
4. <span data-ttu-id="30926-134">Fügen Sie den folgenden Code, "Startup.cs" So konfigurieren Sie die Rückwandplatine:</span><span class="sxs-lookup"><span data-stu-id="30926-134">Add the following code to Startup.cs to configure the backplane:</span></span>

    [!code-csharp[Main](scaleout-with-redis/samples/sample1.cs)]

## <a name="ubuntu-on-hyper-v"></a><span data-ttu-id="30926-135">Ubuntu in Hyper-V</span><span class="sxs-lookup"><span data-stu-id="30926-135">Ubuntu on Hyper-V</span></span>

<span data-ttu-id="30926-136">Verwenden Windows Hyper-V, können Sie problemlos eine Ubuntu-VM unter Windows Server erstellen.</span><span class="sxs-lookup"><span data-stu-id="30926-136">Using Windows Hyper-V, you can easily create an Ubuntu VM on Windows Server.</span></span>

<span data-ttu-id="30926-137">Laden Sie die Ubuntu-ISO-Datei von [ http://www.ubuntu.com ](http://www.ubuntu.com/).</span><span class="sxs-lookup"><span data-stu-id="30926-137">Download the Ubuntu ISO from [http://www.ubuntu.com](http://www.ubuntu.com/).</span></span>

<span data-ttu-id="30926-138">Fügen Sie einen neuen virtuellen Computer in Hyper-V hinzu.</span><span class="sxs-lookup"><span data-stu-id="30926-138">In Hyper-V, add a new VM.</span></span> <span data-ttu-id="30926-139">In der **virtuelle Festplatte verbinden** Schritt select **erstellen Sie eine virtuelle Festplatte**.</span><span class="sxs-lookup"><span data-stu-id="30926-139">In the **Connect Virtual Hard Disk** step, select **Create a virtual hard disk**.</span></span>

![](scaleout-with-redis/_static/image2.png)

<span data-ttu-id="30926-140">In der **Installationsoptionen** Schritt select **Imagedatei (.iso)**, klicken Sie auf **Durchsuchen**, und navigieren Sie zu die ISO für Ubuntu-Installation.</span><span class="sxs-lookup"><span data-stu-id="30926-140">In the **Installation Options** step, select **Image file (.iso)**, click **Browse**, and browse to the Ubuntu installation ISO.</span></span>

![](scaleout-with-redis/_static/image3.png)

## <a name="install-redis"></a><span data-ttu-id="30926-141">Installieren Sie Redis</span><span class="sxs-lookup"><span data-stu-id="30926-141">Install Redis</span></span>

<span data-ttu-id="30926-142">Führen Sie die Schritte unter [ http://redis.io/download ](http://redis.io/download) zum Herunterladen und Erstellen von Redis.</span><span class="sxs-lookup"><span data-stu-id="30926-142">Follow the steps at [http://redis.io/download](http://redis.io/download) to download and build Redis.</span></span>

[!code-console[Main](scaleout-with-redis/samples/sample2.cmd)]

<span data-ttu-id="30926-143">Dies erstellt die Redis-Binärdateien die `src` Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="30926-143">This builds the Redis binaries in the `src` directory.</span></span>

<span data-ttu-id="30926-144">Standardmäßig wird Redis kein Kennwort erforderlich.</span><span class="sxs-lookup"><span data-stu-id="30926-144">By default, Redis does not require a password.</span></span> <span data-ttu-id="30926-145">Bearbeiten Sie zum Festlegen eines Kennworts die `redis.conf` -Datei, die im Stammverzeichnis des Quellcodes befindet.</span><span class="sxs-lookup"><span data-stu-id="30926-145">To set a password, edit the `redis.conf` file, which is located in the root directory of the source code.</span></span> <span data-ttu-id="30926-146">(Stellen Sie eine Sicherungskopie der Datei aus, bevor Sie sie bearbeiten.) Fügen Sie die folgende Anweisung zum `redis.conf`:</span><span class="sxs-lookup"><span data-stu-id="30926-146">(Make a backup copy of the file before you edit it!) Add the following directive to `redis.conf`:</span></span>

[!code-powershell[Main](scaleout-with-redis/samples/sample3.ps1)]

<span data-ttu-id="30926-147">Starten Sie jetzt die Redis-Server:</span><span class="sxs-lookup"><span data-stu-id="30926-147">Now start the Redis server:</span></span>

[!code-css[Main](scaleout-with-redis/samples/sample4.css)]

![](scaleout-with-redis/_static/image4.png)

<span data-ttu-id="30926-148">Geöffneten Port 6379 aufheben, wird der Standardport, der Redis wird lauscht.</span><span class="sxs-lookup"><span data-stu-id="30926-148">Open port 6379, which is the default port that Redis listens on.</span></span> <span data-ttu-id="30926-149">(Sie können die Portnummer in der Konfigurationsdatei ändern.)</span><span class="sxs-lookup"><span data-stu-id="30926-149">(You can change the port number in the configuration file.)</span></span>

## <a name="create-the-signalr-application"></a><span data-ttu-id="30926-150">Erstellen Sie die SignalR-Anwendung</span><span class="sxs-lookup"><span data-stu-id="30926-150">Create the SignalR Application</span></span>

<span data-ttu-id="30926-151">Erstellen einer SignalR-Anwendung entweder mit diesen Lernprogrammen:</span><span class="sxs-lookup"><span data-stu-id="30926-151">Create a SignalR application by following either of these tutorials:</span></span>

- [<span data-ttu-id="30926-152">Erste Schritte mit SignalR 2.0</span><span class="sxs-lookup"><span data-stu-id="30926-152">Getting Started with SignalR 2.0</span></span>](../getting-started/tutorial-getting-started-with-signalr.md)
- [<span data-ttu-id="30926-153">Erste Schritte mit SignalR 2.0 und MVC 5</span><span class="sxs-lookup"><span data-stu-id="30926-153">Getting Started with SignalR 2.0 and MVC 5</span></span>](../getting-started/tutorial-getting-started-with-signalr-and-mvc.md)

<span data-ttu-id="30926-154">Als Nächstes ändern wir die Chat-Anwendung zur Unterstützung von horizontale Skalierung mit Redis.</span><span class="sxs-lookup"><span data-stu-id="30926-154">Next, we'll modify the chat application to support scaleout with Redis.</span></span> <span data-ttu-id="30926-155">Fügen Sie zunächst die `Microsoft.AspNet.SignalR.StackExchangeRedis` NuGet-Paket Ihrem Projekt.</span><span class="sxs-lookup"><span data-stu-id="30926-155">First, add the `Microsoft.AspNet.SignalR.StackExchangeRedis` NuGet package to your project.</span></span> <span data-ttu-id="30926-156">In Visual Studio aus der **Tools** die Option **NuGet Paket-Manager**, wählen Sie **Paket-Manager Konsole**.</span><span class="sxs-lookup"><span data-stu-id="30926-156">In Visual Studio, from the **Tools** menu, select **NuGet Package Manager**, then select **Package Manager Console**.</span></span> <span data-ttu-id="30926-157">Geben Sie im Fenster Paket-Manager-Konsole den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="30926-157">In the Package Manager Console window, enter the following command:</span></span>

[!code-powershell[Main](scaleout-with-redis/samples/sample5.ps1)]

<span data-ttu-id="30926-158">Öffnen Sie als Nächstes die Datei "Startup.cs".</span><span class="sxs-lookup"><span data-stu-id="30926-158">Next, open the Startup.cs file.</span></span> <span data-ttu-id="30926-159">Fügen Sie den folgenden Code der **Konfiguration** Methode:</span><span class="sxs-lookup"><span data-stu-id="30926-159">Add the following code to the **Configuration** method:</span></span>

[!code-csharp[Main](scaleout-with-redis/samples/sample6.cs)]

- <span data-ttu-id="30926-160">"Server" ist der Name des Servers, der Redis ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="30926-160">"server" is the name of the server that is running Redis.</span></span>
- <span data-ttu-id="30926-161">*Port* ist die Nummer des Ports</span><span class="sxs-lookup"><span data-stu-id="30926-161">*port* is the port number</span></span>
- <span data-ttu-id="30926-162">"Kennwort" ist das Kennwort, das Sie in der Datei redis.conf definiert.</span><span class="sxs-lookup"><span data-stu-id="30926-162">"password" is the password that you defined in the redis.conf file.</span></span>
- <span data-ttu-id="30926-163">"AppName" ist eine beliebige Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="30926-163">"AppName" is any string.</span></span> <span data-ttu-id="30926-164">SignalR erstellt einen Redis-Pub/Sub-Kanal mit diesem Namen.</span><span class="sxs-lookup"><span data-stu-id="30926-164">SignalR creates a Redis pub/sub channel with this name.</span></span>

<span data-ttu-id="30926-165">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="30926-165">For example:</span></span>

[!code-csharp[Main](scaleout-with-redis/samples/sample7.cs)]

## <a name="deploy-and-run-the-application"></a><span data-ttu-id="30926-166">Bereitstellen und Ausführen der Anwendung</span><span class="sxs-lookup"><span data-stu-id="30926-166">Deploy and Run the Application</span></span>

<span data-ttu-id="30926-167">Bereiten Sie Ihre Windows Server-Instanzen, um die SignalR-Anwendung bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="30926-167">Prepare your Windows Server instances to deploy the SignalR application.</span></span>

<span data-ttu-id="30926-168">Fügen Sie die IIS-Rolle hinzu.</span><span class="sxs-lookup"><span data-stu-id="30926-168">Add the IIS role.</span></span> <span data-ttu-id="30926-169">Umfassen Sie "Anwendungsentwicklung"-Features, einschließlich der WebSocket-Protokoll.</span><span class="sxs-lookup"><span data-stu-id="30926-169">Include "Application Development" features, including the WebSocket Protocol.</span></span>

![](scaleout-with-redis/_static/image5.png)

<span data-ttu-id="30926-170">Außerdem enthalten Sie den Management-Dienst (unter "Management Tools" aufgeführt).</span><span class="sxs-lookup"><span data-stu-id="30926-170">Also include the Management Service (listed under "Management Tools").</span></span>

![](scaleout-with-redis/_static/image6.png)

<span data-ttu-id="30926-171">**Installieren Sie Web Deploy 3.0.**</span><span class="sxs-lookup"><span data-stu-id="30926-171">**Install Web Deploy 3.0.**</span></span> <span data-ttu-id="30926-172">Wenn Sie die IIS-Manager ausführen, werden Sie zum Installieren von Microsoft-Webplattform aufgefordert, oder Sie können [Herunterladen der Intstaller](https://go.microsoft.com/fwlink/?LinkId=255386).</span><span class="sxs-lookup"><span data-stu-id="30926-172">When you run IIS Manager, it will prompt you to install Microsoft Web Platform, or you can [download the intstaller](https://go.microsoft.com/fwlink/?LinkId=255386).</span></span> <span data-ttu-id="30926-173">Klicken Sie in den Webplattform-Installer Web Deploy suchen Sie und installieren Sie Web Deploy 3.0</span><span class="sxs-lookup"><span data-stu-id="30926-173">In the Platform Installer, search for Web Deploy and install Web Deploy 3.0</span></span>

![](scaleout-with-redis/_static/image7.png)

<span data-ttu-id="30926-174">Überprüfen Sie, dass die Web-Management-Dienst ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="30926-174">Check that the Web Management Service is running.</span></span> <span data-ttu-id="30926-175">Wenn dies nicht der Fall ist, starten Sie den Dienst.</span><span class="sxs-lookup"><span data-stu-id="30926-175">If not, start the service.</span></span> <span data-ttu-id="30926-176">(Wenn Sie Web-Management-Dienst in der Liste der Windows-Dienste nicht angezeigt wird, stellen Sie sicher, dass Sie den Management-Dienst installiert, wenn Sie die IIS-Rolle hinzugefügt haben.)</span><span class="sxs-lookup"><span data-stu-id="30926-176">(If you don't see Web Management Service in the list of Windows services, make sure that you installed the Management Service when you added the IIS role.)</span></span>

<span data-ttu-id="30926-177">Standardmäßig wird TCP-Port 8172 den Web-Management-Dienst lauscht.</span><span class="sxs-lookup"><span data-stu-id="30926-177">By default, the Web Management Service listens on TCP port 8172.</span></span> <span data-ttu-id="30926-178">Erstellen Sie eine neue eingehende Regel zum Zulassen von TCP-Datenverkehr an Port 8172, in der Windows-Firewall.</span><span class="sxs-lookup"><span data-stu-id="30926-178">In Windows Firewall, create a new inbound rule to allow TCP traffic on port 8172.</span></span> <span data-ttu-id="30926-179">Weitere Informationen finden Sie unter [Firewallregeln konfigurieren](https://technet.microsoft.com/library/dd448559(WS.10).aspx).</span><span class="sxs-lookup"><span data-stu-id="30926-179">For more information, see [Configuring Firewall Rules](https://technet.microsoft.com/library/dd448559(WS.10).aspx).</span></span> <span data-ttu-id="30926-180">(Wenn Sie die VMs in Azure hosten, dies direkt im Azure-Portal möglich.</span><span class="sxs-lookup"><span data-stu-id="30926-180">(If you are hosting the VMs on Azure, you can do this directly in the Azure portal.</span></span> <span data-ttu-id="30926-181">Finden Sie unter [Einrichten von Endpunkten für einen virtuellen Computer](https://azure.microsoft.com/documentation/articles/virtual-machines-set-up-endpoints/).)</span><span class="sxs-lookup"><span data-stu-id="30926-181">See [How to Set Up Endpoints to a Virtual Machine](https://azure.microsoft.com/documentation/articles/virtual-machines-set-up-endpoints/).)</span></span>

<span data-ttu-id="30926-182">Jetzt sind Sie bereit für die Bereitstellung von Visual Studio-Projekt vom Entwicklungscomputer auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="30926-182">Now you are ready to deploy the Visual Studio project from your development machine to the server.</span></span> <span data-ttu-id="30926-183">Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste in der Projektmappe, und klicken Sie auf **veröffentlichen**.</span><span class="sxs-lookup"><span data-stu-id="30926-183">In Solution Explorer, right-click the solution and click **Publish**.</span></span>

<span data-ttu-id="30926-184">Weitere Dokumentation zur Bereitstellung finden Sie unter [Einstieg in die Webbereitstellung für Visual Studio und ASP.NET](../../../whitepapers/aspnet-web-deployment-content-map.md).</span><span class="sxs-lookup"><span data-stu-id="30926-184">For more detailed documentation about web deployment, see [Web Deployment Content Map for Visual Studio and ASP.NET](../../../whitepapers/aspnet-web-deployment-content-map.md).</span></span>

<span data-ttu-id="30926-185">Wenn Sie die Anwendung auf zwei Servern bereitstellen, können Sie jede Instanz in einem separaten Browserfenster zu öffnen und sehen, dass jeder SignalR-Nachrichten von einem anderen erhält.</span><span class="sxs-lookup"><span data-stu-id="30926-185">If you deploy the application to two servers, you can open each instance in a separate browser window and see that they each receive SignalR messages from the other.</span></span> <span data-ttu-id="30926-186">(Natürlich in einer produktionsumgebung, die beiden Server hinter einem Load Balancer saß.)</span><span class="sxs-lookup"><span data-stu-id="30926-186">(Of course, in a production environment, the two servers would sit behind a load balancer.)</span></span>

![](scaleout-with-redis/_static/image8.png)

<span data-ttu-id="30926-187">Wenn Sie wissen möchten, finden Sie unter der Nachrichten, die mit Redis, gesendet werden, können Sie die **Redis-Cli** Client, der mit Redis installiert wird.</span><span class="sxs-lookup"><span data-stu-id="30926-187">If you're curious to see the messages that are sent to Redis, you can use the **redis-cli** client, which installs with Redis.</span></span>

[!code-xml[Main](scaleout-with-redis/samples/sample8.xml)]

![](scaleout-with-redis/_static/image9.png)

---
uid: signalr/overview/older-versions/scaleout-with-redis
title: Horizontale Skalierung in SignalR mit Redis (SignalR 1.x) | Microsoft-Dokumentation
author: bradygaster
description: ''
ms.author: bradyg
ms.date: 05/01/2013
ms.assetid: 6abecf80-8ffa-41ba-b0d9-1d9edbe7687b
msc.legacyurl: /signalr/overview/older-versions/scaleout-with-redis
msc.type: authoredcontent
ms.openlocfilehash: 74294bd04d5649f2ec54e58adb744f5e30525162
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836414"
---
<a name="signalr-scaleout-with-redis-signalr-1x"></a><span data-ttu-id="3f213-102">Horizontale Skalierung in SignalR mit Redis (SignalR 1.x)</span><span class="sxs-lookup"><span data-stu-id="3f213-102">SignalR Scaleout with Redis (SignalR 1.x)</span></span>
====================
<span data-ttu-id="3f213-103">durch [Mike Wasson](https://github.com/MikeWasson), [Patrick Fletcher](https://github.com/pfletcher)</span><span class="sxs-lookup"><span data-stu-id="3f213-103">by [Mike Wasson](https://github.com/MikeWasson), [Patrick Fletcher](https://github.com/pfletcher)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

<span data-ttu-id="3f213-104">In diesem Tutorial verwenden Sie [Redis](http://redis.io/) zur Verteilung von Nachrichten in einer SignalR-Anwendung, die auf zwei separaten IIS-Instanzen bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="3f213-104">In this tutorial, you will use [Redis](http://redis.io/) to distribute messages across a SignalR application that is deployed on two separate IIS instances.</span></span>

<span data-ttu-id="3f213-105">Redis ist ein Schlüssel-Wert-Speicher im Arbeitsspeicher.</span><span class="sxs-lookup"><span data-stu-id="3f213-105">Redis is an in-memory key-value store.</span></span> <span data-ttu-id="3f213-106">Darüber hinaus unterstützt er ein messaging-System mit einem Veröffentlichen/Abonnieren-Modell.</span><span class="sxs-lookup"><span data-stu-id="3f213-106">It also supports a messaging system with a publish/subscribe model.</span></span> <span data-ttu-id="3f213-107">Die Redis-SignalR-Backplane verwendet das Pub/Sub-Feature, um Nachrichten an andere Server weiterzuleiten.</span><span class="sxs-lookup"><span data-stu-id="3f213-107">The SignalR Redis backplane uses the pub/sub feature to forward messages to other servers.</span></span>

![](scaleout-with-redis/_static/image1.png)

<span data-ttu-id="3f213-108">In diesem Tutorial verwenden Sie drei Server:</span><span class="sxs-lookup"><span data-stu-id="3f213-108">For this tutorial, you will use three servers:</span></span>

- <span data-ttu-id="3f213-109">Zwei Server unter Windows, das Sie zum Bereitstellen einer SignalR-Anwendung verwenden werden.</span><span class="sxs-lookup"><span data-stu-id="3f213-109">Two servers running Windows, which you will use to deploy a SignalR application.</span></span>
- <span data-ttu-id="3f213-110">Ein Server unter Linux, das Sie zum Ausführen von Redis verwenden.</span><span class="sxs-lookup"><span data-stu-id="3f213-110">One server running Linux, which you will use to run Redis.</span></span> <span data-ttu-id="3f213-111">Die Screenshots in diesem Tutorial verwendet Ubuntu 12.04 TLS.</span><span class="sxs-lookup"><span data-stu-id="3f213-111">For the screenshots in this tutorial, I used Ubuntu 12.04 TLS.</span></span>

<span data-ttu-id="3f213-112">Wenn Sie drei physische Servern für die Verwendung nicht haben, können Sie virtuelle Computer auf Hyper-V erstellen.</span><span class="sxs-lookup"><span data-stu-id="3f213-112">If you don't have three physical servers to use, you can create VMs on Hyper-V.</span></span> <span data-ttu-id="3f213-113">Eine weitere Option ist zum Erstellen von VMs in Azure.</span><span class="sxs-lookup"><span data-stu-id="3f213-113">Another option is to create VMs on Azure.</span></span>

<span data-ttu-id="3f213-114">Obwohl in diesem Tutorial die offizielle Redis-Implementierung verwendet wird, es gibt auch eine [Windows Port von Redis](https://github.com/MSOpenTech/redis) aus MSOpenTech.</span><span class="sxs-lookup"><span data-stu-id="3f213-114">Although this tutorial uses the official Redis implementation, there is also a [Windows port of Redis](https://github.com/MSOpenTech/redis) from MSOpenTech.</span></span> <span data-ttu-id="3f213-115">Setup und Konfiguration sind unterschiedlich, aber die Schritte sind identisch.</span><span class="sxs-lookup"><span data-stu-id="3f213-115">Setup and configuration are different, but otherwise the steps are the same.</span></span>

> [!NOTE] 
> 
> <span data-ttu-id="3f213-116">Horizontale Skalierung in SignalR mit Redis unterstützt Redis-Cluster nicht.</span><span class="sxs-lookup"><span data-stu-id="3f213-116">SignalR scaleout with Redis does not support Redis clusters.</span></span>


## <a name="overview"></a><span data-ttu-id="3f213-117">Übersicht</span><span class="sxs-lookup"><span data-stu-id="3f213-117">Overview</span></span>

<span data-ttu-id="3f213-118">Bevor wir mit dem ausführlichen Tutorial eingehen, sieht ein schnellen Überblick darüber, welche Aktionen Sie ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="3f213-118">Before we get to the detailed tutorial, here is a quick overview of what you will do.</span></span>

1. <span data-ttu-id="3f213-119">Installieren Sie Redis, und starten Sie den Redis-Server.</span><span class="sxs-lookup"><span data-stu-id="3f213-119">Install Redis and start the Redis server.</span></span>
2. <span data-ttu-id="3f213-120">Fügen Sie diese NuGet-Pakete für Ihre Anwendung hinzu:</span><span class="sxs-lookup"><span data-stu-id="3f213-120">Add these NuGet packages to your application:</span></span> 

    - [<span data-ttu-id="3f213-121">Microsoft.AspNet.SignalR</span><span class="sxs-lookup"><span data-stu-id="3f213-121">Microsoft.AspNet.SignalR</span></span>](http://nuget.org/packages/Microsoft.AspNet.SignalR)
    - [<span data-ttu-id="3f213-122">Microsoft.AspNet.SignalR.Redis</span><span class="sxs-lookup"><span data-stu-id="3f213-122">Microsoft.AspNet.SignalR.Redis</span></span>](http://nuget.org/packages/Microsoft.AspNet.SignalR.Redis)
3. <span data-ttu-id="3f213-123">Erstellen einer SignalR-Anwendung.</span><span class="sxs-lookup"><span data-stu-id="3f213-123">Create a SignalR application.</span></span>
4. <span data-ttu-id="3f213-124">Fügen Sie den folgenden Code, der "Global.asax" So konfigurieren Sie die Rückwandplatine:</span><span class="sxs-lookup"><span data-stu-id="3f213-124">Add the following code to Global.asax to configure the backplane:</span></span> 

    [!code-csharp[Main](scaleout-with-redis/samples/sample1.cs)]

## <a name="ubuntu-on-hyper-v"></a><span data-ttu-id="3f213-125">Ubuntu in Hyper-V</span><span class="sxs-lookup"><span data-stu-id="3f213-125">Ubuntu on Hyper-V</span></span>

<span data-ttu-id="3f213-126">Verwenden Windows Hyper-V, können Sie problemlos eine Ubuntu-VM unter Windows Server erstellen.</span><span class="sxs-lookup"><span data-stu-id="3f213-126">Using Windows Hyper-V, you can easily create an Ubuntu VM on Windows Server.</span></span>

<span data-ttu-id="3f213-127">Laden Sie die Ubuntu-ISO-Datei von [ http://www.ubuntu.com ](http://www.ubuntu.com/).</span><span class="sxs-lookup"><span data-stu-id="3f213-127">Download the Ubuntu ISO from [http://www.ubuntu.com](http://www.ubuntu.com/).</span></span>

<span data-ttu-id="3f213-128">Fügen Sie einen neuen virtuellen Computer in Hyper-V hinzu.</span><span class="sxs-lookup"><span data-stu-id="3f213-128">In Hyper-V, add a new VM.</span></span> <span data-ttu-id="3f213-129">In der **virtuelle Festplatte verbinden** Schritt select **erstellen Sie eine virtuelle Festplatte**.</span><span class="sxs-lookup"><span data-stu-id="3f213-129">In the **Connect Virtual Hard Disk** step, select **Create a virtual hard disk**.</span></span>

![](scaleout-with-redis/_static/image2.png)

<span data-ttu-id="3f213-130">In der **Installationsoptionen** Schritt select **Imagedatei (.iso)**, klicken Sie auf **Durchsuchen**, und navigieren Sie zu die ISO für Ubuntu-Installation.</span><span class="sxs-lookup"><span data-stu-id="3f213-130">In the **Installation Options** step, select **Image file (.iso)**, click **Browse**, and browse to the Ubuntu installation ISO.</span></span>

![](scaleout-with-redis/_static/image3.png)

## <a name="install-redis"></a><span data-ttu-id="3f213-131">Installieren Sie Redis</span><span class="sxs-lookup"><span data-stu-id="3f213-131">Install Redis</span></span>

<span data-ttu-id="3f213-132">Führen Sie die Schritte unter [ http://redis.io/download ](http://redis.io/download) zum Herunterladen und Erstellen von Redis.</span><span class="sxs-lookup"><span data-stu-id="3f213-132">Follow the steps at [http://redis.io/download](http://redis.io/download) to download and build Redis.</span></span>

[!code-console[Main](scaleout-with-redis/samples/sample2.cmd)]

<span data-ttu-id="3f213-133">Dies erstellt die Redis-Binärdateien die `src` Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="3f213-133">This builds the Redis binaries in the `src` directory.</span></span>

<span data-ttu-id="3f213-134">Standardmäßig wird Redis kein Kennwort erforderlich.</span><span class="sxs-lookup"><span data-stu-id="3f213-134">By default, Redis does not require a password.</span></span> <span data-ttu-id="3f213-135">Bearbeiten Sie zum Festlegen eines Kennworts die `redis.conf` -Datei, die im Stammverzeichnis des Quellcodes befindet.</span><span class="sxs-lookup"><span data-stu-id="3f213-135">To set a password, edit the `redis.conf` file, which is located in the root directory of the source code.</span></span> <span data-ttu-id="3f213-136">(Stellen Sie eine Sicherungskopie der Datei aus, bevor Sie sie bearbeiten.) Fügen Sie die folgende Anweisung zum `redis.conf`:</span><span class="sxs-lookup"><span data-stu-id="3f213-136">(Make a backup copy of the file before you edit it!) Add the following directive to `redis.conf`:</span></span>

[!code-powershell[Main](scaleout-with-redis/samples/sample3.ps1)]

<span data-ttu-id="3f213-137">Starten Sie jetzt die Redis-Server:</span><span class="sxs-lookup"><span data-stu-id="3f213-137">Now start the Redis server:</span></span>

[!code-css[Main](scaleout-with-redis/samples/sample4.css)]

![](scaleout-with-redis/_static/image4.png)

<span data-ttu-id="3f213-138">Geöffneten Port 6379 aufheben, wird der Standardport, der Redis wird lauscht.</span><span class="sxs-lookup"><span data-stu-id="3f213-138">Open port 6379, which is the default port that Redis listens on.</span></span> <span data-ttu-id="3f213-139">(Sie können die Portnummer in der Konfigurationsdatei ändern.)</span><span class="sxs-lookup"><span data-stu-id="3f213-139">(You can change the port number in the configuration file.)</span></span>

## <a name="create-the-signalr-application"></a><span data-ttu-id="3f213-140">Erstellen Sie die SignalR-Anwendung</span><span class="sxs-lookup"><span data-stu-id="3f213-140">Create the SignalR Application</span></span>

<span data-ttu-id="3f213-141">Erstellen einer SignalR-Anwendung entweder mit diesen Lernprogrammen:</span><span class="sxs-lookup"><span data-stu-id="3f213-141">Create a SignalR application by following either of these tutorials:</span></span>

- [<span data-ttu-id="3f213-142">Erste Schritte mit SignalR</span><span class="sxs-lookup"><span data-stu-id="3f213-142">Getting Started with SignalR</span></span>](../getting-started/tutorial-getting-started-with-signalr.md)
- [<span data-ttu-id="3f213-143">Erste Schritte mit SignalR und MVC 4</span><span class="sxs-lookup"><span data-stu-id="3f213-143">Getting Started with SignalR and MVC 4</span></span>](tutorial-getting-started-with-signalr-and-mvc-4.md)

<span data-ttu-id="3f213-144">Als Nächstes ändern wir die Chat-Anwendung zur Unterstützung von horizontale Skalierung mit Redis.</span><span class="sxs-lookup"><span data-stu-id="3f213-144">Next, we'll modify the chat application to support scaleout with Redis.</span></span> <span data-ttu-id="3f213-145">Fügen Sie zunächst das SignalR.Redis NuGet-Paket Ihrem Projekt ein.</span><span class="sxs-lookup"><span data-stu-id="3f213-145">First, add the SignalR.Redis NuGet package to your project.</span></span> <span data-ttu-id="3f213-146">In Visual Studio aus der **Tools** die Option **NuGet Paket-Manager**, wählen Sie **Paket-Manager Konsole**.</span><span class="sxs-lookup"><span data-stu-id="3f213-146">In Visual Studio, from the **Tools** menu, select **NuGet Package Manager**, then select **Package Manager Console**.</span></span> <span data-ttu-id="3f213-147">Geben Sie im Fenster Paket-Manager-Konsole den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="3f213-147">In the Package Manager Console window, enter the following command:</span></span>

[!code-powershell[Main](scaleout-with-redis/samples/sample5.ps1)]

<span data-ttu-id="3f213-148">Öffnen Sie als Nächstes die Datei "Global.asax".</span><span class="sxs-lookup"><span data-stu-id="3f213-148">Next, open the Global.asax file.</span></span> <span data-ttu-id="3f213-149">Fügen Sie den folgenden Code der **Anwendung\_starten** Methode:</span><span class="sxs-lookup"><span data-stu-id="3f213-149">Add the following code to the **Application\_Start** method:</span></span>

[!code-csharp[Main](scaleout-with-redis/samples/sample6.cs)]

- <span data-ttu-id="3f213-150">"Server" ist der Name des Servers, der Redis ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="3f213-150">"server" is the name of the server that is running Redis.</span></span>
- <span data-ttu-id="3f213-151">*Port* ist die Nummer des Ports</span><span class="sxs-lookup"><span data-stu-id="3f213-151">*port* is the port number</span></span>
- <span data-ttu-id="3f213-152">"Kennwort" ist das Kennwort, das Sie in der Datei redis.conf definiert.</span><span class="sxs-lookup"><span data-stu-id="3f213-152">"password" is the password that you defined in the redis.conf file.</span></span>
- <span data-ttu-id="3f213-153">"AppName" ist eine beliebige Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="3f213-153">"AppName" is any string.</span></span> <span data-ttu-id="3f213-154">SignalR erstellt einen Redis-Pub/Sub-Kanal mit diesem Namen.</span><span class="sxs-lookup"><span data-stu-id="3f213-154">SignalR creates a Redis pub/sub channel with this name.</span></span>

<span data-ttu-id="3f213-155">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="3f213-155">For example:</span></span>

[!code-csharp[Main](scaleout-with-redis/samples/sample7.cs)]

## <a name="deploy-and-run-the-application"></a><span data-ttu-id="3f213-156">Bereitstellen und Ausführen der Anwendung</span><span class="sxs-lookup"><span data-stu-id="3f213-156">Deploy and Run the Application</span></span>

<span data-ttu-id="3f213-157">Bereiten Sie Ihre Windows Server-Instanzen, um die SignalR-Anwendung bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="3f213-157">Prepare your Windows Server instances to deploy the SignalR application.</span></span>

<span data-ttu-id="3f213-158">Fügen Sie die IIS-Rolle hinzu.</span><span class="sxs-lookup"><span data-stu-id="3f213-158">Add the IIS role.</span></span> <span data-ttu-id="3f213-159">Umfassen Sie "Anwendungsentwicklung"-Features, einschließlich der WebSocket-Protokoll.</span><span class="sxs-lookup"><span data-stu-id="3f213-159">Include "Application Development" features, including the WebSocket Protocol.</span></span>

![](scaleout-with-redis/_static/image5.png)

<span data-ttu-id="3f213-160">Außerdem enthalten Sie den Management-Dienst (unter "Management Tools" aufgeführt).</span><span class="sxs-lookup"><span data-stu-id="3f213-160">Also include the Management Service (listed under "Management Tools").</span></span>

![](scaleout-with-redis/_static/image6.png)

<span data-ttu-id="3f213-161">**Installieren Sie Web Deploy 3.0.**</span><span class="sxs-lookup"><span data-stu-id="3f213-161">**Install Web Deploy 3.0.**</span></span> <span data-ttu-id="3f213-162">Wenn Sie die IIS-Manager ausführen, werden Sie zum Installieren von Microsoft-Webplattform aufgefordert, oder Sie können [Herunterladen der Intstaller](https://go.microsoft.com/fwlink/?LinkId=255386).</span><span class="sxs-lookup"><span data-stu-id="3f213-162">When you run IIS Manager, it will prompt you to install Microsoft Web Platform, or you can [download the intstaller](https://go.microsoft.com/fwlink/?LinkId=255386).</span></span> <span data-ttu-id="3f213-163">Klicken Sie in den Webplattform-Installer Web Deploy suchen Sie und installieren Sie Web Deploy 3.0</span><span class="sxs-lookup"><span data-stu-id="3f213-163">In the Platform Installer, search for Web Deploy and install Web Deploy 3.0</span></span>

![](scaleout-with-redis/_static/image7.png)

<span data-ttu-id="3f213-164">Überprüfen Sie, dass die Web-Management-Dienst ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="3f213-164">Check that the Web Management Service is running.</span></span> <span data-ttu-id="3f213-165">Wenn dies nicht der Fall ist, starten Sie den Dienst.</span><span class="sxs-lookup"><span data-stu-id="3f213-165">If not, start the service.</span></span> <span data-ttu-id="3f213-166">(Wenn Sie Web-Management-Dienst in der Liste der Windows-Dienste nicht angezeigt wird, stellen Sie sicher, dass Sie den Management-Dienst installiert, wenn Sie die IIS-Rolle hinzugefügt haben.)</span><span class="sxs-lookup"><span data-stu-id="3f213-166">(If you don't see Web Management Service in the list of Windows services, make sure that you installed the Management Service when you added the IIS role.)</span></span>

<span data-ttu-id="3f213-167">Standardmäßig wird TCP-Port 8172 den Web-Management-Dienst lauscht.</span><span class="sxs-lookup"><span data-stu-id="3f213-167">By default, the Web Management Service listens on TCP port 8172.</span></span> <span data-ttu-id="3f213-168">Erstellen Sie eine neue eingehende Regel zum Zulassen von TCP-Datenverkehr an Port 8172, in der Windows-Firewall.</span><span class="sxs-lookup"><span data-stu-id="3f213-168">In Windows Firewall, create a new inbound rule to allow TCP traffic on port 8172.</span></span> <span data-ttu-id="3f213-169">Weitere Informationen finden Sie unter [Firewallregeln konfigurieren](https://technet.microsoft.com/library/dd448559(WS.10).aspx).</span><span class="sxs-lookup"><span data-stu-id="3f213-169">For more information, see [Configuring Firewall Rules](https://technet.microsoft.com/library/dd448559(WS.10).aspx).</span></span> <span data-ttu-id="3f213-170">(Wenn Sie die VMs in Azure hosten, dies direkt im Azure-Portal möglich.</span><span class="sxs-lookup"><span data-stu-id="3f213-170">(If you are hosting the VMs on Azure, you can do this directly in the Azure portal.</span></span> <span data-ttu-id="3f213-171">Finden Sie unter [Einrichten von Endpunkten für einen virtuellen Computer](https://azure.microsoft.com/documentation/articles/virtual-machines-set-up-endpoints/).)</span><span class="sxs-lookup"><span data-stu-id="3f213-171">See [How to Set Up Endpoints to a Virtual Machine](https://azure.microsoft.com/documentation/articles/virtual-machines-set-up-endpoints/).)</span></span>

<span data-ttu-id="3f213-172">Jetzt sind Sie bereit für die Bereitstellung von Visual Studio-Projekt vom Entwicklungscomputer auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="3f213-172">Now you are ready to deploy the Visual Studio project from your development machine to the server.</span></span> <span data-ttu-id="3f213-173">Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste in der Projektmappe, und klicken Sie auf **veröffentlichen**.</span><span class="sxs-lookup"><span data-stu-id="3f213-173">In Solution Explorer, right-click the solution and click **Publish**.</span></span>

<span data-ttu-id="3f213-174">Weitere Dokumentation zur Bereitstellung finden Sie unter [Einstieg in die Webbereitstellung für Visual Studio und ASP.NET](../../../whitepapers/aspnet-web-deployment-content-map.md).</span><span class="sxs-lookup"><span data-stu-id="3f213-174">For more detailed documentation about web deployment, see [Web Deployment Content Map for Visual Studio and ASP.NET](../../../whitepapers/aspnet-web-deployment-content-map.md).</span></span>

<span data-ttu-id="3f213-175">Wenn Sie die Anwendung auf zwei Servern bereitstellen, können Sie jede Instanz in einem separaten Browserfenster zu öffnen und sehen, dass jeder SignalR-Nachrichten von einem anderen erhält.</span><span class="sxs-lookup"><span data-stu-id="3f213-175">If you deploy the application to two servers, you can open each instance in a separate browser window and see that they each receive SignalR messages from the other.</span></span> <span data-ttu-id="3f213-176">(Natürlich in einer produktionsumgebung, die beiden Server hinter einem Load Balancer saß.)</span><span class="sxs-lookup"><span data-stu-id="3f213-176">(Of course, in a production environment, the two servers would sit behind a load balancer.)</span></span>

![](scaleout-with-redis/_static/image8.png)

<span data-ttu-id="3f213-177">Wenn Sie wissen möchten, finden Sie unter der Nachrichten, die mit Redis, gesendet werden, können Sie die **Redis-Cli** Client, der mit Redis installiert wird.</span><span class="sxs-lookup"><span data-stu-id="3f213-177">If you're curious to see the messages that are sent to Redis, you can use the **redis-cli** client, which installs with Redis.</span></span>

[!code-xml[Main](scaleout-with-redis/samples/sample8.xml)]

![](scaleout-with-redis/_static/image9.png)

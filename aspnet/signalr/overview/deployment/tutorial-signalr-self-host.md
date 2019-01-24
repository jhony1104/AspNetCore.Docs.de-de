---
uid: signalr/overview/deployment/tutorial-signalr-self-host
title: 'Tutorial: Selfhosten von SignalR | Microsoft-Dokumentation'
author: bradygaster
description: In diesem Tutorial wird gezeigt, wie Sie einen selbst gehosteten SignalR-2-Server zu erstellen und wie Sie eine Verbindung mit einem JavaScript-Client herstellen. Softwareversionen, die in diesem Tutorial V verwendet...
ms.author: bradyg
ms.date: 06/10/2014
ms.assetid: 400db427-27af-4f2f-abf0-5486d5e024b5
msc.legacyurl: /signalr/overview/deployment/tutorial-signalr-self-host
msc.type: authoredcontent
ms.openlocfilehash: 6a6359d59a4b715e13fe2bbcef57da6d6d6294b5
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54835751"
---
<a name="tutorial-signalr-self-host"></a><span data-ttu-id="b65c4-104">Tutorial: Selfhosten von SignalR</span><span class="sxs-lookup"><span data-stu-id="b65c4-104">Tutorial: SignalR Self-Host</span></span>
====================
<span data-ttu-id="b65c4-105">durch [Patrick Fletcher](https://github.com/pfletcher)</span><span class="sxs-lookup"><span data-stu-id="b65c4-105">by [Patrick Fletcher](https://github.com/pfletcher)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

[<span data-ttu-id="b65c4-106">Abgeschlossenes Projekt herunterladen</span><span class="sxs-lookup"><span data-stu-id="b65c4-106">Download Completed Project</span></span>](http://code.msdn.microsoft.com/SignalR-Self-Host-Sample-6da0f383)

> <span data-ttu-id="b65c4-107">In diesem Tutorial wird gezeigt, wie Sie einen selbst gehosteten SignalR-2-Server zu erstellen und wie Sie eine Verbindung mit einem JavaScript-Client herstellen.</span><span class="sxs-lookup"><span data-stu-id="b65c4-107">This tutorial shows how to create a self-hosted SignalR 2 server, and how to connect to it with a JavaScript client.</span></span>
>
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="b65c4-108">Softwareversionen, die in diesem Tutorial verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="b65c4-108">Software versions used in the tutorial</span></span>
>
>
> - [<span data-ttu-id="b65c4-109">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="b65c4-109">Visual Studio 2013</span></span>](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - <span data-ttu-id="b65c4-110">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="b65c4-110">.NET 4.5</span></span>
> - <span data-ttu-id="b65c4-111">SignalR-Version 2</span><span class="sxs-lookup"><span data-stu-id="b65c4-111">SignalR version 2</span></span>
>
>
>
> ## <a name="using-visual-studio-2012-with-this-tutorial"></a><span data-ttu-id="b65c4-112">In diesem Tutorial mithilfe von Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="b65c4-112">Using Visual Studio 2012 with this tutorial</span></span>
>
>
> <span data-ttu-id="b65c4-113">Um Visual Studio 2012 mit diesem Tutorial verwenden möchten, führen Sie folgende Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="b65c4-113">To use Visual Studio 2012 with this tutorial, do the following:</span></span>
>
> - <span data-ttu-id="b65c4-114">Aktualisieren Ihrer [-Paket-Manager](http://docs.nuget.org/docs/start-here/installing-nuget) auf die neueste Version.</span><span class="sxs-lookup"><span data-stu-id="b65c4-114">Update your [Package Manager](http://docs.nuget.org/docs/start-here/installing-nuget) to the latest version.</span></span>
> - <span data-ttu-id="b65c4-115">Installieren Sie die [Webplattform-Installer](https://www.microsoft.com/web/downloads/platform.aspx).</span><span class="sxs-lookup"><span data-stu-id="b65c4-115">Install the [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx).</span></span>
> - <span data-ttu-id="b65c4-116">Suchen Sie in den Webplattform-Installer, und installieren Sie **ASP.NET und Web Tools 2013.1 für Visual Studio 2012**.</span><span class="sxs-lookup"><span data-stu-id="b65c4-116">In the Web Platform Installer, search for and install **ASP.NET and Web Tools 2013.1 for Visual Studio 2012**.</span></span> <span data-ttu-id="b65c4-117">Dadurch wird Visual Studio-Vorlagen für SignalR-Klassen wie z. B. installiert **Hub**.</span><span class="sxs-lookup"><span data-stu-id="b65c4-117">This will install Visual Studio templates for SignalR classes such as **Hub**.</span></span>
> - <span data-ttu-id="b65c4-118">Einige Vorlagen (z. B. **OWIN-Startklasse**) nicht zur Verfügung; verwenden Sie für diese stattdessen eine Klassendatei.</span><span class="sxs-lookup"><span data-stu-id="b65c4-118">Some templates (such as **OWIN Startup Class**) will not be available; for these, use a Class file instead.</span></span>
>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="b65c4-119">Fragen und Kommentare</span><span class="sxs-lookup"><span data-stu-id="b65c4-119">Questions and comments</span></span>
>
> <span data-ttu-id="b65c4-120">Lassen Sie Feedback, auf wie Ihnen in diesem Tutorial gefallen hat und was wir in den Kommentaren am unteren Rand der Seite verbessern können.</span><span class="sxs-lookup"><span data-stu-id="b65c4-120">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="b65c4-121">Wenn Sie Fragen, die nicht direkt mit dem Tutorial verknüpft sind haben, können Sie sie veröffentlichen das [ASP.NET SignalR-Forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) oder [StackOverflow.com](http://stackoverflow.com/).</span><span class="sxs-lookup"><span data-stu-id="b65c4-121">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>


## <a name="overview"></a><span data-ttu-id="b65c4-122">Übersicht</span><span class="sxs-lookup"><span data-stu-id="b65c4-122">Overview</span></span>

<span data-ttu-id="b65c4-123">Ein SignalR-Server in der Regel in einer ASP.NET-Anwendung in IIS gehostet wird, aber sie können auch selbst gehostet sein (z. B. in einer Konsolenanwendung oder eine Windows-Dienst) mithilfe der Self-Hosting-Bibliothek.</span><span class="sxs-lookup"><span data-stu-id="b65c4-123">A SignalR server is usually hosted in an ASP.NET application in IIS, but it can also be self-hosted (such as in a console application or Windows service) using the self-host library.</span></span> <span data-ttu-id="b65c4-124">Diese Bibliothek, wie alle SignalR 2 baut auf OWIN ([Open Web Interface for .NET](http://owin.org)).</span><span class="sxs-lookup"><span data-stu-id="b65c4-124">This library, like all of SignalR 2, is built on OWIN ([Open Web Interface for .NET](http://owin.org)).</span></span> <span data-ttu-id="b65c4-125">OWIN definiert eine Abstraktion zwischen Webservern für .NET und Webanwendungen.</span><span class="sxs-lookup"><span data-stu-id="b65c4-125">OWIN defines an abstraction between .NET web servers and web applications.</span></span> <span data-ttu-id="b65c4-126">OWIN entkoppelt die Webanwendung aus dem Server, wodurch OWIN ideal für das Selbsthosting einer Webanwendung in Ihrem eigenen Prozess, außerhalb von IIS.</span><span class="sxs-lookup"><span data-stu-id="b65c4-126">OWIN decouples the web application from the server, which makes OWIN ideal for self-hosting a web application in your own process, outside of IIS.</span></span>

<span data-ttu-id="b65c4-127">Gründe für die nicht in IIS hosten umfassen:</span><span class="sxs-lookup"><span data-stu-id="b65c4-127">Reasons for not hosting in IIS include:</span></span>

- <span data-ttu-id="b65c4-128">Umgebungen, in denen IIS nicht wünschenswert ist, z. B. einer vorhandenen Serverfarm ohne IIS oder verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="b65c4-128">Environments where IIS is not available or desirable, such as an existing server farm without IIS.</span></span>
- <span data-ttu-id="b65c4-129">Der Mehraufwand an Leistung von IIS muss vermieden werden.</span><span class="sxs-lookup"><span data-stu-id="b65c4-129">The performance overhead of IIS needs to be avoided.</span></span>
- <span data-ttu-id="b65c4-130">SignalR-Funktion ist eine vorhandene Anwendung hinzugefügt werden, die in einem Windows-Dienst, Azure-workerrolle oder anderer Prozess ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="b65c4-130">SignalR functionality is to be added to an exising application that runs in a Windows Service, Azure worker role, or other process.</span></span>

<span data-ttu-id="b65c4-131">Wenn eine Projektmappe als Self-Hosting zur Verbesserung der Leistung entwickelt wird, wird auch Test die Anwendung in IIS gehostet wird, um zu bestimmen, die Leistungsvorteile bei empfohlen.</span><span class="sxs-lookup"><span data-stu-id="b65c4-131">If a solution is being developed as self-host for performance reasons, it's recommended to also test the application hosted in IIS to determine the performance benefit.</span></span>

<span data-ttu-id="b65c4-132">In diesem Tutorial enthält die folgenden Abschnitte:</span><span class="sxs-lookup"><span data-stu-id="b65c4-132">This tutorial contains the following sections:</span></span>

- [<span data-ttu-id="b65c4-133">Erstellen des Servers</span><span class="sxs-lookup"><span data-stu-id="b65c4-133">Creating the server</span></span>](#server)
- [<span data-ttu-id="b65c4-134">Zugriff auf den Server mit einem JavaScript-client</span><span class="sxs-lookup"><span data-stu-id="b65c4-134">Accessing the server with a JavaScript client</span></span>](#js)

<a id="server"></a>

## <a name="creating-the-server"></a><span data-ttu-id="b65c4-135">Erstellen des Servers</span><span class="sxs-lookup"><span data-stu-id="b65c4-135">Creating the server</span></span>

<span data-ttu-id="b65c4-136">In diesem Tutorial erstellen Sie einen Server, der gehostet wird in einer Konsolenanwendung, aber der Server kann in jede Art von Prozess, z. B. eine Windows-Dienst oder die Azure-workerrolle gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="b65c4-136">In this tutorial, you'll create a server that's hosted in a console application, but the server can be hosted in any sort of process, such as a Windows service or Azure worker role.</span></span> <span data-ttu-id="b65c4-137">Beispielcode zum Hosten eines SignalR-Servers in einem Windows-Dienst finden Sie unter [Self-Hosting SignalR in einem Windows-Dienst](https://code.msdn.microsoft.com/SignalR-self-hosted-in-6ff7e6c3).</span><span class="sxs-lookup"><span data-stu-id="b65c4-137">For sample code for hosting a SignalR server in a Windows Service, see [Self-Hosting SignalR in a Windows Service](https://code.msdn.microsoft.com/SignalR-self-hosted-in-6ff7e6c3).</span></span>

1. <span data-ttu-id="b65c4-138">Öffnen Sie Visual Studio 2013 mit Administratorrechten aus.</span><span class="sxs-lookup"><span data-stu-id="b65c4-138">Open Visual Studio 2013 with administrator privileges.</span></span> <span data-ttu-id="b65c4-139">Wählen Sie **Datei**, **neues Projekt**.</span><span class="sxs-lookup"><span data-stu-id="b65c4-139">Select **File**, **New Project**.</span></span> <span data-ttu-id="b65c4-140">Wählen Sie **Windows** unter der **Visual C#-** Knoten in der **Vorlagen** Bereich, und wählen Sie die **Konsolenanwendung** Vorlage.</span><span class="sxs-lookup"><span data-stu-id="b65c4-140">Select **Windows** under the **Visual C#** node in the **Templates** pane, and select the **Console Application** template.</span></span> <span data-ttu-id="b65c4-141">Nennen Sie das neue Projekt "SignalRSelfHost", und klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="b65c4-141">Name the new project "SignalRSelfHost" and click **OK**.</span></span>

    ![](tutorial-signalr-self-host/_static/image1.png)
2. <span data-ttu-id="b65c4-142">Öffnen Sie die NuGet-Paket-Manager-Konsole dazu **Tools** > **NuGet Package Manager** > **-Paket-Manager-Konsole**.</span><span class="sxs-lookup"><span data-stu-id="b65c4-142">Open the NuGet package manager console by selecting **Tools** > **NuGet Package Manager** > **Package Manager Console**.</span></span>
3. <span data-ttu-id="b65c4-143">Geben Sie in der Paket-Manager-Konsole den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="b65c4-143">In the package manager console, enter the following command:</span></span>

    [!code-powershell[Main](tutorial-signalr-self-host/samples/sample1.ps1)]

    <span data-ttu-id="b65c4-144">Mit diesem Befehl werden dem Projekt die Bibliotheken selfhosten von SignalR 2 hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="b65c4-144">This command adds the SignalR 2 Self-Host libraries to the project.</span></span>
4. <span data-ttu-id="b65c4-145">Geben Sie in der Paket-Manager-Konsole den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="b65c4-145">In the package manager console, enter the following command:</span></span>

    [!code-powershell[Main](tutorial-signalr-self-host/samples/sample2.ps1)]

    <span data-ttu-id="b65c4-146">Dieser Befehl fügt der Microsoft.Owin.Cors-Bibliothek zum Projekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="b65c4-146">This command adds the Microsoft.Owin.Cors library to the project.</span></span> <span data-ttu-id="b65c4-147">Diese Bibliothek wird für die Unterstützung für domänenübergreifende, verwendet die für Anwendungen erforderlich ist, die SignalR und eine Webseite-Client in unterschiedlichen Domänen hosten.</span><span class="sxs-lookup"><span data-stu-id="b65c4-147">This library will be used for cross-domain support, which is required for applications that host SignalR and a web page client in different domains.</span></span> <span data-ttu-id="b65c4-148">Da Sie den SignalR-Server und der Webclient an unterschiedlichen Ports hosten, bedeutet dies, dass es sich bei diesem domänenübergreifende für die Kommunikation zwischen diesen Komponenten aktiviert werden muss.</span><span class="sxs-lookup"><span data-stu-id="b65c4-148">Since you'll be hosting the SignalR server and the web client on different ports, this means that cross-domain must be enabled for communication between these components.</span></span>
5. <span data-ttu-id="b65c4-149">Ersetzen Sie den Inhalt von Program.cs durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="b65c4-149">Replace the contents of Program.cs with the following code.</span></span>

    [!code-csharp[Main](tutorial-signalr-self-host/samples/sample3.cs)]

    <span data-ttu-id="b65c4-150">Der obige Code enthält drei Klassen:</span><span class="sxs-lookup"><span data-stu-id="b65c4-150">The above code includes three classes:</span></span>

    - <span data-ttu-id="b65c4-151">**Programm**, einschließlich der **Main** Methode, die den primären Pfad, der Ausführung zu definieren.</span><span class="sxs-lookup"><span data-stu-id="b65c4-151">**Program**, including the **Main** method defining the primary path of execution.</span></span> <span data-ttu-id="b65c4-152">Bei dieser Methode eine Webanwendung vom Typ **Start** wird gestartet, an der angegebenen URL (`http://localhost:8080`).</span><span class="sxs-lookup"><span data-stu-id="b65c4-152">In this method, a web application of type **Startup** is started at the specified URL (`http://localhost:8080`).</span></span> <span data-ttu-id="b65c4-153">Wenn Sicherheit auf dem Endpunkt erforderlich ist, kann SSL implementiert werden.</span><span class="sxs-lookup"><span data-stu-id="b65c4-153">If security is required on the endpoint, SSL can be implemented.</span></span> <span data-ttu-id="b65c4-154">Weitere Informationen finden Sie unter [How to: Konfigurieren eines Anschlusses mit einem SSL-Zertifikat](https://msdn.microsoft.com/library/ms733791.aspx) für Weitere Informationen.</span><span class="sxs-lookup"><span data-stu-id="b65c4-154">See [How to: Configure a Port with an SSL Certificate](https://msdn.microsoft.com/library/ms733791.aspx) for more information.</span></span>
    - <span data-ttu-id="b65c4-155">**Beim Start**, Klasse, die die Konfiguration für den SignalR-Server enthält (die einzige Konfiguration, die in diesem Tutorial wird verwendet, ist der Aufruf von `UseCors`), und der Aufruf von `MapSignalR`, die Routen für alle Objekte der Hub im Projekt erstellt.</span><span class="sxs-lookup"><span data-stu-id="b65c4-155">**Startup**, the class containing the configuration for the SignalR server (the only configuration this tutorial uses is the call to `UseCors`), and the call to `MapSignalR`, which creates routes for any Hub objects in the project.</span></span>
    - <span data-ttu-id="b65c4-156">**MyHub**, die SignalR-Hub-Klasse, die die Anwendung für Clients bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="b65c4-156">**MyHub**, the SignalR Hub class that the application will provide to clients.</span></span> <span data-ttu-id="b65c4-157">Diese Klasse verfügt über eine einzelne Methode, **senden**, dass Clients aufrufen werden, um eine Nachricht an alle anderen verbundenen Clients zu übertragen.</span><span class="sxs-lookup"><span data-stu-id="b65c4-157">This class has a single method, **Send**, that clients will call to broadcast a message to all other connected clients.</span></span>
6. <span data-ttu-id="b65c4-158">Kompilieren Sie die Anwendung, und führen Sie sie aus.</span><span class="sxs-lookup"><span data-stu-id="b65c4-158">Compile and run the application.</span></span> <span data-ttu-id="b65c4-159">Die Adresse, die der Server ausgeführt wird, sollte in einem Konsolenfenster angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b65c4-159">The address that the server is running should show in a console window.</span></span>

    ![](tutorial-signalr-self-host/_static/image2.png)
7. <span data-ttu-id="b65c4-160">Wenn die Ausführung mit der Ausnahme fehlschlägt `System.Reflection.TargetInvocationException was unhandled`, Sie müssen Visual Studio mit Administratorrechten neu zu starten.</span><span class="sxs-lookup"><span data-stu-id="b65c4-160">If execution fails with the exception `System.Reflection.TargetInvocationException was unhandled`, you will need to restart Visual Studio with administrator privileges.</span></span>
8. <span data-ttu-id="b65c4-161">Beenden Sie die Anwendung vor dem Fortfahren mit dem nächsten Abschnitt.</span><span class="sxs-lookup"><span data-stu-id="b65c4-161">Stop the application before proceeding to the next section.</span></span>

<a id="js"></a>

## <a name="accessing-the-server-with-a-javascript-client"></a><span data-ttu-id="b65c4-162">Zugriff auf den Server mit einem JavaScript-client</span><span class="sxs-lookup"><span data-stu-id="b65c4-162">Accessing the server with a JavaScript client</span></span>

<span data-ttu-id="b65c4-163">In diesem Abschnitt verwenden Sie den gleichen JavaScript-Client aus der [Tutorials: Erste Schritte](../getting-started/tutorial-getting-started-with-signalr.md).</span><span class="sxs-lookup"><span data-stu-id="b65c4-163">In this section, you'll use the same JavaScript client from the [Getting Started tutorial](../getting-started/tutorial-getting-started-with-signalr.md).</span></span> <span data-ttu-id="b65c4-164">Wir stellen nur eine Änderung an den Client, um die Hub-URL explizit zu definieren.</span><span class="sxs-lookup"><span data-stu-id="b65c4-164">We'll only make one modification to the client, which is to explicitly define the hub URL.</span></span> <span data-ttu-id="b65c4-165">Mit einer selbst gehosteten Anwendung die Server unbedingt auf die gleiche Adresse wie der Verbindungs-URL (aufgrund von Reverseproxys und Load balancer), also die URL muss explizit definiert werden möglicherweise nicht.</span><span class="sxs-lookup"><span data-stu-id="b65c4-165">With a self-hosted application, the server may not necessarily be at the same address as the connection URL (due to reverse proxies and load balancers), so the URL needs to be defined explicitly.</span></span>

1. <span data-ttu-id="b65c4-166">In **Projektmappen-Explorer**mit der rechten Maustaste auf die Projektmappe, und wählen Sie **hinzufügen**, **neues Projekt**.</span><span class="sxs-lookup"><span data-stu-id="b65c4-166">In **Solution Explorer**, right-click on the solution and select **Add**, **New Project**.</span></span> <span data-ttu-id="b65c4-167">Wählen Sie die **Web** Knoten, und wählen die **ASP.NET-Webanwendung** Vorlage.</span><span class="sxs-lookup"><span data-stu-id="b65c4-167">Select the **Web** node, and select the **ASP.NET Web Application** template.</span></span> <span data-ttu-id="b65c4-168">Nennen Sie das Projekt "JavascriptClient", und klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="b65c4-168">Name the project "JavascriptClient" and click **OK**.</span></span>

    ![](tutorial-signalr-self-host/_static/image3.png)
2. <span data-ttu-id="b65c4-169">Wählen Sie die **leere** -Vorlage und die übrigen Optionen deaktiviert lassen.</span><span class="sxs-lookup"><span data-stu-id="b65c4-169">Select the **Empty** template, and leave the remaining options unselected.</span></span> <span data-ttu-id="b65c4-170">Wählen Sie **erstellen Projekt**.</span><span class="sxs-lookup"><span data-stu-id="b65c4-170">Select **Create Project**.</span></span>

    ![](tutorial-signalr-self-host/_static/image4.png)
3. <span data-ttu-id="b65c4-171">In der Konsole des Paket-Manager, wählen Sie das Projekt "JavascriptClient" in der **Standardprojekt** Dropdown-Liste, und führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="b65c4-171">In the package manager console, select the "JavascriptClient" project in the **Default project** drop-down, and execute the following command:</span></span>

    [!code-powershell[Main](tutorial-signalr-self-host/samples/sample4.ps1)]

    <span data-ttu-id="b65c4-172">Dieser Befehl installiert die SignalR und JQuery-Bibliotheken, die Sie benötigen, auf dem Client.</span><span class="sxs-lookup"><span data-stu-id="b65c4-172">This command installs the SignalR and JQuery libraries that you'll need in the client.</span></span>
4. <span data-ttu-id="b65c4-173">Mit der rechten Maustaste auf Ihr Projekt, und wählen Sie **hinzufügen**, **neues Element**.</span><span class="sxs-lookup"><span data-stu-id="b65c4-173">Right-click on your project and select **Add**, **New Item**.</span></span> <span data-ttu-id="b65c4-174">Wählen Sie die **Web** Knoten, und wählen Sie HTML-Seite.</span><span class="sxs-lookup"><span data-stu-id="b65c4-174">Select the **Web** node, and select HTML Page.</span></span> <span data-ttu-id="b65c4-175">Nennen Sie die Seite **"default.HTML"**.</span><span class="sxs-lookup"><span data-stu-id="b65c4-175">Name the page **Default.html**.</span></span>

    ![](tutorial-signalr-self-host/_static/image5.png)
5. <span data-ttu-id="b65c4-176">Ersetzen Sie den Inhalt der neuen HTML-Seite mit den folgenden Code ein.</span><span class="sxs-lookup"><span data-stu-id="b65c4-176">Replace the contents of the new HTML page with the following code.</span></span> <span data-ttu-id="b65c4-177">Stellen Sie sicher, dass die Skriptverweise hier die Skripts im Ordner "Scripts" des Projekts übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="b65c4-177">Verify that the script references here match the scripts in the Scripts folder of the project.</span></span>

    [!code-html[Main](tutorial-signalr-self-host/samples/sample5.html?highlight=31-32)]

    <span data-ttu-id="b65c4-178">Der folgende Code (im obigen Codebeispiel hervorgehoben), ist das Hinzufügen, die Sie an den Client in diesem Erste-Schritte-Tutorial (zusätzlich zum Aktualisieren des Codes auf SignalR-Betaversion 2) verwendet.</span><span class="sxs-lookup"><span data-stu-id="b65c4-178">The following code (highlighted in the code sample above) is the addition that you've made to the client used in the Getting Stared tutorial (in addition to upgrading the code to SignalR version 2 beta).</span></span> <span data-ttu-id="b65c4-179">Diese Codezeile wird explizit die Basis-URL für SignalR, auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="b65c4-179">This line of code explicitly sets the base connection URL for SignalR on the server.</span></span>

    [!code-javascript[Main](tutorial-signalr-self-host/samples/sample6.js)]
6. <span data-ttu-id="b65c4-180">Mit der rechten Maustaste auf die Projektmappe, und wählen **Startprojekte festlegen...** . Wählen Sie die **mehrere Startprojekte** Optionsfeld aus, und legen Sie beide Projekte **Aktion** zu **starten**.</span><span class="sxs-lookup"><span data-stu-id="b65c4-180">Right-click on the solution, and select **Set Startup Projects...**. Select the **Multiple startup projects** radio button, and set both projects' **Action** to **Start**.</span></span>

    ![](tutorial-signalr-self-host/_static/image6.png)
7. <span data-ttu-id="b65c4-181">Mit der rechten Maustaste auf "Default.html", und wählen Sie **als Startseite festlegen**.</span><span class="sxs-lookup"><span data-stu-id="b65c4-181">Right-click on "Default.html" and select **Set As Start Page**.</span></span>
8. <span data-ttu-id="b65c4-182">Führen Sie die Anwendung aus.</span><span class="sxs-lookup"><span data-stu-id="b65c4-182">Run the application.</span></span> <span data-ttu-id="b65c4-183">Die Seite "und" Server werden gestartet.</span><span class="sxs-lookup"><span data-stu-id="b65c4-183">The server and page will launch.</span></span> <span data-ttu-id="b65c4-184">Möglicherweise müssen Sie die Webseite neu zu laden (oder wählen Sie **Weiter** im Debugger) Wenn die Seite geladen wird, bevor der Server gestartet wurde.</span><span class="sxs-lookup"><span data-stu-id="b65c4-184">You may need to reload the web page (or select **Continue** in the debugger) if the page loads before the server is started.</span></span>
9. <span data-ttu-id="b65c4-185">Geben Sie einen Benutzernamen, wenn Sie aufgefordert werden, im Browser.</span><span class="sxs-lookup"><span data-stu-id="b65c4-185">In the browser, provide a username when prompted.</span></span> <span data-ttu-id="b65c4-186">Kopieren Sie die URL der Seite in einer anderen Browserregisterkarte oder Fenster, und geben Sie einen anderen Benutzernamen.</span><span class="sxs-lookup"><span data-stu-id="b65c4-186">Copy the page's URL into another browser tab or window and provide a different username.</span></span> <span data-ttu-id="b65c4-187">Sie werden zum Senden von Nachrichten aus einem Browser zum anderen, wie in den Tutorials: Erste Schritte können.</span><span class="sxs-lookup"><span data-stu-id="b65c4-187">You will be able to send messages from one browser pane to the other, as in the Getting Started tutorial.</span></span>

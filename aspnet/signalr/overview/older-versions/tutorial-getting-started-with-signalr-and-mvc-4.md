---
uid: signalr/overview/older-versions/tutorial-getting-started-with-signalr-and-mvc-4
title: 'Tutorial: Erste Schritte mit SignalR 1.x und MVC 4 | Microsoft-Dokumentation'
author: bradygaster
description: Verwenden Sie SignalR für ASP.NET und ASP.NET MVC 4, um eine chatanwendung mit Echtzeitfunktionalität erstellen.
ms.author: bradyg
ms.date: 03/29/2013
ms.assetid: eeef9f73-6de3-49f9-b50b-9af22108f2ce
msc.legacyurl: /signalr/overview/older-versions/tutorial-getting-started-with-signalr-and-mvc-4
msc.type: authoredcontent
ms.openlocfilehash: dd55ca22004b7e3899f6a8789494c842b984787f
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837792"
---
<a name="tutorial-getting-started-with-signalr-1x-and-mvc-4"></a><span data-ttu-id="2887c-103">Tutorial: Erste Schritte mit SignalR 1.x und MVC 4</span><span class="sxs-lookup"><span data-stu-id="2887c-103">Tutorial: Getting Started with SignalR 1.x and MVC 4</span></span>
====================
<span data-ttu-id="2887c-104">durch [Patrick Fletcher](https://github.com/pfletcher), [Tim Teebken](https://github.com/timlt)</span><span class="sxs-lookup"><span data-stu-id="2887c-104">by [Patrick Fletcher](https://github.com/pfletcher), [Tim Teebken](https://github.com/timlt)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="2887c-105">In diesem Tutorial wird gezeigt, wie mit ASP.NET SignalR eine chatanwendung mit Echtzeitfunktionalität erstellen.</span><span class="sxs-lookup"><span data-stu-id="2887c-105">This tutorial shows how to use ASP.NET SignalR to create a real-time chat application.</span></span> <span data-ttu-id="2887c-106">Sie SignalR eine MVC 4-Anwendung hinzufügen und erstellen Sie eine Chat-Ansicht zum Senden und Meldungen angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="2887c-106">You will add SignalR to an MVC 4 application and create a chat view to send and display messages.</span></span>


## <a name="overview"></a><span data-ttu-id="2887c-107">Übersicht</span><span class="sxs-lookup"><span data-stu-id="2887c-107">Overview</span></span>

<span data-ttu-id="2887c-108">Dieses Tutorial führt Sie in Echtzeit-Webanwendungsentwicklung mit ASP.NET SignalR und ASP.NET MVC 4.</span><span class="sxs-lookup"><span data-stu-id="2887c-108">This tutorial introduces you to real-time web application development with ASP.NET SignalR and ASP.NET MVC 4.</span></span> <span data-ttu-id="2887c-109">Das Lernprogramm verwendet den gleichen Code der Chat-Anwendung wie den [SignalR, erste Schritte-Lernprogramm](tutorial-getting-started-with-signalr.md), aber zeigt, wie sie eine MVC 4-Anwendung, die basierend auf der Internet-Vorlage hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="2887c-109">The tutorial uses the same chat application code as the [SignalR Getting Started tutorial](tutorial-getting-started-with-signalr.md), but shows how to add it to an MVC 4 application based on the Internet template.</span></span>

<span data-ttu-id="2887c-110">In diesem Thema lernen Sie die folgenden Entwicklungsaufgaben von SignalR:</span><span class="sxs-lookup"><span data-stu-id="2887c-110">In this topic you will learn the following SignalR development tasks:</span></span>

- <span data-ttu-id="2887c-111">Eine MVC 4-Anwendung wird die SignalR-Bibliothek hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="2887c-111">Adding the SignalR library to an MVC 4 application.</span></span>
- <span data-ttu-id="2887c-112">Erstellen einer hubklasse, um Inhalt an Clients zu verteilen.</span><span class="sxs-lookup"><span data-stu-id="2887c-112">Creating a hub class to push content to clients.</span></span>
- <span data-ttu-id="2887c-113">Verwenden die SignalR-jQuery-Bibliothek auf einer Webseite zum Senden von Nachrichten und Updates vom Hub angezeigt.</span><span class="sxs-lookup"><span data-stu-id="2887c-113">Using the SignalR jQuery library in a web page to send messages and display updates from the hub.</span></span>

<span data-ttu-id="2887c-114">Der folgende Screenshot zeigt die abgeschlossene Chat-Anwendung, die in einem Browser ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="2887c-114">The following screen shot shows the completed chat application running in a browser.</span></span>

![Chatinstanzen](tutorial-getting-started-with-signalr-and-mvc-4/_static/image2.png)

<span data-ttu-id="2887c-116">Abschnitte:</span><span class="sxs-lookup"><span data-stu-id="2887c-116">Sections:</span></span>

- [<span data-ttu-id="2887c-117">Einrichten des Projekts</span><span class="sxs-lookup"><span data-stu-id="2887c-117">Set up the Project</span></span>](#setup)
- [<span data-ttu-id="2887c-118">Ausführen des Beispiels</span><span class="sxs-lookup"><span data-stu-id="2887c-118">Run the Sample</span></span>](#run)
- [<span data-ttu-id="2887c-119">Überprüfen Sie den Code</span><span class="sxs-lookup"><span data-stu-id="2887c-119">Examine the Code</span></span>](#code)
- [<span data-ttu-id="2887c-120">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="2887c-120">Next Steps</span></span>](#next)

<a id="setup"></a>

## <a name="set-up-the-project"></a><span data-ttu-id="2887c-121">Einrichten des Projekts</span><span class="sxs-lookup"><span data-stu-id="2887c-121">Set up the Project</span></span>

<span data-ttu-id="2887c-122">Erforderliche Komponenten:</span><span class="sxs-lookup"><span data-stu-id="2887c-122">Prerequisites:</span></span>

- <span data-ttu-id="2887c-123">Visual Studio 2010 SP1, Visual Studio 2012 oder Visual Studio 2012 Express.</span><span class="sxs-lookup"><span data-stu-id="2887c-123">Visual Studio 2010 SP1, Visual Studio 2012, or Visual Studio 2012 Express.</span></span> <span data-ttu-id="2887c-124">Wenn Sie nicht über Visual Studio verfügen, finden Sie unter [ASP.NET-Downloads](https://www.asp.net/downloads) um die kostenlose Visual Studio 2012 Express Entwicklungstool zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="2887c-124">If you do not have Visual Studio, see [ASP.NET Downloads](https://www.asp.net/downloads) to get the free Visual Studio 2012 Express Development Tool.</span></span>
- <span data-ttu-id="2887c-125">Für Visual Studio 2010 installieren [ASP.NET MVC 4](https://www.microsoft.com/download/details.aspx?id=30683).</span><span class="sxs-lookup"><span data-stu-id="2887c-125">For Visual Studio 2010, install [ASP.NET MVC 4](https://www.microsoft.com/download/details.aspx?id=30683).</span></span>

<span data-ttu-id="2887c-126">In diesem Abschnitt veranschaulicht das Erstellen einer ASP.NET MVC 4-Anwendung, die SignalR-Bibliothek hinzufügen und erstellen Sie die chatanwendung.</span><span class="sxs-lookup"><span data-stu-id="2887c-126">This section shows how to create an ASP.NET MVC 4 application, add the SignalR library, and create the chat application.</span></span>

1. 1. <span data-ttu-id="2887c-127">Erstellen Sie in Visual Studio eine ASP.NET MVC 4-Anwendung, nennen Sie sie SignalRChat, und klicken Sie auf OK.</span><span class="sxs-lookup"><span data-stu-id="2887c-127">In Visual Studio create an ASP.NET MVC 4 application, name it SignalRChat, and click OK.</span></span>

        > [!NOTE]
        > <span data-ttu-id="2887c-128">Wählen Sie in Visual Studio 2010 **.NET Framework 4** im Framework-Version Dropdown-Steuerelement.</span><span class="sxs-lookup"><span data-stu-id="2887c-128">In VS 2010, select **.NET Framework 4** in the Framework version dropdown control.</span></span> <span data-ttu-id="2887c-129">SignalR-Code, die auf .NET Framework-Versionen 4 und 4.5 ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="2887c-129">SignalR code runs on .NET Framework versions 4 and 4.5.</span></span>

        ![Erstellen von Mvc-Webanwendung](tutorial-getting-started-with-signalr-and-mvc-4/_static/image3.png)
      2. <span data-ttu-id="2887c-131">Wählen Sie die Internet Application-Vorlage, deaktivieren Sie die Option zum **erstellen ein Komponententestprojekts**, und klicken Sie auf OK.</span><span class="sxs-lookup"><span data-stu-id="2887c-131">Select the Internet Application template, clear the option to **Create a unit test project**, and click OK.</span></span>

         ![Mvc-IIS-Site erstellen](tutorial-getting-started-with-signalr-and-mvc-4/_static/image4.png)
      3. <span data-ttu-id="2887c-133">Öffnen der **Tools > NuGet Paket-Manager > Paket-Manager Console** und führen folgenden Befehl aus.</span><span class="sxs-lookup"><span data-stu-id="2887c-133">Open the **Tools > NuGet Package Manager > Package Manager Console** and run the following command.</span></span> <span data-ttu-id="2887c-134">Dieser Schritt fügt dem Projekt hinzu einen Satz von Skriptdateien und Assemblyverweise, die SignalR-Funktionalität zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="2887c-134">This step adds to the project a set of script files and assembly references that enable SignalR functionality.</span></span>

         `install-package Microsoft.AspNet.SignalR -Version 1.1.3`
      4. <span data-ttu-id="2887c-135">In **Projektmappen-Explorer** erweitern Sie den Ordner "Scripts".</span><span class="sxs-lookup"><span data-stu-id="2887c-135">In **Solution Explorer** expand the Scripts folder.</span></span> <span data-ttu-id="2887c-136">Beachten Sie, dass die Skriptbibliotheken für SignalR zum Projekt hinzugefügt wurden.</span><span class="sxs-lookup"><span data-stu-id="2887c-136">Note that script libraries for SignalR have been added to the project.</span></span>

         ![Verweise auf die Typbibliothek](tutorial-getting-started-with-signalr-and-mvc-4/_static/image6.png)
      5. <span data-ttu-id="2887c-138">In **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, wählen Sie **hinzufügen | Neuer Ordner**, und fügen Sie einen neuen Ordner namens **Hubs**.</span><span class="sxs-lookup"><span data-stu-id="2887c-138">In **Solution Explorer**, right-click the project, select **Add | New Folder**, and add a new folder named **Hubs**.</span></span>
      6. <span data-ttu-id="2887c-139">Mit der rechten Maustaste die **Hubs** Ordner, klicken Sie auf **hinzufügen | Klasse**, und erstellen Sie eine neue c#-Klasse, mit dem Namen **ChatHub.cs**.</span><span class="sxs-lookup"><span data-stu-id="2887c-139">Right-click the **Hubs** folder, click **Add | Class**, and create a new C# class named **ChatHub.cs**.</span></span> <span data-ttu-id="2887c-140">Sie verwenden diese Klasse als einen SignalR-Hub, die Nachrichten an alle Clients sendet.</span><span class="sxs-lookup"><span data-stu-id="2887c-140">You will use this class as a SignalR server hub that sends messages to all clients.</span></span>

> [!NOTE]
> <span data-ttu-id="2887c-141">Wenn Sie Visual Studio 2012 und installiert die [Update für ASP.NET und Web Tools 2012.2](../../../visual-studio/overview/2012/aspnet-and-web-tools-20122-release-notes-rtw.md#_Installation), können Sie die neue SignalR-Elementvorlage, um die hubklasse zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="2887c-141">If you use Visual Studio 2012 and have installed the [ASP.NET and Web Tools 2012.2 update](../../../visual-studio/overview/2012/aspnet-and-web-tools-20122-release-notes-rtw.md#_Installation), you can use the new SignalR item template to create the hub class.</span></span> <span data-ttu-id="2887c-142">Zu diesem Zweck Maustaste der **Hubs** Ordner, klicken Sie auf **hinzufügen | Neues Element**Option **SignalR-Hubklasse (v1)**, und nennen Sie die Klasse **ChatHub.cs**.</span><span class="sxs-lookup"><span data-stu-id="2887c-142">To do that, right-click the **Hubs** folder, click **Add | New Item**, select **SignalR Hub Class (v1)**, and name the class **ChatHub.cs**.</span></span>


1. <span data-ttu-id="2887c-143">Ersetzen Sie den Code in die **ChatHub** Klasse durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="2887c-143">Replace the code in the **ChatHub** class with the following code.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample1.cs)]
2. <span data-ttu-id="2887c-144">Öffnen der **"Global.asax"** -Datei für das Projekt, und fügen Sie einen Aufruf der Methode `RouteTable.Routes.MapHubs();` als erste Zeile des Codes in der `Application_Start` Methode.</span><span class="sxs-lookup"><span data-stu-id="2887c-144">Open the **Global.asax** file for the project, and add a call to the method `RouteTable.Routes.MapHubs();` as the first line of code in the `Application_Start` method.</span></span> <span data-ttu-id="2887c-145">Dieser Code registriert die Standardroute für SignalR-Hubs und muss aufgerufen werden, bevor Sie anderen Routen registrieren.</span><span class="sxs-lookup"><span data-stu-id="2887c-145">This code registers the default route for SignalR hubs and must be called before you register any other routes.</span></span> <span data-ttu-id="2887c-146">Die abgeschlossene `Application_Start` Methode sieht wie im folgenden Beispiel aus.</span><span class="sxs-lookup"><span data-stu-id="2887c-146">The completed `Application_Start` method looks like the following example.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample2.cs)]
3. <span data-ttu-id="2887c-147">Bearbeiten der `HomeController` Klasse finden Sie im **Controllers/HomeController.cs** und fügen Sie die folgende Methode der Klasse.</span><span class="sxs-lookup"><span data-stu-id="2887c-147">Edit the `HomeController` class found in **Controllers/HomeController.cs** and add the following method to the class.</span></span> <span data-ttu-id="2887c-148">Diese Methode gibt die **Chat** anzeigen, die Sie in einem späteren Schritt erstellen.</span><span class="sxs-lookup"><span data-stu-id="2887c-148">This method returns the **Chat** view that you will create in a later step.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample3.cs)]
4. <span data-ttu-id="2887c-149">Mit der rechten Maustaste innerhalb der `Chat` Methode, die Sie gerade erstellt, und klicken Sie auf **Ansicht hinzufügen** um eine neue Datei zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="2887c-149">Right-click within the `Chat` method you just created, and click **Add View** to create a new view file.</span></span>
5. <span data-ttu-id="2887c-150">In der **Ansicht hinzufügen** Dialogfeld stellen Sie sicher, dass Sie dieses Kontrollkästchen aktiviert ist **mithilfe eine Seite Layout- oder Masterseite** (deaktivieren Sie die anderen Kontrollkästchen), und klicken Sie dann auf **hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="2887c-150">In the **Add View** dialog, make sure the check box is selected to **Use a layout or master page** (clear the other check boxes), and then click **Add**.</span></span>

    ![Hinzufügen einer Ansicht](tutorial-getting-started-with-signalr-and-mvc-4/_static/image8.png)
6. <span data-ttu-id="2887c-152">Bearbeiten Sie die neue Ansichtsdatei mit dem Namen **Chat.cshtml**.</span><span class="sxs-lookup"><span data-stu-id="2887c-152">Edit the new view file named **Chat.cshtml**.</span></span> <span data-ttu-id="2887c-153">Nach der &lt;h2&gt; markieren, fügen Sie die folgende &lt;Div&gt; Abschnitt und `@section scripts` Codeblock in die Seite.</span><span class="sxs-lookup"><span data-stu-id="2887c-153">After the &lt;h2&gt; tag, paste the following &lt;div&gt; section and `@section scripts` code block into the page.</span></span> <span data-ttu-id="2887c-154">Dieses Skript kann die Seite Chatnachrichten zu senden und Nachrichten vom Server anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="2887c-154">This script enables the page to send chat messages and display messages from the server.</span></span> <span data-ttu-id="2887c-155">Der vollständige Code für die Chat-Sicht wird in den folgenden Codeblock angezeigt.</span><span class="sxs-lookup"><span data-stu-id="2887c-155">The complete code for the chat view appears in the following code block.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="2887c-156">Wenn Sie SignalR und anderen JavaScript-Bibliotheken zu Ihrem Visual Studio-Projekt hinzufügen, kann der Paket-Manager-Versionen der Skripts installiert, jünger als die in diesem Thema aufgeführten Versionen sind.</span><span class="sxs-lookup"><span data-stu-id="2887c-156">When you add SignalR and other script libraries to your Visual Studio project, the Package Manager might install versions of the scripts that are more recent than the versions shown in this topic.</span></span> <span data-ttu-id="2887c-157">Stellen Sie sicher, dass die Skriptverweise im Code die Skriptbibliotheken, die in Ihrem Projekt installiert die Versionen übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="2887c-157">Make sure that the script references in your code match the versions of the script libraries installed in your project.</span></span>

    [!code-cshtml[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample4.cshtml)]
7. <span data-ttu-id="2887c-158">**Speichern Sie alle** für das Projekt.</span><span class="sxs-lookup"><span data-stu-id="2887c-158">**Save All** for the project.</span></span>

<a id="run"></a>

## <a name="run-the-sample"></a><span data-ttu-id="2887c-159">Ausführen des Beispiels</span><span class="sxs-lookup"><span data-stu-id="2887c-159">Run the Sample</span></span>

1. <span data-ttu-id="2887c-160">Drücken Sie F5, um das Projekt im Debugmodus auszuführen.</span><span class="sxs-lookup"><span data-stu-id="2887c-160">Press F5 to run the project in debug mode.</span></span>
2. <span data-ttu-id="2887c-161">Fügen Sie in der Adresszeile des Browsers **/Home/Chat** an die URL der Standardseite für das Projekt.</span><span class="sxs-lookup"><span data-stu-id="2887c-161">In the browser address line, append **/home/chat** to the URL of the default page for the project.</span></span> <span data-ttu-id="2887c-162">Die Chat-Seite lädt in eine Instanz eines Webbrowsers und aufforderungen für einen Benutzernamen ein.</span><span class="sxs-lookup"><span data-stu-id="2887c-162">The Chat page loads in a browser instance and prompts for a user name.</span></span>

    ![Benutzername eingeben](tutorial-getting-started-with-signalr-and-mvc-4/_static/image9.png)
3. <span data-ttu-id="2887c-164">Geben Sie einen Benutzernamen ein.</span><span class="sxs-lookup"><span data-stu-id="2887c-164">Enter a user name.</span></span>
4. <span data-ttu-id="2887c-165">Kopieren Sie die URL aus der Adresszeile des Browsers, und öffnen Sie zwei Browserinstanzen für weitere.</span><span class="sxs-lookup"><span data-stu-id="2887c-165">Copy the URL from the address line of the browser and use it to open two more browser instances.</span></span> <span data-ttu-id="2887c-166">Geben Sie in den einzelnen Instanzen Browser einen eindeutigen Benutzernamen ein.</span><span class="sxs-lookup"><span data-stu-id="2887c-166">In each browser instance, enter a unique user name.</span></span>
5. <span data-ttu-id="2887c-167">Klicken Sie in den einzelnen Instanzen Browser Fügt einen Kommentar hinzu, und klicken Sie auf **senden**.</span><span class="sxs-lookup"><span data-stu-id="2887c-167">In each browser instance, add a comment and click **Send**.</span></span> <span data-ttu-id="2887c-168">Die Kommentare sollte in alle Browserinstanzen angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="2887c-168">The comments should display in all browser instances.</span></span>

    > [!NOTE]
    > <span data-ttu-id="2887c-169">Diese einfache chatanwendung werden den Kontext der Diskussion auf dem Server nicht verwaltet werden.</span><span class="sxs-lookup"><span data-stu-id="2887c-169">This simple chat application does not maintain the discussion context on the server.</span></span> <span data-ttu-id="2887c-170">Der Hub sendet, Kommentare, um alle aktuellen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="2887c-170">The hub broadcasts comments to all current users.</span></span> <span data-ttu-id="2887c-171">Benutzer, die im Chat später zusammenfügen sehen, dass Nachrichten ab dem Zeitpunkt hinzugefügt werden, dass sie aufgenommen werden.</span><span class="sxs-lookup"><span data-stu-id="2887c-171">Users who join the chat later will see messages added from the time they join.</span></span>
6. <span data-ttu-id="2887c-172">Der folgende Screenshot zeigt die chatanwendung, die in einem Browser ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="2887c-172">The following screen shot shows the chat application running in a browser.</span></span>

    ![Chat-Browser](tutorial-getting-started-with-signalr-and-mvc-4/_static/image11.png)
7. <span data-ttu-id="2887c-174">In **Projektmappen-Explorer**, überprüfen Sie die **Skriptdokumente** Knoten für die ausgeführte Anwendung.</span><span class="sxs-lookup"><span data-stu-id="2887c-174">In **Solution Explorer**, inspect the **Script Documents** node for the running application.</span></span> <span data-ttu-id="2887c-175">Dieser Knoten ist sichtbar im Debugmodus, wenn Sie Internet Explorer als Browser verwenden.</span><span class="sxs-lookup"><span data-stu-id="2887c-175">This node is visible in debug mode if you are using Internet Explorer as your browser.</span></span> <span data-ttu-id="2887c-176">Es gibt eine Skriptdatei namens **Hubs** , die den SignalR-Bibliothek wird zur Laufzeit dynamisch generiert.</span><span class="sxs-lookup"><span data-stu-id="2887c-176">There is a script file named **hubs** that the SignalR library dynamically generates at runtime.</span></span> <span data-ttu-id="2887c-177">Diese Datei, verwaltet die Kommunikation zwischen der jQuery-Skripts und serverseitigen Code.</span><span class="sxs-lookup"><span data-stu-id="2887c-177">This file manages the communication between jQuery script and server-side code.</span></span> <span data-ttu-id="2887c-178">Wenn Sie einen Browser als Internet Explorer verwenden, können Sie auch zugreifen, auf den dynamischen **Hubs** Datei navigieren darin direkt, beispielsweise http://mywebsite/signalr/hubs.</span><span class="sxs-lookup"><span data-stu-id="2887c-178">If you use a browser other than Internet Explorer, you can also access the dynamic **hubs** file by browsing to it directly, for example http://mywebsite/signalr/hubs.</span></span>

    ![Generierte Hub-Skript](tutorial-getting-started-with-signalr-and-mvc-4/_static/image13.png)

<a id="code"></a>

## <a name="examine-the-code"></a><span data-ttu-id="2887c-180">Überprüfen Sie den Code</span><span class="sxs-lookup"><span data-stu-id="2887c-180">Examine the Code</span></span>

<span data-ttu-id="2887c-181">Der SignalR Chat-Anwendung zeigt zwei grundlegende Aufgaben für die SignalR-Entwicklung: Erstellen eines Hubs wie das wichtigste Coordination-Objekt, auf dem Server, und mithilfe der SignalR-jQuery-Bibliothek zum Senden und Empfangen von Nachrichten.</span><span class="sxs-lookup"><span data-stu-id="2887c-181">The SignalR chat application demonstrates two basic SignalR development tasks: creating a hub as the main coordination object on the server, and using the SignalR jQuery library to send and receive messages.</span></span>

### <a name="signalr-hubs"></a><span data-ttu-id="2887c-182">SignalR Hubs</span><span class="sxs-lookup"><span data-stu-id="2887c-182">SignalR Hubs</span></span>

<span data-ttu-id="2887c-183">Im Codebeispiel enthält die **ChatHub** Klasse leitet sich von der **Microsoft.AspNet.SignalR.Hub** Klasse.</span><span class="sxs-lookup"><span data-stu-id="2887c-183">In the code sample the **ChatHub** class derives from the **Microsoft.AspNet.SignalR.Hub** class.</span></span> <span data-ttu-id="2887c-184">Ableiten von der **Hub** Klasse ist eine gute Möglichkeit, eine SignalR-Anwendung zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="2887c-184">Deriving from the **Hub** class is a useful way to build a SignalR application.</span></span> <span data-ttu-id="2887c-185">Sie können öffentliche Methoden auf Ihrem Hub-Klasse erstellen und dann Zugriff auf diese Methoden durch einen Aufruf über die jQuery-Skripts auf einer Webseite.</span><span class="sxs-lookup"><span data-stu-id="2887c-185">You can create public methods on your hub class and then access those methods by calling them from jQuery scripts in a web page.</span></span>

<span data-ttu-id="2887c-186">Im Code Chat Clients rufen die **ChatHub.Send** Methode, um eine neue Nachricht zu senden.</span><span class="sxs-lookup"><span data-stu-id="2887c-186">In the chat code, clients call the **ChatHub.Send** method to send a new message.</span></span> <span data-ttu-id="2887c-187">Der Hub wiederum sendet die Nachricht an alle Clients durch den Aufruf **Clients.All.addNewMessageToPage**.</span><span class="sxs-lookup"><span data-stu-id="2887c-187">The hub in turn sends the message to all clients by calling **Clients.All.addNewMessageToPage**.</span></span>

<span data-ttu-id="2887c-188">Die **senden** -Methode demonstriert, mehrere Hub-Konzepte:</span><span class="sxs-lookup"><span data-stu-id="2887c-188">The **Send** method demonstrates several hub concepts :</span></span>

- <span data-ttu-id="2887c-189">Deklarieren Sie öffentliche Methoden für einen Hub, damit Clients, die sie aufrufen können.</span><span class="sxs-lookup"><span data-stu-id="2887c-189">Declare public methods on a hub so that clients can call them.</span></span>
- <span data-ttu-id="2887c-190">Verwenden der **Microsoft.AspNet.SignalR.Hub.Clients** Eigenschaft, um alle Clients zugreifen, die mit diesem Hub verbunden sind.</span><span class="sxs-lookup"><span data-stu-id="2887c-190">Use the **Microsoft.AspNet.SignalR.Hub.Clients** property to access all clients connected to this hub.</span></span>
- <span data-ttu-id="2887c-191">Aufrufen einer jQuery-Funktion auf dem Client (z. B. die `addNewMessageToPage` Funktion) zum Aktualisieren von Clients.</span><span class="sxs-lookup"><span data-stu-id="2887c-191">Call a jQuery function on the client (such as the `addNewMessageToPage` function) to update clients.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample5.cs)]

### <a name="signalr-and-jquery"></a><span data-ttu-id="2887c-192">SignalR und jQuery</span><span class="sxs-lookup"><span data-stu-id="2887c-192">SignalR and jQuery</span></span>

<span data-ttu-id="2887c-193">Die **Chat.cshtml** Ansicht im Codebeispiel zeigt, wie die SignalR-jQuery-Bibliothek, die zur Kommunikation mit einer SignalR-Hub verwenden.</span><span class="sxs-lookup"><span data-stu-id="2887c-193">The **Chat.cshtml** view file in the code sample shows how to use the SignalR jQuery library to communicate with a SignalR hub.</span></span> <span data-ttu-id="2887c-194">Die wesentlichen Aufgaben in der Code erstellen einen Verweis auf den automatisch generierten Proxy für den Hub, Deklarieren einer Funktion, die Inhalt per Pushvorgang an Clients der Server aufrufen können, und starten eine Verbindung zum Senden von Nachrichten an den Hub.</span><span class="sxs-lookup"><span data-stu-id="2887c-194">The essential tasks in the code are creating a reference to the auto-generated proxy for the hub, declaring a function that the server can call to push content to clients, and starting a connection to send messages to the hub.</span></span>

<span data-ttu-id="2887c-195">Der folgende Code deklariert einen Proxy für einen Hub.</span><span class="sxs-lookup"><span data-stu-id="2887c-195">The following code declares a proxy for a hub.</span></span>

[!code-javascript[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample6.js)]

> [!NOTE]
> <span data-ttu-id="2887c-196">In jQuery wird der Verweis auf die Server-Klasse und ihre Member in Camel-Case.</span><span class="sxs-lookup"><span data-stu-id="2887c-196">In jQuery the reference to the server class and its members is in camel case.</span></span> <span data-ttu-id="2887c-197">Das Codebeispiel verweist auf die C#- **ChatHub** Klasse in jQuery als **ChatHub**.</span><span class="sxs-lookup"><span data-stu-id="2887c-197">The code sample references the C# **ChatHub** class in jQuery as **chatHub**.</span></span> <span data-ttu-id="2887c-198">Wenn Sie verweisen möchten die `ChatHub` Klasse in jQuery mit herkömmlichen Pascal Groß-/Kleinschreibung wie in c#-Datei der ChatHub.cs bearbeiten.</span><span class="sxs-lookup"><span data-stu-id="2887c-198">If you want to reference the `ChatHub` class in jQuery with conventional Pascal casing as you would in C#, edit the ChatHub.cs class file.</span></span> <span data-ttu-id="2887c-199">Hinzufügen einer `using` Anweisung auf die `Microsoft.AspNet.SignalR.Hubs` Namespace.</span><span class="sxs-lookup"><span data-stu-id="2887c-199">Add a `using` statement to reference the `Microsoft.AspNet.SignalR.Hubs` namespace.</span></span> <span data-ttu-id="2887c-200">Fügen Sie dann die `HubName` -Attribut auf die `ChatHub` Klasse, zum Beispiel `[HubName("ChatHub")]`.</span><span class="sxs-lookup"><span data-stu-id="2887c-200">Then add the `HubName` attribute to the `ChatHub` class, for example `[HubName("ChatHub")]`.</span></span> <span data-ttu-id="2887c-201">Zum Schluss aktualisieren Sie den jQuery-Verweis auf die `ChatHub` Klasse.</span><span class="sxs-lookup"><span data-stu-id="2887c-201">Finally, update your jQuery reference to the `ChatHub` class.</span></span>


<span data-ttu-id="2887c-202">Der folgende Code zeigt, wie Sie eine Callback-Funktion im Skript zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="2887c-202">The following code shows how to create a callback function in the script.</span></span> <span data-ttu-id="2887c-203">Die hubklasse auf dem Server ruft diese Funktion, um Inhaltsupdates per Pushvorgang an jeden Client übertragen.</span><span class="sxs-lookup"><span data-stu-id="2887c-203">The hub class on the server calls this function to push content updates to each client.</span></span> <span data-ttu-id="2887c-204">Der Aufruf optional die `htmlEncode` Funktion zeigt die Möglichkeit, HTML codieren von Inhalt der Nachricht vor der Anzeige auf der Seite als eine Möglichkeit zum Skript-Injection verhindern.</span><span class="sxs-lookup"><span data-stu-id="2887c-204">The optional call to the `htmlEncode` function shows a way to HTML encode the message content before displaying it in the page, as a way to prevent script injection.</span></span>

[!code-html[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample7.html)]

<span data-ttu-id="2887c-205">Der folgende Code zeigt, wie Sie eine Verbindung mit dem Hub zu öffnen.</span><span class="sxs-lookup"><span data-stu-id="2887c-205">The following code shows how to open a connection with the hub.</span></span> <span data-ttu-id="2887c-206">Der Code wird die Verbindung gestartet und leitet diese dann in eine Funktion zum Behandeln der Click-Ereignis auf der **senden** auf der Seite Chat.</span><span class="sxs-lookup"><span data-stu-id="2887c-206">The code starts the connection and then passes it a function to handle the click event on the **Send** button in the Chat page.</span></span>

> [!NOTE]
> <span data-ttu-id="2887c-207">Dieser Ansatz wird sichergestellt, dass die Verbindung hergestellt wurde, bevor der Ereignishandler ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="2887c-207">This approach ensures that the connection is established before the event handler executes.</span></span>


[!code-javascript[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample8.js)]

<a id="next"></a>

## <a name="next-steps"></a><span data-ttu-id="2887c-208">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="2887c-208">Next Steps</span></span>

<span data-ttu-id="2887c-209">Sie haben gelernt, dass es sich bei SignalR handelt es sich ein Framework zum Erstellen von Echtzeit-Webanwendungen.</span><span class="sxs-lookup"><span data-stu-id="2887c-209">You learned that SignalR is a framework for building real-time web applications.</span></span> <span data-ttu-id="2887c-210">Sie erfahren auch mehrere Aufgaben bei der Entwicklung von SignalR: wie eine ASP.NET-Anwendung SignalR hinzugefügt, wie zum Erstellen einer hubklasse und das Senden und Empfangen von Nachrichten aus dem Hub.</span><span class="sxs-lookup"><span data-stu-id="2887c-210">You also learned several SignalR development tasks: how to add SignalR to an ASP.NET application, how to create a hub class, and how to send and receive messages from the hub.</span></span>

<span data-ttu-id="2887c-211">Erweiterte Konzepte der SignalR-Entwicklungen finden Sie unter den folgenden Websites für SignalR-Quellcode und Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="2887c-211">To learn more advanced SignalR developments concepts, visit the following sites for SignalR source code and resources :</span></span>

- [<span data-ttu-id="2887c-212">SignalR-Projekt</span><span class="sxs-lookup"><span data-stu-id="2887c-212">SignalR Project</span></span>](http://signalr.net)
- [<span data-ttu-id="2887c-213">SignalR Github und Beispiele</span><span class="sxs-lookup"><span data-stu-id="2887c-213">SignalR Github and Samples</span></span>](https://github.com/SignalR/SignalR)
- [<span data-ttu-id="2887c-214">SignalR Wiki</span><span class="sxs-lookup"><span data-stu-id="2887c-214">SignalR Wiki</span></span>](https://github.com/SignalR/SignalR/wiki)
